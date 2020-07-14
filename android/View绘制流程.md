- view 的绘制过程

  - 三大过程都是在 chreographer 回调到 viewrootimpl 之后进行的。有 viewrootimpl 统筹，从根 view decorview 遍历。在 viewrootimpl 执行 tranversal 时，会去拿 window 的大小，对于一个 activity 来说，window 的大小就是屏幕的大小，然后设置的 layoutparams 的模式是 matchparent 或者未指定时，measurespac 的 mode 都是 exactly，当 layoutparams 模式是 wrap_content 时，measurespac 的 mode 都是 at_most，就是硕都会填满 window
  - onMeasure：onMeasure
    - 父 view 与子 lp 比较确定 spec 的过程，
      - 父是 exactly 时
        - 子是 wrapcontent，则是 atmost
        - 子是具体值，则是 exactly
        - 子是 matchparent，是 exactly
      - 父是 atmost 时
        - 子 wrapcontent，则是 atmost
        - 子是具体值，则是 exactly
        - 子是 matchparent，则是 atmost
      - 父是 unspecified
        - 子是 wrapcontent，则是 unspecified
        - 子是具体值，则是 exactly
        - 子是 matchparent，则是 unspecified
    - 对于简单 view，因为没有子 view，onMeasure 时不需要与子 view 的 lp 比较来确定子 view 的 measurespec，只需要注意自身 wrap_content 时的默认值即可，这是因为，在 onMeasure 的默认实现中，根据父 view 传递过来的 spec 计算子 view 宽高时，对于 spec 的 atmost 和 EXACTLY 两种情况，返回的都是 spec 确定的值（另，注意 UNSPECIFIED 返回的是计算的最小值），这就导致 wrapcontent 和 matchparnt 结果一致，也就是 wrapcontent 未生效，所以要针对 wrapcontent 单独设置默认值，比如 textview 会根据字数确定。
    - 对于 viewgroup，则会在每次 onMeasure 时拿自身 spec 与每个子 view 的 lp 比较，确定子 view 的 spec（参照上面），而每个子 view 则会在自己的 onMeasure 中根据 spec 以及自己的 xml 中的属性来计算，要么计算出真正自己的宽高，要么跟随上面的规则继续下去。
  - onLayout
    - onMeasure 之后确定了测量的宽高，然后在 layout 方法中，view 会确定自己在父 view 中的位置，然后再在 onlayout 中确定自己的子 view 的位置，也就是会在确定子 view 的 ltrb 四个参数之后，调用子 view 的 layout 函数，以此规律向下走。通常情况下，各类的 viewgroup 会根据自己要实现的 layout 逻辑，在 onlayout 中改变子 view 的 ltrb，同时保证 r-l=measuredWidth,b-t=measuredHeight，这样在绘制结束后 getMeasuredWidth 就会等于 getWidth，但是，如果我们在 onlayout 时，不保证 r-l=measuredWidth,b-t=measuredHeight，那就会改变 view 的大小，getMeasuredWidth 就不会等于 getWidth。所以说宽高是在 onlayout 时确定的。
  - onDraw
    - onLayout 中确定最终的宽高后，在 onDraw 中会根据 ltrb 进行 canvas 裁剪，这样保证在 draw 过程中，每个 view 的坐标都是自己的左上角是 0，0
    - draw 的过程是 drawbackground，drawcontent，dispatchDraw，drawforeground（就是滚动 bar）

- ViewStub

  - viewstub 懒加载
  - 原理，viewstub 是一个 view，重写了 onMeasure 和 onDraw,onMeasure 中长宽均为 0，起到不占空间的效果，onDraw 中没有任何实现，不绘制就不占用资源，然后，在 inflate 的时候，会加载 layout 的资源，获取 viewstub 的 parent，然后移除 viewstub，并将加载的 layout 的 view add 到 parent 上，在这个过程中，会忽略 layout 根节点的 layoutparams，并使用 viewstub 的 layoutparams 代替

- view 会因为平级 view 变化而重绘么
