# RTC Fix (SSDT-AWAC/RTC0)
### 什么是 RTC?
RTC，实时时钟，即Real-time clock，是一个独立的 BCD 定时器/计数器，因此也称为时钟芯片。它为人们提供精确的实时时间，或者为电子系统提供精确的时间基准，目前实时时钟芯片大多采用精度较高的晶体振荡器作为时钟源。<br>
* RTC 提供一个日历时钟、两个可编程闹钟中断，以及一个具有中断功能的周期性可编程唤醒标志。<br>
* RTC 还包含用于管理低功耗模式的自动唤醒单元。<br>
由于现代新模具的 Windows 电脑大多启用了 AWAC （Always On Always Connected ）时钟，而 macOS 无法与 AWAC 时钟通信，因此需要强制使用传统 RTC 时钟而禁用 AWAC （如果有的话，另外对于没有 RTC 的主板需要创建“假的” RTC 供 macOS 使用）。

### 由 AWAC/RTC 引发问题的 BIOS 设定有三种：<br>
Case 1. 系统启用了 AWAC 而禁用了 RTC，这种是最简单的；<br>
Case 2. AWAC 没有_STA 参数控制，或主板 BIOS 没有 RTC 设备；<br>
Case 3. 对于多数 X99 和 X299 主板，BIOS 提供了两组 RTC memory，但是并不是连续的，因此在启动时会导致 early boot failures，因此需要修复。<br>

