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

### 方式二：

使用 [APK Messenger]( https://pan.baidu.com/s/1QtUXiJq4ktHIoy47W8OSrg) 工具，点击可进行下载，其提取码为: dads 

![](http://pic.yuti.site/doc_APKMessenger.png)

### 方式三：

很直接，问开发，哈哈哈，问你们自己。。。

### 4、日志信息分析

> text.txt 是将运行过程中的日志保存到text的文档中

* CRASH：程序崩溃
* ANR：程序无响应
* Exception：传说中的小问题

