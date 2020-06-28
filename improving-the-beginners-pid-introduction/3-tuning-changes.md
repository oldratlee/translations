# 调整更改

### 问题

对于任何受人尊敬的PID算法，必须具有在系统运行时更改调节参数的功能。

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BadIntegral.png)](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BadIntegral.png)

如果您尝试在运行时更改调整，则初学者的PID会有点疯狂。让我们看看为什么。这是上述参数更改前后初学者PID的状态：

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BadIntegralCode.png)](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BadIntegralCode.png)

因此，我们可以立即将此冲撞归咎于积分项（或“ I项”）。当参数更改时，这是唯一发生剧烈变化的事情。为什么会这样呢？它与初学者对积分的解释有关：

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BadIntegralEqn1.png)](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/BadIntegralEqn1.png)

直到更改Ki为止，这种解释才能正常工作。然后，您突然将这个新Ki乘以您累积的整个误差总和。那不是我们想要的！我们只想影响前进的脚步！

### 解决方案

我知道有几种方法可以解决此问题。我在上一个库中使用的方法是重新缩放errSum。淇加倍？将errSum减半。这样可以防止I Term发生碰撞，并且可以正常工作。不过这有点笨拙，我想出了一些更优雅的方法。（我不可能是第一个想到这一点的人，但是我确实是一个人想到了。这很可恶！）

该解决方案需要一些基本的代数（或者是微积分？）

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/GoodIntegralEqn.png)](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/GoodIntegralEqn.png)

我们没有将Ki置于积分之外，而是将其引入了积分。看起来我们什么都没做，但是我们会发现实际上这有很大的不同。

现在，我们将误差乘以当时的Ki值。然后，我们存储THAT的总和。当Ki发生变化时，就不会有颠簸了，因为可以说所有旧Ki都已经在“银行”中了。我们可以顺利进行转学，而无需进行其他数学运算。这可能使我成为一个极客，但我认为这很性感。

### 代码

因此，我们将errSum变量替换为复合ITerm变量[第4行]。它总结了Ki * error，而不仅仅是错误[第15行]。另外，由于Ki现在被埋在ITerm中，因此已从主PID计算中删除了该行[第19行]。

### 结果

[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/GoodIntegral.png)](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/GoodIntegral.png)  
[![](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/GoodIntegralCode.png)](http://brettbeauregard.com/blog/wp-content/uploads/2011/03/GoodIntegralCode.png)  
    那么这如何解决问题。在更改ki之前，它会重新调整整个错误的总和。我们看到的每个错误值。使用此代码，以前的错误将保持不变，新的ki仅影响前进的事物，而这正是我们想要的。  
[下一个>>](improving-the-beginner’s-pid-reset-windup)  
