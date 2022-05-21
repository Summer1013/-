# JVM探究

- 请你谈谈你对JVM的理解? java8虚拟机和之前的变化更新?
- 什么是OOM? 什么是栈溢出StackOverFlowError? 怎么分析?
- JVM的常用调优参数有哪些?
- 内存快照如何抓取? 怎么分析Dump文件? 
- 谈谈JVM中,你对类加载器的认识?

## 0. JDK JRE JVM的关系

1. JDK包括了JRE和Java工具 比如javac javap
2. JRE包括了JVM和Java类库 比如JUC

![image-20220519091241507](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220519091241507.png)

## 1. JVM的位置

![image-20220423135855415](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220423135855415.png)

## 2. JVM的体系结构 

![image-20220423142425516](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220423142425516.png)

## 3. 类加载器

 作用:加载class文件

![image-20220423145145552](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220423145145552.png)

- 虚拟机自带的类加载器
- 启动类(根)加载器
- 扩展类加载器
- 应用程序类加载器

## 4. 双亲委派机制

保证安全

原理:APP --> EXC --> BOOT(最终执行)

1. 类加载器收到需要加载类的请求
2. 将这个请求向上委托给父类加载器去完成,一直向上委托,直到根加载器
3. 启动类加载器检查是否能够加载此类, 能加载就加载,使用当前的加载器,否则,抛出异常,通知子加载器进行加载
4. 重复步骤3

## 5. 沙箱安全机制



## 6. Native

- 凡是带了native关键字的  说明java的作用范围达不到了  回去调用底层C语言的库!
- 会进入本地方法栈
- 调用本地方法本地接口 JNI
- JNI作用:扩展java的使用,融合不同的编程语言为java所用
- 它在内存中专门开辟了一块标记区域  Native Method Stack 登记 native方法
- 在最终执行的时候  加载本地方法库中的方法  通过JNI

## 7. PC寄存器

程序计数器  Program Counter Register

每个线程都有一个程序计数器  是线程私有的  就是一个指针  指向方法区中的方法字节码(用来存储指向一条指令的地址  也即将要执行的指令代码)  在执行引擎读取下一条指令  是一个非常小的内存空间

## 8. 方法区

`静态变量`(static) `常量`(final) `类信息` (Class模板 **构造方法 接口定义**) `运行时的常量池`存放在方法区中 但是实例变量存放在堆内存中 和方法区无关

## 9. 栈

- 栈内存	主管程序的运行	生命周期和线程同步
- 线程结束  栈内存也就释放 对于栈来说 不存在垃圾回收问题
- 栈中放 8大基本类型 + 对象引用 + 实例的方法

## 10. 三种JVM

- sun公司	HotSpot   当前使用,一般都是这个
- BEA公司    JRockit
- IBM公司    J9VM

## 11.堆

重点

堆(Heap)   一个JVM只有一个堆内存   堆内存的大小是可以调节的 

类加载器读取了类文件后,一般会把什么东西放到堆中?类,方法,常量,变量,保存我们所有引用类型的真实对象

堆内存中还要细分三个区域

- 新生区(伊甸园区)  young/new
- 养老区 old
- 永久区 perm

![image-20220424104502472](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220424104502472.png)

GC垃圾回收,主要是在伊甸园区和养老区

堆内存满了,报错OOM,OutOfMemoryError.

在JDK8以后,永久存储区有另外一个名字,元空间

## 12. 新生区 

 类诞生和成长的地方,甚至死亡.

伊甸园区:所有对象都是在该区new出来的

幸存者0区幸存者1区:如果当伊甸园区中的对象达到了轻GC(GC)处理,处理完没有死亡的话则进入改区,这两个区是一个动态过程

经过研究,99%的对象都是临时对象

## 13. 老年区

当幸存者区达到了重GC(Full GC)的处理,处理完成后没有死亡的话,则进入老年区

## 14. 永久区

- jdk1.6之前    : 永久代, 常量池是在方法区
- jdk1.7           : 永久代, 慢慢的退化了, 去永久代, 常量池在堆中
- jdk1.8之后    : 无永久代, 常量池在元空间

