> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.yuque.com](https://www.yuque.com/calibe/gyotug/2bdb4b50-4f92-4a20-901b-29b5577db9c2)

08_性能优化：基于 Guarded Suspension 模式，优化百万交易系统
=========================================

返回文档08_性能优化：基于 Guarded Suspension 模式，优化百万交易系统  
儒猿架构官网上线，内有石杉老师架构课最新大纲，儒猿云平台详细介绍，敬请浏览  
  
官网：[www.ruyuan2020.com](http://www.ruyuan2020.com/)（建议 PC 端访问）  
一、 Guarded Suspension 模式简介  
  
guarded 在这里是 “保护” 的意思；suspension 在这里是 “暂时挂起” 的意思。所以，Guarded Suspension 模式又称为 “保护性暂挂模式”；  
  
在多线程开发中，常常为了提高应用程序的并发性，会将一个任务分解为多个子任务交给多个线程并行执行，而多个线程之间相互协作时，仍然会存在一个线程需要等待另外的线程完成后继续下一步操作。而 Guarded Suspension 模式可以帮助我们解决上述的等待问题。  
  
还是用交易系统的 “转账” 场景来讲述这个模式的实现。在上一篇文章中，我们提到，【账户 A】转账给【账户 B】，线程 01 需要持有账户 A 的锁，同时也需要持有账户 B 的锁，如果线程 01 拿不到两个锁，则进行 while(!actr.apply(this, target)) 死循环的方式来循环等待，直到一次全部获取到两个锁后，才进行后面的转账操作。  
  
在并发量不大的情况下，这种方案还是可以接受的，但是一旦并发量增大，获取锁的冲突增加的时候，这种方案就不适合了，因为在这种场景下，可能要循环上万次才能获得锁，非常消耗性能，互联网高并发下显然不适合。  
  
在这种场景下，最好的方案就是使用 Guarded Suspension 模式，如果线程 01 拿不到所有的锁，就阻塞自己，进入 “等待 WAITING” 状态。当线程 01 要求的所有条件都满足后，“通知” 等待状态的线程 01 重新执行。  
  
二、看牙医的就诊流程  
  
在讲述 “等待 - 通知” 机制之前，我以周末看牙医的就诊流程举例，因为它有完善的 “等待 - 通知” 机制。通过这个流程，我们可以更好的理解 Guarded Suspension 模式的 “等待 - 通知” 机制。  
  
牙医的就诊流程大致是这样的：  
  
如下图所示：  
  
![](https://www.yuque.com/api/filetransfer/images?url=http%3A%2F%2Fwechatapppro-1252524126.file.myqcloud.com%2Fimage%2Fueditor%2F29817400_1622351187.png&sign=43e4cce9880fd01180cdd0ae9e529e27f7c3c07befa405f1389a92d4b98d5357)  
  
1 患者到了医院后，先去挂牙科号，然后到牙科门诊分诊，等待叫号；2 当叫到自己的号时，患者就可以找医生就诊了；3 当医生检查完成后，会开治疗费缴费单，患者去交费，同时叫下一位患者；4 当患者交完费后，拿缴费单重新分诊，等待叫号；5 当护士再次叫到自己的号时，患者再去找大夫进行治疗。（等待 - 通知）  
大家看完我这个看牙医的案例，应该了解到 “等待 - 通知” 机制的就医流程，不仅能够保证同一时刻，一个牙医大夫只为一个患者服务，而且还能够保证大夫和患者的就医效率。  
  
患者挂完号到就诊室门口等待分诊，类似于线程要去获取互斥锁；当患者被叫到号时，类似线程已经获取到锁了。  
  
大夫检查完让患者去缴费（牙科不缴费不治疗），类似于线程要求的条件没有满足。患者去缴费时，类似于线程进入等待状态；  
  
然后大夫叫下一个患者，类似线程释放持有的互斥锁。  
  
患者交完费，类似于线程要求的条件已经满足；患者拿缴费单重新等待分诊，类似于线程需要重新获取互斥锁。  
  
综合以上流程，就可以得出一个完整的等待 - 通知机制：线程首先获取互斥锁，当线程要求的条件不满足时，释放互斥锁，进入等待状态；当要求的条件满足时，通知等待的线程，重新获取互斥锁。  
  
三、代码举例  
  
下面我们写一段代码来描述这段 “等待 - 通知” 机制：  
  
类图  
  
![](https://www.yuque.com/api/filetransfer/images?url=http%3A%2F%2Fwechatapppro-1252524126.file.myqcloud.com%2Fimage%2Fueditor%2F52160000_1622351187.png&sign=72062626c43400d057a5630139c1d8c7d8810f0bdd737c8a28d4c62d82af2270)  
  
1、 创建 GuardedQueue 类  
  
public class GuardedQueue {  
 private final Queue sourceList;  
  
public GuardedQueue() {  
 this.sourceList = new LinkedBlockingQueue<>(); }  
  
public synchronized Integer get() {  
 while (sourceList.isEmpty()) { try { wait();    // <--- 如果队列为 null，等待  } catch (InterruptedException e) { e.printStackTrace(); } } return sourceList.peek(); }  
  
public synchronized void put(Integer e) {  
 sourceList.add(e); notifyAll();  //<--- 通知，继续执行}}  
  
2、测试一下  
  
public class App {  
 public static void main(String[] args) { GuardedQueue guardedQueue = new GuardedQueue(); ExecutorService executorService = Executors.newFixedThreadPool(3); executorService.execute(() -> { guardedQueue.get(); } ); Thread.sleep(2000); executorService.execute(() -> { guardedQueue.put(20); } ); executorService.shutdown(); executorService.awaitTermination(30, TimeUnit.SECONDS); }}  
  
四、总结与拓展  
  
Guarded Suspension 模式的 “等待 - 通知” 机制是一种非常普遍的线程间协作的方式。我们在平时工作中经常看到有同学使用 “轮询 while(true)” 的方式来等待某个状态，其实都可以用这个 “等待 - 通知” 机制来优化。  
  
另外，有同学可能会问为什么不用 notify() 来实现通知机制呢？  
  
Notify() 和 notifyAll() 这两者是有区别的，notify() 是会随机地通知等待队列中的任意一个线程，而 notifyAll() 会通知等待队列中的所有线程。  
  
觉得 notify() 会更好一些的同学可能认为即便通知所有线程，也只有一个线程能够进入临界区。但是实际上使用 notify() 也很有风险，因为随机通知等待的线程，可能会导致某些线程永远不会被通知到。  
  
所以除非经过深思熟虑，否则尽量使用 notifyAll()。  
儒猿技术窝精品专栏及课程推荐：  
  
●[《从零开始带你成为消息中间件实战高手》](https://apppukyptrl1086.h5.xiaoeknow.com/v1/course/column/p_5d887e7ea3adc_KDm4nxCm?type=3)  
●[《互联网 Java 工程师面试突击》（第 2 季）](https://apppukyptrl1086.h5.xiaoeknow.com/v1/course/column/p_5d3110c3c0e9d_FnmTTtj4?type=3)  
●[《互联网 Java 工程师面试突击》（第 1 季）](https://apppukyptrl1086.h5.xiaoeknow.com/v1/course/column/p_5d3114935b4d7_CEcL8yMS?type=3)  
●[《互联网 Java 工程师面试突击》（第 3 季）](https://apppukyptrl1086.pc.xiaoe-tech.com/detail/p_5dd3ccd673073_9LnpmMju/6?fromH5=true)  
●[《从零开始带你成为 JVM 实战高手》](https://apppukyptrl1086.pc.xiaoe-tech.com/detail/p_5d0ef9900e896_MyDfcJi8/6)  
●[《C2C 电商系统微服务架构 120 天实战训练营》](https://apppukyptrl1086.h5.xiaoeknow.com/v1/course/column/p_5f1e9ddbe4b0a1003cafad34?type=3)  
●[《基于 RocketMQ 的互联网酒店预订系统项目实战》](https://apppukyptrl1086.h5.xiaoeknow.com/v1/course/column/p_5fd03fb3e4b04db7c093b40c?type=3)  
●[《从零开始带你成为 MySQL 实战优化高手》](https://apppukyptrl1086.pc.xiaoe-tech.com/detail/p_5e0c2a35dbbc9_MNDGDYba/6)  
●[《Spring 顶尖高手进阶：102 讲带你实战互联网教育系统项目》](https://apppukyptrl1086.pc.xiaoe-tech.com/detail/p_607d8356e4b0d4eb0392eeba/6)  
[https://apppukyptrl1086.pc.xiaoe-tech.com/detail/i_60b31dfde4b0c726421a65e0/1?from=p_60ab6413e4b07e4d7fd8458a&type=6](https://apppukyptrl1086.pc.xiaoe-tech.com/detail/i_60b31dfde4b0c726421a65e0/1?from=p_60ab6413e4b07e4d7fd8458a&type=6)  

若有收获，就点个赞吧