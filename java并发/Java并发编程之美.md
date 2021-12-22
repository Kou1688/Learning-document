# 1.Java并发编程基础

## 1.3 线程通知与等待

+ 当线程调用共享对象的wait()时，当前线程只会释放当前共享对象的锁，当前线程持有的其他共享对象的监视器锁并不会被释放。
+ 当一个线程调用共享对象的wait（）方法被阻塞挂起后，如果其他线程中断了该线程，则该线程会抛出InterruptedException异常并返回。



## 1.6 让出CPU执行权的yield方法

+ sleep与yield方法的区别在于当线程调用sleep方法时调用线程会被阻塞挂起指定的时间，在这期间线程调度器不会去调度该线程。而调用yield方法时，线程只是让出自己剩余的时间片，并没有被阻塞挂起，而是处于就绪状态，线程调度器下一次调度时就有可能调度到当前线程执行。





## 1.7 线程中断

- interrupt()，在一个线程中调用另一个线程的interrupt()方法，即会向那个线程发出信号——线程中断状态已被设置。至于那个线程何去何从，由具体的代码实现决定。
- isInterrupted()，用来判断当前线程的中断状态(true or false)。
- interrupted()是个Thread的static方法，并且会重置线程的中断状态。



![image-20211216095508992](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211216095508992.png)







## 1.9 线程死锁

死锁的产生必须具备四个条件：

+ 互斥条件
+ 请求并持有条件
+ 不可剥夺条件
+ 环路等待条件





## 1.11 ThreadLocal

ThreadLocal提供线程本地变量，如果你创建了一个ThreadLocal变量，那么访问这个变量的每个线程都会有这个变量的一个本地副本——当多个线程操作这个变量时，实际上操作的是本地内存中的变量，避免了线程安全问题。

### 实现原理简单分析

ThreadLocal继承了Thread。Thread中有一个`threadLocals` `inheritableThreadLocals`，两个都是`ThreadLocalMap类型的变量`，默认都为null。

![image-20211222115738106](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222115738106.png)

可以把`ThreadLocalMap`理解成一个定制化的HashMap。以上两个变量只有在线程调用`ThreadLocal`的set、get方法时才回去创建他们。每个线程的本地变量并不是放在`ThreadLocal`实例里面，而是存放在调用线程的`threadLocals`变量里面，也就是说`ThreadLocal`其实就是一个工具壳，通过set方法将value值保存在调用线程的`threadlocals`里，调用get方法时，再从`threadLocals`变量里面拿出来使用。如果调用线程一直不终止，那么这个本地变量会一直存放在调用线程的`threadLocals`变量里，有可能会造成内存溢出，当不需要使用本地变量的时候需要调用`ThreadLocal`里的remove方法，从`threadLocals`里删除本地变量。下面简单分析set、get、remove方法逻辑。

![image-20211222140410060](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222140410060.png)

set方法首先回去调用getMap(t)方法，返回线程自己的变量：

![image-20211222140515216](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222140515216.png)

如果获取不为空，就设置value值，key就是当前实例对象的引用。否则就去调用creatMap创建threadLocals变量。

![image-20211222140946482](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222140946482.png)

get方法：

![image-20211222141211588](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222141211588.png)

get回去先获取当前线程的threadLocals，如果不为空，则返回当前线程绑定的本地变量。否则去执行`setInitialValue`去初始化。

![image-20211222141512312](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222141512312.png)

![image-20211222141548619](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222141548619.png)

首先初始化值为null，然后去获取当前线程的threadLocals不为空，则设置当前线程的本地变量值为null，否则调用createMap创建。

remove方法：

![image-20211222141842315](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222141842315.png)

![image-20211222142007243](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222142007243.png)

如果当前线程的threadLocals变量不为空，就去删除当前线程中指定的实例变量。

在同一个ThreadLocal变量在父线程中被设置值后，在子线程中是获取不到的。为了解决这个问题，InheritableThreadLocal应运而生。

![image-20211222142331542](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222142331542.png)

可以看出InheritableThreadLocal继承了ThreadLocal并重写了三个方法。那么什么时候会去执行这些方法呢？可以先从Thread的构造方法看起：

![image-20211222142525942](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222142525942.png)

```java
private void init(ThreadGroup g, Runnable target, String name,
                  long stackSize, AccessControlContext acc,
                  boolean inheritThreadLocals) {
    ..............
        
    Thread parent = currentThread();
    
    ......................
        
    if (inheritThreadLocals && parent.inheritableThreadLocals != null)
        this.inheritableThreadLocals =
            ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);
    /* Stash the specified stack size in case the VM cares */
    this.stackSize = stackSize;

    /* Set thread ID */
    tid = nextThreadID();
}
```

在Thread初始化时会去获取父线程，然后去判断父线程inheritableThreadLocal是否为null，前面我们说InheritableThreadLocal重写了createMap方法，那么在调用get或set的时候创建的就不是threadLocals变量而是inheritableThreadLocals变量，所以这里的inheritableThreadLocal不为空，执行createInheritedMap：

![image-20211222143347145](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222143347145.png)

利用父线程的inheritableThreadLocals创建了ThreadMap赋值给子线程的inheritableThreadLocals

![image-20211222143622790](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2021/image-20211222143622790.png)

在ThreadMap里调用了重写的childValue方法，让子线程获取到了父线程的值。

