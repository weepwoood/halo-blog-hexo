---
title: Java多线程
tags:
  - Java
categories:
  - [Java, 多线程]
date: 2020-04-06 22:05:45

---


Java多线程

---

```java
// 多线程创建
/*  1. 创建一个继承与Thread类的子类
    2. 重写Thread类的run()方法
    3. 创建Thread类的子类的对象
    4. 通过此对象调用start()*/

public class CreateThread {

    public static void main(String[] args) {
        //3.创建Thread类的子类的对象
        MyThread t1 = new MyThread();
        //通过此对象调用start()
        t1.start();//线程开始执行或调用当前线程的run()方法
//        t1.run();//直接调用run()方法是单线程

        //获取当前线程的名字
        System.out.println("Thread.currentThread().getName() = " + Thread.currentThread().getName());

        System.out.println("hello");//hello先输出

        //再创建线程
//        t1.start();//报错:IllegalThreadStateException
        //重新创建一个线程对象
        MyThread t2 = new MyThread();
        t2.start();
    }
}

//1.创建一个继承与Thread类的子类
class MyThread extends Thread {
    //2.重写Thread类的run()方法
    //将此线程的操作声明在run()中

    @Override// run+Enter快捷方式
    public void run() {
        //获取当前线程的名字
        System.out.println("Thread.currentThread().getName() = " + Thread.currentThread().getName());
        for (int i = 0; i < 50; i++) {
            if (i % 2 == 0) {
                System.out.println("i = " + i);
            }
        }
    }
}
```

```java
// 创建两个线程一个遍历偶数一个遍历奇数
public class CreateThreadTest {
    public static void main(String[] args) {
        evenThread t1 = new evenThread();
        oddThread t2 = new oddThread();
        t1.start();
        t2.start();

        //简便方式:匿名子类
        new Thread() {
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    if (i % 2 == 0) {
                        System.out.println(Thread.currentThread().getName() + "：i = " + i);
                    }
                }
            }
        }.start();
    }
}

//遍历偶数
class evenThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if (i % 2 == 0) {
                System.out.println(Thread.currentThread().getName() + "：i = " + i);
            }
        }
    }
}

//遍历奇数
class oddThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if (i % 2 != 0) {
                System.out.println(Thread.currentThread().getName() + "：i = " + i);
            }
        }
    }
}
```

```java
// Thread中常用方法
/*
* getPriority() 获取线程优先级
* setPriority() 改变线程优先级
*   MAX_PRIORITY --> 10
*   MIN_PRIORITY --> 1
*   NORM_PRIORITY --> 5
* setName()     设置线程名
* sleep()       睡眠指定时间
* join()        阻塞
* isAlive()     查看线程状态
* yield()       释放当前CPU的使用权
* */

public class ThreadMethod {
    public static void main(String[] args) {
        thread t1 = new thread("0");
        t1.setName("A");//设置线程名
        t1.start();

        System.out.println("t1.isAlive() = " + t1.isAlive());

        //主线程命名
        Thread.currentThread().setName("主线程");
        //获取线程名称
        System.out.println("Thread.currentThread().getName() = " + Thread.currentThread().getName());
        //获取线程优先级
        System.out.println("Thread.currentThread().getPriority() = " + Thread.currentThread().getPriority());
        //改变线程优先级
        Thread.currentThread().setPriority(Thread.MAX_PRIORITY);
        System.out.println("Thread.currentThread().getPriority() = " + Thread.currentThread().getPriority());

        for (int i = 0; i < 100; i++) {
//            try {
//                //睡眠指定时间
//                Thread.currentThread().sleep(1000);
//            }
//            catch (InterruptedException e) {
//                e.printStackTrace();
//            }
            if (i % 2 == 0) {

                System.out.println(Thread.currentThread().getName() + "：i = " + i);
            }
            if (i == 20) {
                try {
                    //阻塞直到其他线程完成
                    t1.join();
                }
                catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
        System.out.println("t1.isAlive() = " + t1.isAlive());
    }
}

class thread extends Thread {

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if (i % 2 != 0) {
                System.out.println(Thread.currentThread().getName() + "：i = " + i);
            }
            if (i % 10 == 0) {
                //释放当前CPU的使用权
                Thread.currentThread().yield();
            }
        }
    }

    //通过构造器对线程命名
    public thread(String name) {
        super(name);
    }
}
```

