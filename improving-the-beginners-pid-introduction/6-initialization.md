# 初始化

### 问题

在上一节中，我们实现了打开和关闭PID的功能。我们关闭了它，但是现在让我们看看重新打开它会发生什么：  
[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/NoInitialization.png "没有初始化")](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/NoInitialization.png)

kes！PID跳回到它发送的最后一个输出值，然后从那里开始调整。这会导致我们不希望有的输入冲击。

### 解决方案

这个很容易修复。因为我们现在知道打开电源的时间（从手动到自动），所以我们只需要初始化即可平滑过渡。这意味着要对2个存储的工作变量（ITerm和lastInput）进行按摩，以防止输出跳跃。

### 代码

我们修改了SetMode（…）以检测从手动到自动的过渡，并添加了初始化功能。它将ITerm = Output设置为整数项，并设置lastInput = Input以防止派生峰值。比例项不依赖于过去的任何信息，因此不需要任何初始化。

### 结果

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/Initialization.png "初始化")](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/Initialization.png)

从上图可以看出，正确的初始化会导致从手动到自动的无扰转移：这正是我们所追求的。
[下一个>>](improving-the-beginners-pid-direction)

### 更新：为什么ITerm = 0？

最近，我收到很多问题，问为什么初始化时不设置ITerm = 0。作为回答，我请您考虑以下情形：pid是手动的，并且用户已将输出设置为50。一段时间后，该过程稳定为输入75.2。用户设定设定值75.2并打开pid。应该怎么办？

我认为在切换到自动后，输出值应保持在50。由于P和D项将为零，因此发生这种情况的唯一方法是将ITerm初始化为Output的值。

如果您需要将输出初始化为零，则无需更改上面的代码。只需在调用例程中设置Output = 0，然后将PID从“手动”转换为“自动”即可。

