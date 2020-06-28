# 开/关

### 问题

拥有PID控制器就好了，有时您根本不在乎它在说什么。

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BadForcedOutput.png "BadForcedOutput")](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BadForcedOutput.png)  
假设您要在程序中的某个时刻将输出强制为某个值（例如0），当然可以在调用例程中执行此操作：

```c
void loop()
{
    Compute();
    Output=0;
}
```

这样，无论PID怎么说，都可以覆盖它的值。但是，这在实践中是一个可怕的想法。PID将变得非常混乱：“我一直在移动输出，什么也没发生！是什么赋予了？！让我再移动一点。” 结果，当您停止覆盖输出并切换回PID时，输出值可能会立即发生巨大变化。

### 解决方案

解决此问题的方法是有一种方法可以打开和关闭PID。这些状态的常用术语是“手动”（我将手动调节值）和“自动”（PID将自动调节输出）。让我们看看如何在代码中完成此操作：

### 代码


一个相当简单的解决方案。如果您不在自动模式下，请立即退出Compute函数，而无需调整输出或任何内部变量。

### 结果

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BetterForcedOutput.png "更好的强制输出")](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BetterForcedOutput.png)  
    的确，您可以通过不从调用例程中调用Compute来达到类似的效果，但是此解决方案可以保留PID的工作原理，这正是我们所需要的。通过将事物保持在内部，我们可以跟踪所处的模式，更重要的是，它使我们知道何时更改模式。这就引出了下一个问题……  
[下一页>>](improving-the-beginner’s-pid-initialization)  

    [![知识共享许可](http://i.creativecommons.org/l/by-sa/3.0/80x15.png)](http://creativecommons.org/licenses/by-sa/3.0/)

    [![这个！](http://brettbeauregard.com/blog/wp-content/plugins/flattr/img/flattr-badge-large.png)](http://brettbeauregard.com/blog/?flattrss_redirect&id=770&md5=c4a77fae8176ec1859b591aaa0f6e32a "平板")

    标签：[Arduino](http://brettbeauregard.com/blog/tag/arduino/)，[初学者的PID](http://brettbeauregard.com/blog/tag/beginners-pid/)，[PID](http://brettbeauregard.com/blog/tag/pid/)

    <small>此项目被张贴上周五，2011年4月15日下午3:05，并提交下[编码](http://brettbeauregard.com/blog/category/pid/coding/)，[PID](http://brettbeauregard.com/blog/category/pid/)。您可以通过[RSS 2.0](http://brettbeauregard.com/blog/2011/04/improving-the-beginner%e2%80%99s-pid-onoff/feed/) feed 跟踪对此条目的任何响应。您可以在自己的网站上[留下回应](#respond)或[引用](http://brettbeauregard.com/blog/2011/04/improving-the-beginner%e2%80%99s-pid-onoff/trackback/)。</small>


