# 1.Java并发编程基础

## 1.3 线程通知与等待

+ 当线程调用共享对象的wait()时，当前线程只会释放当前共享对象的锁，当前线程持有的其他共享对象的监视器锁并不会被释放。
+ 当一个线程调用共享对象的wait（）方法被阻塞挂起后，如果其他线程中断了该线程，则该线程会抛出InterruptedException异常并返回。



## 1.6 让出CPU执行权的yield方法

+ sleep与yield方法的区别在于当线程调用sleep方法时调用线程会被阻塞挂起指定的时间，在这期间线程调度器不会去调度该线程。而调用yield方法时，线程只是让出自己剩余的时间片，并没有被阻塞挂起，而是处于就绪状态，线程调度器下一次调度时就有可能调度到当前线程执行。



## join

join()方法的作用，是等待这个线程结束；

也就是说，t.join()方法**阻塞调用此方法的线程**(calling thread)进入 **TIMED_WAITING** 状态，**直到线程t完成，此线程再继续**；

通常用于在main()主线程内，等待其它线程完成再结束main()主线程。



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





# 2.并发编程的其他基础知识

## 2.9 unSafe的使用

```java
/**
 * Unsafe类compareAndSwap操作(非阻塞原子操作)
 *
 * @author KouChaoJie
 * @since: 2021/12/24 09:32
 */
public class TestUnsafe {
    static final Unsafe unsafe;
    static final long stateOffset;
    private volatile long state = 0;

    static {
        try {
            //通过反射获取Unsafe的成员变量
            Field field = Unsafe.class.getDeclaredField("theUnsafe");

            //设置为可存取
            field.setAccessible(true);

            //获取值
            unsafe = (Unsafe) field.get(null);

            //获取state的偏移量
            stateOffset = unsafe.objectFieldOffset(TestUnsafe.class.getDeclaredField("state"));
        } catch (Exception e) {
            System.out.println(e.getLocalizedMessage());
            throw new Error(e);
        }
    }

    public static void main(String[] args) {
        TestUnsafe testUnsafe = new TestUnsafe();
        boolean b = unsafe.compareAndSwapInt(testUnsafe, stateOffset, 0, 1);
        System.out.println(b);
    }
}
```







# 3.Java并发包中ThreadLocalRandom类原理剖析









# 5.Java并发包中并发List源码剖析



根据源码注释可知`CopyOnWriteArrayList`是一个**线程安全**的**ArrayList**，对其所有的修改操作都是在底层的一个复制的数组上进行的，使用了**写时复制策略**。

![image-20220104174001565](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104174001565.png)





+ 初始化（构造函数）

  + 无参构造函数：

    可知无参构造函数是在其内部创建了一个大小为0的数组作为array的初始值。

  ![image-20220104174054181](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104174054181.png)

  + 有参构造：

    有参构造函数有两个

    此构造函数是将array值设置为入参数组副本。

    ![image-20220104174451396](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104174451396.png)

    第二个有参构造，将入参的集合元素复制到array

    ![image-20220104174831949](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104174831949.png)

+ 添加元素（以`add(E e)`为例）

  ![image-20220104175056918](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104175056918.png)

  根据源码可知，add方法首先会获取到独占锁，所以`CopyOnWriteArrayList`就保证了在一个线程添加元素的过程中其他线程不会对array进行修改。

  add在获取到锁后，首先会执行`Object[] elements = getArray();`获取到array，然后复制array到一个新的数组里并且在原来的数组长度+1，然后将入参元素添加到这个新数组中。从这里可以看出`CopyOnWriteArrayList`是一个无界List，并且添加的操作是在复制的新数组中进行。添加完之后会把这个新数组通过`setArray`替换原数组，并且在return之前释放锁。

+ 获取指定位置元素

  ![image-20220104214317825](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104214317825.png)

  ![image-20220104214345691](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104214345691.png)

  ![image-20220104214440909](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104214440909.png)

  获取指定元素时，首先会获取array，然后通过下标访问指定位置的元素。在整个过程中没有加锁同步，这就会产生**写时复制策略的弱一致性问题**：如果线程x在获取array后，下标查询前，线程y对线程x获取的元素进行了remove操作，remove首先会获取到独占锁，然后复制一份array，在复制的数组里删除线程x要获取的元素，再让array指向复制的新数组，这时候x还在使用被y删除前的数组。

+ 修改指定元素

  ![image-20220104220411201](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104220411201.png)

  查看源码可知set操作首先获取了独占锁，然后调用get获取array数组的指定下标位置的元素，如果新的入参元素与旧元素不相同，就创建一个新数组并复制元素，在新复制的数组的index位置修改成入参元素。为了确保volatile语义，即使入参元素与旧元素相同，也会重新设置array。

+ 删除元素（以`remove(int index)`为例）

  ![image-20220104221831126](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104221831126.png)

  首先获取独占锁，然后获取array数组，获取指定元素，判断是否删除的是最后一个元素，如果是则直接长度-1复制，如果不是，进行两次复制把要删除元素以外的元素复制到新数组，然后让array指向新数组，最后在返回前释放锁。

+ 迭代器的弱一致性

  ![image-20220104224140802](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104224140802.png)

![image-20220104224729140](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104224729140.png)

​	弱一致性问题指的是，返回迭代器后，其他线程对于list的增删改对于迭代器是不见的。如果在迭代器遍历元素过程中，其他线程没有对list进行增删改，snapshot就是array，因为他们是引用关系。但进行增删改后，snapshot就是快照，因为list里的array已经被替换。所以获取迭代器后，使用该迭代器元素时，其他线程对于list的增删改对迭代器不可见，因为他们俩操作的是不同的数组。下面使用一个例子说明弱一致性的效果：

```java
public class CopyList {
    private static volatile CopyOnWriteArrayList<String> arrayList = new CopyOnWriteArrayList<>();

    public static void main(String[] args) throws InterruptedException {
        arrayList.add("hello");
        arrayList.add("welcome");
        arrayList.add("to");
        arrayList.add("cucn");

        Thread thread = new Thread(() -> {
            arrayList.set(3, "CCUUCCNN");
            arrayList.remove(1);
        });

        //在线程启动之前获取迭代器
        Iterator<String> iterator = arrayList.iterator();
        thread.start();
        thread.join();

        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```

输出结果如下：

![image-20220104225928509](https://typora-1259727047.cos.ap-nanjing.myqcloud.com/img/2022/image-20220104225928509.png)

可以看到，子线程里的操作一个都没生效，这就是迭代器弱一致性的体现。







# 6.Java并发包中锁原理剖析

