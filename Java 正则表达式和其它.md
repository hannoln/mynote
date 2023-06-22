
——正则表达式相关看另外一个文件[[正则表达式]]

# 关于Java　class的tips

**可变参数** `...` :当参数个数不确定名单时类型相同时，可以采用可变参数。对于有其它参数的情况，它需要放在最后
```java
class User{
void test(int age,String... name){
System.out.println(name)；//可以传你想要的个数（包括0个）
}
}
```


**静态**  ：把对象无关，只和类相关的属性/方法 成为静态属性/方法 这种方法可以通过类名直接访问
```java
public class Test{
public void static main(String[] args){
Bird.fly();
System.out.printlb(Bird.type);
}
}

cladd Bird{
static String type ="BIRD"；

stativc void fly(){
System.out.println("flying ")
}
}
```

**构造方法**：专门用于构建对象
——如果用户没有定义构造方法 java虚拟机会自动添加一个公共的、无参的构造方法，方便对象的调用
——构造方法传递参数的目的一般是用于对象属性的赋值
基本语法： 类名（）{}

```java
class Test{
Test(){
}
}
```

**继承** `extends`

```java
class Parent{
Strirng name ="wangwu"
void test(){
System.out.pringln("test");
}
}
class Child extends Parent{
}
```

`super` :当属性重名时，用`super`关键字访问父类属性
`this`
```java
class Parent{
Strirng name ="wangwu";
void test(){
System.out.pringln("test");
}
}
class Child extends Parent{
String name="lisi";

void test(){
System.out.pringln(super.name); //print wangwu
System.out.pringln(this.name);//print lisi
//没写关键字默认调用子类里的东西

}
}
```

创建子类对象前会调用父类构造方法，先创建父类

在子类中的构造方法中，如果父类的构造方法不是默认的，要调用`super()`方法显示调用父类的构建方法
```java
class Parent{
Strirng name ="wangwu";
Parent(int age,int weight){

}
void test(){
System.out.pringln("test");
}
}
class Child extends Parent{
String name="lisi";
Child(int age,int weight){
super(age,weight);
}

void test(){
System.out.pringln(super.name); //print wangwu
System.out.pringln(this.name);//print lisi
//没写关键字默认调用子类里的东西

}
}
```

**方法重写后，想要访问父类方法，需要通过`super`关键字访问**

![[Pasted image 20230530210110.png]]

**访问权限**

## 多态



# 线程，什么是线程

`Thread`  `Runnable`  `Callable`
![[Pasted image 20230531190630.png]]



## Thread 类
——线程开启不一定立即执行，由CPU进行调度

- 创建线程的两个方法
	- 将一个类声明为`Tread` 的子类，重写 run方法
	- 创建一个线程声明实现类`Runnable` 接口

### 写一个子类
```java
public class TestThreads extends Thread{
@Override
public void run(){
}
}
```
**run 方法和start 方法的区别**

![[Pasted image 20230531191624.png]]


**调用start 方法才能开启线程**


#### 案例 下载图片

### 实现Runnable

```java
//实现runnable接口，重写run方法
public class Test implements Runnable{
@Override
public void run(){
System.out.println("随便写点啥。")；
}

public static void main(String[] args){
//创建runnable接口的实现类对象
Test test=new Test();
//创建线程对象，通过线程对象来开启我们的线程代理
Thread thread = new Thread(Test);
thread.start();

System.out.println("在主方法随便写点啥。")；

}

}
```

## 多个线程同时操作一个对象

```java
public class TrainTickets implements Runnable {
    private int ticketsNums = 10;
    @Override
    public void run() {
        while (true) {
            System.out.println(Thread.currentThread().getName() + "-->拿到了第" + ticketsNums-- + "票");
            if (ticketsNums <= 0) {
                break;
            }try{
                //模拟延时
                Thread.sleep(1000);
            }catch(InterruptedException e){
                e.printStackTrace();
            }
        }
    }
    public static void main(String[] args){
    //这个new 的是线程的内容，就是你要干什么
        TrainTickets tt= new TrainTickets();
       //这边才是创建线程，它们三个执行一个
        new Thread(tt,"小明").start();
        new Thread(tt,"小李").start();
        new Thread(tt,"小张").start();
    }
}
```
**发现问题：** 多个线程操作同一个资源的情况下，线程不安全，数据紊乱


## callable 接口（了解）
——有机会再了解





