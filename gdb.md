

## P1 Linux C C++调试准备工作

## 环境准备

![image-20230704182424888](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230704182424888.png)

## 基础操作

![image-20230704193138228](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230704193138228.png)

gdb <可执行文件> 启动gdb调试

含有*Reading symbols from <可执行文件>---done*就代表成功启动调试

list(l) 查看源代码，一次默认显示十行

```
list 11//查看11行附近的10行代码
list main.cpp:11//查看main.cpp第11行附近的代码
```

breakpoint(b) 设置断点

b <file_name>

```
b main//在main函数的第一行设置断点
```

b <file_name:line>

```
b test.cpp:8//在test.cpp的第八行设置断点
```

info break(i b) 查看断点信息

run(r) 执行程序，直到遇到第一个断点

next(n)  逐过程执行

print(p) 查看变量

```
p <variable>
```

info locals(i locals) 查看所有的局部变量

continue(c) 继续执行

quit(q) 退出

# P2 启动调试

![image-20230704184527607](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230704184527607.png)

## 启动调试并传递参数

### 1 启动gdb的时候直接设置参数

gdb --args <可执行文件> <参数列表>

```
gdb --args test 1 2 "3 4"//传入了三个参数
```

### 2 启动gdb后，在run前设置参数

```
gdb test
set args 1 2 "3 4"
run
```

### 3 启动gdb后，run的时候直接设置参数

```
gdb test
run 1 2 "3 4"
```

## 附加到进程

如果调试程序的时候，这个程序已经提前启动了，那么就不可以用gdb直接启动。因为直接启动是会在一个新的程序上调试，所以就只能用这种方法

```
gdb attach <pid>
gdb --pid <pid>
```

获取pid

```
ps -aux | grep sec//获取名字中含有sec的进程id
```

附加后处于中断状态

```
gdb attach <pid>
gdb --pid <pid>
```

这段时间可以设置断点等操作

结束中断，使用continue

剥离程序，使用detach

退出程序，使用quit

### 逐过程执行 (step-over)

next(n): 直接执行到下一行代码，哪怕有函数调用也会跳过函数

### 逐语句执行(step-into)

step(s): 直接执行到下一行代码，但是如果有函数调用进入到函数

### 退出当前函数

finish: 退出函数到调用者的下一行，并且会显示返回值

### 退出调试

```
detach//调试器退出了但是程序任然在运行
quit//调试器退出了而且程序也结束了
```

# P3 调试断点管理

![image-20230704212757962](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230704212757962.png)

```
b main.cpp:10//在main.cpp文件的第10行设置断点
```

b 函数名：会把所有相同函数名都设置断点

rb【r是正则表达式的意思】

```
rb work//把所有带有work的函数内第一行全部设置断点（多文件操作的时候特别方便）
```

```
b main.cpp:14 if i==90//当i==90的时候断点
```

临时断点只会执行一次,执行一次后就会自动删除断点

```
tb main.cpp:14
```

![image-20230704215809337](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230704215809337.png)

```
i b 2//只是查看编号为2的断点
diable/enable/delete <编号>
delete//删除所有断点
```

使用disable的时候，如果断点编号有2.1 2.2这样子的，是不会被设置为无效的（enable同理）

# P4 变量查看与修改

![image-20230704221213798](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230704221213798.png)

```
p argc//c++程序的第一个参数是程序本身，所以至少会显示1
```

```
p name//如果name后面有很多结束符，gdb都会显示出来
set print null-stop//现在不会打印结束符了
默认结构体和数组直接显示在一行
set print pretty//显示漂亮一点，一般针对结构体
set print array on//显示数组的时候，像结构体一样显示
p sizeof(int) / strlen(name)//内置函数，一般都是c的函数
```

修改变量

```
p i=5//把i改成5
```

# P5 内存查看与修改

![image-20230704223006177](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230704223006177.png)

```
p &itest//获取地址
x /4b &itest//按照四个字节显示itest
x /x &itest//按照16进制显示itest
```

 **选项类型**

