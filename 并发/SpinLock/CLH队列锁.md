CHL队列是一种自旋锁，每个线程都会轮询自己的前一个线程的状态，直到前一个线程释放锁。
```java
public class CHLLock implements Lock{
    private  class Node{
        private volatile boolean locked=false;
    }
    private AtomicReference<Node> tail=new AtomicReference<Node>();
    private ThreadLocal<Node> mynode;
    private ThreadLocal<Node> prenode;
    private int count;
    public CHLLock(){
        count=0;
        mynode=ThreadLocal.withInitial(Node::new);
        prenode=ThreadLocal.withInitial(()->null);
    }
    @Override
    public void lock() {
        Node node=mynode.get();
        if(node.locked==true){
            count++;
            return;
        }
        node.locked=true;
        Node pre=tail.getAndSet(node);
        if(pre!=null)while (pre.locked);
        count++;
    }

    @Override
    public void unlock() {
        if(mynode.get().locked==true&&--count==0){
            mynode.get().locked=false;
            mynode.set(prenode.get());
        }
    }

    @Override
    public boolean isReentrant() {
        return false;
    }
}
```