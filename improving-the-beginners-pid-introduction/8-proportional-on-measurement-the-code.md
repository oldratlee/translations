# 测量比例-守则

在上[一篇文章中，](http://brettbeauregard.com/blog/2017/06/introducing-proportional-on-measurement/)我将所有时间都花在解释“比例测量”的好处上。在这篇文章中，我将解释代码。人们似乎很欣赏我[上次](http://brettbeauregard.com/blog/2011/04/improving-the-beginners-pid-introduction/)解释事情的逐步方式[，](http://brettbeauregard.com/blog/2011/04/improving-the-beginners-pid-introduction/)所以我将在这里做。下面的3条详细说明了我如何将PonM添加到PID库中。

### 首过–初始输入和比例模式选择

随着输入的变化，“测量比例”会提供越来越大的电阻，但是如果没有参考系，我们的性能将有些不稳定。如果首次打开控制器时PID输入为10000，我们真的要开始以Kp * 10000进行抵抗吗？否。我们希望将初始输入用作参考点（第108行），随着输入从此处变化（第38行），增加或减少阻力。

我们需要做的另一件事是允许用户选择是否要按比例进行误差或测量。在[上](http://brettbeauregard.com/blog/2017/06/introducing-proportional-on-measurement/)一篇[文章](http://brettbeauregard.com/blog/2017/06/introducing-proportional-on-measurement/)之后，PonE似乎毫无用处，但重要的是要记住，对于许多循环而言，它运行良好。这样，我们需要让用户选择他们想要的模式（第51和55行），然后在计算中采取相应的行动（第37和38行）​​。

### 第二遍–动态调整

尽管上面的代码确实可以工作，但是它有一个我们之前已经看到的问题。在运行时更改调整参数时，会出现不希望出现的现象。

[![庞贝](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-blip.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-blip.png)

为什么会这样呢？

[![PonM斑点数学](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-blip-math-1.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-blip-math-1.png)

[我们上次看到此](http://brettbeauregard.com/blog/2011/04/improving-the-beginner%e2%80%99s-pid-tuning-changes/)结果是因为积分是通过新的Ki重新缩放。这次是（Input – initInput）由Kp重新缩放。我选择的解决方案类似于我对Ki所做的解决方案：我没有将Input – initInput视为一个整体单元乘以当前Kp的方法，而是将其分解为各个步骤，然后分别乘以Kp：

[![PonM扩展](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-expansion.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-expansion.png)


现在，我们保留一个有效的总和PTerm，而不是将Input-initInput的整体乘以Kp。在每一步中，我们仅将当前输入更改乘以Kp并从PTerm中减去（第41行）。在这里，我们可以看到更改的影响：

[![PonM-no-blip](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-no-blip.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-no-blip.png)

[![庞氏无斑点数学](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-no-blip-math.png)](http://brettbeauregard.com/blog/wp-content/uploads/2017/06/PonM-no-blip-math.png)

由于旧的Kps位于“银行”中，因此调整参数的变化只会影响我们向前迈进

### 最终通行证–总和。

对于上面的代码有什么问题，我不会详细介绍（流行趋势等）。很好，但是仍然存在重大问题。例如：

1.  **终结，排序的**：虽然最后输出OUTMIN和OUTMAX之间的限制，有可能为PTerm当它不应该增长。它不会像[积分缠绕](http://brettbeauregard.com/blog/2011/04/improving-the-beginner%e2%80%99s-pid-reset-windup/)那样糟糕，但是仍然不能接受
2.  **即时更改**：如果用户在运行时从P_ON_M更改为P_ON_E，则一段时间后返回，PTerm将不会被属性初始化，并且会导致输出颠簸

还有更多，但仅这些就足以了解真正的问题是什么。在创建ITerm之前，我们已经处理了所有这些问题。我决定不为PTerm重新执行相同的解决方案，而是决定使用一种更美观的解决方案。

通过将PTerm和ITerm合并到一个称为“ outputSum”的变量中，P_ON_M代码将从已存在的所有ITerm修复中受益，并且由于代码中没有两个和，因此没有不必要的冗余。


那里有。以上功能是Arduino PID v1.2.0中提供的功能。

### 但是，等等，还有更多：设定值加权。

我没有在Arduino库代码中添加以下内容，但是如果您想自己动手，此功能可能会引起您的兴趣。设定点加权是其核心，它是同时具有PonE和PonM的一种方法。通过在0和1之间指定一个比率，您可以分别具有100％PonM，100％PonE或两者之间的某个比率。如果您的过程集成度不高（例如回流炉），并且需要考虑这一点，这将很有帮助。

最终，我决定此时不将其添加到库中，因为它最终成为要调整/解释的另一个参数，并且我认为由此带来的好处并不值得。无论如何，这是代码，如果您想修改代码以具有设定值权重，而不仅仅是选择PonM / PonE：

现在，pOn不再是将pOn设置为整数的方式，而是提供了一个允许比率的双精度值（第58行）。除了一些标志（第62和63行）以外，还在第77-78行计算了加权Kp项。然后，在第37和43行上，将加权的PonM和PonE贡献添加到总体PID输出中。
