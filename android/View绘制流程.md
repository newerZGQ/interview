- view的绘制过程
  - 三大过程都是在chreographer回调到viewrootimpl之后进行的。有viewrootimpl统筹，从根view decorview遍历
  - onMeasure：onMeasure

- ViewStub
  - viewstub 懒加载
  - 原理，viewstub 是一个 view，重写了 onMeasure 和 onDraw,onMeasure 中长宽均为 0，起到不占空间的效果，onDraw 中没有任何实现，不绘制就不占用资源，然后，在 inflate 的时候，会加载 layout 的资源，获取 viewstub 的 parent，然后移除 viewstub，并将加载的 layout 的 view add 到 parent 上，在这个过程中，会忽略 layout 根节点的 layoutparams，并使用 viewstub 的 layoutparams 代替