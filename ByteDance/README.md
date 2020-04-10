一面
1. TCP、UDP
2. HashMap，put机制
3. java装箱、拆箱
4. 二叉搜索树、红黑树
5. coding：旋转数组，买卖股票（面试官问我做过吗，我说都做过，两人尴尬一笑）

二面
1. 写个单例模式（范围），怎么保证线程安全
2. 写个生产者消费者模式
3. 浏览器输入网址然后进行网页渲染的过程，HTTP协议讲一下
4. 302是什么意思，还能获取数据吗
5. HTTP端口号，HTTPS端口号和原理
* 80，443
6. coding：找到一个数组中只出现一次的那个数字（其他都出现两次），如果有两个出现一次的数字呢、判断链表中是否有环
```c++
//找到一个数组中只出现一次的那个数字，其余数字出现两次
    int singleNumber(vector<int>& nums) {
        int res=0;
        for(auto i:nums){
            res^=i;
        }
        return res;
    }
//找到一个数组中只出现一次的那两个数字,其余数字出现两次
    vector<int> singleNumber(vector<int>& nums) {
        int res=0,res1=0,res2=0,pos=-1;
        for(auto i:nums)
            res^=i;
        for(int i=0;i<32&&pos==-1;i++)
            if(res&(1<<i))pos=i;
        for(auto i:nums){
            if(i&(1<<pos))res1^=i;
            else res2^=i;
        }
        return vector<int>{res1,res2};
    }
//找到一个数组中只出现一次的那个数字,其余数字出现三次
   int singleNumber(vector<int>& nums) {
        int res=0;
        for(int i=0;i<32;i++){
            int cnt=0;
            for(auto j:nums){
                if(j&1<<i)cnt++;
            }
            res|=(cnt%3)<<i;
        }
        return res;
    }
//判断链表有没有环以及返回入环节点
    ListNode *detectCycle(ListNode *head) {
        if(head==NULL||head->next==NULL)return NULL;
        ListNode *n1=head->next,*n2=head->next->next;
        while(n1!=n2){
            n1=n1->next;
            if(n2==NULL||n2->next==NULL)return NULL;
            n2=n2->next->next;
        }
        while(head!=n2){
            n2=n2->next;
            head=head->next;
        }
        return n2;
    }
//一个字符串，打印出其中最长的没有重复字符的子串
    int lengthOfLongestSubstring(string s) {
        set<char> t;
        int st=0,ed=0,maxl=0;
        for(;ed<s.size();){
            if(t.find(s[ed])==t.end()){
                t.insert(s[ed++]);
                maxl=max(ed-st,maxl);
            }
            else{
                t.erase(t.find(s[st]));
                st++;
            }
        }
        return maxl;
    }
//数组旋转O(n)时间复杂度 O(1)空间复杂度
    void rotate(vector<int>& nums, int k) {
        int len=nums.size(),count=0;k=k%len;
        for(int start=0;count<len;start++){
            int pos=start,tmp=nums[start];
            while(1){
                count++;
                int t=(pos-k+len)%len;
                if(t==start){
                    nums[pos]=tmp;
                    break;
                }
                nums[pos]=nums[t];
                pos=t;
            }
            start=pos;
        }
    }
```
三面
1. 聊项目
2. JVM内存管理，垃圾回收
3. Android中的返回上一个应用怎么做（不清楚）
4. 苹果手机的界面是属于OS吗
5. coding：一个字符串，打印出其中最长的没有重复字符的子串

***************************

一面：
1. 自我介绍
2. tcp和udp区别，应用场景
3. tcp三次握手，为什么有第三次
4. get和post区别，其他命令
    * url可见性
    * 幂等性
    * 可缓存性
    * 安全性
    * 在数据传输上（get使用拼接url，post在body中）
    * 传输数据大小（get受url长度限制，post具体看配置）
5. 进程和线程区别，什么时候用多线程，怎么保证线程安全
    * 进程和线程
6. synchronized的使用，是对什么加锁
    * 普通同步方法，锁是当前实例对象（对象锁）
    * 对于静态同步方法，锁是当前类Class对象（类锁）
    * 对于同步方法块，锁是Synchronized括号里配置的对象
7. 栈和队列，怎么用栈实现队列
8. 垃圾回收算法，复制算法中存活对象太多怎么办
    * 标记清除算法
    * 标记整理算法
    * 复制算法
    * 分代算法
