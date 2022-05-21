# JUC

## 1 什么是JUC

- java.util.concurrent
- java.util.concurrent.atomic
- java.util.concurrent.locks

## 2 进程和线程

### 2.1 进程和线程

进程: 一个程序   比如一个QQ  

线程: 比如可以在QQ中可以一边听歌一边聊天  这就是两个线程

java默认有两个线程  一个main 一个GC

对于java而言去开启线程   Thread  Runnable  Callable

java是无法直接开启线程的   无法直接操作硬件  他需要去调用c++

```java
public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
	//本地方法  底层的c++  java无法直接操作硬件
    private native void start0();
```

### 2.2 线程有几个状态

  6个

```java
public enum Statr{	
		//新生
        NEW,
		//运行
        RUNNABLE,
		//阻塞
        BLOCKED,
		//等待	  一直等
        WAITING, 
		//超时等待  等到某个时刻  
        TIMED_WAITING,
		//死亡
        TERMINATED;
}	
```

### 2.3 wait sleep的区别

- 来自不同的类  

  - wait来自 Object类  

  - sleep来自Thread类   

- 关于锁的释放    
  - wait是会释放锁的
  - sleep不会释放锁  

- 使用的范围
  - wait只在同步代码块中起作用
  - sleep可以在任意地方

### 2.4 并发和并行

并发(多线程操作同一个资源)

- cpu单核   模拟出来多条线程  天下武功 唯快不破  快速交替
- 自己的理解  一个核去做多件事情  但是在同一个时刻  该核只做一件事

并行(多个人一起行走)

- cpu多核  多个线程可以同时执行
- 自己的理解   多个核去做多件事情  在同一时刻  一个核也只做一件事情  但是有多核 所以可以同时做多件事情

```java
package com.yuwei.lesson01;

/**
 * @author 于伟
 * 做什么:查看cup的核数
 */
public class CpuNums {
    public static void main(String[] args) {
        int i = Runtime.getRuntime().availableProcessors();
        System.out.println(i);
    }
}
```

并发编程的本质  --> 充分利用CPU的资源

## 3 Lock锁(重点)

传统的  synchronized

```java
package com.yuwei.lesson01;

/**
 * @author 于伟
 * 做什么:基本的买票例子
 * 真正的线程开发 线程就是一个单独的资源类 没有任何附属操作
 * 属性 方法
 */
public class SaleTicketDemo {
    public static void main(String[] args) {
        //并发 多线程操作用一个资源
        Ticket ticket = new Ticket();

        new Thread(()->{
            for (int i = 0; i < 50; i++) {
                ticket.sale();
            }
        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 50; i++) {
                ticket.sale();
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 50; i++) {
                ticket.sale();
            }
        },"C").start();

    }
}

class Ticket{
    private int number = 50;
    int i = 0;

    public synchronized void sale(){
        if(number > 0){
            System.out.println(Thread.currentThread().getName() + "买了第" + (++i) + "张票,剩余:" + --number);
        }
    }
}
```

Lock接口

![image-20220425164225862](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220425164225862.png)



 公平锁  : 十分公平  可以先来后到

非公平锁  : 十分不公平  可以插队

默认是非公平锁  如果传入参数是true则是公平锁  

![image-20220425170306198](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220425170306198.png)

```java
package com.yuwei.lesson01;

import java.util.concurrent.locks.ReentrantLock;

/**
 * @author 于伟
 * 做什么:基本的买票例子
 * 真正的线程开发 线程就是一个单独的资源类 没有任何附属操作
 * 属性 方法
 */
public class SaleTicketLock {
    public static void main(String[] args) {
        //并发 多线程操作用一个资源
        Ticket1 ticket = new Ticket1();

        new Thread(()->{ for (int i = 0; i < 50; i++) ticket.sale(); },"A").start();
        new Thread(()->{ for (int i = 0; i < 50; i++) ticket.sale(); },"B").start();
        new Thread(()->{ for (int i = 0; i < 50; i++) ticket.sale(); },"C").start();
    }
}
class Ticket1{
    private int number = 50;
    ReentrantLock lock = new ReentrantLock();
    int i = 0;

    public void sale(){
        lock.lock();

        try {
            if(number > 0){
                System.out.println(Thread.currentThread().getName() 
                                   + "买了第" + (++i) + "张票,剩余:" + --number);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally{
            lock.unlock();
        }
    }
}
```

synchronized和lock的区别

- 前者是一个关键字  后者是一个类
- 前者是无法判断获取锁的状态  后者可以判断是否获取到了锁
- 前者会自动释放锁  后者是手动释放锁
- 前者 线程一获得锁 线程二就会进入等待 如果线程一阻塞了 线程二就一直等  后者就不会等待了 有个方法trylock
- 前者可重入锁 不可以中断的 非公平的       后者可重入锁  可以判断锁  可以自己设置公平非公平
- 前者适合锁少量的同步代码    后者适合锁大量的同步代码

锁是什么 如何判断锁的是谁

## 4 生产者和消费者问题

> ## synchronized版本     wait  和  notifyAll

```java
package com.yuwei.lesson02;

/**
 * @author 于伟
 * 做什么:
 */
public class PC01 {
    public static void main(String[] args) {
        F ab = new F();

        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) {
                    ab.decrement();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"A").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    ab.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();
    }
}
class F{
    private int number = 0;

    public synchronized void increment() throws InterruptedException {
        if(number != 0){
            this.wait();
        }
        number++;
        System.out.println(number);
        this.notifyAll();
    }

    public synchronized void decrement() throws InterruptedException {
        if(number == 0){
            this.wait();
        }
        number--;
        System.out.println(number);
        this.notifyAll();
    }
}
```

> ## 问题 如果有ABCD4个线程会怎么样   存在虚假唤醒

如何解决?  将if  -->  while

![image-20220426084439747](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220426084439747.png)

```java
package com.yuwei.lesson02;

/**
 * @author 于伟
 * 做什么:
 */
public class PC01 {
    public static void main(String[] args) {
        F ab = new F();

        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) {
                    ab.increment();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"A").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    ab.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    ab.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    ab.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"D").start();
    }
}
class F{
    private int number = 0;

    public synchronized void increment() throws InterruptedException {
        while(number != 0){
            this.wait();//等待
        }
        number++;
        System.out.println(Thread.currentThread().getName() + "-->" +  number);
        this.notifyAll();//通知
    }

    public synchronized void decrement() throws InterruptedException {
        while(number == 0){
            this.wait();//等待
        }
        number--;
        System.out.println(Thread.currentThread().getName() + "-->" +  number);
        this.notifyAll();//通知
    }
}
```

> ## Lock版本的生产者消费者问题

![image-20220426085927388](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220426085927388.png)

```java
package com.yuwei.lesson02;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @author 于伟
 * 做什么:通过lock和condition来解决生产者消费者问题
 */
public class PC02 {
    public static void main(String[] args) {
        H h = new H();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                h.increment();
            }
        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                h.decrement();
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                h.increment();
            }
        },"C").start();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                h.decrement();
            }
        },"D").start();
    }

}
class H{
    private int number = 0;
    Lock lock = new ReentrantLock();
    Condition condition = lock.newCondition();

    public void increment(){
        lock.lock();
        try {
            while (number != 0){
                condition.await();
            }
            number++;
            System.out.println(Thread.currentThread().getName() + "-->" + number);
            condition.signalAll();
        } catch (Exception e){
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decrement(){
        lock.lock();
        try {
            while (number == 0){
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName() + "-->" + number);
            condition.signalAll();
        } catch (Exception e){
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

> ## 如何用lock和condition来完成A->B->C->D

优点: **精确通知** 

```java
package com.yuwei.lesson02;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @author 于伟
 * 做什么:利用lock和condition来达到自定义通知
 */
