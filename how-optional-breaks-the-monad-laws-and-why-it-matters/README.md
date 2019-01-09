原文链接： [How Optional Breaks the Monad Laws and Why It Matters](https://www.sitepoint.com/how-optional-breaks-the-monad-laws-and-why-it-matters/) - _Marcello La Rocca_，2016-09  


# `Optional`是如何打破了`Monad`定律以及为什么这是重要的

`Java 8`引入了`Lambda`和`Stream`，这2个都是期待已久的功能。另一个引入的是`Optional`，在`Stream`管线（`pipeline`）的结尾可能没有元素返回时，用来避免`NullPointerException`。在其它语言中，『可能有值或无值』这样的类型比如`Optional`都是表现良好的`Monad`，但在`Java`的`Optional`却并不是。这点对于我们的日常开发是重要的。


