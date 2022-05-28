# Fixing Embedded Controller (SSDT-EC/USBX)
### 什么是 EC？
在计算机平台上，Embedded Controller 就是我们常说的 EC，它主要是由一颗特殊的 MCU 来构成，通常提供 LPC 的接口，挂在 LPC 总线之下，由 MCU 所集成的一些外设来为 PC 提供一些 Power，thermal，以及一些其他的控制。

### What is the SSDT-EC/USBX used for?
> The purpose of SSDT-EC/USBX is a couple things:
> 
> * On desktops, the EC(or better known as the embedded controller) isn't compatible with AppleACPIEC driver, to get around this we disable this device 
>   when running macOS
> * AppleBusPowerController will look for a device named EC, so we will want to create a fake device for this kext to load onto
>     * AppleBusPowerController also requires a USBX device to supply USB power properties for Skylake and newer, so we will bundle this device in with the >       EC fix
> * On laptops, the EC is used for hotkeys and battery so disabling this isn't all too ideal. Problem is our EC's name isn't compatible, so we will create >   a simple "fake" EC device that will satisfy Apple