public class PC03 {
    public static void main(String[] args) {
        O o = new O();

        new Thread(()->{
            for (int i = 0; i < 20; i++) {
                o.a();
            }
        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 20; i++) {
                o.b();
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 20; i++) {
                o.c();
            }
        },"C").start();

        new Thread(()->{
            for (int i = 0; i < 20; i++) {
                o.d();
            }
        },"D").start();
    }
}
class O{
    private int number = 1;
    Lock lock = new ReentrantLock();
    Condition condition1 = lock.newCondition();
    Condition condition2 = lock.newCondition();
    Condition condition3 = lock.newCondition();
    Condition condition4 = lock.newCondition();

    public void a(){
        lock.lock();
        try{
            if(number != 1){//标志符不是1就等待
                condition1.await();
            }
            number = 2;//设置标志符为2
            condition2.signal();//通知condition2
            System.out.println(Thread.currentThread().getName() + "-->" + "1");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void b(){
        lock.lock();
        try{
            if(number != 2){
                condition2.await();
            }
            number = 3;
            condition3.signal();
            System.out.println(Thread.currentThread().getName() + "-->" + "2");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void c(){
        lock.lock();
        try{
            if(number != 3){
                condition3.await();
            }
            number = 4;
            condition4.signal();
            System.out.println(Thread.currentThread().getName() + "-->" + "3");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void d(){
        lock.lock();
        try{
            if(number != 4){
                condition4.await();
            }
            number = 1;
            condition1.signal();
            System.out.println(Thread.currentThread().getName() + "-->" + "4");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

## 5 八锁现象

如何判断锁的是谁   

锁只锁两种  new对象(多个) class(唯一)

对锁的充分认识

```java
package com.yuwei.lesson03;

import java.util.concurrent.TimeUnit;

/**
 * @author 于伟
 * 做什么:八锁现象  第一个
 * 1.syn修饰的发短信方法先执行还是syn修饰的打电话方法先执行
 * 答案:发短信   打电话
 * 2.让syn修饰的发短信方法休眠四秒看谁先执行
 * 答案:发短信   打电话
 * 3.增加一个普通方法hello 看看是syn修饰的发短信方法先执行还是普通方法hello先执行
 * 答案:hello   发短信  因为发短信前有4s休眠
 * 4.增加一个phone对象 用phone2对象去调用syn修饰的call方法 看谁先执行
 * 答案:打电话   发短信   因为发短信前有4s休眠  两个对象表示两个不同的锁
 * 5.增加两个静态syn方法 只有一个对象 看是打电话先执行还是发短信先执行
 * 答案:发短信   打电话   静态方法锁的就是class对象
 * 6.增加一个对象 两个对象  看是打电话先执行还是发短信先执行
 * 答案:发短信  打电话   class对象一个类只有一个
 * 7.一个静态syn方法  一个非静态syn方法  先执行谁
 * 答案:打电话  发短信
 * 8.一个静态syn方法  一个非静态syn方法 两个对象 先执行谁
 * 答案:打电话  发短信
 *
 * 小结:
 * synchronized 锁的是phone对象
 * static synchronized锁的是class模板
 */
public class Lock01 {
    public static void main(String[] args) throws InterruptedException {
        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        //1.syn修饰的发短信方法先执行还是syn修饰的打电话方法先执行
        //new Thread(()->{phone1.sendMes();}).start();
        //TimeUnit.SECONDS.sleep(1);
        //new Thread(()->{phone1.call();}).start();

        //2.让syn修饰的发短信方法休眠四秒看谁先执行
        //new Thread(()->{phone1.sendMes();}).start();
        //TimeUnit.SECONDS.sleep(1);
        //new Thread(()->{phone1.call();}).start();

        //3.增加一个普通方法hello 看看是syn修饰的发短信方法先执行还是普通方法hello先执行
        //new Thread(()->{phone1.sendMes();}).start();
        //TimeUnit.SECONDS.sleep(1);
        //new Thread(()->{phone1.hello();}).start();

        //4.增加一个phone对象 用phone2对象去调用syn修饰的call方法 看谁先执行
        //new Thread(()->{phone1.sendMes();}).start();
        //TimeUnit.SECONDS.sleep(1);
        //new Thread(()->{phone2.call();}).start();

        //5.增加两个静态syn方法 只有一个对象 看是打电话先执行还是发短信先执行
        //new Thread(()->{phone1.sendMesStatic();}).start();
        //TimeUnit.SECONDS.sleep(1);
        //new Thread(()->{phone1.callStatic();}).start();

        //6.增加一个对象 两个对象  看是打电话先执行还是发短信先执行
        //new Thread(()->{phone1.sendMesStatic();}).start();
        //TimeUnit.SECONDS.sleep(1);
        //new Thread(()->{phone2.callStatic();}).start();

        //7.一个静态syn方法  一个非静态syn方法  先执行谁
        //new Thread(()->{phone1.sendMesStatic();}).start();
        //TimeUnit.SECONDS.sleep(1);
        //new Thread(()->{phone1.call();}).start();

        //8.一个静态syn方法  一个非静态syn方法 两个对象 先执行谁
        //new Thread(()->{phone1.sendMesStatic();}).start();
        //TimeUnit.SECONDS.sleep(1);
        //new Thread(()->{phone2.call();}).start();
    }
}
class Phone {
    //synchronized 锁的对象是方法的调用者
    //两个方法用的是同一个锁,谁先拿到谁执行
    public synchronized void sendMes(){//非静态发短信
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("发短信");
    }

    public static synchronized void sendMesStatic(){//静态发短信
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("发短信");
    }

    public synchronized void call(){//非静态打电话
        System.out.println("打电话");
    }

    public static synchronized void callStatic(){//静态打电话
        System.out.println("打电话");
    }

    public void hello(){//普通方法hello
        System.out.println("hello");
    }

}
```

##  6 线程类不安全

### 6.1 List不安全

```java
package com.yuwei.unsafe;

import java.lang.invoke.VolatileCallSite;
import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

/**
 * @author 于伟
 * 做什么:演示list并发不安全  并给出解决方案
 */
public class listDemo01 {
    public static void main(String[] args) {
        //单线程下是安全的
        //List<String> list = Arrays.asList("1", "2", "3");
        //list.forEach(System.out::println);

        //报错  java.util.ConcurrentModificationException  并发修改异常
        //并发下 ArrayList是不安全的
        //List<String> list = new ArrayList<>();
        //for (int i = 1; i <= 10; i++) {
        //    new Thread(()->{
        //        list.add(UUID.randomUUID().toString().substring(0,5));
        //        System.out.println(list);
        //    },String.valueOf(i)).start();
        //}

        //解决方案
        //1.用Vector()解决 但是Vector从1.0就有了  ArrayList是1.2才有的   这种方案太太太太low了
        //List<String> list = new Vector<>();
        //for (int i = 1; i <= 10; i++) {
        //    new Thread(()->{
        //        list.add(UUID.randomUUID().toString().substring(0,5));
        //        System.out.println(list);
        //    },String.valueOf(i)).start();
        //}
        //2.用Collections.synchronizedList(new ArrayList<>()) 去解决
        //List<String> list = Collections.synchronizedList(new ArrayList<>());
        //for (int i = 1; i <= 10; i++) {
        //    new Thread(()->{
        //        list.add(UUID.randomUUID().toString().substring(0,5));
        //        System.out.println(list);
        //    },String.valueOf(i)).start();
        //}
        //3.用new CopyOnWriteArrayList<>()去解决
        //CopyOnWrite写入时复制 COW 计算机程序设计领域的一种优化策略
        //多个线程调用的时候 list 读取的时候(固定) 写入(覆盖)
        //在写入的时候避免覆盖造成数据问题
        List<String> list = new CopyOnWriteArrayList<>();
        for (int i = 1; i <= 10; i++) {
            new Thread(()->{
                list.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
}
```

### 6.2 Set不安全

```java
package com.yuwei.unsafe;

import java.util.Collections;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;
import java.util.concurrent.CopyOnWriteArraySet;

/**
 * @author 于伟
 * 做什么:演示set并发不安全  并给出解决方案
 */
public class SetDemo01 {
    public static void main(String[] args) {
        //报错  java.util.ConcurrentModificationException  并发修改异常
        //并发下 Set是不安全的
        //Set<String> set = new HashSet<>();
        //for (int i = 1; i <= 30; i++) {
        //    new Thread(()->{
        //        set.add(UUID.randomUUID().toString().substring(0,5));
        //        System.out.println(set);
        //    },String.valueOf(i)).start();
        //}

        //解决方案
        //1.用Collections.synchronizedSet(new HashSet<>())解决
        //Set<String> set = Collections.synchronizedSet(new HashSet<>());
        //for (int i = 1; i <= 30; i++) {
        //    new Thread(()->{
        //        set.add(UUID.randomUUID().toString().substring(0,5));
        //        System.out.println(set);
        //    },String.valueOf(i)).start();
        //}
        //2.用JUC下的new CopyOnWriteArraySet<>()解决
        Set<String> set = new CopyOnWriteArraySet<>();
        for (int i = 1; i <= 30; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(set);
            },String.valueOf(i)).start();
        }
    }
}
```

### 6.3 HashSet底层

```java
//Set的本质就是Map的Key   key是无法重复的  所以set内的元素也不能重复
//PRESENT的作用就是顶替了map中的value
public HashSet() {
    map = new HashMap<>();
}

public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}

private static final Object PRESENT = new Object();
```

### 6.4 Map不安全

Map的基本操作

![image-20220427101527700](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427101527700.png)



```java
package com.yuwei.unsafe;

import java.util.Collections;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

/**
 * @author 于伟
 * 做什么:演示Map并发不安全  并给出解决方案
 */
public class MapDemo01 {
    public static void main(String[] args) {
        //map是这样用的吗  不是 工作中不用HashMap
        //initialCapacity 容量   loadFactor 加载因子
        //报错  java.util.ConcurrentModificationException  并发修改异常
        //并发下 Map是不安全的
        //Map<String, String> map = new HashMap<>();
        //for (int i = 1; i <= 30; i++) {
        //    new Thread(()->{
        //        map.put(Thread.currentThread().getName(),UUID.randomUUID().toString().substring(0,5));
        //        System.out.println(map);
        //    },String.valueOf(i)).start();
        //}

        //解决方案
        //1.Collections.synchronizedMap(new HashMap<>())
        //Map<String, String> map = Collections.synchronizedMap(new HashMap<>());
        //for (int i = 1; i <= 30; i++) {
        //    new Thread(()->{
        //        map.put(Thread.currentThread().getName(),UUID.randomUUID().toString().substring(0,5));
        //        System.out.println(map);
        //    },String.valueOf(i)).start();
        //}
        //2.JUC下的   new ConcurrentHashMap<>()  需要好好研究  在JavaJDK文档里面有
        Map<String, String> map = new ConcurrentHashMap<>();
        for (int i = 1; i <= 30; i++) {
            new Thread(()->{
                map.put(Thread.currentThread().getName(),UUID.randomUUID().toString().substring(0,5));
                System.out.println(map);
            },String.valueOf(i)).start();
        }
    }
}
```

## 7 Callable

![image-20220427103125450](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427103125450.png)



![image-20220427104243914](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427104243914.png)

```java
package com.yuwei.callable_;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

/**
 * @author 于伟
 * 做什么:callable如何通过Thread调用
 * callable可以抛出异常  有返回值
 */
public class CallableDemo01 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyThread myThread = new MyThread();
        FutureTask futureTask = new FutureTask<>(myThread);
        new Thread(futureTask,"A").start();
        new Thread(futureTask,"B").start();//结果会被缓存 效率高

        //返回值在futureTask中 调用get方法就可以得到
        //这个get方法必须等call方法全部执行完才能得到返回值  所以可能造成阻塞
        //一般放到最后一行  或者采用后面学的异步通信方式处理
        String s = (String) futureTask.get();
        System.out.println(s);
    }
}
class MyThread implements Callable<String>{
    @Override
    public String call() throws Exception {
        System.out.println("call()");
        return "调用成功";
    }
}
```

## 8 常用的辅助类

### 8.1 CountDownLatch

![image-20220427110200937](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427110200937.png)

```java
package com.yuwei.add;

import java.util.concurrent.CountDownLatch;

/**
 * @author 于伟
 * 做什么:CountDownLatch测试
 */
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        //总数是6  必须要执行任务的时候再使用
        CountDownLatch countDownLatch = new CountDownLatch(6);

        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "--> Go out");
                countDownLatch.countDown();//数量 -1
            },String.valueOf(i)).start();
        }

        countDownLatch.await();//等待计数器归0  然后再向下执行

        System.out.println("Close Door");
    }
}
```

原理:

- countDownLatch.countDown();//数量 -1
- countDownLatch.await();//等待计数器归0  然后再向下执行
- 每次有线程调用countDownLatch.countDown()数量-1   假设计数器变为0   countDownLatch.await()就会被唤醒

### 8.2 CyclicBarrier

![image-20220427111920819](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427111920819.png)

```java
package com.yuwei.add;

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

/**
 * @author 于伟
 * 做什么:CyclicBarrier测试   加法计数器
 */
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
            System.out.println("7颗龙珠已经集齐 召唤神龙成功");
        });

        for (int i = 0; i < 7; i++) {
            final int temp = i;
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "拿到了" + temp + "颗龙珠");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```



### 8.3 Semaphore

信号量

![image-20220427114549128](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427114549128.png)

```java
package com.yuwei.add;

import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

/**
 * @author 于伟
 * 做什么:6辆车去一个容量为3的地方停车
 */
public class SemaphoreDemo {
    public static void main(String[] args) {
        //停车场容量数
        Semaphore semaphore = new Semaphore(3);
        //6辆车
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                try {
                    //找到一个停车位
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "-->找到停车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "-->离开停车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    //释放停车位
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }

    }
}
```

原理

- semaphore.acquire(); 获得 加入已经满了则会等待  等待非满再加入
- semaphore.release(); 释放 将当前的信号量-1  然后唤醒等待的线程
- 作用  多个共享资源互斥的使用  并发限流  控制最大的线程数

## 9 读写锁

ReadWriteLock

![image-20220427133916039](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427133916039.png)

```java
package com.yuwei.ReadWrite;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * @author 于伟
 * 做什么:测试ReadWriteLockDemo
 * 独占锁(写锁) 一次只能被一个线程占有
 * 共享锁(读锁) 可以被多个线程同时占有
 * 读-读  可以共存
 * 读-写  不可以共存
 * 写-写  不可以共存
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        MyCacheLock myCacheLock = new MyCacheLock();

        ////写入  非加锁
        //for (int i = 1; i <= 5; i++) {
        //    final int temp = i;
        //    new Thread(()->{
        //        myCache.put(temp+"",temp+"");
        //    },String.valueOf(i)).start();
        //}
        ////读取  非加锁
        //for (int j = 1; j <= 5; j++) {
        //    final int temp = j;
        //    new Thread(()->{
        //        myCache.get(temp+"");
        //    },String.valueOf(j)).start();
        //}

        //写入  加锁
        for (int m = 1; m <= 5; m++) {
            final int temp = m;
            new Thread(()->{
                myCacheLock.put(temp+"",temp+"");
            },String.valueOf(m)).start();
        }

        //读取  加锁
        for (int n = 1; n <= 5; n++) {
            final int temp = n;
            new Thread(()->{
                myCacheLock.get(temp+"");
            },String.valueOf(n)).start();
        }



    }
}

//自定义缓存  加锁
class MyCacheLock{
    private volatile Map<String,Object> map = new HashMap<>();
    //读写锁 更加细粒度的控制
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    //写入put  同一时刻 只有一个线程去写
    public void put(String key, Object value){
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "写入" + key);
            map.put(key,value);
            System.out.println(Thread.currentThread().getName() + "写入完毕" + key);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }
    }

    //读取get  同一线程 可以多个线程去读
    public void get(String key){
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "读取" + key);
            map.get(key);
            System.out.println(Thread.currentThread().getName() + "读取完毕" + key);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
    }
}

//自定义缓存  不加锁
class MyCache{
    private Map<String,Object> map = new HashMap<>();

    //写入put
    public void put(String key, Object value){
        System.out.println(Thread.currentThread().getName() + "写入" + key);
        map.put(key,value);
        System.out.println(Thread.currentThread().getName() + "写入完毕" + key);
    }

    //读取get
    public void get(String key){
        System.out.println(Thread.currentThread().getName() + "写入" + key);
        map.get(key);
        System.out.println(Thread.currentThread().getName() + "写入完毕" + key);
    }
}
```

## 10 阻塞队列

BlockingQueue

阻塞(Blocking)

队列(Queue)

### 10.1 什么时候使用

一般在多线程并发和线程池中

![image-20220427141826864](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427141826864.png)

![image-20220427142225998](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427142225998.png)

![image-20220427144358807](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427144358807.png)

学会使用队列   添加  移除

### 10.2 阻塞队列的四组API

1. 如果队列满了再添加或者队列为空再移除  就会抛出异常
2. 如果队列满了再添加或者队列为空再移除  就会返回false
3. 如果队列满了再添加或者队列为空再移除  就会一直等待
4. 如果队列满了再添加或者队列为空再移除  可以设置等待时间  等待时间一过就会添加失败或者移除失败

| 方式         | 抛出异常  | 有返回值 没有异常 | 阻塞一直等待 | 阻塞等待超时         |
| :----------- | :-------- | :---------------- | :----------- | :------------------- |
| 添加         | add(元素) | offer(元素)       | put(元素)    | offer(元素,等待时间) |
| 移除         | remove()  | poll()            | take()       | poll(等待时间)       |
| 查找队首元素 | element() | peek()            | -            | -                    |

```java
package com.yuwei.BQ;

import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.TimeUnit;

/**
 * @author 于伟
 * 做什么:阻塞队列
 * 1.如果队列满了再添加或者队列为空再移除  就会抛出异常
 * 2.如果队列满了再添加或者队列为空再移除  就会返回false
 * 3.如果队列满了再添加或者队列为空再移除  就会一直等待
 * 4.如果队列满了再添加或者队列为空再移除  可以设置等待时间  等待时间一过就会添加失败或者移除失败
 */
public class BlockingQueueDemo {
    public static void main(String[] args) throws InterruptedException {
        //test1();
        //test2();
        //test3();
        test4();

    }

    //抛出异常
    public static void test1(){
        //参数为队列的大小
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        //添加
        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));
        System.out.println(blockingQueue.add("c"));
        System.out.println(blockingQueue);

        //查看队首元素
        System.out.println(blockingQueue.element());

        //再添加的话就超出了队列的大小  会抛出异常
        //java.lang.IllegalStateException: Queue full  队列已满
        //System.out.println(blockingQueue.add("d"));

        //移除
        blockingQueue.remove();
        blockingQueue.remove();
        blockingQueue.remove();
        System.out.println(blockingQueue);

        //队列此时为空 再移除会抛出异常
        //java.util.NoSuchElementException 队列没有元素
        //blockingQueue.remove();
    }

    //有返回值  不抛出异常
    public static void test2(){
        //参数为队列的大小
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        //添加
        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c"));
        System.out.println(blockingQueue.offer("d"));

        //查看队首元素
        System.out.println(blockingQueue.peek());

        //移除
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue);
    }

    //阻塞等待  一直等待
    public static void test3() throws InterruptedException {
        //参数为队列的大小
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        //添加
        blockingQueue.put("a");
        blockingQueue.put("b");
        blockingQueue.put("c");
        System.out.println(blockingQueue);
        //blockingQueue.put("d");//一直等待

        //移除
        blockingQueue.take();
        blockingQueue.take();
        blockingQueue.take();
        System.out.println("==============");
        blockingQueue.take();//一直等待
    }

    //阻塞等待  等待一段时间
    public static void test4() throws InterruptedException {
        //参数为队列的大小
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        //添加
        blockingQueue.offer("a");
        blockingQueue.offer("b");
        blockingQueue.offer("c");
        System.out.println("添加满了 等待两秒结束添加");
        blockingQueue.offer("d",2, TimeUnit.SECONDS);

        //移除
        blockingQueue.poll();
        blockingQueue.poll();
        blockingQueue.poll();
        System.out.println("队列元素为空 等待两秒结束移除");
        blockingQueue.poll(2,TimeUnit.SECONDS);
    }

}
```

## 11 同步队列

SynchronousQueue

```java
package com.yuwei.BQ;

import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.TimeUnit;

/**
 * @author 于伟
 * 做什么:同步队列
 * 不能存储元素
 * 添加进去一个  就取出一个  然后再去添加下一个
 */
public class SynchronousQueueDemo {
    public static void main(String[] args) {
        SynchronousQueue<String> synchronousQueue = new SynchronousQueue<>();

        new Thread(() -> {
            try {
                System.out.println(Thread.currentThread().getName() + " 添加了 a");
                synchronousQueue.put("a");
                System.out.println(Thread.currentThread().getName() + " 添加了 b");
                synchronousQueue.put("b");
                System.out.println(Thread.currentThread().getName() + " 添加了 c");
                synchronousQueue.put("c");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "A").start();

        new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName() 
                                   + " 取出了 " + synchronousQueue.take());
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName() 
                                   + " 取出了 " + synchronousQueue.take());
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName() 
                                   + " 取出了 " + synchronousQueue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "B").start();
    }
}
```

## 12 线程池(重点)

线程池:3大方法 7大参数 4种拒绝策略

### 12.1 **池化技术**

程序运行的本质   占用系统资源   所以我们要优化资源的使用  --->  池化技术

线程池  连接池  内存池  对象池    

创建 销毁  十分的浪费资源

所以引进池化技术   事先准备一些资源 等系统要用就给它  用完还给我

集中创建   共享使用   集中销毁

### 12.2 **线程池的好处**

1.降低资源的内耗  2.提高响应速度  3.方便管理   

线程复用  可以控制最大并大数  管理线程

### 12.3 **线程池三大方法**

```java
package com.yuwei.pool;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * @author 于伟
 * 做什么:Executors类下的三大方法
 * 线程池的三大方法
 */
public class method3 {
    public static void main(String[] args) {
        //test01();
        //test02();
        test03();
    }

    //单个线程
    public static void test01(){
        ExecutorService threadPool = Executors.newSingleThreadExecutor();

        try {
            for (int i = 0; i < 20; i++) {
                final int temp = i;
                threadPool.execute(()->{
                    System.out.println(temp + "  " + Thread.currentThread().getName());
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }

    //固定线程
    public static void test02(){
        ExecutorService threadPool = Executors.newFixedThreadPool(10);

        try {
            for (int i = 0; i < 20; i++) {
                final int temp = i;
                threadPool.execute(()->{
                    System.out.println(temp + "  " + Thread.currentThread().getName());
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }

    //可伸缩线程 遇强则强 遇弱则弱
    public static void test03(){
        ExecutorService threadPool = Executors.newCachedThreadPool();

        try {
            for (int i = 0; i < 100; i++) {
                final int temp = i;
                threadPool.execute(()->{
                    System.out.println(temp + "  " + Thread.currentThread().getName());
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }
}
```

### **12.4 线程池的7大参数**

源码分析

```java
//单线程池源码
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
//固定线程池源码
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
//可伸缩线程池源码
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,//越等于21亿
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
//这三个方法底层用的都是new ThreadPoolExecutor
public ThreadPoolExecutor
    (int corePoolSize,//核心线程池大小   一直开着
     int maximumPoolSize,//最大核心线程池大小   线程太多核心线程池处理不过来 再逐渐开启
     long keepAliveTime,//超时了没有人调用就会释放  超过核心线程池大小的一段时间没有被使用就会关闭
     TimeUnit unit,//超时单位
     BlockingQueue<Runnable> workQueue,//阻塞队列 排队在核心线程后面的线程  银行等候区的人  
     ThreadFactory threadFactory,//线程工厂  创建线程的  一般不用动
     RejectedExecutionHandler handler//拒绝策略  如果最大线程池加上队列都满了 有四种拒绝策略
    ) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
	}
```

![image-20220427173309795](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427173309795.png)

  ![image-20220427180106693](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427180106693.png)

### 12.5 手动创建线程池

```java
package com.yuwei.pool;

import java.util.concurrent.*;

/**
 * @author 于伟
 * 做什么:ThreadPoolExecutor
 */
public class parameter7 {
    public static void main(String[] args) {
        //public ThreadPoolExecutor(int corePoolSize,
        //核心线程池大小   类似于银行此时可以办理业务的窗口  2
        //int maximumPoolSize,
        //最大核心线程池大小  类似于银行办理业务的总窗口    5
        //long keepAliveTime,
        //超时了没有人调用就会释放  类似于除了银行常开的2个窗口外  其他窗口没事做到指定时间就停止办理业务
        //TimeUnit unit,
        //指定时间的单位
        //BlockingQueue<Runnable> workQueue,
        //阻塞队列 类似于银行等候区的人
        //ThreadFactory threadFactory,
        //线程工厂  创建线程的  一般不用动
        //RejectedExecutionHandler handler
        //拒绝策略  如果最大线程池加上队列都满了 有四种拒绝策略
        //new ThreadPoolExecutor.AbortPolicy//银行满了 还有人进来 不处理这个人的 抛出异常
        //new ThreadPoolExecutor.CallerRunsPolicy//哪里来的回哪里
        //new ThreadPoolExecutor.DiscardPolicy//队列满了 丢掉任务 不会排除异常
        //new ThreadPoolExecutor.DiscarOldPolicy//队列满了 尝试和最早的去竞争 不会抛出异常
        // ) {
        //    if (corePoolSize < 0 ||
        //            maximumPoolSize <= 0 ||
        //            maximumPoolSize < corePoolSize ||
        //            keepAliveTime < 0)
        //        throw new IllegalArgumentException();
        //    if (workQueue == null || threadFactory == null || handler == null)
        //        throw new NullPointerException();
        //    this.corePoolSize = corePoolSize;
        //    this.maximumPoolSize = maximumPoolSize;
        //    this.workQueue = workQueue;
        //    this.keepAliveTime = unit.toNanos(keepAliveTime);
        //    this.threadFactory = threadFactory;
        //    this.handler = handler;
        //}
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
                2,//2个一直开启的线程
                5,//总线程数
                2,//如果线程超过两个 且忙的过来  就等待2秒就关闭2个线程以外的线程
                TimeUnit.SECONDS,//单位
                new LinkedBlockingDeque<>(3),//总等候线程数
                Executors.defaultThreadFactory(),//默认的线程工厂 一般不动
                new ThreadPoolExecutor.CallerRunsPolicy()
        );

        try {
            for (int i = 0; i < 100; i++) {
                threadPoolExecutor.execute(()->{
                    System.out.println(Thread.currentThread().getName());
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPoolExecutor.shutdown();
        }
    }
}
```

### 12.6 线程池的4中拒绝策略

<img src="C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220427180808016.png" alt="image-20220427180808016" style="zoom:150%;" />



```java
new ThreadPoolExecutor.AbortPolicy//银行满了 还有人进来 不处理这个人的 抛出异常
new ThreadPoolExecutor.CallerRunsPolicy//哪里来的回哪里
new ThreadPoolExecutor.DiscardPolicy//队列满了 丢掉任务 不会抛出异常
new ThreadPoolExecutor.DiscarOldPolicy//队列满了 尝试和最早的去竞争 不会抛出异常
```

### 12.7 CPU密集型与IO密集型

最大线程如何定义

1.CPU密集型  几核就是几 	保持cpu的效率最高    

可以用Runtime.getRuntime().availableProcessors()  得到电脑是几核的

2.IO密集型  > 判断你程序中十分耗IO的线程

## 13 四大函数式接口(重点 简单 必须掌握)

新时代java程序员   lambda表达式  链式编程  函数式接口 Stream流式计算

函数式接口  只有一个方法的接口

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}

//超级多FunctionalInterface
//简化编程模型  在新版本的框架底层大量应用
//foreach(消费者类的函数式接口)
```

### 13.1 函数型接口function

![image-20220428114052520](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220428114052520.png).

```java
package com.yuwei.function;

import java.util.function.Function;

/**
 * @author 于伟
 * 做什么:函数式接口测试
 */
public class Demo01 {
    public static void main(String[] args) {
        //普通方法测试接口
        //Function<String, String> s = new Function<String, String>() {
        //    @Override
        //    public String apply(String o) {
        //        return o;
        //    }
        //};
        //System.out.println(s.apply("sss"));

        //用lambda表达式测试接口
        Function function = (str)->{return str;};
        System.out.println(function.apply("ada"));
    }
}
```

### 13.2 断定型接口Predicate

![image-20220428115203385](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220428115203385.png)

```java
package com.yuwei.function;

import java.util.function.Predicate;

/**
 * @author 于伟
 * 做什么:断定型接口Predicate
 */
public class Demo02 {
    public static void main(String[] args) {
        //普通方法测试
        //Predicate<String> stringPredicate = new Predicate<String>() {
        //    @Override
        //    public boolean test(String s) {
        //        return s.isEmpty();
        //    }
        //};
        //System.out.println(stringPredicate.test(""));
        //System.out.println(stringPredicate.test("ad"));

        //lambda方法测试
        Predicate<String> string =(str)->{return str.isEmpty();};
        System.out.println(string.test(""));
        System.out.println(string.test("ad"));

    }
}
```

### 13.3 Consumer消费型接口

![image-20220428125727066](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220428125727066.png)

```java
package com.yuwei.function;

import java.util.function.Consumer;

/**
 * @author 于伟
 * 做什么:Consumer消费型接口
 */
public class Demo03 {
    public static void main(String[] args) {
        //普通方法
        //Consumer<String> stringConsumer = new Consumer<String>() {
        //    @Override
        //    public void accept(String o) {
        //        System.out.println(o);
        //    }
        //};
        //
        //stringConsumer.accept("阿诗丹顿");

        //lambda表达式
        Consumer<String> stringConsumer = (s)->{
            System.out.println(s);
        };

        stringConsumer.accept("阿诗丹顿");
    }
}
```

### 13.4 Supplier供给型接口

![image-20220428130242343](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220428130242343.png)



```java
package com.yuwei.function;

import java.util.function.Consumer;
import java.util.function.Supplier;

/**
 * @author 于伟
 * 做什么:Supplier供给型接口
 */
public class Demo04 {
    public static void main(String[] args) {
        //普通方法
        Supplier<String> stringSupplier = new Supplier<String>() {
            @Override
            public String get() {
                return "阿诗丹顿";
            }
        };

        System.out.println(stringSupplier.get());

        //lambda表达式
        Supplier<String> ss = ()->{
            return "阿诗丹顿";
        };

        System.out.println(ss.get());
    }
}
```

## 14 Stream流式计算

什么是流式计算

大数据 = 存储 + 计算

集合 MYSQL 本质就是存储东西的

计算都应该交给流来做

```java
package com.yuwei.stream;

import java.util.Arrays;
import java.util.List;

/**
 * @author 于伟
 * 做什么:题目要求 一分钟完成 一行代码实现
 * 现在有5个用户 筛选
 * 1. ID必须是偶数
 * 2. 年龄必须大于23岁
 * 3. 用户名转为大写字母
 * 4. 用户名字字母倒着排序
 * 5. 只输出一个用户
 *
 */
public class Test {
    public static void main(String[] args) {
        User u1 = new User(1,"aa",23);
        User u2 = new User(2,"bb",23);
        User u3 = new User(3,"cc",24);
        User u4 = new User(4,"dd",25);
        User u5 = new User(6,"ee",26);

        List<User> usersList = Arrays.asList(u1, u2, u3, u4, u5);
        usersList.stream()
                .filter((u)->{return u.getId()%2 == 0;})//ID必须是偶数
                .filter((u)->{return u.getAge() > 23;})//年龄必须大于23岁
                .map((u)->{return u.getName().toUpperCase();})//用户名转为大写字母
                .sorted((user1,user2)->{return user1.compareTo(user2);})//用户名字字母倒着排序
                .limit(2)//只输出一个用户
                .forEach(System.out::println);
    }
}

@Data//可以生成get set toString方法
@NoArgsConstructor//可以生成无参构造
@AllArgsConstructor  //可以生成有参构造
public class User {
    private int id;
    private String name;
    private int age;
}
```

## 15 ForkJoin

### 15.1 什么是ForkJoin  

-  JDK1.7 并行执行任务 提高效率  必须是大数据量

- 大数据 会学一个 Map Reduce(把大任务拆分成小任务)

![image-20220428135910308](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220428135910308.png)

### 15.2 ForkJoin的特点  

- 工作窃取

- 里面维护的是双端队列

![image-20220428140204753](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220428140204753.png) 

### 15.3 ForkJoin的操作

![image-20220428141555153](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220428141555153.png) 

```java
package com.yuwei.forkjoin;

import java.util.concurrent.RecursiveTask;

/**
 * @author 于伟
 * 做什么:如何使用ForkJoin
 * 1.ForkJoinPool 通过它来执行
 * 2.计算任务 forkjoinpool.executor(ForkJoinTask task)
 * 3.计算类要去继承 ForkJoinTask
 */
//
public class forkjoinDemo extends RecursiveTask<Long> {
    private Long start;  //1
    private Long end;    //1990900000
    //临界值
    private Long temp = 10000L;
    public forkjoinDemo(Long start, Long end) {
        this.start = start;
        this.end = end;
    }
    //计算方法
    @Override
    protected Long compute() {
        if((end - start) < temp){
            Long sum = 0L;
            for (Long i = start; i <= end; i++) {
                sum += i;
            }
            return sum;
        }else {
            long middle = (start + end) / 2;//中间值
            forkjoinDemo task1 = new forkjoinDemo(start, middle);
            task1.fork();// 拆分任务 把任务压入线程队列
            forkjoinDemo task2 = new forkjoinDemo(middle + 1, end);
            task2.fork();// 拆分任务 把任务压入线程队列
            return task1.join() + task2.join();//将结果加起来
        }
    }
}
```

```java
package com.yuwei.forkjoin;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.ForkJoinTask;
import java.util.stream.LongStream;

/**
 * @author 于伟
 * 做什么:
 */
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        test01();//7036  普通
        test02();//4899  用forkjoin
        test03();//287   用Stream
    }

    //普通程序员
    public static void test01(){
        Long sum = 0L;
        long start = System.currentTimeMillis();
        for (Long i = 1L; i <= 10_0000_0000; i++) {
            sum += i;
        }
        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + " 时间" + (end - start));
    }

    //中等程序员  会使用forkjoin
    public static void test02() throws ExecutionException, InterruptedException {
        long start = System.currentTimeMillis();
        ForkJoinPool forkJoinPool = new ForkJoinPool();//new一个
        ForkJoinTask<Long> task = new forkjoinDemo(0L, 10_0000_0000L);
        ForkJoinTask<Long> submitResult = forkJoinPool.submit(task);//提交任务
        Long sum = submitResult.get();//得到最终结果
        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + " 时间" + (end - start));
    }

    //高级程序员 会使用Stream并行流
    public static void test03(){
        long start = System.currentTimeMillis();
        long sum = LongStream.rangeClosed(0L, 10_0000_0000L).parallel().reduce(0, Long::sum);
        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + " 时间" + (end - start));
    }
}
```

## 16 异步回调

Future设计的初衷  对将来的某个事件的结果进行建模

```java
package com.yuwei.future;

import java.sql.Time;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;

/**
 * @author 于伟
 * 做什么:异步回调  CompletableFuture
 * 异步回调
 * 成功回调
 * 失败回调
 */
public class Demo01 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //没有返回值的 runAsync  异步回调
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(()->{
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName());
        });
        System.out.println("1111");
        completableFuture.get();//获取阻塞执行结果

        //有返回值的 supplyAsync  异步回调
        // ajax 成功和失败的回调
        // 返回的是错误信息
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName());
            int i = 10/0;
            return 1024;
        });
        //正常 t=1024 u=null
        //错误 t=null u=java.util.concurrent.CompletionException: 		java.lang.ArithmeticException: / by zero
        System.out.println(completableFuture.whenComplete((t, u) -> {
            System.out.println("t = " + t);//正确  t不为null  u为null
            System.out.println("u = " + u);//错误  t为null   u不为null
        }).exceptionally((e) -> {//如果supplyAsync 中有错误  就会执行这里
            System.out.println(e.getMessage());
            return 233;
        }).get());
    }
}
```

## 17 JMM

请你谈谈你对volatile的理解

volatile是java虚拟机的一个**轻量级的同步机制**

1. 保证可见性
2. 不保证原子性
3. 禁止指令重排

什么是JMM

java Memory Model     java内存模型  不存在的东西  概念 约定

 关于JMM的一些同步的约定

1. 线程解锁前 必须把共享变量立刻刷回主存

2. 线程加锁前 必须读取主存中的最新值到工作内存中
3. 加锁和解锁是同一把锁

<img src="C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429101803007.png" alt="image-20220429101803007" style="zoom:80%;" /> 问题:如果线程B修改了值 线程A得不到反馈

![image-20220429102307198](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429102307198.png) 

```java
package com.yuwei.volatiletest;

