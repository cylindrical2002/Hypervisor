# Week 11

本周继续尝试拆分`nimbos`，迁移了一部分内存相关的代码到`hc_mm`中，对于`hc_schedule`，我尝试用`Task`这个`trait`。但是`dyn Task`动态类型中问题比较多。

下周计划：
1. 继续完善`nimbos`中的实现。