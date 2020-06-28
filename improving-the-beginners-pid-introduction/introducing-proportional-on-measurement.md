# 介绍按比例测量

已经有一段时间了，但是我终于更新了[Arduino PID Library](https://github.com/br3ttb/Arduino-PID-Library)。我添加的功能几乎是一个未知的功能，但我认为这将是业余爱好的福音。它称为“测量比例”（简称PonM）。

### 为什么你应该关心

[![流程类型](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/process-types.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/process-types.png)

那里有一些称为“集成过程”的过程。这些是pid输出控制输入变化率的过程。在工业中，这些仅占所有过程的一小部分，但在业余爱好中，这些人**无处不在**：真空，线性滑模和3D打印机挤出机温度都是此类过程的示例。

这些过程令人沮丧的是，使用传统的PI或PID控制时，它们会超出设定值。并非有时，但总是：

[![PonE-9](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonE-9.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonE-9.png)

如果您不知道，这可能会令人发疯。您可以永久调整调整参数，并且过冲仍然存在。基础数学使之成为现实。测量比例会改变基础数学。结果，有可能在不发生超调的情况下找到调整参数集： 仍然可以肯定会发生超调，但这不是不可避免的。借助PonM和正确的调整参数，可以轻松地将视频或线性滑行滑入设定点，而无需继续操作。  
[![庞9号](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-9.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-9.png)  

### 那么什么是测量比例？

与[测量导数](http://brettbeauregard.com/blog/2011/04/improving-the-beginner%e2%80%99s-pid-derivative-kick/)类似，PonM更改比例项的内容。P项将输入PID输入的当前值，而不是错误。

错误比例： [![庞氏当量](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonE-eqn.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonE-eqn.png)

测量比例： [![庞克](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-eqn.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-eqn.png)

与“测量导数”不同，对性能的影响是巨大的。对于DonM，导数项仍然具有相同的作用：抵抗陡峭的变化，从而抑制P和I驱动的振荡。另一方面，“测量比例”从根本上改变了比例项的作用。它不是像I那样的驱动力，而是像D那样的阻力。这意味着对于PonM，更大的Kp将使您的控制器更加保守。

### 大。但这如何消除过冲呢？

为了理解问题并进行修复，它有助于查看不同的术语以及它们对总体PID输出的贡献。这是使用传统PID对积分过程中设定值更改的响应（sous-vide）：

[![PonE组件](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonE-Components.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonE-Components.png)

需要注意的两件事是：

* 当我们处于设定点时，I项是整体输出的唯一贡献者。
* 即使起点和终点的设定值不同，输出也会返回相同的值。该值通常称为“平衡点”：输出为0的输入斜率。对于副食品，这将仅对应于足够的热量以补偿对环境的热损失。

在这里，我们可以看到为什么会发生过冲，并且总是会发生。首次更改设定值时，出现的错误会导致I项增大。为了使过程稳定在新的设定点，输出将需要返回到平衡点。做到这一点的唯一方法是缩小I-Term。发生这种情况的唯一方法是产生负误差，仅当您高于设定点时才会发生。

### PonM改变了游戏

这是使用“按比例测量”控制的相同视频（以及相同的调整参数）：

[![PonM组件](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-Components.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-Components.png)  
在这里您应该注意：

* P术语现在提供抵抗力。输入越高，输入越负。
* 在P项在新设定点变为零之前，它现在继续具有值。

P项不返回0的事实是关键。这意味着I项不必自己返回平衡点。P和I可以一起将输出返回到平衡点，而无需缩小I项。由于不需要缩小，因此不需要超调。

### 如何在新的PID库中使用它

如果您准备尝试“按比例测量”，并且已经安装了最新版本的PID库，则设置起来非常简单。使用PonM的主要方法是在重载的构造函数中指定它：

[![建设者](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/Constructor.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/Constructor.png)

如果您想在运行时在PonM和PonE之间切换，则SetTunings函数也将过载：

[![SetTunings](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/SetTunings.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/SetTunings.png)

您只需要在要切换时调用重载方法即可。否则，您可以使用常规的SetTunings函数，它将记住您的选择。