import java.util.concurrent.TimeUnit;

/**
 * @author 于伟
 * 做什么:
 */
public class jmm {
    private static int i = 0;
    public static void main(String[] args) throws InterruptedException {
        new Thread(()->{
            while (i == 0){

            }
        }).start();

        TimeUnit.SECONDS.sleep(2);

        i = 1;
        System.out.println(i);
    }
}
```

线程不知道主内存的值被修改了 导致程序有问题

## 18 Volatile

保证可见性

```java
package com.yuwei.volatiletest;

import java.util.concurrent.TimeUnit;

/**
 * @author 于伟
 * 做什么:不加volatile会进入死循环
 * 加了volatile可以保证可见性
 */
public class jmm {
    private volatile static int i = 0;
    public static void main(String[] args) throws InterruptedException {
        new Thread(()->{
            while (i == 0){

            }
        }).start();

        TimeUnit.SECONDS.sleep(2);

        i = 1;
        System.out.println(i);
    }
}
```

不保证原子性

原子性  不可分割

线程A在执行任务的时候  不能被打扰 也不能被分割 要么同时成功 要么同时失败

```java
package com.yuwei.volatiletest;

import java.util.concurrent.TimeUnit;

/**
 * @author 于伟
 * 做什么:
 */
public class volatiletest {
    private volatile static int num = 0;