- x 按十六进制格式显示变量。
- d 按十进制格式显示变量。
- u 按十六进制格式显示无符号整型。
- o 按八进制格式显示变量。
- t 按二进制格式显示变量。
- a 按十六进制格式显示变量。
- c 按字符格式显示变量。
- f 按浮点数格式显示变量。
- s按字符串格式显示变量。
- b按照字节格式显示变量。

![image-20230704223322353](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230704223322353.png)

修改变量set {<type>}&<variable>=<newValue>

```
set {int}&test.gender=110//把gender改成n（110是对于的ascii码），转成int
```

# P6 寄存器查看/修改

场景：在没有调试符号的程序下，使用普通gdb命令没有，就需要查看寄存器

![](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705083226173.png)

```
i registers//查看所有通用寄存器
i all-registers//查看所有寄存器
i r rdi//只查看一个寄存器
```

函数参数少于6个，使用寄存器传递，（从左往右）顺序如下。如果多于6个，就会把参数放到函数栈里面

![image-20230705083141440](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705083141440.png)

如果参数是指针，通过查看寄存器就只可以看到地址，那么想要查看里面的内容，就需要使用如下两种方式

```
以下查看字符串char*为例
x /s <address>
p (char*)<address>
```

![image-20230705084036356](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705084036356.png)

查看反汇编地址

info line <line>

```
info line 16//查看16行的反汇编地址，用起始地址
```

修改rip，使得修改程序运行逻辑

```
set var $rip=<address>
p $rip=<address>//这个时候使用n，就会直接跳到指定行(注意是$不是&)
address:就是上一步查看的地址
```

disassemble查看反汇编代码

# P7 源代码查看/管理

![image-20230705101326246](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705101326246.png)

```
l	//显示以当前行会中心，前5行后5行
l	//继续往后显示
l -	//往前显示
```

显示指定的函数，比如有多个类里面有同名函数。不指定的话，全部函数都会显示出来

```
l test_c::test_member	//类名+函数名
```

# P8 搜索源代码 设置源代码搜索目录

![image-20230705102432452](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705102432452.png)

```
search test	//当前位置向前（向下） 查找包含test的字符串/函数等
forward-search test	//同上
<Enter>	//回车代表继续执行上一条命令
reverse-search test	//往回（向上）搜索
```

查看源代码路径

```
show directories
```

![image-20230705103156232](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705103156232.png)

- $cdir: 当前目录
- $cwd: 工作目录

当找不到源代码目录的时候，设置源代码搜索目录

```
directory <path>
```

# P9 函数调用栈管理

![image-20230705111601133](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705111601133.png)

**栈帧**：程序进行函数调用的时候，这些调用信息成为栈帧，包括调用的参数、局部变量、寄存器等

**调用栈**：所有栈帧组成的信息交调用栈

![image-20230705111818797](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705111818797.png)

```
bt	//查看栈帧信息
//0是正在执行的，数字越大代表越先入栈（越先被执行）
```

```
f <address>	//使用栈帧所在地址也可以奇幻
```

# p10 观察点

![image-20230705113412007](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705113412007.png)

![image-20230705113435280](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705113435280.png)

**观察点类型：**

- hardware watchpoint: 不会影响程序
- software watchpoint:  会影响程序，会导致程序性能变慢

```
watch gdata	//gdata被改变的时候停下来
rwatch gdata	//这个数据被读的时候停下来
awatch gdata	//读和写都会停下来
```

```
i threads	//查看线程
watch gdata thread 3	//只有三号线程改变gdata的时候才会停下来（3是线程id，上一条命令的第一列）
```

```
watch gdata+gdata2 > 10 	//只要结果>10就会停下来（类比条件断点）
```

# P11 捕获点

![image-20230705145502966](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705145502966.png)

![image-20230705145520698](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705145520698.png)

使用catch throw而不使用断点的原因：

代码里面有很多try catch throw，设置断点需要设置很多个，而且大部分情况不会执行到throw，所以使用catch throw更加方便，它会自动捕获

```
catch throw	//当代码执行到throw的会自动停下来
停下来过后，用bt查看堆栈情况，然后frame切换到对应发出throw的堆栈，再去寻找问题
```

```
catch catch	//执行到catch里面后会停下来
```

catch syscall <syscallName>

```
catch syscall close	//捕获close
```

