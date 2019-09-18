http://www.cnblogs.com/jackson0714/p/5100372.html

# 多线程

### 内存隔离、数据共享、线程安全

* CLR给每个线程分配自己的内存栈，因此各线程的局部变量互不相干

* 如果多线程拥有同一个对象的引用，则会共享这个对象，容易引起冲突

* 使用`lock`来解决冲突，保证线程安全

  * 如下，只要两线程lock任意一个相同对象就可以了，两线程将会互斥，一个执行时，另一个想要执行的话，必须等待或阻塞，直到该锁被释放

  ```c#
  static bool done = false;
  static readonly object locker = new object();

  static void Main(string[] args){
    new Thread(Go).Start();
    Go();
    Console.ReadKey();
  }

  static void Go(){
    lock (locker){
      if (!done){
        Console.WriteLine("Done");
        done = true;
      }
    }
  }
  ```



### Join、Sleep

* **thread.Join()**：使调用线程阻塞，直到该线程执行完毕

  * 重载参数**int(毫秒)或TimeSpan**，只等待指定的时间，时间到之前完成执行则返回**true**，否则返回**false**

  ```C#
  Thread thread = new Thread(MyThread);
  thread.Start();
  //在thread执行完毕之后，才会继续往下执行
  thread.Join();
  Console.WriteLine("Main End");
  ```

* **Thread.Sleep(int(毫秒)/TimeSpan)** ：阻塞当前线程指定的时间



### 线程怎样工作

1. 多线程由一个线程调度器来进行内部管理，CLR常常委托给操作系统。线程调度器确保所有激活的线程在执行期间被合适的分配，等待或者阻塞的线程（比如，一个独占锁或者等待用户输入）不占用CPU资源。
2. 在单核电脑上，一个线程调度器让时间片在每一个激活的线程中切换。在windows操作系统下，线程切换的时间分片通常为10微秒，远远大于CPU的开销时间（通常小于1微秒）。
3. 在一个多核的电脑上，多线程实现了一个混合的时间片和真正的并发，不同的线程同时在不同的CPU上执行代码。还是存在某些时间片，因为操作系统需要服务它自己的线程，包括其他的应用的线程。
4. 当一个线程的执行被内部因素打断，比如时间片，则说这个线程是抢占式的。在大部分情形下，一个线程不能控制自己何时何地被抢占。、

* 线程在调度和切换线程时会造成资源和CPU的消耗（当激活的线程数量多于CPU的核的数量时）-而且有创建/销毁损耗。



### 传递参数

1. **通过Lambda或匿名方法**：将要执行的方法包在lambda表达式里，利用lambda表达式能直接使用当前的局部变量的特点来传递参数

   ```c#
   string str = "A";
   Thread t = new Thread(() => MyThread(str));

   void MyThread(string str){}
   ```

   ​

2. **通过ParameterizedThreadStart**：

   * **Thread**的构造方法可以接受两种委托，一种是**ThreadStart**，无参无返回；另一种是**ParameterizedThreadStart**，有一个object类型的参数，无返回

   ```c#
   Thread t = new Thread(MyThread);
   t.Start("A");
   //参数类型必须是object
   void MyThread(object o){}
   ```



### 命名线程

每一个线程都有一个**Name**属性，你可以方便用来debugging.当线程显示在Visual Studio里面的Threads Window和Debug Location Toolbar的时候，线程的**Name**属性是特别有用的。**你只可以设置线程的名字一次，之后尝试改变它将会抛出异常信息**。

静态的Thread.CurrentThread属性代表当前执行的线程。



### 前台和后台线程

我们创建的线程默认为**前台线程**，**前台线程**保持这个应用程序一直存活只要其中任意一个正在运行，而**后台线程**在所有**前台线程**完成后立刻终止

