* activity 生命周期
  - onSaveInstance
  - onRestore
* activity launckMode
* fragment 生命周期
  - 懒加载
  - 两种 adapter
* 广播
  - 分类
  - 权限
* service
  - 生命周期
  - onStartCommand 返回值
  - intentService
* AsyncTask
  - 维护了一个任务队列，比较有意思的是维护这个队列的类实现了 excutor 接口，但是又不是一个线程池，这个类保证了所有任务都是串行执行，如果想要并行执行，需要调用executeOnExecutor
  - excute 方法只是将 runnable 包装成了一个 task 放进任务队列，然后执行 next 操作
  - 真正执行异步任务的是一个正常的线程池，设置了核心线程数，最大线程数，线程回收时间等，核心线程数和最大线程数与 cpu 核心有关
* HandlerThread
  - 集成自Thread，并在run方法中开启loop，由handler不断sendmessage来执行异步任务
  - 退出异步执行的方法，quit：直接退出，不管还有没有没有执行的任务，quitsafety：执行完再退出
* LruCache
* HashMap && LinkedHashMap
* SparseArray
* ArrayMap
* RecyclerView
  - 优化帧率的方式
    - 数据处理与视图绑定分离，即在 bindview 的时候不要做数据处理
    - 数据优化，提前加载下一页
    - 局部刷新
    - 布局优化，减少层级
    - 减少 inflate xml，因为有 io 操作，可以使用代码生成 view，减少 new view 的操作
    - 共用 RecycledViewPool：在嵌套 RecyclerView 中，如果子 RecyclerView 具有相同的 adapter，那么可以设置 RecyclerView.setRecycledViewPool(pool)来共用一个 RecycledViewPool。
    - 加大 RecyclerView 的缓存，空间换时间
    - 减少 ItemView 监听器的创建，不要对每个 item 都单独新建一个监听器，可以公用一个，用 id 区分
    - 滑动中停止耗时操作
  - 无线循环
    - 把 getItemCount 的值设置到最大，bindviewHolder 的时候将 postion 对 data size 取余操作
    - 或者自定义 layoutmanager，在滑动的时候检测时候到最后，如果到最后，则 get 0 位置的 vh
  - payload
    - 局部刷新，重写包含 payload 参数的 onbindviewholder 方法，并在其中书写使用 payload 参数局部更新 viewholder 的代码，再 adapter 调用包含 payloads 的 notifyitemchanged 即可，
  - 插入动效怎么做的

- ListView
- ViewStub
  - viewstub 懒加载
  - 原理，viewstub 是一个 view，重写了 onMeasure 和 onDraw,onMeasure 中长宽均为 0，起到不占空间的效果，onDraw 中没有任何实现，不绘制就不占用资源，然后，在 inflate 的时候，会加载 layout 的资源，获取 viewstub 的 parent，然后移除 viewstub，并将加载的 layout 的 view add 到 parent 上，在这个过程中，会忽略 layout 根节点的 layoutparams，并使用 viewstub 的 layoutparams 代替
- Activity 启动过程
  - launcher
- 线程状态 新建 就绪 运行 阻塞 就绪 运行 死亡
- 线程池
  - 核心线程数 最大线程数 BlockingQueue 任务超量策略
  - 逻辑
    - 如果当前运行的线程少于 corePoolSize，则创建新线程来执行任务（注意，执行这一步骤需要获取全局锁）
    - 如果运行的线程等于或多于 corePoolSize，则将任务加入 BlockingQueue。
    - 如果无法将任务加入 BlockingQueue（队列已满），则在非 corePool 中创建新的线程来处理任务（注意，执行这一步骤需要获取全局锁）。
    - 如果创建新线程将使当前运行的线程超出 maximumPoolSize，任务将被拒绝，并调用 RejectedExecutionHandler.rejectedExecution()方法。
  - ctl AtomicInteger 表示工作线程数和线程池状态
  - Worker 内含线程，循环请求 Task 来执行
  - Task 是一个阻塞队列，任务为空时阻塞线程，比如 ArrayBlockingQueue，使用 Lock 阻塞
  - java 实现的很多种线程池，比如 newCachedThreadPool，不限数量，超时回收线程
