`Python` Philosophy原文及相关说明： [http://www.c2.com/cgi/wiki?PythonPhilosophy](http://www.c2.com/cgi/wiki?PythonPhilosophy)    
译文发在：[`Python` Philosophy（`Python`哲学）翻译及简析](http://oldratlee.com/147/tech/python/python-philosophy.html)，2010-09-28

`Python` Philosophy（`Python`哲学）翻译及简析
============================================

![Python-Programming-Language](Python-Programming-Language.png)

原文及译文
----------------------

1. Beautiful is better than ugly.  
美优于丑。
2. Explicit is better than implicit.   
直白优于隐晦。
3. Simple is better than complex **_[1]_**.   
简单优于复杂。
4. Complex is better than complicated **_[2]_** .  
复杂优于纠结。
5. Flat is better than nested.     
扁平优于嵌套。
6. Sparse is better than dense **_[3]_** .  
稀疏优于稠密。
7. Readability counts.  
可读性是有重要价值的。
8. Special cases aren't special enough to break the rules.  
特例可以有，但不能特例到打破规则。
    * Although practicality beats purity.   
      尽管在纯粹性和实用性之间倾向于实用性。
9. Errors should never pass silently.  
出错决不允许静静地Pass。
    * Unless explicitly silenced.   
      除非明确地说明了是静静地Pass。
10. In the face of ambiguity, refuse the temptation to guess.  
面对二义性情况时，要拒绝任何猜的诱惑。
11. There should be one -- and preferably only one -- obvious way to do it.  
应该有一个，并且宁愿只有一个做法，一个显而易见的做法。
    * Although that way may not be obvious at first unless you're Dutch **_[4]_**.  
      尽管在刚开始的时候这个做法可能不是那么显而易见，毕竟你不是荷兰人。
12.  Now is better than never.  
“现在” 优于 “决不”。
    * Although never is often better than **right** now.    
      尽管 “决不” 常常优于 “**马上**”。
13. If the implementation is hard to explain, it's a bad idea.  
如果一个实现难于解释清楚，那它是个差的想法。
14. If the implementation is easy to explain, it may be a good idea.  
如果一个实现很容易解释清楚，那它可能是个好的想法。
15. NameSpaces are one honking great idea -- let's do more of those!  
命名空间是个值得称道的极好想法 — 放手多多使用吧！

### 译注

**_[1] [2]_** 单词complex的意思是 复杂，而complicated 是 结构复杂。

在计算机领域中，complex 可以对应 单个模块复杂，而complicated 则可以对应 多个模块互相关联的复杂。
用计算机的术语来说，complicated 是不同模块之间紧“耦合”，体现了理解不深，设计不好。

软件的核心复杂度不可避免，但要这些集中一起，给使用一个干净高级的接口，
也就是说：如果**complex**（核心复杂度）不可避免可以接受，
但**complicated**一定要想办法去除，随着系统深入理解，模块职责的划分会更简明干净。  
PS： 核心复杂度的说明讨论可以参见[《代码大全》](http://book.douban.com/subject/1477390/)一书。

翻译上，complex 翻成 复杂，complicated 翻成 纠结。

**_[3]_** 稀疏、稠密指的是代码行中操作的疏密。
`C`的Geek，喜欢写稠密的代码，比如使用`++`，`--`运算符来压缩代码行。
在`Python`看来，这个做法不可取，即会让代码更可能出错（如自增操作的负作用），也降低了代码的可读性。

**_[4]_** 这里的荷兰人指`Python`之父Guido，参见说明：[武汉大学开源技术俱乐部 技术交流 第1期](http://qianjigui.javaeye.com/blog/266365)。

在这里作者[TimPeters](http://www.c2.com/cgi/wiki?TimPeters)即含蓄地表达了对`Python`之父Guido的敬意，又体现了自己作为`Python`开发的傲娇，不是吗？ :grin:

个人讨论
----------------------

既有指明大是大非的理念，又有指导细节操作的准则。

既有谆谆教导的推荐，也有声色俱厉的禁止。

每一个点都千锤百炼；每一点都有直指内心的感觉。

看了N遍，每遍会深思。

`Python`说得内容对生活个人觉得一样有指导性，果然是哲学。

参考/阅读资料
----------------------

1. [Python(programming language) - Wikipedia](http://en.wikipedia.org/wiki/Python_%28programming_language%29#Programming_philosophy)
1. [No programming language offers what Python does philosophically.](http://www.indicthreads.com/1062/no-programming-language-offers-what-python-does-philosophically/)  
在编程哲学上，Python超越了其它编程语言。
1. [武汉大学开源技术俱乐部 技术交流 第1期](http://qianjigui.javaeye.com/blog/266365)。  
这里还有其它有关Python有意思的东西。
1. 核心复杂度的说明参见[《代码大全》](http://book.douban.com/subject/1477390/)一书。