9. 可达性分析，GC Root可以是哪些对象
    * 虚拟机栈中引用的对象
    * 方法区类静态属性引用的对象
    * 方法区常量池引用的对象
    * 本地方法栈JNI引用的对象
10. hashmap底层实现，put get过程，扩容，为什么容量为2的倍数
11. java基本类型，基本类型间的转换(short+int得到int类型，不能直接把int型赋给short型)
12. String类，字符串相加+的底层操作，StringBuilder和StringBuffer
13. 算法：给一个数组，使数组奇数在前半段，偶数在后半段，在原数组上操作。
```c++
//给一个数组，使数组奇数在前半段，偶数在后半段，在原数组上操作。
    vector<int> sortArrayByParity(vector<int>& A) {
        int st=0,ed=A.size()-1;
        while(st!=ed){
            if(A[st]&1)swap(A[st],A[ed--]);
            else st++;
        }
        return A;
    }
//第k大，快速排序思想
    int QuickSort(vector<int> &nums, int l, int r, int k){
        int key = nums[l];
        int st = l, ed = r;
        while (st < ed){
            while (nums[ed]<key&&ed>st){
                ed--;
            }
            nums[st]=nums[ed];
            while (nums[st]>=key&&ed>st){
                st++;
            }
            nums[ed]=nums[st];
        }
        nums[ed]=key;
        if(ed==k)return nums[ed];
        if(ed<k)return QuickSort(nums, ed + 1, r, k);
        return QuickSort(nums, l, ed - 1, k);
    }
    int findKthLargest(vector<int> &nums, int k){
        return QuickSort(nums, 0, nums.size() - 1, k-1);
    }
```
二面：
1. 手写线程安全的单例模式，为什么两次判断空，为什么用volatile
2. 算法：二叉树反向层次遍历
```c++
//二叉树反向层次遍历
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> vec; 
        if(root==NULL)return vec;
        TreeNode *t=root;
        queue<TreeNode*> q;
        q.push(t);
        while(!q.empty()){
            vector<int> tmp;
            int siz=q.size();
            while(siz--){
                TreeNode *now=q.front();
                q.pop();
                if(now->left!=NULL)q.push(now->left);
                if(now->right!=NULL)q.push(now->right);
                tmp.push_back(now->val);
            }
            vec.insert(vec.begin(),tmp);
        }   
        return vec;
    }
};
```
3. https，ssl握手过程
* https过程
        * 客户端发起一个http请求，连接到服务器的443端口。
        * 服务端把自己的信息以数字证书的形式返回给客户端（证书内容有密钥公钥，网站地址，证书颁发机构，失效日期等）。证书中有一个公钥来加密信息，私钥由服务器持有。
        * 验证证书的合法性
        * 生成随机密码（RSA签名）
        * 生成对称加密算法
4. http1.0、1.1（断点续传的一些标志）、2.0区别，http状态码
* 三者区别：
    * http1.0:无连接,无状态,一次请求一个tcp连接
    * http1.1:持久连接,请求管道化(有一些缺陷) ,增加了host字段,缓存,断点续传
    * http2.0 : 二进制分帧(多路复用的实现基础), 多路复用,头部压缩
* 断点续传： 客户端的请求头对应的参数是range，服务端的响应头对应的参数是Content-Range
5. sleep和wait的区别，用什么对象调用wait
6. 类加载机制，双亲委派，解决了什么问题（避免重复加载，安全）
    * 加载 加载字节码文件,将类对象加载到方法区
    * 准备  
        * 验证  文件验证，字节码验证，元数据验证，符号引用验证
        * 准备  将静态变量赋初值，final static 赋值，引用类型赋null
        * 解析  符号引用转化为直接引用，静态链接
    * 初始化    执行<cinit>与<init>  

    双亲委派
    * 引导类加载器
    * 扩展类加载器
    * 系统类加载器
    * 自定义类加载器
    * 好处：
        * 避免重复加载
        * 防止核心类被篡改
7. 设计模式，动态代理  
    工厂模式，单例模式

