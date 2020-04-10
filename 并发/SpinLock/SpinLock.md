### 简单实现的自选锁
自旋锁的策略是当线程去获取一个锁时，如果发现该锁已经被其它线程占有，那么它不马上放弃 CPU 的执行时间片，而是进入一个“无意义”的循环，查看该线程是否已经放弃了锁。但自旋锁适用于临界区比较小的情况，如果锁持有的时间过长，那么自旋操作本身就会白白耗掉系统的性能。
```java
public class SpinLock implements Lock{
  private AtomicReference<Thread> owner=new AtomicReference<Thread>();
    private int count=0;
    @Override
    public void lock() {
        Thread thread=Thread.currentThread();
        if(owner.get()==thread){
            count++;
            return;
        }
        while (!owner.compareAndSet(null,thread));
        count++;
    }
    @Override
    public void unlock() {
        Thread thread=Thread.currentThread();
        if(owner.get()==thread&&--count==0){
            owner.compareAndSet(thread,null);
        }
    }
}
```
上面代码中， owner 变量保存获得了锁的线程。这里的自旋锁有一些缺点，
第一个是没有保证公平性，等待获取锁的线程之间，无法按先后顺序分别获得锁；
另一个，由于多个线程会去操作同一个变量 owner，在 CPU 的系统中，存在着
各个 CPU 之间的缓存数据需要同步，保证一致性，这会带来性能问题。
