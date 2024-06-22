# C指针笔记

1. 字符串常量

当字符串常量出现在表达式中，它的值是一个指针常量，指向第一个字符。

2. A NUL byte is one whose bits are all 0,written as a charactor constant like this : '\0'

3. A NULL pointer as a pointer value that does not point to anything at all. To make a pointer variable NULL you assign it the value zero.

4. 左值和右值

能出现在赋值符号左边的叫左值，能出现在赋值符号右边的叫右值。

左值一定是程序分配过的内存空间（声明过的变量，数组元素）

下标引用（[]）、访问结构体成员（.）、访问结构体指针（->）、间接访问（*）运算符的结果是一个左值，可以放在赋值操作符的左边，可以使用&运算符