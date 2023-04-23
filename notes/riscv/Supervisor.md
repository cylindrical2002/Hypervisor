# Supervisor Mode

`S-mode`是`RISC-V`用于支撑操作系统的模式，这个模式下一共有

1. `sstatus`，`stval`，`stvec`，`sepc`，`sscratch`，`sip`，`sie`，`scause`八个异常寄存器
2. `satp`页表寄存器
3. `time`，`cycle`，`instret`，`hpmcounter3-31`性能寄存器
4. `scounteren`性能寄存器的开关寄存器
5. `senvcfg`环境配置寄存器

以及`SRET`还有`SFENCE.VMA`两个新增指令。

### Register

1. `sstatus`是记录状态的寄存器，`RISC-V`中记录状态的寄存器一共有`mstatus`，`hstatus`，还有`vsstatus`，这个寄存器每个字段有每个字段的功能：
   
   1. `UXL = [33:32]`，表示用户态采用的`ISA`的长度。用户态的`ISA`必须小于或等于内核态的`ISA`。
   2. `SPP = [8]`，表示进入`SuperVisor Mode`前的状态，如果`SPP=0`则为`User`态，如果`SPP=1`则为`Supervisor`态。
   
      **注意**：一旦`SRET`执行，那么`SPP`将被设置为`0`
   
   3. `SIE = [1]`表示`Supervisor Mode`下是否允许中断，`SPIE = [5]`表示`Trap`前`SIE`的值。`Trap`W时`SPIE = SIE`，`SIE = 0`（默认情况下`Supervisor Mode`下不接受中断），`SRET`时`SIE = SPIE`，`SPIE = 1`
   4. `MXR = [19]`表示`Executable`的是否被视为`Readable`的，如果`MXR = 1`那么对`X = 1 R = 0`的虚拟内存使用`load`操作一样可以读取成功。
   5. `SUM = [18]`表示`Supervisor Mode`下能不能访问`U = 1`的页表项。
   6. `UBE = [6]`表示`User`态是大端序还是小端序，`UBE = 1`为大端序，`UBE = 0`为小端序。现代处理器常常固定大小端序，因此`UBE`常常是不可修改的。
   7. `SD = [63]`，`XS = [16:15]`，`FS = [14:13]`，`VS[10:9]`与扩展有关

2. `stvec`是说明`Trap`后会去哪里的寄存器，`BASE`字段提供了一个`62`位的基地址，`MODE = [1:0]`字段提供了寻址方式，

   1. `MODE = 1`，如果是`Exception`，`pc = BASE << 2` ，如果是`Interrupt`，`pc = BASE << 2 + 4 * cause`
   2. `MODE = 0`，`pc = BASE << 2`

3. `sip`是表示是否正在等待中断的寄存器，`sie`是表示是否开启中断的寄存器，只有在`SIE = 1`时有用。这两个寄存器上的每个字段有每个字段的功能。
   
   1. `SSIP = [1]`和`SSIE = [1]`，都是表示软件中断的，前者在`sip`中，是`read-only`的，表示现在是否有一个软件中断在`pending`，后者在`sie`中，是`read-write`的，表示`Supervisor Mode`下是否开启软件中断。
   1. `STIP = [5]`和`STIE = [5]`，都是表示时钟中断的，前者在`sip`中，是`read-only`的，表示现在是否有一个时钟中断在`pending`，后者在`sie`中，是`read-write`的，表示`Supervisor Mode`下是否开启时钟中断。
   1. `SEIP = [9]`和`SEIE = [9]`，都是表示外部中断的，前者在`sip`中，是`read-only`的，表示现在是否有一个外部中断在`pending`，后者在`sie`中，是`read-write`的，表示`Supervisor Mode`下是否开启外部中断。

4. `sscratch`是一个用于储存`Trap`中临时值的寄存器，没有其他含义。
5. `sepc`是记录`pc`值的寄存器，这个寄存器在`Trap`进入`Supervisor Mode`时会被设置为`Trap`前的虚拟地址。在`SRET`时会被读出并设为`pc`值。
6. `scause`是寄存当前发生的`Trap`信息的寄存器，这个寄存器在`Trap`进入`Supervisor Mode`时会被设置为`Trap`的类别。该寄存器的首位是`Interrupt`位，表示这个`Trap`是否是`Interrupt`。后面是`Exception Code`，表示这个`Trap`的类别。
