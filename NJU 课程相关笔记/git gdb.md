
# git 紧急学一下

## 什么是版本控制
![[Pasted image 20230505210223.png]]
↑这是一个手动版本控制
**git就是一个自动的版本控制工具**
还有其它很多版本控制工具 git是目前最流行的那个

## 版本控制的分类
- 本地版本控制
- 集中式版本控制
	- 服务器寄了就统统完蛋

- 分布式版本控制 git
	- 每个用户都有最新版本的代码

## git基本理论

git 本地有三个工作区域：
- 工作目录（working directory）
- 暂存区（stage/index）
- 资源库（repository）

远程还有一个git仓库，加上就有四个
`git add file `  将文件提交到暂存区
`git commit`   对文件进行本地提交
`git push`  将文件进行远程提交

`git pull` 从远程仓库拉取文件
`git reset`  文件回滚
`git checkout`  从暂存区



- **关于配置**
所有的配置文件都保存在本地
在git安装目录下的gitconfig 文件里
``` bash
git config -l 查看配置信息
```

你的一些个人信息，每次提交都会附带
必须要配置才能完成后续的提交功能
``
```bash
git config --global user.name "your name here"  
git config --global user.name "your Email here"  也可以不加引号

```

## git 项目搭建

### 初始化
`git init`      会在当前目录下生成一个.git 的隐藏目录
`git clone [url]`     从远程仓库复制一份文件

### 基本命令

`git status`  查看仓库状态
![[Pasted image 20230505222540.png]]

总之新建一个文件夹创建一个git 仓库↓
![[Pasted image 20230505223041.png]]
可以看到，在创建好的仓库中添加文件后，再使用`git status` 命令 会显示“ untracked files...”
并提示你使用`git add` 命令将它添加进追踪
我们试一试
![[Pasted image 20230505223626.png]]
红色的提示消失了
这里如果使用`git add .` 命令，则能够把所有文件提交至暂存区

现在我们来试着commit一下 采用`git commit -m "your message"` 命令
![[Pasted image 20230505224218.png]]
-m 后面需要接你想加的信息

- 并不是所有文件都需要提交 通过 gitignore
![[Pasted image 20230505225344.png]]
# GDB使用方法一个紧急的学
——但是我摆烂用VSCode的WSL Remote了 真的有必要再学它吗？
	gdb - The GNU Debugger

在使用gcc编译的时候，用形如 `gcc -g xxx.c`  的命令对源文件进行编译
编译后 用`gdb ./a.out` 进入调试模式

在调试模式中：
- `r`   run the program
- `list`  list all code
- `b <number>`  add a breakpoint at line\<number\>
- `b <functionname>` add a break point at a function 
- `info b`  to see information of breakpoint
- `quit`  quit GDB
- `p <varaible name>` print variables value or you can use &\<variable name> to see its adress
- `n`  run program line by line 
- `s`  go in a function 


一些小技巧：
- 在gdb中 可以通过`shell <command>`  执行一些你在terminal 中可以执行的程序
	比如说`shell ls`  可以列出当前路径下所有的`.c`   `.out` 文件

- set logging on  日志功能

- watchpoint 观察变量是否变化

- core 调试一个正在运行的程序
	-在shell 中使用 `ulimit -a`  查看当前系统限制
	可以自己`man ulimit` 看它有什么功能