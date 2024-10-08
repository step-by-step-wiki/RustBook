# PART1. 什么是所有权

所有程序在运行时都必须管理它们使用计算机内存的方式

- 有些语言有GC机制,在程序运行时,他们会不断地寻找不再使用的内存
- 有些语言没有GC机制,需要程序员显式地分配和释放内存

Rust采用了第三种方式:

- 内存通过所有权系统来管理,这套所有权系统中包含一组编译器在编译时检查的规则
- 当程序运行时,所有权特性不会减慢程序的运行速度.因为Rust把内存管理的工作转移到了编译时

# PART2. Stack vs Heap

在Rust这种系统级编程语言中,一个值被分配在Stack上还是Heap上对语言的行为和你要做出的决定有重大影响

## 2.1 存储数据

- Stack按值的接受顺序来存储,按相反的顺序将它们移除(后进先出, LIFO)
  - 添加数据叫压栈
  - 移除数据叫出栈
- 所有存储在Stack上的数据必须有一个已知的固定大小
  - 编译时大小未知的数据或运行时大小可能发生改变的数据必须存放在Heap上

Heap的组织性则差一些:

- 当把数据存入Heap时,你会请求一定大小的空间
- OS在Heap中找到一块足够大的空间,把这块空间标记为在用,并返回一个指针,也就是这块空间的地址
- 这个过程叫做在Heap上分配,有时仅仅称为分配

而把值压入Stack就不叫分配.

对于指针而言,指针是大小固定的,因此可以把指针存放在Stack上.

- 但是指针指向的数据存放在Heap上,因为数据的大小在编译时未知.如果你想访问实际数据,则必须使用指针来定位

把数据压到Stack上,要比在Heap上分配快得多:

- 因为OS不需要寻找用于存储新数据的空间,这个位置永远是Stack的顶部

而在Heap上分配空间则需要更多的工作:

- OS首先需要找到一个足够大的空间来存放数据,然后要做好记录方便下次分配

## 2.2 访问数据

- 访问Heap中的数据要比访问Stack中的数据慢,因为需要通过指针才能找到Heap中的数据.多了指针跳转的环节
  - 对于现代处理器而言,由于Cache的存在,导致指令在内存中跳转的次数越少,程序运行速度就越快
- 若数据存放的距离比较近,那么处理器的处理速度就会快一些,例如Stack
- 若数据存放的距离比较远,那么处理器的处理速度就会慢一些,例如Heap
  - 在Heap上分配大量的空间,也是需要时间的

## 2.3 函数调用

当你的代码调用函数时,值(实参)被传入到函数中(实参也包括指向Heap的指针).函数本地的变量被压到Stack上.当函数结束后,函数本地的变量会从Stack上弹出

## 2.4 所有权存在的原因

所有权解决的问题:

- 跟踪代码的哪些部分正在使用Heap的哪些数据
- 最小化Heap上的重复数据量
- 清理Heap上不再使用的数据以避免空间不足(内存泄漏)

一旦你了解所有权,就不需经常考虑Stack还是Heap了

管理Heap上的数据是所有权存在的原因,这有助于解释它为什么会这样工作