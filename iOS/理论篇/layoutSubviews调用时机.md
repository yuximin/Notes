# layoutSubviews调用时机

## 视图布局发生改变时

当视图布局发生改变时，会调用 superView 的 layoutSubviews，然后从上而下调用视图的 layoutSubviews 方法。

**注意，这里只有当视图的尺寸发生变化时，才会调用其 layoutSubviews。**

个人猜测原因很简单，layoutSubviews方法是用来处理子视图的frame的，当视图本身只发生位置变化而未发生尺寸变化时，不会影响其内部的子视图布局变化，所以不用调用其layoutSubviews方法。

当同级多个视图的布局发生改变时，只会调用一次 superView 的 layoutSubviews 方法。

使用自动布局时，因自身尺寸变化，造成兄弟视图的位置发生变化时，不会调用兄弟视图的 layoutSubviews.