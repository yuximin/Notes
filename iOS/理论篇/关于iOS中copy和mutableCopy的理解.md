# 关于iOS中copy和mutableCopy的理解

浅拷贝，不会将对象的内容复制到新的内存空间，仅仅是生成一个新的指针指向原先对象的内存地址。

深拷贝，将对象的内容拷贝到新的内存空间，并返回指向新内存地址的指针。



copy 操作的作用



mutableCopy 操作的作用？



copy 和 mutableCopy 的区别？



可变类型 不可变类型

copy mutableCopy
