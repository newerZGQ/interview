- ContentProvider
  - 用于进程间的数据共享，比如日历、通讯录之类的
  - onCreate运行在主线程
  - query、insert、update这些数据处理的运行在binder线程，要注意线程安全