    public static void add(){
        num++;
    }
    public static void main(String[] args) {
        for (int i = 0; i < 20; i++) {
            new Thread(()->{
                for (int j = 0; j < 1000; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount() > 2){
            Thread.yield();
        }

        System.out.println(num);
    }
}
```

如果不加lock和synchronized如何保证原子性呢

```java
package com.yuwei.volatiletest;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @author 于伟
 * 做什么:
 */
public class volatiletest {
    private volatile static AtomicInteger num = new AtomicInteger();

    public static void add(){
        num.getAndIncrement();
    }
    public static void main(String[] args) {
        for (int i = 0; i < 20; i++) {
            new Thread(()->{
                for (int j = 0; j < 1000; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount() > 2){
            Thread.yield();
        }

        System.out.println(num);
    }
}
```

指令重排

什么是指令重排  你写的程序  计算机并不是按照你写的那样去执行的

源代码 -> 编译器优化的重排 -> 指令并行也可能会重排 -> 内存系统也会重排 -> 去执行

处理器在进行指令重排的时候   考虑数据之间的依赖性

```java
int x = 1;  //1
int y = 2;  //2
x = x + 5;  //3
y = x * x;  //4
//我们所期望的顺序是  1 2 3 4
//但是可能是2134  1324
```

| 线程A | 线程B |
| ----- | ----- |
| x=a   | y=b   |
| b=1   | a=2   |

abxy初始值为0   结果 x = 0  y = 0

但是可能由于指令重排

| 线程A | 线程B |
| ----- | ----- |
| b=1   | a=2   |
| x=a   | y=b   |

结果x = 2  y = 1

volatile避免指令重排

内存屏障.cpu指令 (计算机课程)

作用:保证特定的操作的执行顺序  可以保证某些变量的内存可见性(利用这些特性volatil实现了可见性)



![image-20220429112218363](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429112218363.png)









## 19 彻底玩转单例模式

### 19.1 饿汉式

```java
package com.yuwei.single;

/**
 * @author 于伟
 * 做什么:饿汉式单例
 */
public class Hungry {
    //可能会浪费空间
    private byte[] data1 = new byte[1024 * 1024];
    private byte[] data2 = new byte[1024 * 1024];
    private byte[] data3 = new byte[1024 * 1024];
    private byte[] data4 = new byte[1024 * 1024];

    private Hungry(){

    }

    private static Hungry HUNGRY = new Hungry();

    public static Hungry getInstance(){
        return HUNGRY;
    }
}
```

### 19.2 DCL懒汉式

```java
package com.yuwei.single;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;

/**
 * @author 于伟
 * 做什么:DCL懒汉式单例
 */
public class LazyMan {

    private static boolean yuwei = false;

    //防止反射破坏单例模式
    private LazyMan(){
        if(yuwei == false){
            yuwei = true;
        }else {
            throw new RuntimeException("反射破坏单例异常");
        }

        //synchronized (LazyMan.class){
        //    if(lazyMan != null){
        //        throw new RuntimeException("反射破坏单例异常");
        //    }
        //}

        //System.out.println(Thread.currentThread().getName());
    }

    private volatile static LazyMan lazyMan;

    //双重监测锁模式 懒汉式单例 也叫DCL懒汉式
    public static LazyMan getInstance(){
        if(lazyMan == null){
            synchronized (LazyMan.class){
                if(lazyMan == null){
                    lazyMan = new LazyMan();
                    //不是一个原子性的操作
                    //1.分配内存空间
                    //2.执行构造方法 初始化对象
                    //3.把这个对象指向这个空间
                }
            }
        }
        return lazyMan;
    }

    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException, NoSuchFieldException {
        //创建多线程去测试单例模式
        //for (int i = 0; i < 100; i++) {
        //    new Thread(()->{
        //        LazyMan.getInstance();
        //    }).start();
        //}

        //利用反射去破解单例模式
        //LazyMan instance = LazyMan.getInstance();
        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);
        Field declaredField = LazyMan.class.getDeclaredField("yuwei");
        declaredField.setAccessible(true);
        LazyMan instance1 = declaredConstructor.newInstance();
        declaredField.set(instance1,false);
        LazyMan instance2 = declaredConstructor.newInstance();
        //System.out.println(instance);
        System.out.println(instance1);
        System.out.println(instance2);

    }
}

```

### 19.3 静态内部类

```java
package com.yuwei.single;

/**
 * @author 于伟
 * 做什么:静态内部类
 */
public class Holder {
    private Holder(){

    }

    public static Holder getInstance(){
        return InnerClass.holder;
    }

    public static class InnerClass{
        private static final Holder holder = new Holder();
    }
}
```

### 19.4 单例不安全 反射

```java
package com.yuwei.single;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;

/**
 * @author 于伟
 * 做什么:懒汉式单例
 */
public class LazyMan {

    private static boolean yuwei = false;

    //防止反射破坏单例模式
    private LazyMan(){
        if(yuwei == false){
            yuwei = true;
        }else {
            throw new RuntimeException("反射破坏单例异常");
        }

        //synchronized (LazyMan.class){
        //    if(lazyMan != null){
        //        throw new RuntimeException("反射破坏单例异常");
        //    }
        //}

        //System.out.println(Thread.currentThread().getName());
    }

    private volatile static LazyMan lazyMan;

    //双重监测锁模式 懒汉式单例 也叫DCL懒汉式
    public static LazyMan getInstance(){
        if(lazyMan == null){
            synchronized (LazyMan.class){
                if(lazyMan == null){
                    lazyMan = new LazyMan();
                    //不是一个原子性的操作
                    //1.分配内存空间
                    //2.执行构造方法 初始化对象
                    //3.把这个对象指向这个空间
                }
            }
        }
        return lazyMan;
    }

    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException, NoSuchFieldException {
        //创建多线程去测试单例模式
        //for (int i = 0; i < 100; i++) {
        //    new Thread(()->{
        //        LazyMan.getInstance();
        //    }).start();
        //}

        //利用反射去破解单例模式
        //LazyMan instance = LazyMan.getInstance();
        Constructor<LazyMan> declaredConstructor = LazyMan.class.getDeclaredConstructor(null);
        declaredConstructor.setAccessible(true);
        Field declaredField = LazyMan.class.getDeclaredField("yuwei");
        declaredField.setAccessible(true);
        LazyMan instance1 = declaredConstructor.newInstance();
        declaredField.set(instance1,false);
        LazyMan instance2 = declaredConstructor.newInstance();
        //System.out.println(instance);
        System.out.println(instance1);
        System.out.println(instance2);

    }
}
```

### 19.5 单例不安全 枚举

```java
package com.yuwei.single;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

/**
 * @author 于伟
 * 做什么:枚举单例模式
 */
public enum EnumSingle {
    INSTANCE;
    public EnumSingle getInstance(){
        return INSTANCE;
    }
}

class test{
    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        EnumSingle instance1 = EnumSingle.INSTANCE;
        Constructor<EnumSingle> declaredConstructor = EnumSingle.class.getDeclaredConstructor(String.class,int.class);
        declaredConstructor.setAccessible(true); 
        EnumSingle instance2 = declaredConstructor.newInstance();
        System.out.println(instance1);
        System.out.println(instance2);
    }
}
```

![image-20220429135459471](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429135459471.png) 

![image-20220429133756322](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429133756322.png) 

![image-20220429134017524](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429134017524.png) 

枚举单例模式的反编译源码

```java
package com.yuwei.single;

public final class EnumSingle extends Enum{
    public static EnumSingle[] values(){
        return (EnumSingle[])$VALUES.clone();
    }
    
    public static EnumSingle valueOf(String name){
        return (EnumSingle)Enum.ValueOf(com/yuwei/single/EnumSingle,name);
    }
    
    public  EnumSingle(String s,int i){
        //有参构造器  
        //javap编译出来的是无参  这是用jad工具反编译出来的
        super(s, i);
    }
    
    public static EnumSingle getInstance(){
        return INSTANCE;
    }
    
    public static final EnumSingle INSTANCE;
    private static fanal EnumSingle $VALUES[];
    
    static{
        INSTANCE = new EnumSingle("INSTANCE", 0);
        $VALUES = (new EnumSingle[] {
            INSTANCE;
        });
    }
}
```

## 20 深入理解CAS

### 20.1 什么是CAS

大厂你必须要深入研究底层!!!

```java
package com.yuwei.cas;

import java.util.concurrent.atomic.AtomicInteger;

/**
 * @author 于伟
 * 做什么:cas测试
 */
public class casdemo {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(20);
        // 期望 更新
        // public final boolean compareAndSet(int expect, int update) {
        //     return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
        // }
        //如果和我期望的值一样 那么就更新 否则就不更新 CAS是cpu的并发原语
        System.out.println(atomicInteger.compareAndSet(20, 21));
        System.out.println(atomicInteger.get());
        System.out.println(atomicInteger.compareAndSet(21, 2021));
        System.out.println(atomicInteger.get());
    }
}
```

### 20.2 unsafe类

![image-20220429140714724](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429140714724.png)

![image-20220429141050289](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429141050289.png)

![image-20220429141951254](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429141951254.png)

CAS: 比较当前工作内存中 和主内存的值  如果这个值和期望值一样 那么就执行更新操作 如果不是就一直循环

缺点: 循环浪费时间  一次性只能保证一个共享变量的原子性  ABA问题

ABA问题(狸猫换太子)

```java
package com.yuwei.cas;

import java.util.concurrent.atomic.AtomicInteger;

/**
 * @author 于伟
 * 做什么:ABA问题
 */
public class casdemo {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2020);
        // 期望 更新
        // public final boolean compareAndSet(int expect, int update) {
        //     return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
        // }
        //如果和我期望的值一样 那么就更新 否则就不更新 CAS是cpu的并发原语
        //========捣乱的线程========
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());
        System.out.println(atomicInteger.compareAndSet(2021, 2020));
        System.out.println(atomicInteger.get());
        //========期望的线程========
        System.out.println(atomicInteger.compareAndSet(2020, 6666));
        System.out.println(atomicInteger.get());

    }
}
```

## 21 原子引用

```java
package com.yuwei.cas;

