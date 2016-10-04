原文链接：[A Week with `Elixir`](http://joearms.github.io/2013/05/31/a-week-with-elixir.html)，[_Joe Armstrong_](http://joearms.github.io/)，2013-05-31

------------------------

<img src="elixir-logo.png" align="right" >

## 目录

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [与`Elixir`相处的一周](#%E4%B8%8Eelixir%E7%9B%B8%E5%A4%84%E7%9A%84%E4%B8%80%E5%91%A8)
- [上周我下载了`Elixir`然后开始学习……](#%E4%B8%8A%E5%91%A8%E6%88%91%E4%B8%8B%E8%BD%BD%E4%BA%86elixir%E7%84%B6%E5%90%8E%E5%BC%80%E5%A7%8B%E5%AD%A6%E4%B9%A0%E2%80%A6%E2%80%A6)
- [编程语言设计的三定律](#%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80%E8%AE%BE%E8%AE%A1%E7%9A%84%E4%B8%89%E5%AE%9A%E5%BE%8B)
- [在源文件中没有版本](#%E5%9C%A8%E6%BA%90%E6%96%87%E4%BB%B6%E4%B8%AD%E6%B2%A1%E6%9C%89%E7%89%88%E6%9C%AC)
- [`funs`和`defs`不同](#funs%E5%92%8Cdefs%E4%B8%8D%E5%90%8C)
- [函数名称中有额外的句点](#%E5%87%BD%E6%95%B0%E5%90%8D%E7%A7%B0%E4%B8%AD%E6%9C%89%E9%A2%9D%E5%A4%96%E7%9A%84%E5%8F%A5%E7%82%B9)
- [发送操作符](#%E5%8F%91%E9%80%81%E6%93%8D%E4%BD%9C%E7%AC%A6)
- [管道运算符](#%E7%AE%A1%E9%81%93%E8%BF%90%E7%AE%97%E7%AC%A6)
- [`Elixir`还有`Sigils`](#elixir%E8%BF%98%E6%9C%89sigils)
- [`Docstrings`](#docstrings)
- [defmacro 引用](#defmacro-%E5%BC%95%E7%94%A8)
- [强调符](#%E5%BC%BA%E8%B0%83%E7%AC%A6)
- [空格符](#%E7%A9%BA%E6%A0%BC%E7%AC%A6)
- [Closures完全一样](#closures%E5%AE%8C%E5%85%A8%E4%B8%80%E6%A0%B7)
- [最后](#%E6%9C%80%E5%90%8E)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

------------------------

# 与`Elixir`相处的一周

差不多一周前我开始看[`Elixir`](http://elixir-lang.org)，关于`Elixir`之前只有些模糊的了解，没打算花时间去看细节。

但在得知_Dave Thomas_出版了[_Programming Elixir_](http://pragprog.com/book/elixir/programming-elixir)这本书的消息后，我的想法就彻底改变了。_Dave Thomas_帮我修订过我的那本`Erlang`的书并且作为`Ruby`的倡导者做得非常出色，所以要是_Dave_对一样东西产生了兴趣，那说明这样东西的有趣性是毫无疑问的。

_Dave_对`Elixir`很感兴趣, 在他的书里这样写道:

> 在1998年的时候，由于我是`comp.lang.misc`邮件组的忠实读者，机缘巧合得知了`Ruby`，然后下载、编译、与`Ruby`坠入爱河。
> （没听过`comp.lang.misc`？那去问问你老爹吧。）
> 就像任何一次相爱经历一样，你很难解释是什么原因。
> `Ruby`的工作方式和我想的灵犀默契，而且总是有足够的深度持续点燃着我的热情。
>
> 一眨眼过去的15年里，我无时无刻不在寻找一个也能给出这样感觉的新『对象』。
>
> 在不久前我遇上了`Elixir`，但由于一些原因，我完全没能沉浸其中。
> 但在几个月前，和_Corey Haines_聊了一次，在如何不用学院派的书给大家介绍哪些有吸引力的函数式编程概念这个问题上诉了些苦。
> 他告诉我再去看看`Elixir`。我照做了，然后我有了第一次看到`Ruby`时相同的感觉。

我能理解这种感觉，一种先行于逻辑的内心感性的感觉。就像我知道一件事是对的，我却并不知道我是如何和为什么知道这是对的。但是对原因的解释常常在几周甚至几年后我才找到。_Malcolm Gladwell_在他的[_Blink: The Power of Thinking Without Thinking_](https://www.amazon.com/Blink-Power-Thinking-Without/dp/0316010669/ref=sr_1_1?s=books&ie=UTF8&qid=1369995752&sr=1-1&keywords=blink)一书中曾探讨过这个问题。某些领域专家们常常能凭直觉判断事情的正确与否，但却给不出原因。

但得知_Dave_对`Elixir`的关注，我很想知道为什么他会这样。

无独有偶，_Simon St. Laurent_也出了本`Elixir`的书。_Simon_的[_Introducing Erlang_](http://www.amazon.com/Introducing-Erlang-Simon-St-Laurent/dp/1449331769)一书表现不俗，我和他还通过邮件沟通过几次，还有有些熟悉的。而且_Pragmatic Press_和_O'Reilly_出版社都在争着要出版`Elixir`，我知道在`Erlang VM`上有事要发生，而我自己还没注意到。毫无疑问我Out了！

我给_Dave_和_Simon_发了封邮件，之后他们爽快地借给我了样书，现在可以开始阅读了……谢了……

# 上周我下载了`Elixir`然后开始学习……

没多久我觉得就上手了。确实是个好货！有趣的是`Erlang`和`Elixir`两者在在底层一样的，对于我来说『感觉』是一样的。事实上也确实这样，两者都会被编译成`EVM`(`Erlang Virtual Machine`)指令 —— 实际上`EVM`这个叫法之前没人用，都叫成`Beam`，但为了和`JVM`区分开，我觉得我们应该开始用`EVM`这个<C-D-Z>叫法了。

`Erlang`和`Elixir`为什么有相同的『语意』？这得从虚拟机底层谈起。垃圾回收行为，不共享并发机制，底层的错误处理和代码加载机制都是一致的。当然这些肯定都是一致的：他们都运行在相同的`VM`里。这也是`Scala`和`Akka`区别于`Erlang`的原因。`Scala`和`Akka`都运行在`JVM`之上，垃圾回收和代码加载机制从根本上就不一样。

你直接看到的`Elixir`是完全不同的上层语法，源自`Ruby`。看起来不那么『可怕』语法和很多附加的甜点。

`Erlang`的语法源自`Prolog`，并受到`Smalltalk`、`CSP`和函数式编程的影响很大。`Elixir`则受到`Erlang`和`Ruby`的影响很大。从`Erlang`借鉴了模式匹配（`pattern matching`）、高阶函数（`higher order functions`）以及整个进程（`process`）和任其崩溃的（`let it crash`）错误处理（`error handling`）机制。从`Ruby`借鉴了`Sigils`和快捷语法（`shortcut syntaxes`）。当然也有自创的甜点，像`|>`管道操作（`|> pipe operator`），让人想到`Prologs`的`DCGs`和`Haskell`的`Monads`（尽管相比要简单不少，更类似于`Unix`的管道操作），还有宏的引用和反引用操作符（`macro quote and unquote operators`，对应的是`Lisp`的反引号和逗号操作符）。

> 【译注】：`Sigils`是指在变量名中包含符号来表达数据类型或作用域，通常作为前缀，如`$foo`，其中`$`就是个`Sigil`。  
> 详见[`wikipedia`词条`Sigils`](https://en.wikipedia.org/wiki/Sigil_(computer_programming))

`Elixir`还提供一个新的下层`AST`，取代了每个`Form`都是独有表示的`Erlang AST`，`Elixir AST`有一个统一得多的表示，这使得元编程（`meta-programming`）要简单得多。

`Elixir`的实现出奇的可靠，尽管有几个地方和我预想的不一样。字符串插值（`string interpolation`）的工作方式有时候不好使（字符串插值是个很棒的想法) ：

```ex
IO.puts "...#{x}..."
```

对`x`求值后把`x`友好格式化的表示（`a pretty-printed representation`）插入到字符串中。但是只对简单形式的`x`可行。

这点可以通过从`Elixir`调用`Erlang`的方式很简单就能解决掉。

`IO.puts "...#{pp(x)}..."`这样就总是可行的。我只是把`pp(x)`定义成

```ex
def pp(x) do
    :io_lib.format("~p", [x])
    |> :lists.flatten
    |> :erlang.list_to_binary
end
```

用`Erlang`则表述成:

```erl
pp(X) ->
  list_to_binary(lists_flatten(li_lib:format("~p"), [X])))
```

很『显然』这和`Elixir`的版本表述是一样。当然`Elixir`的写法要更容易阅读。上面用到的`|>`操作符意思是把`io_lib:format`的结果输入到`lists:flatten`，然后再到`list_to_binary`。就像好用的老家伙`Unix`的管道符`|`。

`Elixir`打破了一些`Erlang`神圣信条 —— 在顺序结构中变量可重绑定（`re-bound`）。这实际上也是可以做到的，因为最终结果还是可以规范化成静态单赋值（`static-single-assignment`，`SSA`）的形式。尽管在顺序结构中这是可以的，但在循环结构中，一定确定以及肯定不要这么做。但这不是个问题，因为`Elixir`木有循环，只有递归。实际上`Elixir`不能在循环中包含可变的变量（`mutable variables`），因为这样编译出来的东西下层`EVM`是支持不了的。顺序结构的`SSA`变量挺好的，`EVM`知道如何对其做优化。但在循环结果不行，所以`Elixir`没有这么做。关于这方面的优化甚至可以更往下挖到`LLVM`汇编器（`LLVM assembler`） —— 但又是另一个很长的故事先就此打住吧。

# 编程语言设计的三定律

1. 你做对的，无人为你提。
1. 你做错的，有人跟你急。
1. 难于理解的，你必须一而再再而三地去给人解释。

一些语言有的设计做得太好，结果大家都懒得去提，这些好的设计是正确的、是的优雅，是易于理解的。

对于错误的设计，你完了。你成了2B，如果好设计比坏设计多，你可能被原谅。你想在以后干掉这些坏设计，却因为向后兼容性或者是有些SB已经用上所有那些坏设计写上了1T行代码，结果是你改不了了。

而难以理解的部分才是真正无赖。你必须一而再再而三地解释，直到你吐血，可还是有些人永远不懂，你必须写上百邮件和数千文字来一遍又一遍地地解释这是什么意思以及为什么会如此。对于一个语言的设计者或作者来说，这是个痛苦的深渊。

下面我要说到的几件事，我认为也会落入这三类情况中。

在我开始前，我先要说，`Elixir`做对了灰常灰常多对的事情，而且远远多于错的。

关于`Elixir`有利的是，要改正它的错误还不算晚。这只能在无数代码行被写下和众多程序员开始使用它之前才能做到 —— 所以解决这些问题的时日并不多了。

# 在源文件中没有版本

`XML`文件总是这样开始的：

```xml
<?xml version="1.0"?>
```

这点非常好。读取`XML`文件的第一行就像是听到拉赫玛尼诺夫的第三钢琴协奏曲的第一小节（【译注】：指其富有辨识度）。这是一个令人赞叹的经验。赞美`XML`设计师，愿他们的名字得到荣光，给这帮伙计颁图灵奖吧。

所有源文件中加上语言的版本是必要的。为什么呢？

早期的`Erlang`没有列表推导（`list comprehensions`）。如果我们对一个新版的`Erlang`模块用一个旧的`Erlang`编译器去编译。新版的代码含有列表推导，但旧编译器并不知道列表推导，所以旧编译器会认为这是一个语法错误。

如果 **版本3** `Erlang`编译器处理这样开始的文件:

```erl
-version(5,0).
```

则可以给出这样提示信息：

> 啊～～～～咦～～～～
>
> 哦，烦炸了，我只是版本3的编译器，看不懂未来。
>
> 你刚刚给我一个版本5的程序，这说明我在地球上的寿命已过。
>
> 你将不得不杀掉我，把我卸载，然后安个版本5的新编译器。曾经玉树临风的我现在没了价值，我将不再存在。
>
> 再见吧，老朋友。
>
> 我感觉头痛。我要休息一下……

这是数据设计的第一法则：

> 所有未来可能会改变的数据应该标记上版本号。

而 模块 **_是_** 数据。

# `funs`和`defs`不同

在写_Programming Erlang_一书时_Dave Thomas_问函数为什么不能输入到`shell`里。

如果模块里有这样的代码：

```erl
fac(0) when N > 0 -> 1;
fac(N)            -> N* fac(N-1).
```

不能直接复制到`shell`里运行，得到相同的结果。_Dave_问这是为什么，并说这样很傻。

在`Lisp`等其它语言主中这做是没问题的。_Dave_说了『这很会让人很迷惑』类似这样的话 —— 他说的对并且这确实让人迷惑了。在论坛里关于此的问题肯定有成百上千条。

我解释这个问题已经无数遍了，从黑发解释到白发，我现在头发真白了就是因为这个原因。

原因是`Erlang`的一个`bug`。

- `Erlang`的模块是一系列的 **`FORMS`** 。
- `Erlang` `shell`解析的是一系列 **`EXPRESSIONS`** 。
- 但`Erlang`的`FORMS` 不是 **`EXPRESSIONS`** 。

```erl
double(X) -> 2*X.            in an Erlang module is a FORM

Double = fun(X) -> 2*X end.  in the shell is an EXPRESSION
```

上面两个是**不**同的。这小点愚蠢成了`Erlang`一个永远的痛，当时我们没有注意到，到了现在我们就只能学会和它相处。

在`Elixir`模块可以这么写

```ex
def triple(x) do
   3 * x;
end
```

估计很多人都会直接从编辑器复制到`shell`里直接运行，然后收到的是出错信息：

```ex
ex> def triple(x) do 3*x; end
** (SyntaxError) iex:66: cannot invoke def outside module
```

如果你不解决这个问题就要花后面20年的时间去解决为什么 —— 就像`Erlang`曾经所做的。

顺便说一下，修复这个问题真的真的很简单。我在`erl2`作为了尝试就解决了。`Erlang`中没法修复这个问题 (版本兼容问题)，所以我就在[erl2](https://github.com/joearms/erl2)解决。只需要小改一下`erl_eval`和解析器的几个微调。

主要原因是`FORMS`不是`expressions`, 所以加了个关键字`def`

```erl
Var = def fac(0) -> ; fac(N) -> N*fac(N-1) end.
```

这就定义了一个有副作用的`expression`。由于是`expressions`，可以在`shell`中求值了，记得在`shell`中只能求值`expressions`。

副作用指的是需要创建一个`shell:fac/1`功能（就像在模块中定义的一样）。

```ex
iex> double = fn(x) -> 2 * x end;

iex> def double(x) do 2*x end;
```

上面两者应该是一致的，并且**都是**定义一个名为`Shell.double`的函数。

做了这样的修改，妈妈再也不用担心我会白头了。

# 函数名称中有额外的句点

```ex
iex> f = fn(x) -> 2 * x end
#Function<erl_eval.6.17052888>
iex> f.(10)
20
```

在学校里我学会了写`f(10)`来调用函数而不是`f.(10)` —— 这是个『真正』的函数，函数名是`Shell.f(10)`（一个在`shell`中定义的函数）。`shell`部分是隐式的，所以可以只用`f(10)`来调用。

如果这点你置之不理，那就等着用你生命的接下来的二十年去解释为什么吧。等着在数百论坛里的数千封邮件吧。

# 发送操作符

```ex
Process <- Message
```

这是啥玩意？你知道从`occam-pi`转成`Elixir`有多难么。

这点让你现在在失去`occam-pi`社区路上。发送操作符就应该是**`!`**，像这样：

```erl
Process ! Message
```

接下来的一周，我的大脑会变成浆糊，我的神经网络要被重编程，这样我才能『看到』`<-`时才能反应成`!` —— 这点不是在说如何我思考，而是指要重编程我更深植在脊柱里无意识反应。发送操作符已经不在我大脑里，而是在我的脊柱里。我的大脑想着『发送一个消息给一个进程』并发送信号给我的手指，我的脊柱马上加上**`!`**，接着大脑要**回退删除**这个字符改成`<-`。

这是一个语法问题。让人爱恨交织的语法。如果10分制的评级标准，10代表『非常非常烂』，1代表『好吧，我可以适应』的话，这个问题我给3分。

这点会使`Occam-pi`的程序员很难转到`Elixir`，什么，只需要简单地使用**`!`**就能完成`<-`的功能？这可真是出人意料啊。相信会有很多人受到鼓舞的

# 管道运算符

这是一个很好很好的东西并且很简单就能掌握，以至于没人会给你称赞。这就是生活。

这是来自`Prolog`语言的基因。 在`Prolog`中的基因是显而易见的, 但是在`Erlang`中确实不明显的（`Prolog`的儿子）但是又在`Elixir`（`Prolog`的儿子的儿子）中重新显现了。

`x |> y`意味着调用了`x`然后获取了`x`的输出并且将它作为`y`的另外一个参数(第一个参数)。

所以

```ex
x(1,2) |> y(a,b,c)
```

意味着

```erl
newvar = x(1,2);
y(newvar,a,b,c);
```

着非常有用。假设我们要把握的是把一个变量 ‘abc’ 转换为 ‘Abc’。 在Elixir中没有利用的函数但是还有一个功能，就是去控制一个字符串。 所以我们需要现将这个变量转换为string,在Erlang中，我们这样写。 

```erl
capitalize_atom(X) ->
    list_to_atom(binary_to_list(capitalize_binary(list_to_binary(atom_to_list(X))))).
```

这样写太业余了，所以，所以我们还可能会写成这样。

```erl
capitalize_atom(X) ->
    V1 = atom_to_list(X),
    V2 = list_to_binary(V1),
    V3 = capitalize_binary(V2),
    V4 = binary_to_list(V3),
    binary_to_atom(V4).
```

但是，这更糟-恶心的代码。 我都不想再看到他了。 
于是 |> 来了:

```ruby
X |> atom_to_list |> list_to_binary |> capitalize_binary
  |> binary_to_list |> binary_to_atom
```

为什么我回调用这个隐藏的方法？ 

`Erlang` 从 `Prolog` 中演化而来, 而且 `Elixir` 也继承了 `Erlang`。 

`Prolog` 有 `DCGs` ，所以

```prolog
foo --> a,b,c.
```

得到了扩展成

```prolog
foo(In, Out) :- a(In, V1), b(V1,V2), c(V2, Out).
```

这基本上是同样的想法。我们创建了一个额外的隐藏参数的方法调用线程的方式在函数调用序列的进出。这是一种典型的`Haskell`方式，但请保守这个秘密。

`Prolog` 有 `DCGs`, `Erlang` 没有, `Elixir` 有管道操作符！

# `Elixir`还有`Sigils`

`Sigils`很好，我们要加到`Erlang`里。

字符串是一种抽象。 编程语言通常使用双引号圈住它们。就像这样:

```erl
x = "a string"
```

编译器在按照语意将其转换。

在`Erlang`里

```erl
X = "abc"
```

代表 “X 是ASCII中a,b,c的整数表示” 

但也可能自定义。`Elixir`里x = “abc” 代表x 是 UTF8 字符集y。 通过在最前面加上r可以改变默认集，`Erlang`里这样写:

```ex
X = r"...."
```

代表编译过的正则表达式, i。e.和 X = re:compile("…")一样 -将已知字符解释为想要的不同模式。下面代码：

```ex
A = "Joe",
B = s"Hello #{A}".
```

B值是“Hello Joe” - s代表&nbsp;“替换当中的字符”。

`Elixir`在这方面做得很好，有很多不同的`sigils`。

`Elixir`的`sigil`语法不太一样，如下：

```ruby
%C{.....}
```

在{}或 []接上C。

`Sigils`确实不错。 `Erlang` 15年前就有这个功能，现在引入也不会有什么兼容性问题。</div></td>

# `Docstrings`

`Docstrings`真好。

但有个小建议。 把`docstring`放到功能定义里。

如此

```python
@doc """
 ...
"""

def foo do
   ...
end
```
这般引入: 

```python
def foo do
  @doc """
  ...
  """
end
```

否则会出“detached comment”: 代码会出错。注释会在调用功能时断开。

Erlang里我没法分出注释是上一个功能还是下个功能的。 最好就是写到对应的功能模块内部。

# defmacro 引用

好东西。 这是解析模式的转换。 好处是无需关注其实现，引用帮你解决问题了。

这就是那些妙不可言的东西之一 - 需要解释一下。 就像`Haskell`的`monads` - Yup, `monads`确实很容易解释，难怪需要有那么多文章解释它有多好用（其实不简单）。 

`Elixir`宏很简单 -就是`lisp`的`quasiquote`和comma operator - 简单吧)

# 强调符

像这样:

```ruby
iex> lc x inlist [1,2,3], do: 2*x
[2,4,6]
```

不是这样: 

```ruby
iex> lc x inlist [1,2,3] do: 2*x 
** (SyntaxError) iex:3: syntax error before: do
```

后面的冒号让人迷惑。

# 空格符

```python
iex> lc x inlist [1,2,3], do : 2*x
** (SyntaxError) iex:2: invalid token: : 2*x
```

应该是“do:” 不是“do :”

空格就是空格。 字符串里面不能随便用，但除此之外即便为了美观我也会经常使用空格符。

但 Elixir不行 - 让人不喜欢。

# Closures完全一样

`Elixir` (fn’s)的`Closures` 和 `Erlang` (fun’s)一样。 

`fn`能捕获在其功能范围能的任何变量值，这一点非常有用(ie 创建恒定 `closures`) 。 `JavaScript` 这块做的很不好。一个`JavaScript` 和 `Elixir`的对比: 

```js
js> a = 5;
5
js> f = function(x) { return x+a }; 
function (x){return x+a}
js> f(10)
15
js> a = 100
100
js> f(10)
110
```

功能 f错了。 定义的f, 开始使用。 重定义f就不行了。函数是编程的好处就是使编程变得简单。如果 f(10)的值是15，就不应该在变来变去了。

`Elixir`呢? 没问题: 

```python
iex> a = 5  
5
iex> f = fn(x) -> x + a end
#Function<erl_eval.6.17052888>
iex> f.(10)
15
iex> a = 100
100
iex> f.(10)
15
```

正确的`closures`就应该指向恒定的数据地址 (就像`Erlang`) - 而不是数据的可变部分。 如果`closure`里有指向可变数据的指针，后面改动了数据就会破坏`closure`的一致性。 死锁互斥问题。 

一般使用`closure`的代价很高，需要复制环境里的所有变量深拷贝，但`Erlang` 和`Elixir`不是这样, 数据都是恒定的。可以用的时候再去找。也是通过指针实现的(内部操作) 垃圾回收机制会移除所有没有指针关联的数据么，避免空指针。

`closures`可以写到 `shell`, 但不能写到模块里。 

如果写成这样 

```ruby
a = 10;
f = fn(x) -> a + x end;
```

在`shell`里

为什么不能这样呢？

```erl
a = 10;
def f(x) do
   a + x
end
```

在`erlang2`是可行的。

# 最后

这是我开始`Elixir`的第一周, 确实没让人失望。

`Elixir` 语法简洁并融合了`Ruby`和`Erlang`的长处。还有自己的创新。

这是门新的语言,但介绍的书却不是同步的。第一本介绍`Erlang` 的书在`Erlang`被发明后7年才出现,畅销书更是在14年后才出现。用21年去等一本真正的介绍书籍，代价太大。

Dave很喜欢`Elixir`，我也觉得很酷，我想我们会在使用过程中找到更多乐趣的。

`Erlang`在占世界二分之一的手机网络中提供了抢到的支持，像是`WhatsApp`。在简化版出现后，会有更多的新鲜血液加入这个阵营，我想以后的发展一定更加有意义。

这是篇即兴短文。也许会有些不妥之处，欢迎大家指正。
