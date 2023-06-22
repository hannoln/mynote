——带大量碎碎念版
#20230512
（ps：之前的记录在石墨文档，因为感觉markdown顺手以后的记录都存这里）
#20230516
这个笔记还是记得正常一点罢

#20250606 
不好说，总之我胡汉三又回来了（不是

# PA1 
## RTFSC

现在我们从 `nemu_main.c`  开始阅读整个代码框架
```c
int main(int argc, char *argv[]) {

  /* Initialize the monitor. */
#ifdef CONFIG_TARGET_AM
  am_init_monitor();
#else
  init_monitor(argc, argv);
#endif
  /* Start engine. */
  engine_start();
  return is_exit_status_bad();
}
```

- 首先我们看到一个预处理条件编译命令，进行一个右键查看definition发现没有，所以我们的程序应该执行`init_monitor(argc,argv)` 函数

---
关于`CONFIG_TARGET_AM` 还记得之前在构建整个项目的时候，通过`make menuconfig` 来生成了配置文件，这个参数的有无应该与它有关系

---



在之前我们已经了解到 NEMU可以分为四个部分
 - CPU
 - Memory
 - Monitor
 - 设备

那么很明显，这个函数的功能是实现monitor的初始化
转到该函数的定义可以看到我们跳转到 `monitor/monitor.c` 文件中 ，里面还有一些其它的代码，我们先看`init_monitor()`
```c
void init_monitor(int argc, char *argv[]) {
  /* Perform some global initialization. */
  /* Parse arguments. */
  parse_args(argc, argv);
  /* Set random seed. */
  init_rand();
  /* Open the log file. */
  init_log(log_file);
  /* Initialize memory. */
  init_mem();
  /* Initialize devices. */
  IFDEF(CONFIG_DEVICE, init_device());
  /* Perform ISA dependent initialization. */
  init_isa();
  /* Load the image to memory. This will overwrite the built-in image. */
  long img_size = load_img();
  /* Initialize differential testing. */
  init_difftest(diff_so_file, img_size, difftest_port);
  /* Initialize the simple debugger. */
  init_sdb();
#ifndef CONFIG_ISA_loongarch32r
  IFDEF(CONFIG_ITRACE, init_disasm(
    MUXDEF(CONFIG_ISA_x86,     "i686",
    MUXDEF(CONFIG_ISA_mips32,  "mipsel",
    MUXDEF(CONFIG_ISA_riscv32, "riscv32",
    MUXDEF(CONFIG_ISA_riscv64, "riscv64", "bad")))) "-pc-linux-gnu"
  ));

#endif
  /* Display welcome message. */
  welcome();
}
```
- 这个函数通过调用一水儿的其它函数实现了所有需要的东西的初始化，每个函数的具体功能我们依次来看

- `parse_args()`:这个函数看起来像是用于解析外部命令，然后根据带的命令不同实现相应功能的
- `init_rand()`:生成了随机数种子，作用不明
- `load_img()`: 加载图片？、？？什么图片？？ 应该不是图片的意思吧，可能是什么初始化信息之类的
	- 看它的函数定义用到`FILE` 读取文件信息，应该大差不差
	- 试图找到一个叫‘rb’的文件，没有找到……
	- 看函数里这段
```c
  if (img_file == NULL) {
    Log("No image is given. Use the default build-in image.");
    return 4096; // built-in image size
  }
```
	- 一开始‘rb’文件可能还真没有，使用的是build-in的 image
	- 推测这个可以用户自己创建或者系统在build-in一次后生成

其它的看名字和注释就能知道功能，这里不赘述

在`init_monitor()`  之后，我们获得所有的初始化参数，terminal出现欢迎字样
这个时候说明我们进入`engine_start()` 函数
现在来看看它的功能：
```c
void engine_start() {
#ifdef CONFIG_TARGET_AM
cpu_exec(-1);
#else
  /* Receive commands from user. */
  sdb_mainloop();
#endif
}
```
很简单的一个函数！
它就是在一只执行一个主循环。
再看看它调用的那个主循环函数：
```c
void sdb_mainloop() {
  if (is_batch_mode) {
    cmd_c(NULL);
    return;
  }
  for (char *str; (str = rl_gets()) != NULL; ) {
    char *str_end = str + strlen(str);
    /* extract the first token as the command */
    char *cmd = strtok(str, " ");
    if (cmd == NULL) { continue; }
    /* treat the remaining string as the arguments,
     * which may need further parsing
     */
    char *args = cmd + strlen(cmd) + 1;
    if (args >= str_end) {
      args = NULL;
    }

#ifdef CONFIG_DEVICE
    extern void sdl_clear_event_queue();
    sdl_clear_event_queue();
#endif
    int i;
    for (i = 0; i < NR_CMD; i ++) {
      if (strcmp(cmd, cmd_table[i].name) == 0) {
        if (cmd_table[i].handler(args) < 0) { return; }
        break;
      }
    }

    if (i == NR_CMD) { printf("Unknown command '%s'\n", cmd); }

  }

}
```

