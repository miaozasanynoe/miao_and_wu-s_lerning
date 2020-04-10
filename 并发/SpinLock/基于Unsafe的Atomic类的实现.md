## 基于Unsafe的Atomic类的实现


### 1. Unsafe工具类
Unsafe工具类里面有一系列Naive方法提供了对内存中int,long以及Object类的一些原子性的操作，以下是sun.misc.Unsafe里面的一部分代码：
```java
@CallerSensitive
public static Unsafe getUnsafe() {
    Class var0 = Reflection.getCallerClass();
    if (!VM.isSystemDomainLoader(var0.getClassLoader())) {
        throw new SecurityException("Unsafe");
    } else {
        return theUnsafe;
    }
}

 public final native boolean compareAndSwapObject(Object var1, long var2, Object var4, Object var5);

    public final native boolean compareAndSwapInt(Object var1, long var2, int var4, int var5);

    public final native boolean compareAndSwapLong(Object var1, long var2, long var4, long var6);

```
我们可以看到Unsafe类提供了一个静态方法getUnsafe，但是加了@CallerSensitive注解，jvm的开发者认为Unsafe类危险，不希望开发者调用，就把这种危险的方法用 @CallerSensitive修饰，并在“jvm”级别检查，使得由@CallerSensitive修饰方法只能由引导类和扩展类加载器加载的类才能调用。
但是我们可以通过反射获取到Unsafe类的实例,并封装成一个Usafer工具类
```java
public final class Unsafer {
    private  static Unsafe unsafe;
    static {
        try {
            Field f = null;
            f = Unsafe.class.getDeclaredField("theUnsafe");
            f.setAccessible(true);
            unsafe = (Unsafe) f.get(null);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            e.printStackTrace();
        }
    }
    public static Unsafe getUnsafe(){
        return unsafe;
    }
}
```
### 2. 几种原子类的实现
有了Unsafe工具类，我们就可以实现原子类了  
* 首先是AtomicInteger：
```java
import sun.misc.Unsafe;
public class AtomicInteger {
    private static Unsafe unsafe=Unsafer.getUnsafe();
    private volatile int value;
    private static  long valueOffset;
    static {
        try {
            valueOffset = unsafe.objectFieldOffset(AtomicReference.class.getDeclaredField("value"));
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        }
    }
    public AtomicInteger(){
        this(0);
    }
    public AtomicInteger(int value){
        this.value=value;
    }
    public boolean compareAndSet(int expect,int update){
        return unsafe.compareAndSwapInt(this,valueOffset,expect,update);
    }
    public int getAndIncrement(){
        return unsafe.getAndAddInt(this,valueOffset,1);
    }
    public int get(){
        return unsafe.getInt(this,valueOffset);
    }
}
```
* 接着是AtomicReference
```java

import sun.misc.Unsafe;

public class AtomicReference<T> {
    private static  Unsafe unsafe=Unsafer.getUnsafe();
    private volatile T value;
    private static  long valueOffset;
    static {
        try {
            valueOffset = unsafe.objectFieldOffset(AtomicReference.class.getDeclaredField("value"));
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        }
    }

    public AtomicReference() {
        this(null);
    }

    public AtomicReference(T value) {
        this.value = value;
    }
    public T get(){
        return (T)unsafe.getObject(this,valueOffset);
    }
    public boolean compareAndSet(T expect, T update){
        return unsafe.compareAndSwapObject(this,valueOffset,expect,update);
    }
    public T getAndSet(T update){
        return (T)unsafe.getAndSetObject(this,valueOffset,update);
    }
}
```