如果直接捕获信号，会停在很多系统文件哪里，只是没有必要的。小技巧：先b mian再r运到到这个断点，也就是让程序先跑起来，再去设置catch syscall <syscallName>，这样子停下来的就全是自己的文件的

# P12 2-3断点执行命令

![image-20230705152851532](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705152851532.png)

```
自动在断点处执行相关命令
commands <num>
>p *curr
>p prev	
>printf "prev node data is %d\n", prev_node_data	//还可以执行c语言函数
>end
//默认运行在最后一个断点处执行上述命令,写了num，那就在num号断点执行命令
```

删除自动执行命令

```
commands <num>
>end
//中间不加任何命令就行
```

断点信息保存到文件

```
save breakpoints <file>
```

载入文件

```
source <file>
```

# P13 文本用户界面

![image-20230705153851531](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705153851531.png)

```
layout split	//水平切分
```

tui模式最多显示三个窗口

源码窗口下：

![image-20230705154031656](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705154031656.png)

B+表示命中过一次

b+表示还没有命中

ps: +表示断点可用(enable) -表示断点不可用(disable) h表示硬件断点

```
info win	//查看拥有焦点的窗口
focus(fs) <window>	//切换窗口
focus cmd	//切换到命令行窗口
//在聚焦到命令窗口的时候，按上下左右会直接操作到命令窗口
```

# P14 查看变量类型信息

![image-20230705155743104](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705155743104.png)

```
whatis <variable>	//查看变量类型
whatis <function>	//查看函数类型
```

```
ptype test1	//查看变量，会把类的成员都显示出来
ptype /m test1	//只显示成员变量，不显示成员函数
ptype /t test3	//typedef也不显示
ptype /o node	//查看node结构体的成员偏移量和大小，和node总大小

ptype test2	//加入test2是test1的派生类，那么只会显示test1的成员
set print object on	//这样子才会显示派生类的类型
```

![image-20230705160417162](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705160417162.png)

```
i variables	<name>	//查看包含name的所有全局/静态变量在那个文件那个位置定义的
```

# P15 多线程基本知识

![image-20230705162125936](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705162125936.png)

创建线程

![image-20230705162228607](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705162228607.png)

![image-20230705162323733](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705162323733.png)

```
pthread_create：创建线程
thread：返回的线程id
attr：线程的属性（传NULL默认属性）
start routine：线程执行的函数
arg：线程传入的参数
```

![image-20230705162529287](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705162529287.png)

```
pthread_join：等待一个线程结束（线程是异步的）
thread：线程id
retval：接收线程的返回值
```

# P16 多线程基本知识-II

原生的创建线程，只支持全局/静态函数，c++方式生成线程还支持类函数如下

![image-20230705163135257](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705163135257.png)

![image-20230705163150516](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705163150516.png)

# P17 线程管理

![image-20230705164600227](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230705164600227.png)

```
ps -aL	//查看轻量级进程（all LWP）
```

bt是查看当前线程的栈帧使用情况，当需要查看指定线程的栈帧使用情况时，需要先切换线程

# P18 线程管理2

```
thread find <name>	//查找含有name的线程（name可以是id/地址/lwp/名字）
thread name main 	//修改当前线程名字为main
b 15 thread 2	//线程2执行到15行会停下来，其他线程不会
```

# P19 2-10为线程执行命令

```
thread apply <num> <cmd>	//num可以指定多个
thread apply 3 i args	//在3号线程执行i args, 也就是查看3号线程的参数
thread apply 1 2 3 i locals
thread apply 1-3 i locals
//前面两个命令都是查看线程1 2 3的局部变量
thread apply all bt	//所有线程
thread apply all -s i bt	//不显示错误信息
thread apply 1-5 -q i locals	//不显示线程信息，只显示命令结果
```

# P20 2-11线程日志信息控制

```
show print thread-events	//当为on的时候，会打印线程的日志信息
set print thread-events off/on	//不再/重新打印线程日志信息
```

最后一个还没有学

# P21 2-12执行外部命令以及保存命令及输出

在gdb执行外部命令

![image-20230706080022174](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706080022174.png)

