参考学习：<https://www.cnblogs.com/dolphin0520/p/3923167.html>

新的锁出现，肯定是因为原有的锁不能满足一些业务场景，所以新锁就是为了弥补旧锁的不足

那么synchronized的缺陷是什么呢？

1.锁释放由jvm控制，由虚拟机控制是否需要释放，针对一般的异常处理都可以正常释放锁，但对于阻塞就无法很好的释放

2.作用范围有限，只能应用于方法体或代码块，不够灵活



简而言之Lock就是灵活，synchronized简单但作用有限，在非复杂场景中使用

```java
public class LockTest {
    Log log = LogFactory.getLog(this.getClass());
    private int index = 0;

    public static void main(String[] args) {
        final LockTest test = new LockTest();
        new Thread(new Runnable() {
            @Override
            public void run() {
                test.doTest();
            }
        }).start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                test.doTest();
            }
        }).start();
    }

    public void doTest(){
        Thread thread = Thread.currentThread();
        Lock lock = new ReentrantLock();
        lock.lock();
        log.info("获取锁："+thread.getName());
        try {
            index++;
            System.out.println(index);
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();  //必须解锁，防止发生死锁
            log.info("释放锁："+thread.getName());
        }
    }
}

// 输出结果
2018-12-25 10:29:08,941 [com.lp.framework.thread.LockTest]-[INFO] 获取锁：Thread-1
2018-12-25 10:29:08,941 [com.lp.framework.thread.LockTest]-[INFO] 获取锁：Thread-0
1
2
2018-12-25 10:29:08,942 [com.lp.framework.thread.LockTest]-[INFO] 释放锁：Thread-1
2018-12-25 10:29:08,942 [com.lp.framework.thread.LockTest]-[INFO] 释放锁：Thread-0

------------------------------------------------------------------------------------
根据上面的输出结果，可以看出锁没有起作用，那这是为什么呢？
经过分析，lock这个变量不是全局的，两个线程分别在各自的方法里面获取锁，需要搞清楚的是这分别是两把锁，互不影响的两把锁
```

```java
public class LockTest {
    Log log = LogFactory.getLog(this.getClass());
    private int index = 0;
    Lock lock = new ReentrantLock();        // 定义为全局变量，同一对象共用

    public static void main(String[] args) {
        final LockTest test = new LockTest();
        new Thread(new Runnable() {
            @Override
            public void run() {
                test.doTest();
            }
        }).start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                test.doTest();
            }
        }).start();
    }

    public void doTest(){
        Thread thread = Thread.currentThread();
        lock.lock();
        log.info("获取锁："+thread.getName());
        try {
            index++;
            System.out.println(index);
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();  //必须解锁，防止发生死锁
            log.info("释放锁："+thread.getName());
        }
    }
}
```

```java
方法摘要
void lock()
          获取锁。
void lockInterruptibly()
          如果当前线程未被中断，则获取锁。（可中断，获取不到就不获取了）
boolean tryLock()
          仅在调用时锁为空闲状态才获取该锁。（不可中断，获取不到就等着）
boolean tryLock(long time, TimeUnit unit)
          如果锁在给定的等待时间内空闲，并且当前线程未被中断，则获取锁。（定时可中断，获取不到，到了设定时间就不获取了）
```

ReadWriteLock  读写分离

如果有一个线程已经占用了读锁，则此时其他线程如果要申请读锁，可以立马获取读锁。

如果有一个线程已经占用了读锁，则此时其他线程如果要申请写锁，则申请写锁的线程会一直等待释放读锁。

如果有一个线程已经占用了写锁，则此时其他线程如果申请写锁或者读锁，则申请的线程会一直等待释放写锁。

**读和写存在互斥**

参考学习：<https://www.cnblogs.com/caolei1108/p/6281807.html>

Condition









Semaphore    信号量







ThreadLocal    局部线程







LinkedBlockingQueue    锁分离（见源码）