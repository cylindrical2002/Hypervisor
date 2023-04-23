# Week 9

这周原本想改改双页表，但是和`discussion`里两位学长交流后认为应该先更改架构。

阅读了：

1. [x86文档](../materials/x86/325462-sdm-vol-1-2abcd-3abcd.pdf)
2. [linux/kvm](https://github.com/torvalds/linux)
3. [zircon/vm](https://fuchsia.googlesource.com/fuchsia/+/refs/heads/main/zircon/kernel)

思考了一下架构，基本上放弃了之前的所有实现，除了`arch/riscv`。

目前暂定是实现成一些小模块，包括

1. `hc_vm`，用于实现`VM`的基本操作接口，这个和贾越凯学长在 [RVM-Tutorial](https://github.com/equation314/RVM-Tutorial) 中实现的`rvm`模块，以及齐呈祥学长即将实现的 [hypercraft](https://github.com/KuangjuX/hypercraft) 模块非常相似。应该只需要简单修改即可。
2. `hc_vm_sche`，用于实现`VM`的调度问题，我认为`Hypervisor`的核心就是`Guest OS`的调度问题，不应该简单复用`Host OS`的接口。但是具体实现为什么算法好，我还没有来得及调研（下周工作）。
3. `hc_xxx_simu`，用于实现外设的虚拟化，我认为可以暂时实现成直通+锁的形式，之后再尝试改成模拟。
