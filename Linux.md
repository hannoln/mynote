# 基础知识

## linux目录结构
——linux的目录是一个树型结构，不像Windows有很多盘符号
它之后一个`./` 所有文件都在它下面

## 常用命令


**命令的格式：**

`command [-options]  [parameter]`
- `command`  命令本身
- `[-options]` 可选项，通过选项控制命令的行为细节
- `[parameter]` 可选项，多数用于命令的指向目标等

**——语法中的\[ \] 表示可选项**

### ls

——以平铺形式列出目录下的内容
（点开它的man 好家伙八百个option）

**常用option**
-a ：显示隐藏文件
——linux中所有隐藏文件都以`.` 开头
-l :列表形式列出目录下内容

ps：可以组合使用（不分先后）

### cd/pwd

——cd：change directory

`cd /`  回到根目录

pwd ：print work directory

### mkdir
——创建mul
- `mkdir -p` 一次创建多个层级的目录

```
mkdir -p mydoc/note/2023
```

这里如果直接创建2023文件夹会报错，因为上级目录不存在，所以要用-p 来进行目录的创建

### touch
——可以创建文件

### cat
—— 查看文件内容

### more
—— 支持翻页的文件查看

### cp
——复制文件/文件夹

### mv 
——移动命令

### rm
——删除文件

### 通配符 
——`*`
- `test*` 表示匹配任何以test开头的内容
- `*test` 表示匹配任何以test结尾的内容
- `*test*`表示匹配任何包含test的内容