![](https://uploader.shimo.im/f/PbLwLvnYJzb1kkXT.png!thumbnail?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJleHAiOjE2ODQxMzY5MDAsImZpbGVHVUlEIjoibThBWlY4ekQ2OGlYWkdBYiIsImlhdCI6MTY4NDEzNjYwMCwiaXNzIjoidXBsb2FkZXJfYWNjZXNzX3Jlc291cmNlIiwidXNlcklkIjo0NzM5NzEzMn0.UTgK1PLnLPFxZtHgNi7Vn0417M0k2yLRdizZGIb_hmQ)
运行后直接退出会出现如下报错
我们打开`nemu_main.c` 修改main函数的 return 值为0 发现这个报错消失了
不确定，再修改成别的值，
![[Pasted image 20230515154521.png]]
![[Pasted image 20230515154531.png]]
可以观察到Error值变成 对应的数字
因此可以推测出  `is_exit_status_bad()`  的返回值为1 ，而对于make来说，只有返回0  才不会出现这个错误
~~直接进行一个`return 0` 的改，我管你什么鬼函数~~
总之看看函数：
![[Pasted image 20230515155259.png]]

老实讲，不知道这些参数都是些什么，总之我给它加上一行代码打印`good` 的值
![[Pasted image 20230515155247.png]]
然后发现它的值为0
稍加思索……
把 `return !good;` 改成`return good;`

![[Pasted image 20230515155624.png]]
报错消失哩！

**一个疑问**
为什么返回值出现的错误，但是在终端的提示错误定位是在`native.mk` 文件的第38行？

**An answer from Stackoverflow**
![[Pasted image 20230515160251.png]]

### 梳理一下框架代码

——明天一定（20230515）

#### 在梳理框架代码之前
——来了解一下kcongfig文件是干什么的吧！
![[Pasted image 20230605212118.png]]
——以上是handbook里的描述，是不是感觉非常抽象。就像在讲把大象扔冰箱只要三步一样。

---

所以现在来自己进行一个资料的查阅。
https://zhuanlan.zhihu.com/p/133527102
![[Pasted image 20230606090406.png]]

---
可以理解为这个文件是用于初始化你的机器的，就是说这个机器提供了各种不同的功能，然后你选择自己需要的功能。
打个比方可能类似于买冰淇淋选择要加什么料……那么个意思。

https://www.kernel.org/doc/html/next/kbuild/kconfig-language.html 这个网址给出了它的相应语法
可以看到它的语法类似于给你A、B、C几个选项然后你选择需要的那个


然后就是Makefile 时从Kconfig读出配置菜单 生成 .config 文件 编译的时候根据文件信息进行相关的编译

![[Pasted image 20230606093406.png]]
### Answer of  Questions

#### 为什么全是函数？
	阅读`init_monitor()`函数的代码, 你会发现里面全部都是函数调用. 按道理, 把相应的函数体在`init_monitor()`中展开也不影响代码的正确性. 相比之下, 在这里使用函数有什么好处呢?

看着简洁方便，我们不需要了解函数的具体实现细节就能够知道它的功能。
后续如果需要对某一个功能进行修改，我们只需要找到对应的函数定义去改，不用在`init_monitor()`里到处翻
能形成一个很好的框架……嗯，就是说……~~说不出来~~ 理解精神一下

#### 这些宏的功能非常神奇, 你知道这些宏是如何工作的吗?
	我们已经在上文提到过, kconfig会根据配置选项的结果在 `nemu/include/generated/autoconf.h`中定义一些形如`CONFIG_xxx`的宏, 我们可以在C代码中通过条件编译的功能对这些宏进行测试, 来判断是否编译某些代码. 例如, 当`CONFIG_DEVICE`这个宏没有定义时, 设备相关的代码就无需进行编译.
	为了编写更紧凑的代码, 我们在`nemu/include/macro.h`中定义了一些专门用来对宏进行测试的宏. 例如`IFDEF(CONFIG_DEVICE, init_device());`表示, 如果定义了`CONFIG_DEVICE`, 才会调用`init_device()`函数; 而`MUXDEF(CONFIG_TRACE, "ON", "OFF")`则表示, 如果定义了`CONFIG_TRACE`, 则预处理结果为`"ON"`(`"OFF"`在预处理后会消失), 否则预处理结果为`"OFF"`.


#### 究竟要执行多久?
	在`cmd_c()`函数中, 调用`cpu_exec()`的时候传入了参数`-1`, 你知道这是什么意思吗?

参数为uint64,无符号64位整型，-1表示取最大值（整数用补码表示，负数的补码为=反码+1，正数的补码就是其原码；负数的反码为符号位不变，数值位按位取反。-1的原码：1,000…0001，因此，-1的补码为1,111…111。）


#### 谁来指示程序的结束?
	在程序设计课上老师告诉你, 当程序执行到`main()`函数返回处的时候, 程序就退出了, 你对此深信不疑. 但你是否怀疑过, 凭什么程序执行到`main()`函数的返回处就结束了? 如果有人告诉你, 程序设计课上老师的说法是错的, 你有办法来证明/反驳吗? 如果你对此感兴趣, 请在互联网上搜索相关内容.

**那必然是有的**
在main（）结束以后：
- 使用`atexit()`函数，来执行相关清理工作
	- 该函数在`#include<cstdlib>` 中
	- 函数原型：`int atexit ( void ( * function ) (void) );`



### 进行一些紧急的学

#### 内存对齐关键字
Q：什么是内存对齐？
	就是说让你分配到的内存，它从满足某个规则的内存位置开始（如4的倍数）
	
https://zhuanlan.zhihu.com/p/417061548
![[Pasted image 20230515160824.png]]










## 基础设施

### 简易调试器

#### 一些前置工作
通过`man` 或者其它方式了解
 `readline`
 `strtok`
 的用法
 ```
man 3 str<TAB><TAB>
```
![[Pasted image 20230515172906.png]]
嗯……首先需要在`sdb.c` 文件的 `cmd_table` 中添加对应的命令符号、说明、和相应函数
![[Pasted image 20230515173555.png]]
甚至有一个贴心的注释提示
再实现各个函数的相应功能就可以了！
注意命名格式统一

添加的命令记得添进帮助菜单（有这样的菜单吗？应该有的吧？）
	看了一下cmd_help的函数定义，不用自己加，加进结构体之后它自己就会去读数据



#### C语言函数作为参数传递
函数作为参数传递时由3个部分组成
`RETURN_TYPE  (*pointer_name)  (parameter_list)`
返回类型  函数名（这个是自己定义的函数的名字）  参数列表
```c
static struct {
  const char *name;      //命令的名字
  const char *description;    //命令的描述
  int (*handler) (char *);     //
```
如在上面的结构体中， 定义了返回值类型为int 参数为char* 的一系列函数

则对于在上一节中的：`int atexit ( void ( * function ) (void) );` 括号里的参数就很好理解了，即 atexit 函数接收一个 返回值为空，参数变量为空的函数作为传入参数。

![[Pasted image 20230516152148.png]]
好怪哦，在VScode 的退出和在WSL里的退出一个有报错一个没有
（估计是之前的修改有问题）

#### 单步执行si

回忆上一节的内容：
	在命令提示符后键入`c`后, NEMU开始进入指令执行的主循环`cpu_exec()` (在`nemu/src/cpu/cpu-exec.c`中定义). `cpu_exec()`又会调用`execute()`, 后者模拟了CPU的工作方式: 不断执行指令. 具体地, 代码将在一个for循环中不断调用`exec_once()`函数, 这个函数的功能就是我们在上一小节中介绍的内容: 让CPU执行当前PC指向的一条指令, 然后更新PC.

所以我们只需要调用`cpu_exec()`  函数就可以实现单步执行的功能
总之在`cmd_si` 函数中添加`cpu_exec(1)` 后运行是这么个效果
![[Pasted image 20230516155520.png]]
怎么写？
我们来看看`cmd_help（）`这个函数是如何实现功能的

#### 打印寄存器信息
注意在源文件中，有多个ISA可供选择，其中都有提供 `isa_reg_display()` 函数，需要选择你选的对应ISA文件中的函数进行更改。
- 要打印什么？
	 - 寄存器名称  
	 - 寄存器里的内容

- 总之得先找到寄存器的结构体定义


### Answer of  Questions
**如何测试字符串处理函数？**
	你可能会抑制不住编码的冲动: 与其RTFM, 还不如自己写. 如果真是这样, 你可以考虑一下, 你会如何测试自己编写的字符串处理函数?