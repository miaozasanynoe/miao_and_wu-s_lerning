## 公平的TicketLock实现
为了解决公平性问题，可以让每个锁拥有一个服务号，表示正在服务的线程，
而每个线程尝试获取锁之前需要先获取一个排队号，然后不断轮询当前锁的
服务号是否是自己的服务号，如果是，则表示获得了锁，否则就继续轮询。
虽然解决了公平性的问题，但依然存在前面说的多 CPU 缓存的同步问题，
因为每个线程占用的 CPU 都在同时读写同一个变量 serviceNum，这会
导致繁重的系统总线流量和内存操作次数，从而降低了系统整体的性能。
```java
    private AtomicInteger serviceNum=new AtomicInteger();
    private AtomicInteger ticketNum=new AtomicInteger();
    private ThreadLocal<Integer> myNum=new ThreadLocal<Integer>();
    private int count=0;
    @Override
    public void lock() {
        if(myNum.get()!=null&&myNum.get()==serviceNum.get()){
            count++;
            return;
        }
        int thisNum=ticketNum.getAndIncrement();
        while (serviceNum.get()!=thisNum);
        myNum.set(thisNum);
        count++;
    }
    @Override
    public void unlock() {
        int ticket=myNum.get();
        if(--count==0) serviceNum.compareAndSet(ticket,ticket+1);
    }
}

```
