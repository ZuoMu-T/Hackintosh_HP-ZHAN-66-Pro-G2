# Fixing Embedded Controller (SSDT-EC-USBX.aml)
### 什么是 EC？
在计算机平台上，Embedded Controller 就是我们常说的 EC，它主要是由一颗特殊的 MCU 来构成，通常提供 LPC 的接口，为笔记本电脑提供一些电源管理，如笔记本进入待机或关机模式，外部电源系统的电力调度，智能电池的电力检测，充放电任务，以及一些实用的快捷按纽等。EC是一个单独的简单处理器，在开机前和开机过程中对整个系统起着全局的管理，而 BIOS 是在等 EC 把内部的物理环境初始化后才开始运行的。

### What is the SSDT-EC-USBX.aml used for?
> The purpose of SSDT-EC-USBX.aml is a couple things:
> 
> * On desktops, the EC(or better known as the embedded controller) isn't compatible with AppleACPIEC driver, to get around this we disable this device when running macOS
> * AppleBusPowerController will look for a device named EC, so we will want to create a fake device for this kext to load onto
> 	* AppleBusPowerController also requires a USBX device to supply USB power properties for Skylake and newer, so we will bundle this device in with the EC fix
> * On laptops, the EC is used for hotkeys and battery so disabling this isn't all too ideal. Problem is our EC's name isn't compatible, so we will create a simple "fake" EC device that will satisfy Apple

<br>
<br>

### 查找 ACPI 的路径
在 Windows 下使用 [SSDTTime](https://github.com/corpnewt/SSDTTime) 提取 DSDT.aml，使用 [MaciASL](https://github.com/acidanthera/MaciASL) 打开，搜索 `PNP0C09`，应该可以得到以下信息（以我的 DSDT 为例）：

```
Scope (_SB.PCI0.LPCB)
{
	Device (EC0)
    	{
    		Name (_HID, EisaId ("PNP0C09") /* Embedded Controller Device */)  // _HID: Hardware ID
    		Name (_UID, One)  // _UID: Unique ID
     		Name (_GPE, 0x6E)  // _GPE: General Purpose Events
       		Method (_STA, 0, NotSerialized)  // _STA: Status
   		{
    			Return (0x0F)
		}
	...
	
	}
}
```

从以上代码中可以看到：<br>
* 嵌入式控制器的名称： EC0
* 嵌入式控制器的路径：\_SB\_.PCI0.LPCB
<br>
在此基础上，我们要查看此 EC0 下是否有_STA 的 Method，如果没有需要在 SSDT-EC-USBX 里构建。<br>
<br>
<br>

### 以 [SSDT-EC-USBX.dsl](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/EC/SSDT-EC-USBX.dsl) 为基础构建 SSDT-EC-USBX.aml <br>

```
External (_SB_.PC00.LPC0, DeviceObj)   <-  修改此路径

Scope (_SB.PC00.LPC0)   <- 修改此路径
```

（不要管源代码里是什么，用你自己的路径去替换）
<br>

完成后得到的代码结构如下：<br>

```
External (_SB_.PCI0.LPCB, DeviceObj) 

Scope (_SB.PCI0.LPCB) 
```

<br>

#### 注： <br>
* 台式机禁用系统 EC0，笔记本不禁用，即不要取消禁用 EC0 的注释，但都需要创建 EC<br>
* 是否启用 \_STA 取决于原本 EC0 下是否有 \_STA<br>
* EC 的名称可能是 EC0，H_EC
* USBX 的代码是通用的
<br>

#### 最后另存为 SSDT-EC-USBX.aml 即可。
