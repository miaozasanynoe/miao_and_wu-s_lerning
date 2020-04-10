## ThreadLocal使用及原理

### 1. ThreadLocal的使用  
ThreadLocal提供了线程内存储变量的能力，这些变量不同之处在于每一个线程读取的变量是对应的互相独立的。通过get和set方法就可以得到当前线程对应的值。
```java
static final ThreadLocal<T> sThreadLocal = new ThreadLocal<T>();
sThreadLocal.set()
sThreadLocal.get()
```
### 2.ThreadLocl的原理
每个线程内部有一个threadLocals的Map，以ThreadLocal为Key，要保存的值为Value,在java.lang.Thread中180行有定义
```java
  /* ThreadLocal values pertaining to this thread. This map is maintained
     * by the ThreadLocal class. */
ThreadLocal.ThreadLocalMap threadLocals = null;
```
由于修饰符是Default所以访问范围为包，java.lang.ThreadLocal封装了对threadLocals的操作。
在java.lang.ThreadLocal下有一个ThreadLocalMap的内部类,下面摘取了主要的代码
```java
  static class ThreadLocalMap {
        /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;
            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
        /**
         * The initial capacity -- MUST be a power of two.
         */
        private static final int INITIAL_CAPACITY = 16;
        /**
         * The table, resized as necessary.
         * table.length MUST always be a power of two.
         */
        private Entry[] table;
        /**
         * The number of entries in the table.
         */
        private int size = 0;

        /**
         * The next size value at which to resize.
         */
        private int threshold; // Default to 0

        /**
        .......
```
很明显一个线程的ThreadLocal对象的线程自己私有的，以键值对的形式保存在Thread对象里面，有ThreadLocal类提供访问的方法。
### 3.内存泄漏分析
根据上面的代码我们可以得出这张引用图  
[引用图](Imag/ThreadLocal1.png)  
在方法栈里面ThreadLocal直接引用了ThreadLocal，然后与ThreadLocalMap里面的Key存在弱引用关系，当前的线程与ThreadLocalMap存在强引用关系。  
首先我们要分析为什么ThreadLocal与Key是弱引用关系.  
当我们对栈里面的ThreaLocal赋null之后，ThreadLocal按道理应该要被GC的，假设这里是强引用关系，当GC的时候垃圾回收器进行可达性分析，发现ThreadLocal还可以到达ConcurentThreadLocal，则不会将其回收，所以要将线程里面threadLocals里面的Key与ThreadLocal设置为弱引用关系，只要在方法栈里面将ThreadLocal置空，堆内存里面的ThreadLocal就会在第二次GC的时候被回收.  
那么问题来了，ThreadLocalMap里面的value与当前线程还是存在强引用关系的，当我们将方法栈里面的ThreadLocal置空后Key就会被回收，Value则会一直在堆内存中与当前线程维持着强引用关系，直到线程结束，而我们也不能访问到这个value，当在线程一直不会结束的情况下就极其危险，下面我来展示一下造成内存泄漏的代码：  
```java
public class ThreadLoclaTest {
    public static class Apple{
        String name="apple";
        @Override
        protected void finalize() throws Throwable {
            super.finalize();
            System.out.println("apple 被GC了");
        }
    }
    public static class Threadt<T> extends ThreadLocal<T>{
        @Override
        protected void finalize() throws Throwable {
            super.finalize();
            System.out.println("ThreadLocal被GC了");
        }
    }
    static Main.Threadl<Apple> appleThreadl = new Main.Threadl<>();

    public static void main(String[] args) {
        ExecutorService service=Executors.newFixedThreadPool(1);
        service.execute(()->{
            appleThreadl.set(new Apple());
            appleThreadl.remove();
            appleThreadl=null;
            System.out.println(2);//断点1
            System.gc();
        });
    }
}

```
首先我们先注释掉a=null执行，这时候经过两次GC，ThreadLoMap里面key和value应该还在，运行结果确实如此：  
[注释a=null执行结果](Imag/ThreadLocal2.png)
取消注释后，当上面的代码执行到a=null的时候我们的String("1000")应该在堆内存里面被连接回收的，现在我们来看看运行的情况：  
[取消注释执行结果](Imag/ThreadLocal3.png)
从上面的图片可以看出了，虽然Key(refrent)以及为null了，但是value="1000"还保存在threadlocals里面，并且我们访问不到，就造成了内存泄漏，因为执行这段代码的是线程池里面的核心线程，所以这个内存泄漏会一直维持到线程池关闭为止。
解决方法：解决方法就是我们使用完这个ThreadLocal之后将其Remove掉，代码如下：
```java
public class ThreadLoclaTest {
    public static class Apple{
        String name="apple";
        @Override
        protected void finalize() throws Throwable {
            super.finalize();
            System.out.println("apple 被GC了");
        }
    }
    public static class Threadt<T> extends ThreadLocal<T>{
        @Override
        protected void finalize() throws Throwable {
            super.finalize();
            System.out.println("ThreadLocal被GC了");
        }
    }
    static Main.Threadl<Apple> appleThreadl = new Main.Threadl<>();

    public static void main(String[] args) {
        ExecutorService service=Executors.newFixedThreadPool(1);
        service.execute(()->{
            appleThreadl.set(new Apple());
            appleThreadl.remove();
            appleThreadl=null;
            System.out.println(2);//断点1
            System.gc();
        });
    }
}
```

