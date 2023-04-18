# Week 8

本周实现了一个基本的`hypervisor`，主要基于`nimbOS`

### Method

事实上，主要是修改了`exec`系统调用，在`exec`中判断这个任务是不是一个`Guest`，强行修改`TrapFrame`之后运行了`Guest`。

实现了`hypercall`支持了`Guest`的`sbi_call`。 实现了`hgatp`（具体的实现非常垃圾）

进行了异常处理的分级，将`VS-mode`下的异常推到`VS-mode`下处理。

### Future

1. 实现双页表，完善`GuestPageFault`
2. 实现设备树
3. 大改架构

### Run

```
qemu-system-riscv64 -nographic -m 128M -machine virt -bios default -kernel target/riscv64/release/hchyper.bin

OpenSBI v1.0
   ____                    _____ ____ _____
  / __ \                  / ____|  _ \_   _|
 | |  | |_ __   ___ _ __ | (___ | |_) || |
 | |  | | '_ \ / _ \ '_ \ \___ \|  _ < | |
 | |__| | |_) |  __/ | | |____) | |_) || |_
  \____/| .__/ \___|_| |_|_____/|____/_____|
        | |
        |_|

Platform Name             : riscv-virtio,qemu
Platform Features         : medeleg
Platform HART Count       : 1
Platform IPI Device       : aclint-mswi
Platform Timer Device     : aclint-mtimer @ 10000000Hz
Platform Console Device   : uart8250
Platform HSM Device       : ---
Platform Reboot Device    : sifive_test
Platform Shutdown Device  : sifive_test
Firmware Base             : 0x80000000
Firmware Size             : 252 KB
Runtime SBI Version       : 0.3

Domain0 Name              : root
Domain0 Boot HART         : 0
Domain0 HARTs             : 0*
Domain0 Region00          : 0x0000000002000000-0x000000000200ffff (I)
Domain0 Region01          : 0x0000000080000000-0x000000008003ffff ()
Domain0 Region02          : 0x0000000000000000-0xffffffffffffffff (R,W,X)
Domain0 Next Address      : 0x0000000080200000
Domain0 Next Arg1         : 0x0000000087000000
Domain0 Next Mode         : S-mode
Domain0 SysReset          : yes

Boot HART ID              : 0
Boot HART Domain          : root
Boot HART ISA             : rv64imafdcsuh
Boot HART Features        : scounteren,mcounteren,time
Boot HART PMP Count       : 16
Boot HART PMP Granularity : 4
Boot HART PMP Address Bits: 54
Boot HART MHPM Count      : 0
Boot HART MIDELEG         : 0x0000000000001666
Boot HART MEDELEG         : 0x0000000000f0b509

 _          _   _                       
| |__   ___| | | |_   _ _ __   ___ _ __ 
| '_ \ / __| |_| | | | | '_ \ / _ \ '__|
| | | | (__|  _  | |_| | |_) |  __/ |   
|_| |_|\___|_| |_|\__, | .__/ \___|_|   
                  |___/|_|              

[Hypervisor] Start HyperVisor
[Hypervisor] arch = riscv64
[Hypervisor] platform = qemu-virt-riscv
[Hypervisor] build_mode = release
[Hypervisor] log_level = warn
[Hypervisor] RISC-V H ISA Available
[Hypervisor] Initializing kernel heap at: [0xffffffc0803a00a8, 0xffffffc0807a00a8)
[Hypervisor] Initializing frame allocator at: [PA:0x807a1000, PA:0x88000000)
[Hypervisor] Mapping .text: [0xffffffc080200000, 0xffffffc080211000)
[Hypervisor] Mapping .rodata: [0xffffffc080211000, 0xffffffc080219000)
[Hypervisor] Mapping .data: [0xffffffc080219000, 0xffffffc08039c000)
[Hypervisor] Mapping .bss: [0xffffffc0803a0000, 0xffffffc0807a1000)
[Hypervisor] Mapping boot stack: [0xffffffc08039c000, 0xffffffc0803a0000)
[Hypervisor] Mapping physical memory: [0xffffffc0807a1000, 0xffffffc088000000)
[Hypervisor] Mapping MMIO: [0xffffffc00c000000, 0xffffffc00c210000)
[Hypervisor] Mapping MMIO: [0xffffffc010000000, 0xffffffc010001000)
[Hypervisor] Initializing drivers...
[Hypervisor] Initializing task manager...

[Hypervisor] /**** APPS ****
[Hypervisor] 
[Hypervisor] cyclictest
[Hypervisor] exit
[Hypervisor] fantastic_text
[Hypervisor] forktest
[Hypervisor] forktest2
[Hypervisor] forktest_simple
[Hypervisor] forktest_simple_c
[Hypervisor] forktree
[Hypervisor] hello_c
[Hypervisor] hello_world
[Hypervisor] matrix
[Hypervisor] sleep
[Hypervisor] sleep_simple
[Hypervisor] stack_overflow
[Hypervisor] thread_simple
[Hypervisor] user_shell
[Hypervisor] usertests
[Hypervisor] yield
[Hypervisor] 
[Hypervisor] /**** GUESTS ****
[Hypervisor] 
[Hypervisor] hcminikernel

[Hypervisor] Running tasks...
[Hypervisor] test kernel task: pid = TaskId(2), arg = 0xdead
[Hypervisor] test kernel task: pid = TaskId(3), arg = 0xbeef
[Application] Rust user shell
[Application] >> hcminikernel
[Hypervisor] Get a guest OS to run: hcminikernel
[Hypervisor] Load OS successfully

   __   __  _______  __   __  ___   __    _  ___   ___   _  _______  ______    __    _  _______  ___     
  |  | |  ||       ||  |_|  ||   | |  |  | ||   | |   | | ||       ||    _ |  |  |  | ||       ||   |    
  |  |_|  ||       ||       ||   | |   |_| ||   | |   |_| ||    ___||   | ||  |   |_| ||    ___||   |    
  |       ||       ||       ||   | |       ||   | |      _||   |___ |   |_||_ |       ||   |___ |   |    
  |       ||      _||       ||   | |  _    ||   | |     |_ |    ___||    __  ||  _    ||    ___||   |___ 
  |   _   ||     |_ | ||_|| ||   | | | |   ||   | |    _  ||   |___ |   |  | || | |   ||   |___ |       |
  |__| |__||_______||_|   |_||___| |_|  |__||___| |___| |_||_______||___|  |_||_|  |__||_______||_______|

[Operating System] [kernel] Hello, world!
[Operating System] [TRACE] [kernel] .text [0x80200000, 0x80204000)
[Operating System] [DEBUG] [kernel] .rodata [0x80204000, 0x8020b000)
[Operating System] [ INFO] [kernel] .data [0x8020b000, 0x8021c000)
[Operating System] [ WARN] [kernel] boot_stack top=bottom=0x8022c000, lower_bound=0x8021c000
[Operating System] [ERROR] [kernel] .bss [0x8022c000, 0x8022d000)
[Operating System] [kernel] num_app = 4
[Operating System] [kernel] app_0 [0x8020b030, 0x8020f0f0)
[Operating System] [kernel] app_1 [0x8020f0f0, 0x802131b0)
[Operating System] [kernel] app_2 [0x802131b0, 0x80217270)
[Operating System] [kernel] app_3 [0x80217270, 0x8021b330)
[Operating System] [kernel] Loading app_0
[Operating System] [kernel] Load successful
[Operating System] [kernel] Next User Stack at: 0x80209000
[Operating System] [kernel] TrapContext at: 0x8022bcc8
[Operating System] [TRACE] kernel: sys_write
[Application] Hello, world from user mode program!
[Operating System] [TRACE] kernel: sys_write
[Operating System] [TRACE] [kernel] Application exited with code 0
[Operating System] [kernel] Loading app_1
[Operating System] [kernel] Load successful
[Operating System] [kernel] Next User Stack at: 0x80209000
[Operating System] [kernel] TrapContext at: 0x80206b68
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [10000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [20000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [30000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [40000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [50000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [60000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [70000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [80000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [90000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [100000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [110000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [120000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [130000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [140000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [150000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [160000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [170000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [180000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [190000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_3 [200000/200000]
[Operating System] [TRACE] kernel: sys_write
[Application] 3^200000 = 871008973(MOD 998244353)
[Operating System] [TRACE] kernel: sys_write
[Application] Test power_3 OK!
[Operating System] [TRACE] kernel: sys_write
[Operating System] [TRACE] [kernel] Application exited with code 0
[Operating System] [kernel] Loading app_2
[Operating System] [kernel] Load successful
[Operating System] [kernel] Next User Stack at: 0x80209000
[Operating System] [kernel] TrapContext at: 0x80206b68
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [10000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [20000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [30000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [40000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [50000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [60000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [70000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [80000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [90000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [100000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [110000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [120000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [130000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_5 [140000/140000]
[Operating System] [TRACE] kernel: sys_write
[Application] 5^140000 = 386471875(MOD 998244353)
[Operating System] [TRACE] kernel: sys_write
[Application] Test power_5 OK!
[Operating System] [TRACE] kernel: sys_write
[Operating System] [TRACE] [kernel] Application exited with code 0
[Operating System] [kernel] Loading app_3
[Operating System] [kernel] Load successful
[Operating System] [kernel] Next User Stack at: 0x80209000
[Operating System] [kernel] TrapContext at: 0x80206b68
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [10000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [20000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [30000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [40000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [50000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [60000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [70000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [80000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [90000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [100000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [110000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [120000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [130000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [140000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [150000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] power_7 [160000/160000]
[Operating System] [TRACE] kernel: sys_write
[Application] 7^160000 = 667897727(MOD 998244353)
[Operating System] [TRACE] kernel: sys_write
[Application] Test power_7 OK!
[Operating System] [TRACE] kernel: sys_write
[Operating System] [TRACE] [kernel] Application exited with code 0
[Operating System] All applications completed!
[Operating System] [ WARN] Shutting down...
[Hypervisor] Guest OS Shutdown
[Application] Shell: Process 5 exited with code 0
[Application] >> 
```