- ## 工厂模式
- join
  - 首先 join() 是一个 synchronized 方法， 里面调用了 wait()，这个过程的目的是让持有这个同步锁的线程进入等待，那么谁持有了这个同步锁呢？答案是主线程，因为主线程调用了 threadA.join()方法，相当于在 threadA.join()代码这块写了一个同步代码块，谁去执行了这段代码呢，是主线程，所以主线程被 wait()了。然后在子线程 threadA 执行完毕之后，JVM 会调用 lock.notify_all(thread);唤醒持有 threadA 这个对象锁的线程，也就是主线程，会继续执行
- wait 超时后唤醒并重新请求锁


- 阿里的图片库是什么
- service
  - 运行在主线程
  - service中可以弹toast
  - service注册在哪里，servicemanager
    - 生命周期由谁控制
  - 生命周期
    - onCreate只在启动的时候回调
    - onStartCommond在每次调用startService时回调
    - onBind只在第一次绑定时回调
    - onUnBind只在所有client解绑时回调
    - onDestroy只在stop掉所有client同时unbind掉所有client时回调
  - 本地Service remote service区别
    - 运行进程不同
    - 有没有ipc
  - 前台service
    - 
  - acitvitymanagerservice和activitymanager
  - 一个进程几个binder线程
- 应用启动过程
- binder机制
  - binder机制是android中实现进程间通信的主要手段，区别于linux传统的ipc机制，传统的linux进程间通信走的是两次内存拷贝的方式，及在内核区开辟一块内存，当客户端与服务端交互时，会使用系统调用copy_from_user先将请求的数据从客户端拷贝到内核缓存区，然后服务端接受数据时，现在自己的用户态缓存区开辟一块内存区域，由系统调用copy_to_user将内核缓存区的数据拷贝过去，经历两次内存复制，这样的方式缺陷很明显，一是效率不行，因为两次拷贝，二是需要开辟的内存空间大小不知道，只能尽可能大的开辟。而Android的binder机制采用内存映射机制将两次拷贝将到了一次拷贝，在binder位于的内核中，开辟一块内核缓存A和数据接收缓存区B，当client请求向server传递数据时，会先将数据拷贝到A中，这也是唯一的一次copy，之后的过程，由于A到B，B到server的用户态内存区存在内存映射，所以A中的数据变化会直接反映到server的内存中.
  - 那么在这个过程中，binder起到什么样的作用呢？在binder机制中，涉及到几个模块，client，server，servicemanager，binder。从server说起，service有我们自己开发的service，只要在注册的时候设置成export true就行，应用的service在应用启动的时候注册，也有系统service，比如ActivityManagerService，在系统启动的时候注册，注册的时候，由server创建binder的实例，并将binder的名字以及实体打包通过binder驱动传给servicemanager，这个时候的binder数据已经跨进程了，然后servicemanager根据这些数据注册server，就是记录下，有哪个server对应了哪个binder，同时会在binder驱动的内核区创建这个binder的实体节点并在servicemanager中记录。
  - 那么注册server需要用到binder，使用server也用到binder，到底谁先谁后呢？其实，在系统启动时，就创建了一个binder，这个binder由servicemanager创建，用于注册server，这个binder的实体引用固定为0，任何应用都可直接使用。
  - 之后，client就可以通过0号binder，根据名称，由servicemanager在注册表中查询请求到某个server的binder，就与server建立的连接
  - 使用binder请求数据的过程：将客户端的请求参数通过Parcel包装后传到远程服务端，远程服务端解析数据并执行对应的操作，同时客户端线程挂起，当服务端方法执行完毕后，再将返回结果写入到另外一个Parcel中并将其通过Binder传回到客户端，客户端接收到返回数据的Parcel后，Binder会解析数据包中的内容并将原始结果返回给客户端，至此，整个Binder的工作过程就完成了
  - binder中的代理模式，从aidl的生成类中可以看到，有一个抽象类stub和一个proxy类，stub类继承了IBinder，这个类在创建连接时由service创建，要实现我们自定义的接口，并在server中操作数据。而proxy是给client用的，因为binder虽然实现了我们的接口，但是是由server实现的，就是说他的实现不是给我们client调用使用的。而proxy实现了我们定义的接口，同时使用了binder的trasact方法向server发起请求。所以我们在proxy的实现方法中，一般都是这个流程，首先向系统申请parcel内存空间，这个空间包括两部分，一个是要传给server的，一个是让server写入数据返回的，然后就是写入parcel数据，调用binder的trasact方法，通过binder驱动请求数据。这个代理模式在acitivitymanagerservice中也有体现，代理执行的是一个ActivitymanagerNative类
  - messenger也是用的binder机制，只不过比aidl更方便