* 使用线程的**IsBackgroud**属性**查询或改变**一个线程的后台状态
* 当**后台线程**被强行终止，它里面的**finally**语句块将不会被执行。如果你的线程使用**finally(or using)**语句块去执行如释放资源或者删除临时文件的清理工作，这将是一个问题。为了避免这个，你可以显示地等待后台线程退出应用程序。如：
  1. 如果你自己创建了这个线程，可以在这个线程上调用Join方法。
  2. 如果你使用线程池，可以使用一个事件去等待处理这个线程。



### 线程优先级

优先级通过线程的**Priority**属性进行设置，等级如下:

```C#
[Serializable]
[ComVisible(true)]
public enum ThreadPriority{
    Lowest = 0, BelowNormal = 1, Normal = 2, AboveNormal = 3, Highest = 4,
}
```

* 当提升一个线程的优先级时，不会使它执行实时工作，因为它被应用程序的进程优先级限制了。为了执行实时工作，你也必须通过使用`System.Diagnostices`的`Process`类来提升进程的优先级：

  ```C#
  using (Process p = Process.GetCurrentProcess()){
      p.PriorityClass = ProcessPriorityClass.High;
  }
  ```

  * `ProcessPriorityClass.High`事实上是优先级最高的一档：实时。设置一个进程优先级到实时状态将会导致其他线程无法获得CPU时间片。如果你的应用程序意外地进入一个无限循环的状态，你甚至会发现操作被锁住了，只有电源键能够拯救你了。<u>针对这个原因，High通常对于实时应用程序是最好的选择</u>(?)
  * 如果你的实时应用程序有一个用户界面，提高程序的优先级将会使刷新界面占用昂贵的CPU的时间，且会使整个系统变得运行缓慢（尤其是UI很复杂的时候）。降低主线程优先级且提升进程的优先级来确保实时线程不会被界面重绘所抢占，但是不会解决其他进程对CPU访问缺乏的问题，因为操作系统整体上会一直分配不成比例的资源给进程。一个理想的解决方案是让实时线程和用户界面用不同的优先级运行在不同的进程中，通过远程和内存映射文件来通信。即使提高了进程优先级，在托管环境中处理硬实时系统需求还是对适用性有限制。此外，潜藏的问题会被自动垃圾回收引进，操作系统会遇到新的挑战，即使是非托管代码，使用专用硬件或者特殊的实时平台，那将被最好的解决。



### 异常处理

在任何**try/catch/finally** 语句块作用域内创建的线程，当这个线程开始时，这个线程和语句块是没有关联的。应该把**try/catch/finally**放在线程所执行的方法内



## 线程池

无论你什么时候开始一个线程，几百毫秒会花在整理一个新的local variable stack。每一个线程默认会消耗**1MB**的内存。**线程池**通过**分享和回收线程**来削减这些开销，允许多线程被应用在一个非常颗粒级的级别而没有性能损失。当充分利用多核系统去执行密集型计算的并行代码时这是非常有用的。

* **当使用线程池时需要注意下面的事情：**
  * 你**不能设置一个线程的名字**，因为设置线程的名字将会使调试更困难
  * 线程池中的线程总是**后台线程**。
  * 在应用程序的开始期间，阻塞一个线程可能会触发一个延迟，除非你调用ThreadPool.SetMinThreads
  * 你**不能任意地改变池中的线程的优先级**-因为当它释放回池中的时候，优先级会被还原为正常状态。
  * 你可以通过属性`Thread.CurrentThread.IsThreadPoolThread`查询线程是否是正在运行的一个池中的线程



### 通过TPL进入线程池

你可以使用在**Task Parallel Library**中的**Task**类来轻松的进入线程池。

无返回值**Task**：

```C#
//普通的
Task task = new Task(MyTask);
task.Start();

//也可以用Task.Factory来快速得到并启动一个Task
Task task = Task.Factory.StartNew(MyTask);
task.Wait();	//相当于thread.Join()
```

* 当你调用task的**Wait**方法时，若有未处理的异常，会重新抛出到宿主线程上。若不调用，则一个未处理的异常将会关闭掉这个进程(?线程)