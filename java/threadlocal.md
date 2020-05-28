ThreadLocal有一个内部类ThreadLocalMap，这是自定义的hashmap，里面维护一个数组，根据hashcode确定位置。    
Thread中维护一个ThreadLocalMap  
ThreadLocal set时，会以先获取当前线程的ThreadLocalMap，然后以当前ThreadLocal的实例为key，要存储的值为value，存入map中  
ThreadLocal get时，会以先获取当前线程的ThreadLocalMap，然后以当前ThreadLocal的实例为key，去获取value    
需要注意的是，ThreadLocalMap并不是Java中的HashMap而是另一种实现方式，以Entry[]来实现，Entry集成了WeakRefrence，WeakRefrence有一个renfrence成员变量，刚好作为key值使用，Entry中又声明一个value变量用于存储值，用开放地址法处理hash冲突