# CPU

- 实模式：可以随意执行全部CPU指令，随意直接通过物理地址访问内存
- 保护模式：将指令划分为ring0到ring3，调用CPU指令或者访问内存需要CPU和操作系统允许
- 长模式

中断，即通过硬件或软件的方式告诉CPU来执行一段特殊的代码。比如键盘输入，就是通过硬件中断的方式告知操作系统的

## 指令集

参考：<https://time.geekbang.org/column/intro/100117801?tab=catalog>

Intel 和 AMD 以售卖 x86 芯片为主，而 ARM Ltd 直接售卖 ARM 芯片的源码，连生产芯片的步骤都省了。

### CISC 复杂指令集

- AMD64 基于x86架构的64位扩展

于1999年由AMD设计，所以也称之为AMD64(Advanced Micro Devices)，也叫 x86-64、x86_64、x64，后被Intel采用称之为Intel64

### RISC 精简指令集

- ARMv8/AArch64/ARM64 ARM(Advanced RISC Machines)体系架构的64位扩展
- ARMv7/AArch32
- RISC-V：基于 RISC 的开源指令集架构，可以在BSD许可下自由使用，指令简单，模块化设计易于扩展
- 其它（衰退）：mips、sparc

## 内存和CPU

```shell
# 查看CPU
lscpu
"""
Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         46 bits physical, 48 bits virtual
  Byte Order:            Little Endian
CPU(s):                  1
  On-line CPU(s) list:   0
Vendor ID:               GenuineIntel
  Model name:            QEMU Virtual CPU version (cpu64-rhel6)
    CPU family:          6
    Model:               13
    Thread(s) per core:  1
    Core(s) per socket:  1
    Socket(s):           1
    Stepping:            3
    BogoMIPS:            5199.99
    Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm rep_good nopl xtopology cpuid tsc
                         _known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt aes xsave avx f16c rdrand hypervisor lahf_lm pti fsgsbase smep xsaveopt
Virtualization features: 
  Hypervisor vendor:     KVM
  Virtualization type:   full
Caches (sum of all):     
  L1d:                   32 KiB (1 instance)
  L1i:                   32 KiB (1 instance)
  L2:                    4 MiB (1 instance)
  L3:                    16 MiB (1 instance)
NUMA:                    
  NUMA node(s):          1
  NUMA node0 CPU(s):     0
Vulnerabilities:         
  Itlb multihit:         KVM: Mitigation: VMX unsupported
  L1tf:                  Mitigation; PTE Inversion
  Mds:                   Vulnerable: Clear CPU buffers attempted, no microcode; SMT Host state unknown
  Meltdown:              Mitigation; PTI
  Spec store bypass:     Vulnerable
  Spectre v1:            Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:            Mitigation; Retpolines, STIBP disabled, RSB filling
  Srbds:                 Not affected
  Tsx async abort:       Not affected
"""
```
