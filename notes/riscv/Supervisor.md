# Supervisor Mode

`S-mode`是`RISC-V`用于支撑操作系统的模式，这个模式下一共有

1. `sstatus`，`stval`，`stvec`，`sepc`，`sscratch`，`sip`，`sie`，`scause`八个异常寄存器
2. `satp`页表寄存器
3. `time`，`cycle`，`instret`，`hpmcounter3-31`性能寄存器
4. `scounteren`性能寄存器的开关寄存器

以及`SRET`还有`SFENCE.VMA`两个新增指令。

### Register

1. `sstatus`是记录状态的寄存器，`RISC-V`中记录状态的寄存器一共有`mstatus`，`hstatus`，还有`vsstatus`，这个寄存器每个字段有每个字段的功能：
   1. `UXL = [33:32]`，表示用户态采用的`ISA`的长度。用户态的`ISA`必须小于或等于内核态的`ISA`。
   2. `SPP = [8]`，表示进入`SuperVisor Mode`前的状态
