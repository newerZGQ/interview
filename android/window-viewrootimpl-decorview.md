- window
  - window是view的承载，最直观的表现就是我们在activity中setcontentView时，其实是调用了window的setcontentview。window维护一个跟view
  - window的创建，在ActivityThread中使用ApplicationThread和ams交互后，进入主Activity或者启动一个新的Activity时，ams会通过binder调用ApplicationThread的一些binder方法，然后通过主线程的消息机制回调到performLaunchActivity方法中，这里会初始化Activity的实例，然后直接调用在activity的attach方法。
  - 这个attach方法中，创建了PhoneWindow的实例，然后这个Activity所有的view都会交由window管理，window也会负责将点击事件或者key事件向下分发到Activity，Activity再向window上的decorview上分发。

- decorview与viewrootimpl
  - View类里面有个变量mParent，mParent的类型ViewParent是个接口，ViewParent的实现类有两个，ViewRootimpl和ViewGroup，对于普通的View，其mParent就是父View，也就是ViewGroup类型，对于DecorView，其parent则是viewrootimpl。
  - 我们在修改view的属性后，一般都会调用requestLayout或者invalidate，这两个方式都会根据parent向上按层寻找parent，直到找到viewrootimpl，然后由viewrootimpl调用scheduleTraversals，开始一次viewtree的遍历与绘制过程
  - 所以viewrootimpl的作用是作为View和WindowManager之间的桥梁，比如在resume时，windowmanager会将decorview添加到window上，windowmanager的addview操作会最终回到viewrootimpl上，然后由其调用scheduleTraversals，开始一次绘制
  - 普通View的mParent为它的父View