```
pipe(|) i locals | grep test	//只显示test相关的i locals结果。通过管道符来过滤或者重定向输出结果
info function	//显示函数
```

```
set logging on	//默认把后面操作的输出结果存到gdb.txt中
set logging file <name>	//指定输出文件
set logging overwrite	//设置为覆盖，默认为追加。设置后set logging on就会删除原文件内容
```

# P22 3-1 跳转执行-任意执行代码，穿越到过去

![image-20230706082041209](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706082041209.png)

```
以前学习的方法：修改pc/rip的值
info line 12 	//获取起始地址
set var $rip=<address>	或者	p $rip=<address>
```

```
b 12
jump 12	//如果跳转的位置有断点，就会停在这里
```

![image-20230706082701558](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706082701558.png)

```
可以使用标签跳转
jump test_label
```

jump只能使用于本函数跳转，不要跨函数，否则可能出现无法预期的错误

```
使用jump跳到其他函数的方法
1、先在函数第一行设置断点
2、jump到本函数调用其他函数的位置
jump会跳到指定行后，开始执行，然后他就会进入函数，停在断点处
```

# P23 3-2反向执行-调试中的undo

![image-20230706084609804](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706084609804.png)

反向执行对文件没有用

```
record	//记录操作，才可以回退
reverse-next(rn)	//反向执行
reverse-step(rs)	//反向进入函数
record stop	//结束记录操作，后面不可以回退了
```

和跳转的区别：

跳转只改变程序指针，不回退在jump行号上面的值，并不是撤销！后续值会出问题的。

```
reverse-finish	//反向执行到调用者的上一行
如果再重新n，那就会执行到我当时没有执行完的地方
```

# P24 3-3调试子进程

![image-20230706091911668](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706091911668.png)

![image-20230706093505217](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706093505217.png)

```
默认情况下，只可以调试父进程，也就是说在子进程设置断点是没用的。
show follow-fork-mode	//查看模式
set follow-fork-mode child/parent	//设置调试父/子进制模式
```

父子进程同时调试

```
set follow-fork-mode child
set detach-on-fork off	//默认是on，调试子进程的时候，防止父进程被脱离
info inferiors	//查看gdb的子进程
inferior <num>	//切换子进程
```

# P25 3-4

![image-20230706095822711](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706095822711.png)

![image-20230706100639711](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706100639711.png)

inferior表示调试对象

```
i inferiors	//查看所有进程信息
//gdb启动的时候默认会有一个null的inferior，程序调试过程中，才有对应信息
add-inferior	//添加一个进程，但是是空的，类似一个空房间
inferior <num>	//切换
set schedule-multiple on	//多个进程都可以执行
detach inferiors <num>	//脱离某个进程
remove-inferios <num>	//删除某个进程，活跃进程不可以删除，只可以删空房间
```

# P26 3-5

![image-20230706110041277](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706110041277.png)

```
先让程序跑起来，才可以调用
//调用test函数
call test(10,20)	
p test(10, 20)	//如果返回值是void,会显示返回值是void，call不会显示
```

可以直接对$5（表示变量）操作

![image-20230706110542866](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706110542866.png)

没有调试信息，使用call/p调用函数

```
call (int)test(10,20)	//加返回值，不然gdb不知道返回值是什么
//参数有稍微复杂的类型也需要转换，比如结构体啥的
```

# P27 3-6

![image-20230706151148955](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706151148955.png)

有这种情况test_str(test.get_str()),我们想调试test_str而不是get_str。此时s会进入get_str，所以需要skip跳过这个get_str()

```
skip test_c::get_str()
skip file test.cpp	//会跳过整个test.cpp文件的内容
skip -gfi common/*.*	//跳过common目录下的所有文件（通配符方法）
```

# P28 3-7

![image-20230706153937409](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706153937409.png)

制作release版本的方法：

```
1、写两个makefile，一个带有-g一个不带
2、先把编译-g的，然后使用这个语句 strip -g release-section -o release去除-g调试信息
```

两种方法调试release版本

```
1、//带有调试信息的文件作为符号文件来启动没有调试符号的文件
gdb --symbol=release-section -exec=release
2、//把调试信息单独提取出来，在启动
objcopy --only-keep-debug release-section debug.sym
gdb --symbol=debug.sym -exec=release
```

