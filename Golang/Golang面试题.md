# Golang面试题

# 基础

## new和make的区别？

make函数：

- 能够**分配并初始化**类型所需的内存空间和结构，返回引用类型的本身。
- 具有使用范围的局限性，仅支持channel、map、slice三种类型。
- 具有独特的优势，make函数会对三种类型的内部数据结构（长度、容量等）赋值。

new函数：

- 能够**分配**类型所需的内存空间，返回指针引用（指向内存的指针）。
- 可被替代，能够通过字面值快速初始化。