三面：
1. 算法：给一个list删除里面指定的值为target的元素；查找有序数组中出现target的第一个位置，如果没有返回-1（二分查找）
2. 项目，非常仔细，虽然我做的项目跟岗位没有一点关系。。
3. 多态，为什么可以实现多态  
4. 重载和重写  
    java三大特性，封装，继承，多态
    多态的实现方式，重载与重写
    重载是在同一个类里面根据参数类型，数量，顺序进行重载
    重写是重写父类的方法


1. mapreduce过程
2. HTTP相关
3. TCP三次握手改成两次可以吗，为什么
4. TCP、UDP区别
* 应用层-------。ICP/IP协议族在这一层面有着很多协议来支持不同的应用，许多大家所熟悉的基于Internet的应用的实现就离不开这些协议。如我们进行万维网（WWW）访问用到了HTTP协议、文件传输用FTP协议、电子邮件发送用SMTP、域名的解析用DNS协议、远程登录用Telnet协议等等，都是属于TCP/IP应用层的；就用户而言，看到的是由一个个软件所构筑的大多为图形化的操作界面，而实际后台运行的便是上述协议。（FTP、SMTP、telnet、DNS、tftp）

* 传输层--------这一层的的功能主要是提供应用程序间的通信，TCP/IP协议族在这一层的协议有TCP和UDP。（UDP）

* 网络层---------是TCP/IP协议族中非常关键的一层，主要定义了IP地址格式，从而能够使得不同应用类型的数据在Internet上通畅地传输，IP协议就是一个网络层协议。（IP数据包）

* 网络接口层-------这是TCP/IP软件的最低层，负责接收IP数据包并通过网络发送之，或者从网络上接收物理帧，抽出IP数据报，交给IP层。(帧，网络接口协议)
5. 手机的消息推送属于TCP还是UDP
* UDP
6. 网页相应特别慢有什么原因，如何解决
    * 服务器内存占用情况
    * 数据库+后端程序设计
    * 服务器带宽，链路情况，是否跨运营商
7. HTTP和HTTPS区别
    * https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
    * http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
    * http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
    * http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。
    
8. 对称加密和非对称加密
9. 数据库事务隔离级别
    * 读未提交
    * 读已提交
    * 可重复读
    * 串行化
10. 死锁产生条件，如何解决  
* 产生条件：
    * 互斥：某种资源一次只允许一个进程访问，即该资源一旦分配给某个进程，其他进程就不能再访问，直到该进程访问结束。  
    * 占有且等待：一个进程本身占有资源（一种或多种），同时还有资源未得到满足，正在等待其他进程释放该资源。
    * 不可抢占：别人已经占有了某项资源，你不能因为自己也需要该资源，就去把别人的资源抢过来。
    * 循环等待：存在一个进程链，使得每个进程都占有下一个进程所需的至少一种资源。
* 避免： 
    * 银行家算法 ：在银行中，客户申请贷款的数量是有限的，每个客户在第一次申请贷款时要声明完成该项目所需的最大资金量，在满足所有贷款要求时，客户应及时归还。银行家在客户申请的贷款数量不超过自己拥有的最大值时，都应尽量满足客户的需要。
11. 进程线程区别
12. 快速排序，时间空间复杂度，如何避免快排最坏情况的发生
13. 进程的最大线程数是多少
    * 一个进程虚拟内存4G,内核占1G,一个线程占8-10M，线程数=3*1024/8=300多个
14. Http get post区别
15. HTTP其他方法
16. 代码题给一个翻转数组类似这种：{14,16,19,35,    1,2,3,6,8,9}，用logn的时间复杂度查找，提示二分查找思想。




* 对面向对象的理解
* 死锁
* 线程和进程的区别
* HTTP和HTTPS的区别
* TCP三次握手四次挥手
* 互斥锁
    * Synchronize，ReentrantLock
* 内存对齐
    * 一个空对象占用8字节( 空对象：占8字节 64位bit )
    * 一个类的大小应该是8的整数倍
    * 
    int------Interger---4  
    short----Short------2  
    double---Double-----8  
    char-----Char-------2  
    byte-----Byte-------1  
    long-----Long-------8  
    float----Float------4  
    boolean--Boolean----1  
* static变量的存储区域
* 方法区
* 死锁是进程还是线程、竞争的资源是CPU分配的吗
* 内存泄漏
    * ThreadLocal
    * 自己写一个堆，但是pop不将元素置null

* 算法：给有序数组和一个数字，若数组中有两个数字相加等于输入的数字，输出这两个数的索引  O(n)