## lamda表达式
```java
(params)->expression
(params)->statment
(params)->{statment}

new Thread (()->System.out.println("THis is a SAMPLE"));
```

- **为什么用**
	避免匿名内部类定义过多
	代码看着简洁
	去掉没有意义的代码，只留下核心逻辑

**函数式接口**
	一个接口只包含一个抽象方法

**对于函数式接口，我们可以用lamda表达式创建该接口的对象**

## 线程状态

几个方法
```java
setPriority(int newPriority)  //更改线程优先级
static void sleep(long millis)  //休眠 millis 毫秒
void join() //  暂停当前正在执行的线程对象，并执行其它线程  插队执行
static void yield()  //让CPU重新调度，有可能CPU继续让当前线程执行
void interrupt()  //中断线程 不要用
boolean  isAlive()  //测试线程是否处于活动中
```


`Thread.getState()` 返回线程状态






## 线程同步
——多个线程操作同一个资源
实质是一种等待机制 

### 队列和锁
——解决线程同步的安全性

`synchronized` :锁机制 当一个线程获得对象的排它锁，独占资源，其它线程必须等待使用后释放锁
	问题：
		一个线程持有会导致其它所有需要此锁的线程挂起
		在多线程竞争下 加锁/释放锁 会导致比较多的上下文切换和调度延时，引起性能问题
		如果一个优先级高的线程等待一个优先级低的线程释放锁，会导致优先级倒置 引起性能问题

`public synchronized void methord(int args){}`
**该方法会影响执行效率**


## 生产者消费者问题

——线程通信

java提供的几个进程通信方法
```
wait() 表示线程一直等待，直到其它线程通知。 会释放锁
wait(long timeout)   指定等待的毫秒数
notify() 唤醒一个处于等待状态的线程
notifyAll()  唤醒同一个对象上所有调用wait() 方法的线程 ，优先级别高的线程优先调度
```


## 线程池

——经常创建和销毁、使用量特别大的资源 对性能影响很大
**解决方案：**
	提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。
	避免了频繁创建销毁、实现重复利用。  类比于公共交通工具

**好处：**
	提高响应速度
	降低资源消耗
	便于线程管理
```
corePooLSize  核心池的大小
maximumPoolSize 最大线程数
keepAliveTime  线程没有任务时最长存活时间
```

**线程相关API**
`ExcutorService` :真正的线程池接口、
`Executors` 工具类、线程池的工厂类，用于创建并返回不同类型的线程池

---

# 什么是正则表达式

1.专门处理各种文本的识别、提取问题，对字符串执行模式匹配的技术
2.正则表达式（regular expression） =》 regexp 
## 来 试试看

```java

import java.util.regex.*;
//正则表达式快速体验
public class one {
    public static void main(String[] args){
        String content="在1951 年,一位名叫Stephen Kleene的数学科学家，"+
        "他在Warren McCulloch和Walter Pitts早期工作的基础之上"+
       " ，发表了一篇题目是《神经网事件的表示法》的论文，"+
        "利用称之为正则集合的数学符号来描述此模型，引入了正则表达式的概念。"+
        "正则表达式被作为用来描述其称之为“正则集的代数”的一种表达式，"+
        "因而采用了“正则表达式”这个术语。"+
        "之后一段时间，人们发现可以将这一工作成果应用于其他方面。"+
        "1968年，Unix之父Ken Thompson就把这一成果应用于计算搜索算法的一些早期研究"+
        "。Ken Thompson将此符号系统引入编辑器QED，然后是Unix上的编辑器ed，并最终引入grep。"+
        "Jeffrey Friedl 在其著作《Mastering Regular Expressions (2nd edition)》"+
        "（中文版译作：精通正则表达式，已出到第三版）中对此作了进一步阐述讲解，如果你希望更多了解正则表达式理论和历史，"+
        "推荐你看看这本书。";
        //提取文章中所有的英文单词
        //传统方法：利用ascii码进行遍历，效率不高
        //正则表达式魔法！
        //1.先创建一个Pattern对象 模式对象，可以理解成就是一个正则表达式对象
        Pattern pattern= Pattern.compile("[a-zA-Z]+");
        Pattern pattern2= Pattern.compile("[0-9]+");
        //匹配数字和字母
        //Pattern pattern2= Pattern.compile("[0-9]|([a-zA-Z]+)");
        //创建一个匹配器对象
        Matcher matcher=pattern.matcher(content);
        Matcher matcher2=pattern2.matcher(content);
        //3.开始循环比配
        //理解：就是matcher 匹配器按照pattern 到content文本中去匹配
        //找到就返回turn，否则返回false
        while(matcher.find()){
            //匹配内容，文本，放到m.group()
            System.out.println("find:"+matcher.group(0));
        }

        while(matcher2.find()){
            //匹配内容，文本，放到m.group()
            System.out.println("find:"+matcher2.group(0));
        }
    }
}
```

