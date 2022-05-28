# Fixing Embedded Controller (SSDT-EC-USBX.aml)
### 什么是 EC？
在计算机平台上，Embedded Controller 就是我们常说的 EC，它主要是由一颗特殊的 MCU 来构成，通常提供 LPC 的接口，挂在 LPC 总线之下，由 MCU 所集成的一些外设来为 PC 提供一些 Power，thermal，以及一些其他的控制。

### What is the SSDT-EC-USBX.aml used for?
> The purpose of SSDT-EC-USBX.aml is a couple things:
> 
> * On desktops, the EC(or better known as the embedded controller) isn't compatible with AppleACPIEC driver, to get around this we disable this device when running macOS
> * AppleBusPowerController will look for a device named EC, so we will want to create a fake device for this kext to load onto
> 	* AppleBusPowerController also requires a USBX device to supply USB power properties for Skylake and newer, so we will bundle this device in with the EC fix
> * On laptops, the EC is used for hotkeys and battery so disabling this isn't all too ideal. Problem is our EC's name isn't compatible, so we will create a simple "fake" EC device that will satisfy Apple
<br\>
<br\>

### 查找 ACPI 的路径
在 Windows 下使用 [SSDTTime] ([https://github.com/corpnewt/SSDTTime]) 提取 DSDT.mal，使用 [MaciASL] (https://github.com/acidanthera/MaciASL) 打开，搜索 PNP0C09，应该可以得到以下信息（以我的 DSDT 为例）：
``` <br\>
		Scope (_SB.PCI0.LPCB) <br>
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
* 此 DSDT 的嵌入式控制器的名称： EC0
* 嵌入式控制器的路径：_SB_.PCI0.LPCB
<br>
<br>
在此基础上，我们要查看此 EC0 下是否有_STA 的 Method，如果没有需要在SSDT-EC-USBX 里构建。<br>

### 以 SSDT-EC-USBX.dsl 为基础构建 SSDT-EC-USBX.aml <br>
```<br>
External (_SB_.PCI0.LPCB, DeviceObj)  <-  重命名此路径

Scope (_SB.PCI0.LPCB)  <- 重命名此路径
```<br>
完成后得到的代码结构如下：
```<br>
External (_SB_.PCI0.LPCB, DeviceObj) 

Scope (_SB.PCI0.LPCB) 
```<br>

注：<br>
	* 台式机禁用系统 EC0，笔记本不用禁用，即不要取消禁用 EC0 的注释<br>
	* 是否启用 _STA 取决于原本 EC0 下是否有_STA<br>
最后另存为 SSDT-EC-USBX.aml 文件即可。