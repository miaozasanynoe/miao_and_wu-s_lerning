MCS与CHL不同，他是不断的轮询自己的节点,当一个线程释放锁之后，会将下一个线程的locked标记改为false
MSC与CLH最大的不同并不是链表是显示还是隐式，而是线程自旋的规则不同:CLH是在前趋结点的locked域上自旋等待，而MCS是在自己的

结点的locked域上自旋等待。正因为如此，它解决了CLH在NUMA（非对称性存储）系统架构中获取locked域状态内存过远的问题。
```java
public class MCSLock implements Lock {
    private  class Node{
        private volatile Node nxt;
        private volatile boolean locked;
    }
    AtomicReference<Node> tail =new AtomicReference<Node>();
    ThreadLocal<Node> mynode=ThreadLocal.withInitial(Node::new);
    @Override
    public void lock() {
       Node nd=mynode.get();
       nd.locked=true;
       Node t=tail.getAndSet(nd);
       if(t==null)return;;
       t.nxt=nd;
       while (nd.locked);
    }

    @Override
    public void unlock() {
        Node nd=mynode.get();
        if(nd.nxt==null){
        if(tail.compareAndSet(nd,null)){
        return;
        }
        while (nd.nxt==null);
        }
        nd.nxt.locked=false;
        nd.nxt=null;
        }

    @Override
    public boolean isReentrant() {
        return false;
    }
}
```