这个区域常驻内存的,用来存放JDK自身携带的Class对象,存放Interface元数据,存储的是Java运行时的一些环境或类信息,这个区域不存在垃圾回收机制,关闭JVM虚拟机则会自动释放该部分内存

堆内存满, OOM出现的原因

- 一个启动类, 加载了大量的jar包
- Tomcat部署了太多的应用
- 大量动态生成的反射类
- 不断的被加载

## 15. 堆内存调优

```java
package com.yuwei.lesson01;

/**
 * @author 于伟
 * 做什么:堆内存调优
 */
public class test01 {
    public static void main(String[] args) {
        //返回虚拟机试图使用的最大内存
        long maxMemory = Runtime.getRuntime().maxMemory();

        //返回虚拟机的初始化总内存
        long totalMemory = Runtime.getRuntime().totalMemory();

        //1796MB 大约为运行内存的1/4
        System.out.println("maxMemory = " + maxMemory + "字节" + (maxMemory/(double)1024/1024)+ "MB");
        //123MB  大约为运行内存的1/64
        System.out.println("totalMemory = " + totalMemory + "字节" + (totalMemory/(double)1024/1024)+ "MB");

        //OOM
        //1.尝试扩大堆内存结果
        //2.分析内存,看一下是哪个地方的问题(专业工具)
    }
}
```

在一个项目中,突然出现了OOM故障,该如何排除

- 能够看到代码第几行出错  内存快照分析工具  MAT  Jprofiler
- Debug 一行一行的去分析

内存快照分析工具  MAT  Jprofiler作用

- 分析Dump内存文件, 快速定位内存泄漏
- 获得堆中的数据
- 获得大的对象
- ...

HeapDump

```java
package com.yuwei.lesson01;

/**
 * @author 于伟
 * 做什么:
 */
//-Xms1m -Xmx1m -XX:+HeapDumpOnOutOfMemoryError
public class TestJProfiler01 {
    public static void main(String[] args) {
        String summer = "summer";
        try {
            while (true) {
                summer += "sssssssssssssssssssssssssssssss";
            }
        }catch (Error e){
            e.printStackTrace();
        }


    }
}
```

## 16. GC

GC的作用区域: 方法区和堆

JVM在进行GC时,并不是对这三个分区统一回收,绝大部分都是在对新生区进行回收

- 新生区
- 幸存区(from  to)
- 老年区

GC两种类型:  轻GC(普通GC)  重GC(全局GC   FullGC)

GC题目:

- JVM的内存模型和分区  详细到每个区放什么
- 堆里面的分区有哪些? Eden from to old 说说他们的特点
- GC的算法有哪些? 标记清除法  标记整理(标记压缩)  复制算法  引用计数器  怎么用的
- 轻GC 和 重GC分别在什么时候发生

GC算法:

引用计数法

![image-20220425105304522](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220425105304522.png)

复制算法

![image-20220425110426735](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220425110426735.png)

![image-20220425111641419](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220425111641419.png)

-  好处   没有内存的碎片
- 坏处   浪费了内存空间  多了一半空间永远是空to  假设对象100%存活(极端情况)

复制算法的最佳使用场景   对象存货度较低的时候

标记清除算法

![image-20220425112500018](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220425112500018.png)

- 缺点 两次扫描严重浪费时间   会产生内存碎片
- 优点 不需要额外的空间

压缩

![image-20220425112755836](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220425112755836.png)

算法总结

内存效率       复制算法 > 标记清除算法 > 标记压缩算法

内存整齐度   复制算法 = 标记压缩算法 > 标记清除算法

内存利用率   标记压缩算法 = 标记清除算法 >  复制算法



思考一个问题   难道没有最优的算法吗

没有  只有最合适的算法    GC  分代收集算法



年轻代   存活率低  用复制算法比较合适

老年代  区域大 存活率高 用标记清除(内存碎片不是太多) 和 标记压缩混合实现

