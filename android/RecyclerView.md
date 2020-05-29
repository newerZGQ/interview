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