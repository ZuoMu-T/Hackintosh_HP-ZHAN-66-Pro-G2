# Part1：关于 Hackintosh / HP ZHAN 66 Pro G2(15.6)
淘汰下来的机子装个黑苹果，仅供参考。`如发生意外，后果自负！！！`
### 机器配置及相关说明
###### 虽然是淘汰下来的，但是从朋友那拿了内存和硬盘——全升级了 :sunglasses:
| 硬件 | Descriptions | 说明 |
| :-: | :-: | :- |
| CPU | Intelᴿ Coreᵀᴹ i5-8265U 四核 @1.60GHz |
|  dGPU | Nvidia GeForce MX250 | 无法驱动 |
| IGPU | UHD Graphics 620 | 在 Monterey 下掉帧严重 |
| Memory | Kingston 2666MHz 8Gx2 | i5-8265U 最高仅支持 2400 MHz |
|  WiFi | BCM94360CS2 with Bluetooth 4.0 | 拆机卡，AirDrop 等正常，需配合反向转接卡使用 |
| Ethernet | Realtek RTL8168H/8111H | 支持 ASPM-L1 低功耗待机模式 |
| 声卡 |  ALC236 | 扬声器及麦克风正常，无杂音 |
| SSD | WD Blue SN550 NVMe 1T(PCIe 3.0) | 支持 ASPM-L1 低功耗待机模式 |
| SATA | Samsung 870 EVO 500GB | 可用来装 Windows |
| USB | Intel(R) USB 3.1 可扩展主机控制器 | 已定制 USB，type-c 速率正常 |
| SDCard | Intel SD控制器 | 生产厂家 BayHubTech，无法驱动 |

##### 其他说明：</br>
* 此为双硬盘双系统配置，单硬盘双系统请另觅他法
* USB 需根据自己机器定制，不保证不同批次机型相同
* 使用 SSDT-RMCF-PS2Map-Zhan66G2.aml 重新映射了键盘，降低亮度为`fn+f3`，提高亮度`fn+f4`，请勿在系统设置更改
* CPU & 集显</br>

  | CPU编号 | 集显 | 集显基本频率 | 集显最大动态频率 | 集显 ID |
  | :-: | :-: | :-: | :-: | :-: |
  | [i5-8265U](https://ark.intel.com/content/www/us/en/ark/products/149088/intel-core-i58265u-processor-6m-cache-up-to-3-90-ghz.html) | UHD 620 | 300 MHz | 1.10 GHz | 3EA0, 3EA1 |
 
* 声卡</br>

  | Vendor | Codec | Revisions and layouts | MinKernel | MaxKernel |
  | :-: | :-: | :-: | :-: | :-: |
  | Realtek | [ALC236](https://github.com/acidanthera/AppleALC/tree/master/Resources/ALC236) | 0x100001, 0x100002</br>layout 3?,11,12,13,14,15,16,17,18,23,36,54,99 | 13 (10.9) | - |
  * 3，11，12 未测试

</br>
</br>

# Part2：关于部分 SSDT 的构建说明
详见 [ACPI_Build](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/tree/master/ACPI_Build) 部分

### 00-ASL 语法基础
* 我即忙又懒 :joy:，ASL 语法基础请移步[远景论坛](https://bbs.pcbeta.com)以下尊位，特此感谢作者 @suhetao

  远景论坛 -> [suhetao：[原创内容] 自己动手写DSDT系列教程1-ASL（ACPI Source Language）基础篇](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=944566&archive=2&extra=page%3D1&page=1)



### 01-EC
* [关于台式机、笔记本修复 EC（Embedded Controller）的方法](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/01-EC/README.md)


### 02-Disabling dGPUs
* [直接调用 `.off` 函数禁用独显的方法](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/02-Disabling%20dGPUs/README.md)


### 03-AWAC:RTC Fix
* [由 AWAC/RTC 导致引导、启动问题的三个 Case](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/tree/master/ACPI_Build/03-AWAC:RTC%20Fix)
  * Case 1. 系统启用了 AWAC 而禁用了 RTC <br>
    
  * Case 2. AWAC 没有 \_STA 参数控制，或主板没有 RTC 设备 <br>
    
  * Case 3. AWAC/RTC 在多数 X99 和 X299 主板上导致的 early boot failures 问题 <br>


### 04-Fixing NVRAM
* 待更新......



</br>
</br>
</br>

### 如发现问题请 QQ 1639791757 不一定有时间

<br>
<br>

## 免责声明
  使用本配置产生的任何意外，后果自负！！！

<br>
<br>

# Credits
* 特别感谢
  * @ [Dortania](https://github.com/dortania) 基于 Opencore 0.78 的 [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
  * @ [Dortania](https://github.com/dortania) 制作 [Getting-Started-With-ACPI](https://github.com/dortania/Getting-Started-With-ACPI)
  * @ [Acidanthera](https://github.com/acidanthera) maintaining [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)

<br>

* 感谢
  * @ [liaoyudong2](https://github.com/liaoyudong2)
  * @ 大头蔡Cass
  * @ suhetao
  * @ 感谢我的朋友提供硬件 :rofl:
  * @ ......
