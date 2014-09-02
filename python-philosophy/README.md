Python Philosophy（Python哲学）
============================================

![Python-Programming-Language](Python-Programming-Language.png)

原文及译文
----------------------

1. Beautiful is better than ugly.  
美优于丑。
2. Explicit is better than implicit.   
直白优于隐晦。
3. Simple is better than complex ***[1]***.   
简单优于复杂。
4. Complex is better than complicated ***[2]*** .  
复杂优于纠结。
5. Flat is better than nested.     
扁平优于嵌套。
6. Sparse is better than dense ***[3]*** .  
稀疏优于稠密。
7. Readability counts.  
可读性有重要意义的。
8. Special cases aren't special enough to break the rules.  
特例可以，但不能特例到打破规则。
    * Although practicality beats purity.   
      尽管我们在纯粹性和实用性之间是倾向实用性的。
9. Errors should never pass silently.  
出错决不允许静静地Pass。
    * Unless explicitly silenced.   
      除非明确的说明了是静静地Pass。
10. In the face of ambiguity, refuse the temptation to guess.  
面对二义性情况时，要拒绝任何猜的诱惑。
11. There should be one -- and preferably only one -- obvious way to do it.  
显而易见的做法应该是一个，并且宁愿只有一个。
    * Although that way may not be obvious at first unless you're Dutch ***[4]***.  
      尽管这个做法可能刚开始的时候不是那么显而易见的，毕竟你不是荷兰人`Python`之父。
12.  Now is better than never.  
“现在” 优于 “决不”。
    * Although never is often better than **right** now.    
      尽管 “决不” 常常优于 “**马上**”。
13. If the implementation is hard to explain, it's a bad idea.  
如果一个实现很难解释清楚，说明实现的idea有问题。
14. If the implementation is easy to explain, it may be a good idea.  
如果一个实现很容易解释清楚，那可能是个好的实现idea。
15. NameSpaces are one honking great idea -- let's do more of those!  
Namespace真真儿是个好想法 — 放手多多使用吧！

### 译注

***[1] [2]*** 单词complex的意思是 复杂，而complicated 是 结构复杂。

在计算机中，单个模块复杂理解为complex，而多个模块互相关联的复杂理解为complicated。用计算机的术语，complicated是模块之间“耦合”很多，说明理解不深，设计不好。

软件的核心复杂度不可避免，但要这些集中一起，给使用一个干净高级的接口，也就是说：如果**complex**（核心复杂度）不可避免可以接受，但**complicated**一定要想办法去除，随着系统深入理解，模块职责的划分会更简明干净。  
PS： 核心复杂度的说明讨论可以参见[《代码大全》](http://book.douban.com/subject/1477390/)一书。

翻译上，complex 翻成 复杂，complicated 翻成 纠结。

***[3]*** 稀疏、稠密指的是代码行中操作的疏密。`C`的Geek，喜欢写稠密的代码，比如使用`++`，`--`运算符来压缩代码行。在Python看来，这个做法不可取，即会让代码更可能出错（如自增操作的负作用），也降低了代码的可读性。

***[4]*** 这里的荷兰人指`Python`之父Guido，参见说明：[武汉大学开源技术俱乐部 技术交流 第1期](http://qianjigui.javaeye.com/blog/266365 "http://qianjigui.javaeye.com/blog/266365")

个人讨论
----------------------

每一个点都千锤百炼；每一点都有直指内心的感觉。

既有指明大是大非的理念，又有指导细节操作的准则。

既有谆谆教导的推荐，也有声色俱厉的禁止。

看了N遍，每遍会深思。

`Python`说得内容对生活个人觉得一样有指导性，果然是哲学。

参考/阅读资料
----------------------

1. Python Philosophy原文 [http://www.c2.com/cgi/wiki?PythonPhilosophy](http://www.c2.com/cgi/wiki?PythonPhilosophy)
1. [Python(programming language) - Wikipedia](http://en.wikipedia.org/wiki/Python_%28programming_language%29#Programming_philosophy)
1. [No programming language offers what Python does philosophically.](http://www.indicthreads.com/1062/no-programming-language-offers-what-python-does-philosophically/)  
没有一门编程语言做到了Python在编程哲学上所做的。
1. [武汉大学开源技术俱乐部 技术交流 第1期](http://qianjigui.javaeye.com/blog/266365)。  
这里还有其它有关Python有意思的东西。
1. 核心复杂度的说明参见[《代码大全》](http://book.douban.com/subject/1477390/)一书。