# 正则表达式的底层实现
```java
public class RegTheory {
    public static void main(String[] args){
        String content = "Java 是由 Sun Microsystems 公司于 1995 年 5 月推出的 Java 面向对象程序设计语言和 Java 平台的总称。"+
        "由 James Gosling和同事们共同研发，并在 1995 年正式推出。";
        //目标：匹配所有四个数字
        //1. //d 表示一个任意的数字
        String regStr ="\\d\\d\\d\\d";
        //2.创建模式对象[即正则表达式对象]
        Pattern pattern =Pattern.compile(regStr);
        //3.创建匹配器
        //说明：创建匹配器matcher，按照正则表达式的规则 去匹配 content
        Matcher matcher=pattern.matcher(content);
        while(matcher.find()){
            System.out.println("find: "+matcher.group(0));
        }
    }
}
```

matcher.find():
1.根据指定的规则，定位满足规则的子字符串
2.找到后，将子字符串的开始的索引记录到matcher 对象的属性 `int[] groups;`中
`groups[0]=0`把该子字符串的结束索引+1的值记录到group[1]=4
3.同时记录oldLast的值为 *子字符串的结束索引+1的值*  就是那个4，即下次执行find时，就从4开始匹配
4.根据`group[0]`和`group[1]=4` 截取字符串
（这是不是就是用那个kmp 算法实现的啊！）

```java
String regStr ="(\\d\\d)(\\d\\d)"; //分组了
```
当这个匹配字符串变成这样↑，规则变化了。
分组：正则表达式中的一组小括号。

# 正则表达式语法

元字符 \\\\  转义号
\\\\ :不使用转义字符会报错
（java里面用两个杠，其他的一个就行……总之大差不差但是会有细节的区别）

转义符的作用是告诉java你要找的是这个符号而不是其他的它被规定的含义
![[Pasted image 20230512123128.png]]

一些符号的功能
![[Pasted image 20230512123354.png]]
![[Pasted image 20230512123551.png]]

- JAva正则表达式默认区分大小写
	- abc  匹配字符串区分大小写
	- (?i )abc   匹配abc字符串不区分大小写  (?i) 默认有效范围是它以后的所有字符串，要项规定范围把它和要设定这个规则的字符用括号圈起来
		a((?!)b)c 这个表示在abc这个字符串中，仅b不区分大小写
	也可以通过：
```java
Pattern pat=Pattern.compile(regEX,Pattern.CASE_INSENSITIVE);
```
来更改字母的匹配模式为不区分大小写

### 选择匹配符 |
——**匹配”|“ 之前或之后的表达式**

 ab|bc   匹配”ab“或”cd“
 



## 限定符
——**限定字符出现次数**


### ' * '
——指定字符**重复**0或n次
(abc)*  可以匹配单个abc 或多个abc 的连续字符串 abc  abcabcabc都可以匹配到

### ‘ + ’
——指定字符重复一次或n次
m+(abc)*   以至少1个m开头，后接任意个abc的字符串     

### ' ? '
——指定字符串重复0次或1次（至多1次）   
m+(abc)?   以至少1个m开头 
对于‘ ? ’  在没有括号将字符串括起来的情况下，只作用于它前方一个字符串，即对于：
**abc?**  ?只对c负责， 可以匹配 ab abc 这两个字符串
### {n,m}
——指定至少n个匹配
[abcd]{3,}    由abcd中字母组成的任意长度不小于3的字符串
abcd]{3,5}    由abcd中字母组成的任意长度不小于3，不大于5的字符串




## 选择匹配符
## 分组组合和反向引用符
## 特殊字符
## 字符匹配符
[] 可接收的字符列表
## 定位符
——**规定要匹配的字符串出现的位置**

### ‘ ^ ’
——**指定起始字符**
^[0-9]+[a-z]*   以至少一个数字开头，后接任意个小写字母字符串

### ‘ $ ’
——**指定结束字符**
^[0-9]\\-[a-z]+$  以一个数字开头后接‘ - ’，并以至少1个小写字母结尾的字符串







