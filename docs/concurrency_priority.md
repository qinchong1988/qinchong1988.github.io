定制并发类（三）实现一个基于优先级的Executor类
--------
声明：本文是《 Java 7 Concurrency Cookbook 》的第七章，作者： Javier Fernández González     译者：许巧辉

#### 实现一个基于优先级的Executor类

在Java并发API的第一个版本中，你必须创建和运行应用程序中的所有线程。在Java版本5中，随着执行者框架（Executor framework）的出现，对于并发任务的执行，一个新的机制被引进。

使用执行者框架（Executor framework），你只要实现你的任务并把它们提交给执行者。这个执行者负责执行你的任务的线程的创建和执行。

在内部，一个执行者使用一个阻塞队列来存储待处理任务。以任务到达执行者的顺序来存储。一个可能的替代就是使用一个优先级列队来存储新的任务。这样，如果一个高优先级的新任务到达执行者，它将比其他已经在等待一个线程来执行它们，且低优先级的任务先执行。

在这个指南中，你将学习如何实现一个执行者，它将使用优先级队列来存储你提交执行的任务。

#### 准备工作

这个指南的例子使用Eclipse IDE实现。如果你使用Eclipse或其他IDE，如NetBeans，打开它并创建一个新的Java项目。


#### 如何做…

按以下步骤来实现的这个例子:

1.创建一个MyPriorityTask类，它实现Runnable接口和参数化为MyPriorityTask类的Comparable接口。


```
public class MyPriorityTask implements Runnable,

Comparable<MyPriorityTask> {

```

2.声明一个私有的、int类型的属性priority。

```
private int priority;
声明一个私有的String类型的属性name。
private String name;
```
4.实现这个类的构造器，并初始化它的属性。

```
public MyPriorityTask(String name, int priority) {
    this.name=name;
    this.priority=priority;
}
```

5.实现一个方法来返回priority属性的值。

```
public int getPriority(){
    return priority;
}
```

6.实现声明在Comparable接口中的compareTo()方法。它接收一个MyPriorityTask对象作为参数，比较这两个对象（当前对象和参数对象）的优先级。让优先级高的任务先于优先级低的任务执行。

```
@Override
public int compareTo(MyPriorityTask o) {
if (this.getPriority() < o.getPriority()) {
    return 1;
}
if (this.getPriority() > o.getPriority()) {
    return -1;
}
    return 0;
}
```

7.实现run()方法。令当前线程睡眠2秒。

```
@Override
public void run() {
    System.out.printf("MyPriorityTask: %s Priority :%d\n",name,priority);
    try {
        TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
        e.printStackTrace();
        }
}
```

8.实现这个例子的主类，通过创建Main类，并实现main()方法。

```
public class Main {
public static void main(String[] args) {
```

9.创建一个ThreadPoolExecutor对象，名为executor。使用参数化为Runnable接口的PriorityBlockingQueue作为执行者用来存储待处理任务的队列。

```
ThreadPoolExecutor executor=new ThreadPoolExecutor(2,2,1,TimeUnit.SECONDS,new PriorityBlockingQueue<Runnable>());
```

10.提交4个使用循环计数器作为优先级的任务给执行者。使用execute()方法提交这些任务给执行者。

```
for (int i=0; i<4; i++){
MyPriorityTask task=new MyPriorityTask ("Task "+i,i);
executor.execute(task);
}
```

11.令当前线程睡眠1秒。

```
try {
    TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
    e.printStackTrace();
}
```

12.提交4个额外的，使用循环计数器作为优先级的任务给执行者。使用execute()方法提交这些任务给执行者。

```
for (int i=4; i<8; i++) {
    MyPriorityTask task=new MyPriorityTask ("Task "+i,i);
    executor.execute(task);
}
```

13.使用shutdown()方法关闭这个执行者。

```
    executor.shutdown();
```

14.使用awaitTermination()方法等待这个执行者的结束。

```
try {
    executor.awaitTermination(1, TimeUnit.DAYS);
    } catch (InterruptedException e) {
    e.printStackTrace();
}
```

15.写入一条信息表明这个程序的结束。

```
System.out.printf("Main: End of the program.\n");
```

#### 它是如何工作的…

很容易将执行者转换成一个基于优先级的（执行者）。你只要传入一个参数化为Runnable接口的PriorityBlockingQueue对象作为参数。但是，使用执行者时，你应该知道存储在优先级列队中的所有对象必须实现Comparable接口。

你已经实现了MyPriorityTask类，（作为一个任务）它实现了Runnable接口和Comparable接口，它被存储在优先级队列中。这个类有一个Priority属性，用来存储任务的优先级。如果一个任务的这个属性有更高的值，它将被更早的执行。compareTo()方法决定任务在优先级列队中的顺序。在Main类，你提交8个不同优先级的任务给执行者。你提交给执行者的第一个任务将第一个被执行。由于执行者闲置的，正在等待任务被执行，当第一个任务到达执行者时，执行者立即执行它们。你已经创建有2个执行线程的执行者，所以，前两个任务将第一个被执行。然后，剩下的任务将按它们的优先级来执行。

#### 不止这些…

你可以使用任何实现BlockingQueue接口（的队列）来配置执行者。DelayQueue是一个有趣的实现。这个类被用来存储延迟激活（delayed activation）的元素。它提供只返回活动对象的方法。你可以使用这个类来实现自己版本的ScheduledThreadPoolExecutor类。

#### 参见

[在第4章，线程执行者中的创建一个线程执行者指南](http://ifeve.com/thread-executors-2/)

在第7章，定制并发类中的定制ThreadPoolExecutor类指南

[在第6章，并发集合中的使用优先级排序的阻塞线程安全数列指南](http://ifeve.com/concurrent-collections-4/)

（全文完）如果您喜欢此文请点赞，分享，评论。

原创文章转载请注明出处：[定制并发类（三）实现一个基于优先级的Executor类](http://ifeve.com/customizing-concurrency-classes-3/)
