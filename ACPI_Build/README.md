# ACPI_Build

## 写在前面
此部分主要参考 [Dortania：Getting-Started-With-ACPI](https://dortania.github.io/Getting-Started-With-ACPI/#a-quick-explainer-on-acpi)，特此感谢！

<br>

## 目录及说明
### 00-ASL 语法基础
* 我即忙又懒 :joy:，ASL 语法基础请移步[远景论坛](https://bbs.pcbeta.com)以下尊位，特此感谢作者 @suhetao

  远景论坛 -> [suhetao：[原创内容] 自己动手写DSDT系列教程1-ASL（ACPI Source Language）基础篇](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=944566&archive=2&extra=page%3D1&page=1)



### 01-EC
* [关于台式机、笔记本修复 EC（Embedded Controller）的方法](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/01-EC/README.md)
  * [SSDT-EC-USBX.dsl](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/01-EC/SSDT-EC-USBX.dsl)



### 02-Disabling dGPUs
* [直接调用 .off 函数禁用独显的方法](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/02-Disabling%20dGPUs/README.md)
  * [SSDT-dGPU-Off.dsl](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/02-Disabling%20dGPUs/SSDT-dGPU-Off.dsl)



### 03-AWAC:RTC Fix
* 由 AWAC/RTC 导致引导、启动问题的三个 Case
  * [Case 1. 系统启用了 AWAC 而禁用了 RTC](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/tree/master/ACPI_Build/03-AWAC:RTC%20Fix) <br>
    * [SSDT-AWAC-No-RTC-Yes-对应 Case 1.dsl](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/03-AWAC:RTC%20Fix/SSDT-AWAC-No-RTC-Yes-%E5%AF%B9%E5%BA%94%20Case%201.dsl)
  * [Case 2. AWAC 没有 \_STA 参数控制，或主板没有 RTC 设备](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/tree/master/ACPI_Build/03-AWAC:RTC%20Fix) <br>
    * [SSDT-RTC0-对应 Case 2.dsl](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/03-AWAC:RTC%20Fix/SSDT-RTC0-%E5%AF%B9%E5%BA%94%20Case%202.dsl)
  * [Case 3. AWAC/RTC 在多数 X99 和 X299 主板上导致的 early boot failures 问题](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/tree/master/ACPI_Build/03-AWAC:RTC%20Fix) <br>
    * [SSDT-RTC0-RANGE-对应 Case 3.dsl](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/03-AWAC:RTC%20Fix/SSDT-RTC0-RANGE-%E5%AF%B9%E5%BA%94%20Case%203.dsl)



### 04-Fixing NVRAM
* 



<br>
<br>
<br>
<br>
<br>
<br>

## 太忙了太忙了太忙了！！！
有时间了在更新。。。

<br>
<br>


## :mortar_board:  References
* [Dortania：Getting-Started-With-ACPI](https://dortania.github.io/Getting-Started-With-ACPI/#a-quick-explainer-on-acpi)
* [suhetao：[原创内容] 自己动手写DSDT系列教程1-ASL（ACPI Source Language）基础篇](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=944566&archive=2&extra=page%3D1&page=1)