import java.sql.Time;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicStampedReference;

/**
 * @author 于伟
 * 做什么:用原子引用解决aba问题
 *
 */
public class aba {
    public static void main(String[] args) {
        AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference<>(20,1);

        new Thread(()->{
            int stamp = atomicStampedReference.getStamp();
            System.out.println("a1 -> " + stamp);
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(atomicStampedReference.compareAndSet(20, 21,
                    atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
            System.out.println("a2 -> " + atomicStampedReference.getStamp());

            System.out.println(atomicStampedReference.compareAndSet(21, 20,
                    atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
            System.out.println("a3 -> " + atomicStampedReference.getStamp());
        },"A").start();

        new Thread(()->{
            int stamp = atomicStampedReference.getStamp();
            System.out.println("b1 -> " + stamp);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(atomicStampedReference.compareAndSet(20,30,
                    stamp, stamp + 1));
            System.out.println("b2 -> "+ atomicStampedReference.getStamp());
        },"B").start();
    }
}
```

注意点

![image-20220429144730753](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220429144730753.png)

## 22 各种锁的理解

### 22.1 公平锁 非公平锁

公平锁: 不能够插队

非公平锁: 可以插队

```java
//默认
public ReentrantLock(){
    sync = new NonfairSync();
}
//可以传入一个布尔值 来选择公平锁非公平锁
public ReentrantLock(boolean fair){
    sync = fair ? new FairSync() : new NonfairSync();
}
```

### 22.2 可重入锁

也叫递归锁

![image-20220430113255357](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220430113255357.png)

```java
package com.yuwei.lock;

/**
 * @author 夏天
 * 有bug请联系QQ:1205232048
 * 做什么:可重入锁
 */
public class Demo01 {
    public static void main(String[] args) {
        Phone phone = new Phone();

        new Thread(()->{
            phone.sms();
        },"A").start();

        new Thread(()->{
            phone.sms();
        },"B").start();
    }
}
class Phone{
    public synchronized void sms(){
        System.out.println(Thread.currentThread().getName() + "sms");
        call();
    }

    public synchronized void call(){
        System.out.println(Thread.currentThread().getName() + "call");
    }
}
```

```java
package com.yuwei.lock;

import java.util.concurrent.locks.ReentrantLock;

/**
 * @author 夏天
 * 有bug请联系QQ:1205232048
 * 做什么:
 */
public class Demo02 {
    public static void main(String[] args) {
        Phone1 phone = new Phone1();

        new Thread(()->{
            phone.sms();
        },"A").start();

        new Thread(()->{
            phone.sms();
        },"B").start();
    }
}
class Phone1{
    ReentrantLock lock = new ReentrantLock();
    public void sms(){
        lock.lock();//锁必须配对
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "sms");
            call();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
            lock.unlock();
        }

    }

    public void call(){
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "call");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

### 22.3 自旋锁

```java
package com.yuwei.lock;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicReference;

/**
 * @author 夏天
 * 有bug请联系QQ:1205232048
 * 做什么:用CAS自旋锁
 */
public class Demo03 {
    static AtomicReference<Thread> atomicReference = new AtomicReference();

    public static void main(String[] args) throws InterruptedException {
        new Thread(()->{
            MyLock();
            MyUnLock();
        },"A").start();

        //TimeUnit.SECONDS.sleep(1);

        new Thread(()->{
            MyLock();
            MyUnLock();
        },"B").start();

    }

    public static void MyLock(){
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName() + "==> mylock");
        while (!atomicReference.compareAndSet(null,thread)){

        }
    }

    public static void MyUnLock(){
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName() + "==> myunlock");
        atomicReference.compareAndSet(thread,null);
    }
}
```

### 22.4 死锁

![image-20220430130101990](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220430130101990.png)

A拿到A锁后 还没有释放就想去拿B锁  此时B拿到了B锁也没释放又想去拿A锁  导致死锁  

死锁如何排除

```java
package com.yuwei.lock;

/**
 * @author 夏天
 * 有bug请联系QQ:1205232048
 * 做什么:死锁如何处理
 */
public class Demo04 {
    public static void main(String[] args) {
        new Thread(new deadLock("A锁","B锁"),"A").start();
        new Thread(new deadLock("B锁","A锁"),"B").start();
    }
}
class deadLock implements Runnable{
    private String lockA;
    private String lockB;

    public deadLock(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA){
            System.out.println(Thread.currentThread().getName() + "拿到了" + lockA + "又想去拿" +lockB);

            synchronized (lockB){
                System.out.println(Thread.currentThread().getName() + "拿到了" + lockB + "又想去拿" +lockA);
            }
        }
    }
}
```

通过`jps -l`得到进程号

![image-20220430130944092](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220430130944092.png)

通过`jstack 进程号`得到进程信息 定位死锁

![image-20220430131221790](C:\Users\Summer\AppData\Roaming\Typora\typora-user-images\image-20220430131221790.png)