- SharedPreference
  - 原理：数据是用xml格式存储在data cache文件夹中
  - 线程安全，在get和editor.apply等方法中均有synchronized修饰，首先在getsp的时候通过一个map缓存了所有已使用过的sp，保证拿的都是同一个sp对象，另外在每个get方法中，都会做一个同步操作，就是sp的实现类sharedpreferencesimpl里，有一个boolean类型的标志位用于指示当前是否在做load文件的异步操作，如果在load xml文件则阻塞等待load完成，这样拿到的都是最新的数据，另外这个类会监听xml文件的版本和大小变化，如果有变化也会更新缓存。至于写操作，editor负责写操作，editor的每次操作不会直接写入文件，而是先记录在缓存中，在经过commit或者apply时才会做writetofile操作。apply是异步操作。commit同步，在主线程中。writetofile也是加了锁的。

- ContentProvider
  - 用于进程间的数据共享，比如日历、通讯录之类的
  - onCreate运行在主线程
  - query、insert、update这些数据处理的运行在binder线程，要注意线程安全


- ApplicationThread和ActivityThread
  - 首先ActivityThread是应用进程启动的时候加载的类，就像一般的java应用一样，这个类里有一个main方法，主线程就从这个main方法启动
- arraymap

- decorview与viewrootimpl
  - View类里面有个变量mParent，mParent的类型ViewParent是个接口，ViewParent的实现类有两个，ViewRootimpl和ViewGroup，对于普通的View，其mParent就是父View，也就是ViewGroup类型，对于DecorView，其parent则是viewrootimpl。
  - 我们在修改view的属性后，一般都会调用requestLayout或者invalidate，这两个方式都会根据parent向上按层寻找parent，直到找到viewrootimpl，然后由viewrootimpl调用scheduleTraversals，开始一次viewtree的遍历与绘制过程
  - 所以viewrootimpl的作用是作为View和WindowManager之间的桥梁，比如在resume时，windowmanager会将decorview添加到window上，windowmanager的addview操作会最终回到viewrootimpl上，然后由其调用scheduleTraversals，开始一次绘制
  - 普通View的mParent为它的父View
- view的绘制过程
  - 三大过程都是在chreographer回调到viewrootimpl之后进行的。有viewrootimpl统筹，从根view decorview遍历
  - onMeasure：onMeasure
- window
  - window是view的承载，最直观的表现就是我们在activity中setcontentView时，其实是调用了window的setcontentview。window维护一个跟view
  - window的创建，在ActivityThread中使用ApplicationThread和ams交互后，进入主Activity或者启动一个新的Activity时，ams会通过binder调用ApplicationThread的一些binder方法，然后通过主线程的消息机制回调到performLaunchActivity方法中，这里会初始化Activity的实例，然后直接调用在activity的attach方法。
  - 这个attach方法中，创建了PhoneWindow的实例，然后这个Activity所有的view都会交由window管理，window也会负责将点击事件或者key事件向下分发到Activity，Activity再向window上的decorview上分发。

- 消息队列优先级
  - 消息同步屏障机制，当我们有一个高优的任务需要执行，此时我们可以向任务队列中插入一个同步屏障，用以阻碍队列中同步消息的执行。同步屏障的根本本质是当前Looper从任务队列中取出一个同步屏障之后，此时就会从任务队列中取出第一个异步消息，用来执行

- 判断是否在前台
  - ActivityLifecycleCallbacks，对activity计数
