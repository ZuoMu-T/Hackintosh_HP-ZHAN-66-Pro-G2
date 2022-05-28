# Part1：关于Hackintosh / HP ZHAN 66 Pro G2(15.6)
淘汰下来的机子装个黑苹果，仅供参考。`如发生意外，后果自负！！！`
### 机器配置及相关说明
###### 虽然是淘汰下来的，但是从朋友那拿了内存和硬盘——全升级了 :sunglasses:
| 硬件 | Descriptions | 说明 |
| :-: | :-: | :- |
| CPU | Intelᴿ Coreᵀᴹ i5-8265U 四核 @1.60GHz |
|  dGPU | Nvidia GeForce MX250 | 无法驱动 |
| IGPU | UHD Graphics 620 | 在Monterey下掉帧严重 |
| Memory | Kingston 2666MHz 8Gx2 | i5-8265U最高仅支持 2400 MHz |
|  WiFi | BCM94360CD2 with Bluetooth 4.0 | 拆机卡需配合反向转接卡使用 |
| Ethernet | Realtek RTL8168H/8111H | 支持ASPM-L1低功耗待机模式 |
| 声卡 |  ALC236 | 扬声器及麦克风正常，无杂音 |
| SSD | WD Blue SN550 NVMe 1T(PCIe 3.0) | 支持ASPM-L1低功耗待机模式 |
| SATA | Samsung 870 EVO 500GB | 可用来装 Windows |
| USB | Intel(R) USB 3.1 可扩展主机控制器 | 已定制 USB，type-c 速率正常 |
| SDCard | Intel SD控制器 | 生产厂家BayHubTech，无法驱动 |

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

# Part2：关于部分SSDT的说明
详见 [ACPI_Build](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/tree/master/ACPI_Build) 部分
</br>
</br>
</br>

# Credits
* 特别感谢
  * @ [Dortania](https://github.com/dortania) 基于 Opencore 0.78 的 [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
  * @ [Dortania](https://github.com/dortania) 制作 [Getting-Started-With-ACPI](https://github.com/dortania/Getting-Started-With-ACPI)
  * @ [Acidanthera](https://github.com/acidanthera) maintaining [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)
* 感谢
  * @ [liaoyudong2](https://github.com/liaoyudong2)
  * @ 大头蔡Cass
  * @ 感谢我的朋友提供硬件 :rofl:
  * @ ......
