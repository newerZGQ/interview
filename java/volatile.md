保证可见性和有序性，不保证原子性
可见性：lock指令，写入时直接将值刷进主内存，同时使其他工作线程里的值失效

有序性：内存屏障，写之前插入StoreStore，写之后StoreLoad,读之后插入loadload，loadstore