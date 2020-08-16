

# 基础知识整理

## 字符与字节

字符：char  String数组 底层就是一个==char数组==  所以String的length就是char数组的长度，汉字有多少个长度就多少个

字节：byte

一个汉字可以用一个字符表示 比如Unicode编码

一个汉字如果要用字节表示的话得用==两个字节==



> Java语言中，中文字符所占的字节数取决于字符的编码方式，一般情况下，采用ISO8859-1编码方式时，一个中文字符与一个英文字符一样只占1个字节；采用GB2312或GBK编码方式时，一个中文字符占2个字节；而采用UTF-8编码方式时，一个中文字符会占3个字节。

在java中，char和byte都是基础数据类型，其中的byte和C++中的char类型是一样的，8位，1个字节，-128-127。但是，char类型，是16位，2个字节， '\u0000'-'\uFFFF'。

为什么java里的char是2个字节？ 

因为java内部都是用unicode的，所以java其实是支持中文变量名的，比如string 世界 = "我的世界";这样的语句是可以通过的。 

综上，java中采用GB2312或GBK编码方式时，一个中文字符占2个字节，而char是2个字节，所以是对的

## package

1. 为了更好地组织类，Java提供了包机制。包是类的容器，用于分隔类名空间。如果没有指定包名，所有的示例都属于一个默认的无名包。Java中的包一般均包含相关的类，java是跨平台的，所以java中的包和操作系统没有任何关系，java的包是用来组织文件的一种==虚拟文件系统==
2. import语句并没有将对应的java源文件拷贝到此处仅仅是引入，告诉编译器有使用外部文件，编译的时候要去读取这个外部文件。B错
3. Java提供的包机制与IDE没有关系
4. 定义在同一个包（package）内的类可以不经过import而直接相互使用。

## 接口和抽象类

```java
1、一个类可以有多个接口；
2、一个类只能继承一个父类；
3、接口中可以不声明任何方法，和成员变量
interface testinterface{
	
}
4、抽象类可以不包含抽象方法，但有抽象方法的类一定要声明为抽象类
 abstract class abstclass{
	abstract void meth();
}
```

## 类加载器ClassLoader

JDK中提供了三个ClassLoader，根据层级从高到低为：

```java
Bootstrap ClassLoader，主要加载JVM自身工作需要的类。 
Extension ClassLoader，主要加载%JAVA_HOME%\lib\ext目录下的库类。 
Application ClassLoader，主要加载Classpath指定的库类，一般情况下这是程序中的默认类加载器，也是**ClassLoader.getSystemClassLoader()** 的返回值。（这里的Classpath默认指的是环境变量中配置的Classpath，但是可以在执行Java命令的时候使用-cp 参数来修改当前程序使用的Classpath） 
```

JVM加载类的实现方式，我们称为 ==**双亲委托模型**==： 

如果一个类加载器收到了类加载的请求，他首先不会自己去尝试加载这个类，而是把这个请求委托给自己的父加载器，每一层的类加载器都是如此，因此所有的类加载请求最终都应该传送到顶层的**Bootstrap ClassLoader**中，只有当父加载器反馈自己无法完成加载请求时，子加载器才会尝试自己加载。 

**==双亲委托模型的重要用途是为了解决类载入过程中的安全性问题。==**

假设有一个开发者自己编写了一个名为***[Java](http://lib.csdn.net/base/java).lang.Object\***的类，想借此欺骗JVM。现在他要使用**自定义ClassLoader**来加载自己编写的***java.lang.Object\***类。然而幸运的是，**双亲委托模型**不会让他成功。因为JVM会优先在**Bootstrap ClassLoader**的路径下找到***java.lang.Object\***类，并载入它

## 类型转换

![image-20200726194955694](Java学习笔记.assets/image-20200726194955694.png)

# 多线程

之前面试的时候华为面试官问过，但是我当时没怎么复习操作系统，而且也没有多线程的编程经验，当时没有答好。

现在随着工作中用到的多线程的知识越来越多，需要梳理一下

## 多线程通信方式

 ### 锁

锁，用的最多的也就是synchronized关键字了，不多说

gitbook https://github.com/RedSpider1/concurrent 

阿里大神写的，做了参考

### 等待/通知机制

这也是一种通信方式，也就是Java里面的notify和wait

这里要注意，notify和wait要成对使用

然后注意下wait和sleep的区别:

* wait可以指定时间，也可以不指定；而sleep必须指定时间。
* wait释放cpu资源，同时释放锁；sleep释放cpu资源，但是不释放锁，所以易死锁。
* wait必须放在同步块或同步方法中，而sleep可以再任意位

## join方法

A线程调用B线程的join 方法，则A线程需要等待B线程结束之后才能被唤醒

# 设计模式

## 单例模式

### 何时用单例？

到现在还是很迷糊，到底啥时候用单例模式啊 ，现在只知道是数据库的连接用单例，然后Android里面见到广播接收器manager是单例，还是不太清楚为何用单例。

### 双重校验

为了减少锁的开销，只对创建对象的时候进行加锁

```java
public class Singleton {
    private volatile static Singleton singleton;
    private Singleton (){}
    public static Singleton getSingleton() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```

> 需要两次判空的原因：第一次判断是否需要创建对象，第二次是为了防止，两个线程同时满足第一个判空条件进入同步代码区的时候，先拿到锁的会去创建对象，对于后面拿到锁的需要再次判断是否对象已经创建好了。



##  策略模式

说白了就是抽象处理方法，对于不同场景使用不同的算法进行处理

## 观察者模式

Subject 包含Observe，需要的时候去通知他们。

### 观察者模式的改进

正常的观察者模式里面Subject里面还是依赖Observe对象，需要Observe对象实现具体的接口。

万一重构的代码是已经实现好的类，这时候观察者模式就不太好用了。

这个时候就可以用到**委托模式**

>  实际上就是委托第三方去添加删除Observe，同时在需要通知的时候去执行所有Observe的特定的方法

```java
//数学老师
Teacher math = new Teacher();
//打游戏的小明同学
GameObserver a = new GameObserver("小明",math);
//看NBA地小杨同学
NBAObserver b = new NBAObserver("小杨", math);
//将二者方法委托到老师的更新上
math.Update += new EventHandler(a.CloseGame);
math.Update += new EventHandler(b.CloseNBA);
//老师回来了
math.SubjectState = "数学老师来教室了！";
//触发通知
math.Notify();
```



> 在Java中就是使用反射，EventHandler传入函数名，通过反射来根据函数名来执行对应的方法

w