#### Case 1
在 Windows 下使用 [SSDTTime](https://github.com/corpnewt/SSDTTime) 提取 DSDT.aml，使用 [MaciASL](https://github.com/acidanthera/MaciASL) 打开，搜索 `ACPI000E`（这是 AWAC 设备在 BIOS 中的 Hardware_ID），应该可以得到以下信息（以我的 DSDT 为例）：

```ASL
   Scope (_SB)
    {
        Device (AWAC)
        {
            Name (_HID, "ACPI000E" /* Time and Alarm Device */)  // _HID: Hardware ID
            Name (WAST, Zero)
            Name (WTTR, Zero)
            Method (_PRW, 0, NotSerialized)  // _PRW: Power Resources for Wake
            {
                Return (GPRW (0x72, 0x04))
            }

            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                If ((STAS == Zero))
                {
                    Return (0x0F)
                }
                Else
                {
                    Return (Zero)
                }
            }
	...
	}
```

从以上代码中可以看出：主板 BIOS 启用了 AWAC，是通过 _STA 函数控制的（如果没有请转[Case 2]()），函数返回参数 STAS = 0 时，启用 AWAC [^1]。<br>
接下来搜索 `PNP0B00` （这是 RTC 设备在 BIOS 中的 Hardware_ID）以确定 BIOS 是否有 RTC 设备 ,可以得到以下信息（以我的 DSDT 为例）：
<br>

```ASL
     Device (RTC)
        {
            Name (_HID, EisaId ("PNP0B00") /* AT Real-Time Clock */)  // _HID: Hardware ID
            Name (_CRS, ResourceTemplate ()  // _CRS: Current Resource Settings
            {
                IO (Decode16,
                    0x0070,             // Range Minimum
                    0x0070,             // Range Maximum
                    0x01,               // Alignment
                    0x08,               // Length
                    )
                IRQNoFlags ()
                    {8}
            })
            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                If ((STAS == One))
                {
                    Return (0x0F)
                }
                Else
                {
                    Return (Zero)
                }
            }
        }
```

从以上代码中可以看出：主板 BIOS 禁用了 RTC，并且是通过 _STA 函数控制，函数返回参数 STAS = 1 时，禁用 RTC [^2]。<br>

**通过 [^1]、[^2] 不难分析出：只需令返回参数 STAS = 0，既可禁用 AWAC，又可启用 RTC。**因此我们可以直接将 SSDT-AWAC-No-RTC-Yes-对应 Case 1.dsl 另存为 SSDT-AWAC-No-RTC-Yes.aml 以解决此问题。<br>
<br>

#### Case 2
对于搜索 `ACPI000E`（这是 AWAC 设备在 BIOS 中的 Hardware\_ID）无法找到_STA 函数，或是搜索 `PNP0B00` （这是 RTC 设备在 BIOS 中的 Hardware\_ID）无法找到 RTC 设备的用户，需要查找自己 LPC （LowPinCount Path） 和 PCI （Peripheral Component Interconnect Path），以为新创建的 RTC 设置路径。

第一步，先搜索 `Name (_ADR, 0x001F0000)` 以确定 LPC 的路径：

```ASL
      Device (LPCB)
        {
            Name (_ADR, 0x001F0000)  // _ADR: Address
            Method (_DSM, 4, Serialized)  // _DSM: Device-Specific Method
            {
                If (PCIC (Arg0))
                {
                    Return (PCID (Arg0, Arg1, Arg2, Arg3))
                }

                Return (Buffer (One)
                {
                     0x00                                             // .
                })
            }
	}
```

<br>
第二步，先搜索 `PNP0A08` 以确定 PCI 的路径（如果出现多个，请使用第一个 PCI 路径）：

```ASL
      Device (PCI)
        {
            Name (_HID, EisaId ("PNP0A08") /* PCI Express Bus */)  // _HID: Hardware ID
            Name (_CID, EisaId ("PNP0A03") /* PCI Bus */)  // _CID: Compatible ID
            Name (_SEG, Zero)  // _SEG: PCI Segment
            Name (_ADR, Zero)  // _ADR: Address
            Method (^BN00, 0, NotSerialized)
            {
                Return (Zero)
            }

            Method (_BBN, 0, NotSerialized)  // _BBN: BIOS Bus Number
            {
                Return (BN00 ())
            }
	}
```

通过以上两步，就可以确定 RTC 的路径应为 _SB.PCI0.LPCB，接下来去修改 [SSDT-RTC0-对应 Case 2.dsl] 中的路径：

```ASL
External (_SB_.PCI0.LPCB, DeviceObj)     		<- 修改此路径

Scope (_SB.PCI0.LPCB)  			<- 修改此路径
```

（不要管源代码里是什么，用你自己的路径去替换）
<br>
修改完成后得到的代码结构如下：<br>
<br>

```ASL
External (_SB_.PCI0.LPCB, DeviceObj)     		<- 修改此路径

Scope (_SB.PCI0.LPCB) 
```

然后另存为 SSDT-RTC.aml 即可。<br>
<br>

#### Case 3
对于X99 和 X299，在 Windows 下使用 [SSDTTime](https://github.com/corpnewt/SSDTTime) 提取 DSDT.aml，使用 [MaciASL](https://github.com/acidanthera/MaciASL) 打开，搜索 `PNP0B00 `（这是 AWAC 设备在 BIOS 中的 Hardware_ID），应该可以得到以下信息（以Asus Strix X299 - E Gaming 为例）：

```ASL
    Name (_CRS, ResourceTemplate ()  // _CRS: Current Resource Settings
    {
        IO (Decode16,
            0x0070,             // Range Minimum 1
            0x0070,             // Range Maximum 1
            0x01,               // Alignment 1
            0x02,               // Length 1
           )
        IO (Decode16,
            0x0074,             // Range Minimum 2
            0x0074,             // Range Maximum 2
            0x01,               // Alignment 2
            0x04,               // Length 2
           )
        IRQNoFlags ()
            {8}
    })
```

从上面的代码中，我们看到有 2 组 RTC memory：
第一组：从 0x70 开始，长度（Length 1）为 0x2，分别是0x70，0x71，缺少0x72 和 0x73映射；<br>
第二组：从 0x74 开始，长度（Length 2）为 0x4，分别是0x74，0x75，0x76，0x77。<br>

由于第一组缺少0x72 和 0x73映射，在启动时会导致 early boot failures，因此需要扩大范围以将0x72 和 0x73映射包含在第一组RTC memory下。<br>

Note that not all boards will be the same, verify with your ACPI tables for both the range and 
            * missing regions.

**Note：
	* 并非所有主板板都如同例子中这样，请认真检查 `PNP0B00 ` 的范围和缺失的映射值；<br>
	* 如果你的主板 BIOS 只有一组 RTC memory，长度（Length）为 0x08，那它就不存在此问题。**<br>
<br>

接着，需要确定你的 RTC 设备是否有 _STA 函数。如果没有，需要在SSDT-RTC0-RANGE-对应 Case 3.dsl 中启用。

然后，搜索 `PNP0B00` 以确定 RTC 设备的路径。

下一步，去修改 [SSDT-RTC0-RANGE-对应 Case 3.dsl] 中的路径：

```ASL
    External (_SB_.PC00.LPC0, DeviceObj)  		<- 修改此路径
    External (_SB_.PC00.LPC0.RTC_, DeviceObj)   	<- 修改此路径

    Scope (_SB.PC00.LPC0)
    {
       /* 
        * 如果您的 ACPI 没有 AWAC 设备（ACPI000E），请取消下方注释启用此 _STA 函数
        */
       /*
        Scope (RTC)
        {
            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                If (_OSI ("Darwin"))
                {
                    Return (Zero)
                }
                Else
                {
                    Return (0x0F)
                }
            }
        }
        */

```

<br>

```ASL
        Device (RTC0)		<- 修改此路径
        {
           /*
            * Change the below _CSR range to match your hardware.
            *
            * For this example, we'll use the Asus Strix X299-E Gaming's ACPI, and show how to correct it.
            * Within the original RTC device, we see that sections 0x70 through 0x77 are mapped:
            *
            *    Name (_CRS, ResourceTemplate ()  // _CRS: Current Resource Settings
            *    {
            *        IO (Decode16,
            *            0x0070,             // Range Minimum 1
            *            0x0070,             // Range Maximum 1
            *            0x01,               // Alignment 1
            *            0x02,               // Length 1
            *           )
            *        IO (Decode16,
            ...
	      ...
	      ...
	}
```

（不要管源代码里是什么，用你自己的路径去替换）

最后另存为 SSDT-RTC0-RANGE.aml 即可。

Last But Not Least
此处仅涉及 AWAC/RTC 导致的安装启动相关问题，有关 AWAC/RTC 的其他功用，请参考其他作者的教程。

<br>
<br>

## Thanks To
* [Getting：Getting Started With ACPI](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html)





