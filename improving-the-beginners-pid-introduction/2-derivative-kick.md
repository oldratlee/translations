# 微分踢

### 问题

此修改将稍微调整派生项。目的是消除被称为“衍生踢”的现象。

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/DonE.png "微分踢")](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/DonE.png)

上图显示了该问题。由于error = Setpoint-Input，因此，Setpoint中的任何更改都会导致瞬时错误。这种变化的导数是无穷大（在实践中，由于dt不为0，所以它实际上是一个非常大的数字。）该数字被输入pid方程，这会导致输出出现不希望的峰值。幸运的是，有一种简单的方法可以摆脱这种情况。

### 解决方案

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/DonMExplain.png "DonM解释")](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/DonMExplain.png)  
结果表明，当设定值改变时，误差的导数等于输入的负导数，EXCEPT。最后这是一个完美的解决方案。代替加（Kd * Error的导数），我们减去（Kd * Input的导数）。这称为使用“测量导数”

### 代码


这里的修改非常简单。我们用-dInput替换+ dError。现在我们不用记住lastError了，而是记住了lastInput

### 结果

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/DonM.png "唐")](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/DonM.png)

这些修改为我们带来了这些。请注意，输入看起来仍然相同。因此，我们获得了相同的性能，但是每次设定值更改时，我们都不会发出巨大的输出峰值。

这可能不重要。这完全取决于您的应用程序对输出峰值的敏感程度。从我的角度来看，不费吹灰之力就不需要做更多的工作，那为什么不正确呢？  
[下一个>>](improving-the-beginner’s-pid-tuning-changes)  

[![知识共享许可](http://i.creativecommons.org/l/by-sa/3.0/80x15.png)](http://creativecommons.org/licenses/by-sa/3.0/)

[![这个！](http://brettbeauregard.com/blog/wp-content/plugins/flattr/img/flattr-badge-large.png)](http://brettbeauregard.com/blog/?flattrss_redirect&id=765&md5=60afbb931f0038052433ba10a872d183 "平板")

标签：[Arduino](http://brettbeauregard.com/blog/tag/arduino/)，[初学者的PID](http://brettbeauregard.com/blog/tag/beginners-pid/)，[PID](http://brettbeauregard.com/blog/tag/pid/)

<small>此项目被张贴上周五，2011年4月15日在下午3点02分，并提交下[编码](http://brettbeauregard.com/blog/category/pid/coding/)，[PID](http://brettbeauregard.com/blog/category/pid/)。您可以通过[RSS 2.0](http://brettbeauregard.com/blog/2011/04/improving-the-beginner%e2%80%99s-pid-derivative-kick/feed/) feed 跟踪对此条目的任何响应。您可以在自己的网站上[留下回应](#respond)或[引用](http://brettbeauregard.com/blog/2011/04/improving-the-beginner%e2%80%99s-pid-derivative-kick/trackback/)。</small>


