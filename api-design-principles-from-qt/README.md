原文链接：[API Design Principles](http://qt-project.org/wiki/API-Design-Principles)  
基于[Gary的影响力](http://blog.csdn.net/gaoyingju)上 _Gary Gao_ 的译文稿：[`C++`的API设计指导](http://blog.csdn.net/gaoyingju/article/details/8245108)

## :apple: 译序

此文为`Qt`官网上的`API`设计指导准则，其中有不少原则具有普遍适用性，整个篇幅中有很多示例，是`Qt`在`API`设计上的实践。　

# `API`设计原则

一致、易于掌握和强大的`API`是`Qt`最著名优点中的一个。此文总结了我们在设计`Qt`风格`API`的过程中所积累的诀窍（`know-how`）。其中许多准则是通用的；而其他的则更偏向于约定，遵循这些约定主要是为了与已有的`API`保持一致。　　　　

虽然这些准则主要用于公共`API`，但在设计私有`API`时也推荐遵循相同的技术，作为与其他开发者之间的礼仪（`courtesy`）。

如有兴趣也可以读一下 _Jasmin Blanchette_ 的[Little Manual of API Design (PDF)](http://www4.in.tum.de/~blanchet/api-design.pdf) 或是本文的前身 _Matthias Ettrich_ 的[Designing Qt-Style C++ APIs](https://doc.qt.io/archives/qq/qq13-apis.html)。

-------------------------------------------------------------------------------

<img src="api-design.jpg" width="40%" align="right" />

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [1. 好`API`的6个特点](#1-%E5%A5%BDapi%E7%9A%846%E4%B8%AA%E7%89%B9%E7%82%B9)
    - [1.1 极简](#11-%E6%9E%81%E7%AE%80)
    - [1.2 完备](#12-%E5%AE%8C%E5%A4%87)
    - [1.3 语义清晰简单](#13-%E8%AF%AD%E4%B9%89%E6%B8%85%E6%99%B0%E7%AE%80%E5%8D%95)
    - [1.4 符合直觉](#14-%E7%AC%A6%E5%90%88%E7%9B%B4%E8%A7%89)
    - [1.5 易于记忆](#15-%E6%98%93%E4%BA%8E%E8%AE%B0%E5%BF%86)
    - [1.6 引导`API`用户写出可读代码](#16-%E5%BC%95%E5%AF%BCapi%E7%94%A8%E6%88%B7%E5%86%99%E5%87%BA%E5%8F%AF%E8%AF%BB%E4%BB%A3%E7%A0%81)
- [2. 静态多态](#2-%E9%9D%99%E6%80%81%E5%A4%9A%E6%80%81)
    - [2.1 好的案例](#21-%E5%A5%BD%E7%9A%84%E6%A1%88%E4%BE%8B)
    - [2.2 差的案例](#22-%E5%B7%AE%E7%9A%84%E6%A1%88%E4%BE%8B)
    - [2.3 值得斟酌的案例](#23-%E5%80%BC%E5%BE%97%E6%96%9F%E9%85%8C%E7%9A%84%E6%A1%88%E4%BE%8B)
- [3. 基于属性的`API`](#3-%E5%9F%BA%E4%BA%8E%E5%B1%9E%E6%80%A7%E7%9A%84api)
- [4. `C++`细节](#4-c%E7%BB%86%E8%8A%82)
    - [4.1 值 vs. 对象](#41-%E5%80%BC-vs-%E5%AF%B9%E8%B1%A1)
        - [4.1.1 指针 vs. 引用](#411-%E6%8C%87%E9%92%88-vs-%E5%BC%95%E7%94%A8)
        - [4.1.2 按常量引用传递 vs. 按值传递](#412-%E6%8C%89%E5%B8%B8%E9%87%8F%E5%BC%95%E7%94%A8%E4%BC%A0%E9%80%92-vs-%E6%8C%89%E5%80%BC%E4%BC%A0%E9%80%92)
    - [4.2 虚函数](#42-%E8%99%9A%E5%87%BD%E6%95%B0)
        - [4.2.1 避免虚函数](#421-%E9%81%BF%E5%85%8D%E8%99%9A%E5%87%BD%E6%95%B0)
        - [4.2.2 虚函数 vs. 复制](#422-%E8%99%9A%E5%87%BD%E6%95%B0-vs-%E5%A4%8D%E5%88%B6)
    - [4.3 关于`const`](#43-%E5%85%B3%E4%BA%8Econst)
        - [4.3.1 输入参数：`const`指针](#431-%E8%BE%93%E5%85%A5%E5%8F%82%E6%95%B0const%E6%8C%87%E9%92%88)
        - [4.3.2 返回值：`const`值](#432-%E8%BF%94%E5%9B%9E%E5%80%BCconst%E5%80%BC)
        - [4.3.3 返回值：非`const`的指针还是有`const`的指针](#433-%E8%BF%94%E5%9B%9E%E5%80%BC%E9%9D%9Econst%E7%9A%84%E6%8C%87%E9%92%88%E8%BF%98%E6%98%AF%E6%9C%89const%E7%9A%84%E6%8C%87%E9%92%88)
        - [4.3.4 返回值：按值返回 还是 按`const`引用返回？](#434-%E8%BF%94%E5%9B%9E%E5%80%BC%E6%8C%89%E5%80%BC%E8%BF%94%E5%9B%9E-%E8%BF%98%E6%98%AF-%E6%8C%89const%E5%BC%95%E7%94%A8%E8%BF%94%E5%9B%9E)
        - [4.4.5 `const` vs. 对象的状态](#445-const-vs-%E5%AF%B9%E8%B1%A1%E7%9A%84%E7%8A%B6%E6%80%81)
- [5. `API`的语义和文档](#5-api%E7%9A%84%E8%AF%AD%E4%B9%89%E5%92%8C%E6%96%87%E6%A1%A3)
- [6. 命名的艺术](#6-%E5%91%BD%E5%90%8D%E7%9A%84%E8%89%BA%E6%9C%AF)
    - [6.1 通用的命名规则](#61-%E9%80%9A%E7%94%A8%E7%9A%84%E5%91%BD%E5%90%8D%E8%A7%84%E5%88%99)
    - [6.2 类的命名](#62-%E7%B1%BB%E7%9A%84%E5%91%BD%E5%90%8D)
    - [6.3 枚举类型和值的命名（naming enum types and values）](#63-%E6%9E%9A%E4%B8%BE%E7%B1%BB%E5%9E%8B%E5%92%8C%E5%80%BC%E7%9A%84%E5%91%BD%E5%90%8Dnaming-enum-types-and-values)
    - [6.4 函数和参数的命名（Naming Functions and Parameters）](#64-%E5%87%BD%E6%95%B0%E5%92%8C%E5%8F%82%E6%95%B0%E7%9A%84%E5%91%BD%E5%90%8Dnaming-functions-and-parameters)
    - [6.5 `bool`类型的`getter`与`setter`的命名（Naming Boolean Getters, Setters, and Properties）](#65-bool%E7%B1%BB%E5%9E%8B%E7%9A%84getter%E4%B8%8Esetter%E7%9A%84%E5%91%BD%E5%90%8Dnaming-boolean-getters-setters-and-properties)
- [7. 避免常见陷阱（`avoiding common traps`）](#7-%E9%81%BF%E5%85%8D%E5%B8%B8%E8%A7%81%E9%99%B7%E9%98%B1avoiding-common-traps)
    - [7.1 简化的陷阱（`convenience trap`）](#71-%E7%AE%80%E5%8C%96%E7%9A%84%E9%99%B7%E9%98%B1convenience-trap)
    - [7.2 `boolean`参数的陷阱（`boolean parameter trap`）](#72-boolean%E5%8F%82%E6%95%B0%E7%9A%84%E9%99%B7%E9%98%B1boolean-parameter-trap)
- [8. 案例研究](#8-%E6%A1%88%E4%BE%8B%E7%A0%94%E7%A9%B6)
    - [8.1 `QProgressBar`](#81-qprogressbar)
    - [8.2 `QAbstractPrintDialog` & `QAbstractPageSizeDialog`](#82-qabstractprintdialog--qabstractpagesizedialog)
    - [8.3 `QAbstractItemModel`](#83-qabstractitemmodel)
    - [8.4 `QLayoutIterator` & `QGLayoutIterator`](#84-qlayoutiterator--qglayoutiterator)
    - [8.5 `QImageSink`](#85-qimagesink)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

-------------------------------------------------------------------------------

# 1. 好`API`的6个特点

`API`之于程序员就如同`GUI`之于用户。`API`中的『`P`』实际上指的是『程序员』，而不是『程序』，这强调了所有`API`都是给序员使用的事实。

在 _Matthias_ 的第13期[`Qt`季刊](http://doc.qt.io/archives/qq/)关于`API`设计的文章中提出了观点：`API`应该极简（`minimal`）且完备（`complete`）、语义清晰简单（`have clear and simple semantics`）、符合直觉（`be intuitive`）、易于记忆（`be easy to memorize`）和引导`API`用户写出可读代码（`lead to readable code`）。

## 1.1 极简

极简的`API`是指每个公有类的公有成员尽可能少，公有类也尽可能少。这样的API更易理解、记忆、调试和变更。

## 1.2 完备

完备的`API`是指期望有的功能都包含了。这点会和保持`API`极简有些冲突。如果一个成员函数放到了错误的类里，那么这个函数的潜在用户就会找不到，这也是违反完备性的。

## 1.3 语义清晰简单

就像其他的设计一样，我们应该遵守最少意外原则(`principle of least surprise`)。常见的任务应该很简单地完成，而对不常见的任务应该能做到且不会很费心神；当没有需求时不要过度通用化解决方案。（举个例子，在`Qt 3`中，`QMimeSourceFactory`不应命名成`QImageLoader`并有不一样的`API`。）

## 1.4 符合直觉

就像计算机里的其他事物一样，`API`应该符合直观。对于什么是符合直觉的什么不符合，不同的经验和背景的人会有不同的看法。`API`符合直观的测试方法：经验不很丰富的用户不用阅读`API`文档就能搞懂`API`，而且程序员不用了解`API`就能明白使用`API`的代码。

## 1.5 易于记忆

为使`API`易于记忆，`API`的命名约定应该具有一致性和精确性。使用易于识别的模式和概念，并且避免用缩写。

## 1.6 引导`API`用户写出可读代码

代码只写一次，却要多次阅读（还有调试和修改）。写出可读性好的代码花费更多的时间，对于产品的整个生命周期来说反而是节省了时间成本。

# 2. 静态多态

相似的类应该有相似的`API`。在继承（`inheritance`）合适时可以用继承达到这个效果，即运行时多态。然而多态也发生在设计阶段。例如，如果你用`QProgressBar`替换`QSlider`，或者是`QString`替换`QByteArray`，你会发现`API`的相似性使的替换很容易。此即是所谓的『静态多态』（`static polymorphism`）。

静态多态也使记忆`API`和编程模式更加容易。因此，一组相关类有相似的`API`有的时候比每个类都有各自的一套`API`更好。

一般来说在`Qt`中，如果没有足够的理由要使用继承关系，我们更倾向于依赖静态多态。这样可以减少`Qt`公有类的个数，也使刚学习`Qt`的用户在翻看文档时更有方向感。

## 2.1 好的案例

`QDialogButtonBox`与`QMessageBox`，在按钮操作（`addButton()`、`setStandardButtons()`等等）上，有相似的`API`，而没有继承某个`QAbstractButtonBox`类。

## 2.2 差的案例

`QTcpSocket`与`QUdpSocket`都继承了`QAbstractSocket`，这两个类的交互行为的模式（`mode of interaction`）非常不同。似乎没有什么人以通用和有意义的方式（`in a generic and useful way`）用过`QAbstractSocket`指针（或者 **_能_** 以通用和有意义的方式使用`QAbstractSocket`指针）。

## 2.3 值得斟酌的案例

`QBoxLayout`是`QHBoxLayout`与`QVBoxLayout`的基类，好处：可以在工具栏上使用`QBoxLayout`调用`setOrientation()`使其变为水平/垂直。坏处：需要一个额外的类，并且有可能导致用户写出这样没什么意义的代码，`((QBoxLayout *)hbox)->setOrientation(Qt::Vertical)`。

# 3. 基于属性的`API`

新的`Qt`类倾向于基于属性（`property`）的`API`，例如：

```cpp
QTimer timer;
timer.setInterval(1000);
timer.setSingleShot(true);
timer.start();
```

这里的 **_属性_** 是指任何的概念特征（`conceptual attribute`）即一部分对象状态 —— 无论它是不是`Q_PROPERTY`。在实践中，用户应该可以以任何顺序设置属性，也就是说，属性之间应该是正交的（`orthogonal`）。例如，上面的代码可以写成：

```cpp
QTimer timer;
timer.setSingleShot(true);
timer.setInterval(1000);
timer.start();
```

>【译注】：正交特性：改变某个特性而不会影响到其他的特性。《程序员修炼之道》中讲了一个关于正交性的直升飞机坠毁的例子。

为了方便，也写成：

```cpp
timer.start(1000)；
```

类似地，对于`QRegExp`，

```cpp
QRegExp regExp;
regExp.setCaseSensitive(Qt::CaseInsensitive);
regExp.setPattern(".");
regExp.setPatternSyntax(Qt::WildcardSyntax);
```

为实现这种类型的`API`，不得不懒创建底层的对象。例如，对于`QRegExp`的例子，在不知道模式语法（`pattern syntax`）的情况下，在`setPattern()`中编译`"."`就为时过早了。

属性之间常常有关联的；在这种情况下，我们必须小心处理。思考下面的问题：当前风格（`current style`）提供了『默认的图标尺寸』，而`QToolButton`有『`iconSize`』属性：

```cpp
toolButton->setStyle(otherStyle);
toolButton->iconSize();    // returns the default for otherStyle
toolButton->setIconSize(QSize(52, 52));
toolButton->iconSize();    // returns (52, 52)
toolButton->setStyle(yetAnotherStyle);
toolButton->iconSize();    // returns (52, 52)
```

提醒一下，一旦设置了`iconSize`，它就一直保持设置状态，即使改变当前的风格。这 **_很好_**。有的情况需要能重置某个属性，有两种方法：

1. 传入一个特殊值（如`QSize()`、`-1`或者`Qt::Alignment(0))`，意味着『重置』
1. 提供一个明确的重置接口，如`resetFoo()`和`unsetFoo()`

对于`iconSize`，使用`QSize()`（比如 `QSize(–1, -1)`）表示『重置』就足够了。

在某些情况下，`getter`方法返回的结果与所设置的值不同。例如，如果调用`widget->setEnabled(true)`，如果它的parent处于`disabled`状态，`widget->isEnabled()`仍然返回 `false`。这样是OK的，因为正是我们想要的（`widget`的父`widget`处于`disabled`状态，此`widget`也应该变为灰色，就好象自身处于`disabled`状态一样；同时它会记得自身处于`enabled`状态，正等待它的父`widget`变为`enabled`），但诸如这些必须恰当地记入文档。

# 4. `C++`细节

## 4.1 值 vs. 对象

### 4.1.1 指针 vs. 引用

指针（`pointer`）还是引用（`reference`）哪个是最好的输出参数（`out-parameters`）？

```cpp
void getHsv(int *h, int *s, int *v) const;
void getHsv(int &h, int &s, int &v) const;
```

大多数`C++`书籍推荐尽可能使用引用，基于一个普遍的观点：引用比指针『更加安全和优雅』。与此相反，我们在开发`Qt`时倾向于指针，因为指针让用户代码可读性更好。比较下面例子：

```cpp
color.getHsv(&h, &s, &v);
color.getHsv(h, s, v);
```

只有第一行代码清楚表达出`h`、`s`、`v`参数在函数调用中非常可能被修改。

### 4.1.2 按常量引用传递 vs. 按值传递

【TODO：补这节翻译】

## 4.2 虚函数

在`C++`中，当类的成员函数声明为`virtual`，主要是为了通过在子类重载此函数能够定制函数的行为。将函数声明为`virtual`的目的是为了让对这个函数已有的调用变成执行你的代码路径。对于没有在类外部调用的函数，是否将其声明为`virtual`你应该多加小心。

```cpp
// QTextEdit in Qt 3: member functions that have no reason for being virtual
virtual void resetFormat();
virtual void setUndoDepth( int d );
virtual void setFormat( QTextFormat *f, int flags );
virtual void ensureCursorVisible();
virtual void placeCursor( const QPoint &pos;, QTextCursor **c = 0 );
virtual void moveCursor( CursorAction action, bool select );
virtual void doKeyboardAction( KeyboardAction action );
virtual void removeSelectedText( int selNum = 0 );
virtual void removeSelection( int selNum = 0 );
virtual void setCurrentFont( const QFont &f );
virtual void setOverwriteMode( bool b ) { overWrite = b; }
```

`QTextEdit`从`Qt 3`移植到`Qt 4`的时候，几乎所有的虚函数都被移除了。有趣的是（不过并不是没有预料到的），并没有人对此有大的抱怨，为什么？因为`Qt 3`没用到`QTextEdit`的多态行为 —— 你可以；简单得说，没有理由去继承`QTextEdit`并重新实现这些函数，除非你自己调用了这些方法。如果在`Qt`在外部你的应用程序你需要多态，你可以自己添加多态。

### 4.2.1 避免虚函数

在`Qt`中，我们有很多理由尽量减少虚函数的数量。每一次对虚函数的调用会在函数调用链路中插入一个未掌控的节点（某种程度上使结果更无法预测），使得`bug`修复变得更复杂。用户在重新实现的虚函数中可以做很多疯狂的事：

- 发送事件
- 送信号
- 重新进入事件循环（例如，通过打开一个模态文件对话框）
- 删除对象（即触发『`delete this`』）

还有其他很多原因要避免过度使用虚函数：

- 添加、移动或是删除虚函数都带来二进制兼容问题（`binary binary/BC`）
- 重载虚函数并不容易
- 编译器几乎不能优化或内联（`inline`）对虚函数的调用
- 虚函数调用需要查找虚函数表（`v-table`），这比普通函数调用慢了2到3倍
- 虚函数使得类很难按值复制（尽管可能，但是非常混乱并且不建议这样做）

经验告诉我们，没有虚函数的类一般`bug`更少、维护成本也更低。

一般的经验法则是，除非我们以这个类作为工具包或是作为这个类的主要用户来调用函数，否则这个函数九成不应该设计成虚函数。

【TODO：工具包这句理解不了翻译得不清！】

### 4.2.2 虚函数 vs. 复制

多态对象（`polymorphic objects`）和值类型的类（`value-type classes`）两者很难协作好。

包含虚函数的类必须把析构函数声明为虚函数，以防止基类析构时没有清理子类的数据，导致内存泄漏。

如果要使一个类可以复制和赋值或者能按值比较，需要拷贝构造函数、赋值操作符（`operator =`）和相等操作符（`operator ==`）。

```cpp
class CopyClass {
public:
    CopyClass();
    CopyClass(const CopyClass &other);
    ~CopyClass();
    CopyClass &operator =(const CopyClass &other);
    bool operator ==(const CopyClass &other) const;
    bool operator !=(const CopyClass &other) const;

    virtual void setValue(int v);
};
```

如果继承`CopyClass`这个类，预料之外的事就已经在代码时酝酿了。一般情况下，如果没有虚成员函数和虚析构函数，就不能有依赖多态的子类。然而，如果存在虚成员函数和虚析构函数，这突然变成了要有子类去继承的理由，而且开始变得复杂了。_起初认为只要简单声明上虚操作符重载函数（`virtual operators`）。_ 但其实是走上了一条混乱和毁灭之路（看不明白？读作写出的是『不可读代码』）。看看下面的这个例子：

```cpp
class OtherClass {
public:
    const CopyClass &instance() const; // 这个方法返回的是什么？可以赋值什么？
};
```

（未完等续）

## 4.3 关于`const`

_`C++`的关键词`const`表明了内容不会改变或是没有副作用。可以应用于简单的值、指针及指针所指的内容，也可以作为一个特别的属性应用于类的成员函数上，表示成员函数不能修改对象的状态。_

然而，`const`本身并没有提供太大的价值 —— 很多编程语言甚至没有类似`const`的关键词，但是却并没有因此产生问题。实际上，如果你不用函数重载，并在`C++`源代码用搜索和替换来删除所有的`const`，几乎总能编译通过并且正常运行。尽量让使用的`const`保持实用有效，这点很重要。

让我们看一下在`Qt`的`API`设计中与`const`相关的场景。

### 4.3.1 输入参数：`const`指针

有输入指针参数的`const`成员函数，几乎总是`const`指针参数。

如果函数声明为`const`，意味着既没有副作用，也不会改变对象的可见状态。那为什么它需要一个没有`const`限定的输入参数呢？记住`const`类型的函数通常被其他`const`类型的函数调用，接收到的一般都是`const`指针（只要不主动`const_cast`，我们推荐尽量避免使用`const_cast`）

以前：

```cpp
bool QWidget::isVisibleTo(QWidget *ancestor) const;
bool QWidget::isEnabledTo(QWidget *ancestor) const;
QPoint QWidget::mapFrom(QWidget *ancestor, const QPoint &pos) const;
```

`QWidget`声明了许多非`const`指针输入参数的`const`成员函数。注意，这些函数可以修改传入的参数，不能修改对象自己。使用这样的函数常常要借助`const_cast`转换。如果是`const`指针输入参数，就可以避免这样的转换了。

之后：

```cpp
bool QWidget::isVisibleTo(const QWidget *ancestor) const;
bool QWidget::isEnabledTo(const QWidget *ancestor) const;
QPoint QWidget::mapFrom(const QWidget *ancestor, const QPoint &pos) const;
```

注意，我们在`QGraphicsItem`中对此做了修正，但是`QWidget`要等到`Qt 5`:

```cpp
bool isVisibleTo(const QGraphicsItem *parent) const;
QPointF mapFromItem (const QGraphicsItem *item, const QPointF &point) const;
```

### 4.3.2 返回值：`const`值

调用函数返回的非引用类型的结果，称之为右值（`R-value`）。

非类（`non-class`）的右值总是无`cv`限定类型（`cv-unqualified type`）。虽然从语法上讲，加上`const`也可以，但是没什么意义，因为鉴于访问权限这些值是不能改变的。多数现代编译器在编译这样的代码时会提示警告信息。

> 【译注】：`cv-qualified`的类型（与`cv-unqualified`相反）是由`const`或者`volatile`或者`volatile const`限定的类型。详见[cv (const and volatile) type qualifiers - `C++`语言参考](http://en.cppreference.com/w/cpp/language/cv)

当在类类型（`class type`）右值上添加`const`关键字，则禁止访问非`const`成员函数以及对成员的直接操作。

不加`const`则没有以上的限制，但几乎没有必要加上`const`，因为右值对象生存时间（`life time`）的结束一般在`C++`全清理（`full-removed`）的时候（通俗的说，下一个分号地方），而对右值对象的修改随着右值对象的生存时间就一起结束了。

【TODO：什么是full-removed ？？需要调查理解！】

示例：

```cpp
struct Foo {
    void setValue(int v) { value = v; }
    int value;
};

Foo foo() {
    return Foo();
}

const Foo cfoo() {
    return Foo();
}

int main() {
    // The following does compile, foo() is non-const R-value which
    // can't be assigned to (this generally requires an L-value)
    // but member access leads to a L-value:
    foo().value = 1; // Ok, but temporary will be thrown away at the end of the full-expression.

    // The following does compile, foo() is non-const R-value which
    // can't be assigned to, but calling (even non-const) member
    // function is fine:
    foo().setValue(1); // Ok, but temporary will be thrown away at the end of the full-expression.

    // The following does _not_compile, foo() is ''const'' R-value
    // with const member which member access can't be assigned to:
    cfoo().value = 1; // Not ok.

    // The following does _not_compile, foo() is ''const'' R-value,
    // one cannot call non-const member functions:
    cfoo().setValue(1); // Not ok
}
```

### 4.3.3 返回值：非`const`的指针还是有`const`的指针

谈到`const`函数应该返回非`const`的指针还是`const`指针这个话题时，多数人发现在`C++`中关于『`const`正确性』（`const correctness`）在概念上产生了分歧。 _问题起源是：**`const`函数本身不能修改对象自身的状态，却可以返回成员的非`const`指针**。返回指针这个简单动作本身既不会影响整个对象的可见状态，当然也不会改变这个函数职责范围内涉及的状态。但是，这却使得程序员可以间接访问并修改对象的状态。_

下面的例子演示了通过返回非`const`指针的`const`函数绕开`const`约定（`constness`）的诸多方式中的一种：

```cpp
QVariant CustomWidget::inputMethodQuery(Qt::InputMethodQuery query) const {
    moveBy(10, 10); // doesn't compile!
    window()->childAt(mapTo(window(), rect().center()))->moveBy(10, 10); // compiles!
}
```

返回`const`指针的函数正是保护以避免这些（可能是不期望的/没有预料到的）副作用，至少是在一定程度上。但哪个函数你会觉得更想返回`const`指针，或是不止一个函数？

若采用`const`正确（`const-correct`）的方法，每个返回某个成员的指针（或多个指向成员的指针）的`const`函数必须返回`const`指针。在实践中，很不幸这样的做法将导致无法使用的`API`：

```cpp
QGraphicsScene scene;
// … populate scene

foreach (const QGraphicsItem *item, scene.items()) {
    item->setPos(qrand() % 500, qrand() % 500); // doesn't compile! item is a const pointer
}
```

`QGraphicsScene::items()`是一个`const`函数，顺着思考看起来这个函数只应该返回`const`指针。

在`Qt`中，我们几乎只有非`const`的使用模式。我们选择的是实用路子：
相比滥用非`const`指针返回类型带来的问题，返回`const`指针更可能招致过分使用`const_cast`的问题。

### 4.3.4 返回值：按值返回 还是 按`const`引用返回？

若返回的是对象的拷贝，那么返回`const`引用是更直接的方案；
然而，这样的做法限制了后面想要对这个类的重构（`refactor`）。
（以`d-point`的典型做法（`idiom`）为例，我们可以在任何时候改变`Qt`类在内存表示（`memory representation`）；但却不能在不破坏二进制兼容性的情况下把改变函数的签名，返回值从`const QFoo &`变为`QFoo`。）
基于这个原因，除去对运行速度敏感（`speed is critical`）而重构不是问题的个别情形（例如，`QList::at()`），我们一般返回`QFoo`而不是`const QFoo &`。

### 4.4.5 `const` vs. 对象的状态

`const`正确性的问题就像`C`圈子中`vi`与`emacs`讨论，因为这个话题在很多地方都存在分歧（比如基于指针的函数）。

但通用准则是`const`函数不能改变类的可见状态。『状态』的意思是『自身以及涉及的职责』。这并不是指非`const`函数能够改变自身的私有成员，也不是指`const`函数改变不了。而是指函数是活跃的并存在可见的副作用（`visible side effects`）。`const`函数一般没有任何可见的副作用，比如：

```cpp
QSize size = widget->sizeHint(); // const
widget->move(10, 10); // not const
```

代理（`delegate`）负责在其它对象上绘制内容。
它的状态包括它的职责，因此包括在哪个对象做绘制这样的状态。
调用它的绘画行为必然会有副作用；
它改变了它绘制所在设备的外观（及其所关联的状态）。鉴于这些，`paint()`作为`const`函数并不合理。
进一步说，任何`paint()`或`QIcon`的`paint()`的视图函数是`const`函数也不合理。
没有人会从内部的`const`函数去调用`QIcon::paint()`，除非他想显式的绕开`const`这个特性。
如果是这种情况，使用`const_cast`会更好。

```cpp
// QAbstractItemDelegate::paint is const
void QAbstractItemDelegate::paint(QPainter **painter, const QStyleOptionViewItem &option, const QModelIndex &index) const

// QGraphicsItem::paint is not const
void QGraphicsItem::paint(QPainter *painter, const QStyleOptionGraphicsItem option, QWidget *widget)
```

`const`关键字并不能按你期望的样子起作用。应该考虑将其移除而不是去重载`const`/非`const`函数。

# 5. `API`的语义和文档

当传值为`-1`的参数给函数，函数会是什么行为？有很多类似的问题……

是警告、致命错误还是其它？

`API`需要的是质量保证。`API`第一个版本一定是不对的；必须对其进行测试。
以阅读使用`API`的代码的方式编写用例，且验证这样代码是可读的。

还有其他的验证方法，比如

- 让别人使用`API`（看了文档或是先不看文档都可以）
- 给类写文档（包含类的概述和每个函数）

# 6. 命名的艺术

命名很可能是设计`API`时最简单最重要的方面。类应该用什么名字？成员函数应该用什么名字？

## 6.1 通用的命名规则

有几个规则对于所有类型的命名都等同适用。第一个，之前已经提到过，不要使用缩写。即使是明显的缩写，比如把`previous`缩写成`prev`，从长远来是回报是负的，因为用户必须要记住缩写词的含义。

如果`API`本身没有一致性，之后事情自然就会越来越糟；例如，`Qt 3` 中同时存在`activatePreviousWindow()`与`fetchPrev()`。恪守『不缩写』规则更容易地创建一致性的`API`。

另一个时重要但更微妙的准则是在设计类时应该保持子类名称空间的干净。在`Qt 3`中，此项准则没有被一直追随。以`QToolButton`为例对此进行说明。如果调用`QToolButton`的 `name()`、`caption()`、`text()`或者`textLabel()`，你觉得会返回什么？用`Qt`设计器在`QToolButton`上自己先试试吧：

- `name`属性是继承自`QObject`，返回内部的对象名称，用于调试和测试。　
- `caption`属性继承自`QWidget`，返回窗口标题，对`QToolButton`来说毫无意义，因为它在创建的时候parent就存在了。
- `text`函数继承自`QButton`，一般用于按钮。当`useTextLabel`不为`true`，才用这个属性。　
- `textLabel`属性在`QToolButton`内声明，当`useTextLabel`为`true`时显示在按钮上。

为了可读性，`name`在`Qt 4`中改成了`objectName`，`caption`改为了`windowTitle`，`QToolButton`中再也没有`textLabel`了。

当你找不到好的名称时，开始写文档是一种好好的寻找方式：尝试为类、方法、枚举类型、值等写文档，把写下的第一句作为启发。如果找不确切的名称，这说明这个东西不该存在。如果所有尝试都失败了，并且你认为不如发明一个新名称，你就知道`widget`，`event focus`和`buddy`是如何产生的了。

## 6.2 类的命名

用把类的名称分组的方式替换为每个类单独命名的方法。例如，所有`Qt 4`的了解模型（`model-aware`）的视图（`view`）类后缀都是`View`（`QListView`、`QTableView`、`QTreeView`），相应的基于`item`的类后缀是`Widget`（`QListWidget`、`QTableWidget`、`QTreeWidget`）。

## 6.3 枚举类型和值的命名（naming enum types and values）

`C++`中枚举值没有类型（与`Java`、`C#`不同），声明枚举类型时需要记住这一点。下面的例子说明了给枚举值起过于通用的名字的危害：

```cpp
namespace Qt
{
    enum Corner { TopLeft, BottomRight, ... };
    enum CaseSensitivity { Insensitive, Sensitive };
    ...
};

tabWidget->setCornerWidget(widget, Qt::TopLeft);
str.indexOf("$(QTDIR)", Qt::Insensitive);
```

在最后一行，`Insensitive`是什么意思？（容易引起混淆）。命名枚举类型的一个准则是在枚举值至少重复此枚举类型名称中的一个元素：

```cpp
namespace Qt
{
enum Corner { TopLeftCorner, BottomRightCorner, … };
enum CaseSensitivity { CaseInsensitive,
CaseSensitive };
…
};

tabWidget->setCornerWidget(widget, Qt::TopLeftCorner);
str.indexOf("$(QTDIR)", Qt::CaseInsensitive);
```

当对枚举值进行或运算并作为某种标志（`flag`）时，传统的做法是把或运算的结果保存在`int`型的值中，这不是类型安全的。`Qt 4`提供了一个模板类，`QFlags<T>`，其中的T是枚举类型。为方便使用，`Qt`用`typedef`重新定义了`QFlag`类型，所以可以用`Qt::Alignment`代替`QFlags<Qt::AlignmentFlag>`。

习惯上，枚举类型命名为单数名词（因为它一次只能『持有』一个`flag`），把可容纳多个『`flag`』的类型用复数命名，例如：

```cpp
enum RectangleEdge { LeftEdge, RightEdge, ... };
typedef QFlags<RectangleEdge> RectangleEdges;
```

在某性情形下，这种可容纳多个`flag`的类型名称为单数形式。而枚举类型的后缀变为`Flag`：

```cpp
enum AlignmentFlag { AlignLeft, AlignTop, ... };
typedef QFlags<AlignmentFlag> Alignment;
```

## 6.4 函数和参数的命名（Naming Functions and Parameters）

函数命名的第一准则是可以从名称看出来此函数是否有副作用。在`Qt 3`中，`QString::simplifyWhiteSpace()`违反了此准则，因为它返回了一个`QString` 而不是按名称暗示的那样，改变调用它的`QString`对象。在`Qt 4`中，此函数重命名为`QString::simplified()`。

虽然参数名称不会在使用`API`的代码中出现，但是它们给程序员提供了重要信息。因为现在的`IDE`都会在写代码时显示参数名称，在头文件中给参数起一个恰当的名称并在文档中使用相同的名称很值得。

## 6.5 `bool`类型的`getter`与`setter`的命名（Naming Boolean Getters, Setters, and Properties）

为`bool`成员的获取函数(`getter`)和设置函数(`setter`)命名真痛苦。`getter`应该叫做`checked()`还是`isChecked()`？`scrollBarsEnabled()`或者`areScrollBarEnabled()`？

`Qt 4`中，我们套用以下准则为`getter`命名：

- 形容词以`is`为前缀，例子：
    - `isChecked()`
    - `isDown()`
    - `isEmpty()`
    - `isMovingEnabled()`
- 然而，修饰名词的形容词没有前缀：
    - `scrollBarsEnabled()`，而不是 `areScrollBarsEnabled()`
- 动词没有前缀，也不使用第三人称(-s)：
    - `acceptDrops(),` not `acceptsDrops()`
    - `allColumnsShowFocus()`
- 名词一般没有前缀：
    - `autoCompletion()`，而不是`isAutoCompletion()`
    - `boundaryChecking()`
- 有时，没有前缀容易混淆，我们会加上`is`前缀：
    - `isOpenGLAvailable()`，而不是`openGL()`
    - `isDialog()`，而不是`dialog()`  
　　 （一个叫做`dialog()`的函数，一般会被认为是返回`QDialog`。）

`setter`的名称来源于`getter`，只是去掉了`is`前缀，在前面加上了`set`；例如，`setDown()`与`setScrollBarsEnabled()`。

# 7. 避免常见陷阱（`avoiding common traps`）

## 7.1 简化的陷阱（`convenience trap`）

实现某样东西需要写的代码越少，`API`设计的越好这种观点是一种误解。应该记住代码只写一次，却被多次阅读和理解。例如：

```cpp
QSlider *slider = new QSlider(12, 18, 3, 13, Qt::Vertical, 0, "volume");
```

这段代码比下面这个难理解多了：

```cpp
QSlider *slider = new QSlider(Qt::Vertical);
slider->setRange(12, 18);
slider->setPageStep(3);
slider->setValue(13);
slider->setObjectName("volume");
```

## 7.2 `boolean`参数的陷阱（`boolean parameter trap`）

`bool`类型的参数总是带来无法阅读的代码。给现有的函数增加一个`bool`型的参数几乎永远是一种错误的行为。仍以`Qt`为例，`repaint()`有一个`bool`类型的可选参数用于指定背景是否被擦出。可以写出这样的代码：

```cpp
widget->repaint(false);
```

初学者很可能是这样理解的，『不要重新绘制!』（Don’t repaint!），能有多少`Qt`用户真心知道下面3行是什么意思：

```cpp
widget->repaint();
widget->repaint(true);
widget->repaint(false);
```

更好的`API`设计应该是这样的：

```cpp
widget->repaint();
widget->repaintWithoutErasing();
```

在`Qt 4`中，我们通过移除了重新绘制(`repaint`)而不擦出`widget`的能力来解决了此问题。`Qt 4`的双缓冲使这种特性被废弃。

还有更多的例子：

```cpp
widget->setSizePolicy(QSizePolicy::Fixed, QSizePolicy::Expanding, true);
textEdit->insert("Where's Waldo?", true, true, false);
QRegExp rx("moc_***.c??", false, true);
```

一种较为明显的解决方案是使用枚举值替代`bool`类型的值。我们在`Qt 4`中的`QString`使用了此方法，对下面两种方式作一个比较：

```cpp
str.replace("%USER%", user, false);               // Qt 3
str.replace("%USER%", user, Qt::CaseInsensitive); // Qt 4
```

# 8. 案例研究

## 8.1 `QProgressBar`

为了展示上文各种准则的实际应用。我们来学习一下`Qt 3`中`QProgressBar`的`API`，并与`Qt 4`中对应的`API`作比较。

在`Qt 3`中：

```cpp
class QProgressBar : public QWidget
{
    ...
public:
    int totalSteps() const;
    int progress() const;

    const QString &progressString() const;
    bool percentageVisible() const;
    void setPercentageVisible(bool);

    void setCenterIndicator(bool on);
    bool centerIndicator() const;

    void setIndicatorFollowsStyle(bool);
    bool indicatorFollowsStyle() const;

public slots:
    void reset();
    virtual void setTotalSteps(int totalSteps);
    virtual void setProgress(int progress);
    void setProgress(int progress, int totalSteps);

protected:
    virtual bool setIndicator(QString &progressStr,
                              int progress,
                              int totalSteps);
    ...
};
```

以上`API`有点复杂且不一致；例如，`reset()`，`setTotalSteps()`，`setProgress()`紧密联系，而`API`中对此不明晰。

改善此`API`的关键是注意到`QProgressBar`与`Qt 4`的`QAbstractSpinBox`及其子类`QSpinBox`,`QSlider`,`QDail`有相似之处。怎么做？把`progress`,`totalSteps`替换为`minimum`,`maximum`和`value`。增加一个valueChanged() 消息，再增加一个 setRange() 函数。

下一个发现是`progressString`, `percentage` 与 `indicator`其实是一回事：显示在进度条上的文字。通常这个文字是某个百分数，但是可通过`setIndicator()`设置为任何内容。以下是新的API：

```cpp
virtual QString text() const;
void setTextVisible(bool visible);
bool isTextVisible() const;
```

默认情况下，文字的内容是百分数，重写`text()`可以对此作改变。

`Qt 3`的`setCenterIndicator()`与`setIndicatorFollowsStyle()`是两个影响对齐的函数。他们可被一个`setAlignment()`函数代替：

```cpp
void setAlignment(Qt::Alignment alignment);
```

如果API用户未调用`setAlignment()`，那么对齐方式由风格决定。对于基于`Motif`的风格，文字内容在中间显示；对于其他风格，在右侧显示。

下面是已经改善的`QProgressBar` `API`:

```cpp
class QProgressBar : public QWidget
{
    ...
public:
    void setMinimum(int minimum);
    int minimum() const;
    void setMaximum(int maximum);
    int maximum() const;
    void setRange(int minimum, int maximum);
    int value() const;

    virtual QString text() const;
    void setTextVisible(bool visible);
    bool isTextVisible() const;
    Qt::Alignment alignment() const;
    void setAlignment(Qt::Alignment alignment);

public slots:
    void reset();
    void setValue(int value);

signals:
    void valueChanged(int value);
    ...
};
```

## 8.2 `QAbstractPrintDialog` & `QAbstractPageSizeDialog`


## 8.3 `QAbstractItemModel`

有关模型/视图(`model`/`view`)的问题的细节在其他地方已经作了描述，但是有一个重要的总结是某个抽象的类不应该仅仅是联合(`union`)所有可能的子类的。这样的抽象基类几乎不是一个好的方案。`QAbstractItemModel`就犯了这个错误——它其实是`QTreeOfTablesModel`，包含了一些复杂的`API`，但却被很多类继承。仅仅是增加抽象不会把`API`设计得更好。

## 8.4 `QLayoutIterator` & `QGLayoutIterator`

## 8.5 `QImageSink`

`Qt 3`有多个类用来逐渐加载图像并做成动画 —— `QImageSource`/`Sink`/`QASyncIO`/`QASyncImageIO`。由于他们完全可由变换的`QLabel`代替，所以这些类全都被移除了。我们获得的教训是不要通过增加抽象来应对某些模糊的未来情况。保持简洁，当这些情况出现时，把它们纳入一个简单的系统要比纳入一个复杂的系统容易得多。

