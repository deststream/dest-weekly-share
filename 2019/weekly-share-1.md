# 知识共享第1期

## 一、Android下的MonkeyTest

### 1、设备连接

```
adb devices
```

> 设备得连接成功，接下来的步骤才有意义

### 2、monkey测试命令

```
adb shell monkey -p appPackage -v 5000 > text.txt
```

### 3、appPackage查询方式

#### 方式一：

```
adb shell 

dumpsys window windows | grep -i current
```

>  该方法下得先在手机中打开我们要查询的App

#### 方式二：

使用 [APK Messenger]( https://pan.baidu.com/s/1QtUXiJq4ktHIoy47W8OSrg) 工具，点击可进行下载，其提取码为: dads 

![](http://pic.yuti.site/doc_APKMessenger.png)

#### 方式三：

很直接，问开发，哈哈哈，问你们自己。。。

### 4、日志信息分析

> text.txt 是将运行过程中的日志保存到text的文档中

* CRASH：程序崩溃
* ANR：程序无响应
* Exception：传说中的小问题




## 二、大小端

大小端问题，指多字节存储在寄存器中的安排问题，由什么引起的？常见于哪些应用场景？如何解决？

### 大小端是什么

**小端（Little-Endian）**：低位字节排放在内存的低地址端，高位字节排放在内存的高地址端。

**大端（Big-Endian）**：高位字节排放在内存的低地址端，低位字节排放在内存的高地址端。

比如数字 0x 12 34 56 78 在内存中的表示形式为：

- 大端模式：

低地址 -----------------> 高地址
0x12  |  0x34  |  0x56  |  0x78

- 小端模式：

低地址 ------------------> 高地址
0x78  |  0x56  |  0x34  |  0x12

#### 两个例子

- 16bit宽的数0x1234在Little-endian模式（以及Big-endian模式）CPU内存中的存放方式（假设从地址0x4000开始存放）为：

|内存地址 | 小端模式存放内容 | 大端模式存放内容 |
| --- | --- | --- |
|0x4000 | 0x34 | 0x12 |
|0x4001 | 0x12 | 0x34 |

- 32bit宽的数0x12345678在Little-endian模式以及Big-endian模式）CPU内存中的存放方式（假设从地址0x4000开始存放）为：

|内存地址 | 小端模式存放内容 | 大端模式存放内容 |
| --- | --- | --- |
|0x4000 | 0x78 | 0x12 |
|0x4001 | 0x56 | 0x34 |
|0x4002 | 0x34 | 0x56 |
|0x4003 | 0x12 | 0x78 |


#### 优劣势

大端小端没有谁优谁劣，各自优势便是对方劣势：

- 小端模式 ：强制转换数据不需要调整字节内容，1、2、4字节的存储方式一样。
- 大端模式 ：符号位的判定固定为第一个字节，容易判断正负。


### 引起原因

在计算机系统中，我们是以字节为单位的，每个地址单元都对应着一个字节，一个字节为8bit。但是在C语言中除了8bit的char之外，还有16bit的short型，32bit的long型（要看具体的编译器），另外，对于位数大于8位的处理器，例如16位或者32位的处理器，由于寄存器宽度大于一个字节，那么必然存在着一个如果将多个字节安排的问题。因此就导致了大端存储模式和小端存储模式。例如一个16bit的short型x，在内存中的地址为0x0010，x的值为0x1122，那么0x11为高字节，0x22为低字节。对于大端模式，就将0x11放在低地址中，即0x0010中，0x22放在高地址中，即0x0011中。小端模式，刚好相反。我们常用的X86结构是小端模式，而KEIL C51则为大端模式。很多的ARM，DSP都为小端模式。有些ARM处理器还可以由硬件来选择是大端模式还是小端模式。

### 常见场景及解决方法

很多时候，字节序的工作已由编译器完成了，但是在一些小的细节上，仍然需要去仔细揣摩考虑，尤其是在以太网通讯、MODBUS通讯、软件移植性方面。

项目开发中，手机蓝牙和STM8单片机蓝牙通信，手机为armv7a架构存储模式为大端，STM8为小端模式，为了单片机端处理方便，手机App端将数据处理成小端数组：

```
for (int i = 0; i < code.length; i += 4) {
    c = code[i];
    code[i] = code[i + 3];
    code[i + 3] = c;
    c = code[i + 1];
    code[i + 1] = code[i + 2];
    code[i + 2] = c;
}

```



### 参考


[https://blog.csdn.net/ce123_zhouwei/article/details/6971544](https://blog.csdn.net/ce123_zhouwei/article/details/6971544)

## 三、Java多线程程序设计

### 1、多线程简单概念

>首先，分为基于进程的多任务与基于线程的多任务。其实不用过多纠结两者的区别，无非就是进程与线程的区别（具体可百度进程与线程的差异）。多线程主要是使系统处理器提高效率的一种手段，同时防止了阻塞，在程序运行中起了很大作用。比如我们的智慧宿管项目中，在网络请求异步的操作中，就应用了多线程的手段。

### 2、简单了解线程创建
#### Thread类与Runnable接口
两句话表达他们之间的关系：
①、Java中真正能创建新线程的只有Thread类对象
②、通过实现Runnable的方式，最终还是通过Thread类对象来创建线程

```
// 步骤1：创建线程辅助类，实现Runnable接口
 class MyThread implements Runnable{
    ....
// 步骤2：复写run（），定义线程行为
    @Override
    public void run(){

    }
}

// 步骤3：创建线程辅助对象，即 实例化 线程辅助类
  MyThread mt=new MyThread();

// 步骤4：创建线程对象，即 实例化线程类；线程类 = Thread类；
// 创建时通过Thread类的构造函数传入线程辅助类对象
// 原因：Runnable接口并没有任何对线程的支持，我们必须创建线程类（Thread类）的实例，从Thread类的一个实例内部运行
  Thread td=new Thread(mt);

// 步骤5：通过 线程对象 控制线程的状态，如 运行、睡眠、挂起  / 停止
// 当调用start（）方法时，线程对象会自动回调线程辅助类对象的run（），从而实现线程操作
  td.start();
```
#### 补充：对于MyThread类的改进
```
//此处在内部定义了Thread类的thread对象，并用工厂方法createAndStart创建并执行实例化对象，为程序提供了方便
class MyThread implements Runnable{
Thread thread；

MyThread(String name){
thread = new Thread(this,name);
}

public static MyThread createAndStart(String name){
Mythread mythread = new MyThread(name);
mythread.thread.start();
return mythread;
}

@Override
public void run(){

 }
```
### 3、确定线程何时结束

>  Thread提供了两种方法，一种为isAlive()，这个简单粗暴，可以通过返回值来判断是否结束。
第二种为join()方法，顾名思义，join方法意思是等待（它调用的线程）加入它，直到（它调用的线程)终止时，才会执行join方法以后的代码。

### 4、关于线程的优先级

>首先明确一个概念，将低优先级赋予另一个线程并不一定意味着前一个线程就会比后一个线程运行得快或者获得的运行时间更多。高优先级的线程仅具有占用更多CPU时间的可能。
设置优先级的方法  final void setPriority(int level);  level一共有1-10个值，可用MIN_PRIORITY到MAX_PRIORITY之间来表示。

### 5、同步
>  同步是多线程操作的一个很重要的概念。同步是使线程协调工作的一个过程，，通过“锁”的概念来实现对对象或方法的一个唯一访问，防止资源的错乱。
常用的方法一：使用synchronized关键字修饰需要同步的方法。注：该方法在同一个对象内，只能同时被一个操作调用。
常用的方法二：使用synchronized代码块的形式控制。形如：
```
synchronized(object o){
//对象o的方法描述
}
```
#### 在同步之中，对资源利用率的提高。
>  通常情况下，每个线程使用的资源不止一个，而当一个线程在使用资源A时，又需要使用资源B。然而资源B正在被其他线程使用，并且使用了synchronized来控制资源B的占用。这时，该线程只能无限等待资源B被解放，才能够运行下去。然而这会使资源A被一直占用而无法释放，大大降低了资源的利用率。这时，我们需要使用notify(),wait(),notifyAll() 方法。在线程占用A时，而得不到资源B的时候，使用wait()方法，使该线程等待，从而释放资源A，当另一线程释放资源B的时候，使用notify()方法，告诉该线程，资源B已经可以使用了，从而让该线程继续执行。