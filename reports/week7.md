# Week 7

本周代码进度上没有推进。

完整读完了 [RISCV Privileged](./riscv-privileged-20211203.pdf)，理解了`Hypervisor Extension`的工作机制。

参考了以下仓库：

1. https://github.com/equation314/RVM-Tutorial 
2. https://github.com/torvalds/linux
3. https://github.com/rcore-os/rCore
4. https://github.com/KuangjuX/hypocaust-2
5. https://github.com/rcore-os/RVM1.5
6. https://github.com/KarmaD7/rHyper
7. https://github.com/equation314/nimbos

有以下的问题：

1. 如何支持 `Recursive Hypervisor`？ 为什么H态的扩展寄存器不使用`MMIO`实现？
2. 切换`GUEST`的时候，`OS`的计时器要不要保存到`Context`，包括`time`, `cycle`, `instret` 等等，个人认为需要保存，因为这个是绑定`OS`的`CSR`。但是事实上`ISA`没有抽象`VS-mode`下的这些寄存器，是不是意味着这些寄存器是共享的？
3. 是否有一个完整的`rust`语言的`arch`封装？

下周安排：

基本目标是跑一个`hello world`，写好指令集的封装，最好能跑一个`NimbOS`