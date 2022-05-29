# Disabling laptop dGPUs (SSDT-dGPU-Off.aml)
除了部分旧模具的 NVIDIA 独显可以在 macOS 中驱动外，现在新模具的 NVIDIA 独显都无法驱动。但由于系统启动时所有硬件都会被初始化，以供系统加载时识别 I/O 总线，从主机总线适配器调度 opROM（option ROM）等操作，因此独显在未被禁用时仍然会有电能损耗。
<br>
### 禁用独显的几种方法
1. BIOS 直接禁用。（应该没人这么做吧）<br>
2. 使用 WhatGreen 的 -wegnoegpu 导参数对 macOS `隐藏` dGPU，但此时 dGPU 仍在缓慢消耗电池电量。此方法仅推荐在安装时使用。<br>
3. 直接调用独显的 .off 方法，这种方法应该适用于大多数用户（此 SSDT 的方法）。

> ```
> Note：
> 	_OFF 方法可能在 DSDT 或 SSDT 中
> 	
> 	_OFF 也可能有不同的名称，如GPOF、OPOF、SGOF _PS3 等
> ```

4. 对于某些机器，简单的 .off 调用无法禁用独显，只能调用 _PS3 将 dGPU 限制到 D3 状态，这是设备可以支持的最低功耗状态。（此方法不在此讨论）<br>

<br>
<br>

### 获取独显的 BIOS 路径
在 Windows 上进行操作，执行以下操作：

```
Device Manager  --> Display Adapters  --> dGPU  --> Properties  --> Details  --> BIOS device name
```

以我的机器为例：<br>

```
NVIDIA GeForce MX250： _SB.PCIO.RP05.PXSX
```


接下来我们只需要更改 [SSDT-dGPU-Off.dsl](https://github.com/ZuoMu-T/Hackintosh_HP-ZHAN-66-Pro-G2/blob/master/ACPI_Build/02-Disabling%20dGPUs/SSDT-dGPU-Off.dsl) 中的 ACPI 路径：<br>

```
External(_SB.PCI0.PEG0.PEGP._OFF, MethodObj)	<-- 修改此处

If (CondRefOf(\_SB.PCI0.PEG0.PEGP._OFF)) { \_SB.PCI0.PEG0.PEGP._OFF () }	<-- 修改此处
```

<br>
修改完成后得到的代码结构如下：<br>
<br>

```
External(_SB.PCIO.RP05.PXSX._OFF, MethodObj)

If (CondRefOf(\_SB.PCIO.RP05.PXSX._OFF)) { \_SB.PCIO.RP05.PXSX._OFF () }
```

<br>
<br>

#### 最后另存为 SSDT-dGPU-Off.aml 即可。

<br>
<br>

## References
* [RehabMan：[Guide] Disabling discrete graphics in dual-GPU laptops](https://www.tonymacx86.com/threads/guide-disabling-discrete-graphics-in-dual-gpu-laptops.163772/)