gdb调试core-dump（当程序运行过程中发生异常, 程序异常退出时, 由操作系统把程序当前的内存状况存储在一个core文件中, 叫core dump.）

```
在gdb中generate-core-file rel.core	//生成一个dump文件
gdb release-section rel.core	//调试core dump文件，可执行文件和core dump文件需要对应
```

# P29 3-8

![image-20230706161132840](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706161132840.png)

```
gdb --wrirte <可执行文件>	//默认不可写，需要加write参数
disassemble /mr check_some	//反汇编check_som的函数代码，mr值显示源码和机器码一一对应
//然后修改对应地址的值,反汇编只给起始地址，从左往右加1（16进制）
p {unsigned char}<address>=0x<newVal>(16进制)	//只改一个字节
set {}<address>=<newVal>也可以
```

```
info functions //查看函数
```

# P30 4-1 内存泄漏检查

file:memleak-section

![image-20230706162810378](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706162810378.png)

![image-20230706163153368](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706163153368.png)

前三个表示当前线程使用的资源

后面表示整个进程使用的资源

ps：分配1024，可以实际分配内存更大，用来存放内存相关信息

```
call malloc_stats()
比较两次，分配内存前观察看一次，如果程序结束时发现内存没有回到分配前，说明没有释放内存
call malloc_info(0, stdout)
重点关注rest的变化
```

# P31 4-2

file:memcheck-section

![image-20230706170940238](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706170940238.png)

# P32 4-3 远程调试（没有实践）

file:remote-section

![image-20230706180627498](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706180627498.png)

防火墙可以关掉

```
服务端：启动gdbserver
gdbserver ip:port ./remote-section	//ip是远程服务端的，port不要冲突就好
客户端：
gdb
target remote ip:port	//连接上服务端
不可以用run，直接c就行了
结束调试：
1、ctrl+c结束调试，然后q
如果直接q，远程程序不会介素
2、detach后q
```

如果程序已经跑起来了，那就用attach

```
gdbserver ip:port --attach <pid>
```

# P33 4-4 多线程死锁调试

file:deadlock-section

![image-20230706183735687](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706183735687.png)

![image-20230706183755682](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706183755682.png)

```
ps aux | grep deadlock-section	
gdb attach <pid>
thread apply all bt
i threads	//逐个查看bt
查看到阻塞的部分后，去看这个锁被谁占用了
thread <num>
p _mutex2	//注意Owner ID-拥有这个锁的线程id

```

# P34 4-5

file：coredump1-section

![image-20230706185557533](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706185557533.png)

![image-20230706190651334](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706190651334.png)

```
活着的进程产生core:
gcore test.core
generate-core-file test.core
让程序崩溃的时候自动生成core dump
ulimit -c	//如果是0那就不生成
ulimit -c unlimited	//现在会生成了

生成的core文件由/proc/sys/kernel/core_pattern控制
echo -e "%e-%p-%t" > /proc/sys/kernel/core_pattern	//e表示进程名称，p表示pid，t表示时间戳。这样子每次都会产生不同名字的core文件
```

# P35 4-6 （还没有找到core文件。。。）

file:coredump2-section

alloca可以自动释放内存，在栈空间申请内存，建议man一下看看

```
gdb <可执行文件> <core>
ulimit -a	//查看内存大小
```

# P36 4-7无调试符号core dump分析

file:coredump3-section

```
where = bt	//查看堆栈
因为p没法用过所以使用寄存器查看参数
i r rdi等，从而定位问题
```

# P37 4-8软件密码“破解”

file：crack-section

```
b *<address>	//在这个地址处断点
ni	//汇编的next
```

```
输入密码的时候，ctrl+c中断线程，然后查看堆栈信息，去看对应的汇编代码，找到这个错误信息在哪里出现的。
一般情况都是从main开始往下找，找到对应信息后，退出gdb，重新进去，设置断点到对应位置
```



在gdb里面写循环！！！

![image-20230706201755765](C:\Users\Mr.Helen\AppData\Roaming\Typora\typora-user-images\image-20230706201755765.png)

0x555555555519
