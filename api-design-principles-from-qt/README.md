原文链接：[API Design Principles](http://qt-project.org/wiki/API-Design-Principles)  
基于[Gary的影响力](http://blog.csdn.net/gaoyingju)上 _Gary Gao_ 的译文稿：[C++的API设计指导](http://blog.csdn.net/gaoyingju/article/details/8245108)

# C++的API设计指导

# 摘要：

此文为Qt 官网上的API设计(for C++)指导准则，其中有不少原则具有普遍适用性，整个篇幅中有很多示例，是Qt在API设计上的实践。　

# 正文　　

Qt 一致、易掌握、强大的API是它的众多著名的优点之一。此文总结了我们在设计Qt风格的API所积累的做法。其中许多准则是通用的；而其他内容更偏向与约定，遵守它主要是为了与已有的API保持一致。　　　　

虽然这些准则主要用于公共API， 你也可以在设计私有API时使用它们，把它作为与其他开发者之间的约定。

# 1. 好API的6个特点（Six Characteristics of Good API）

API对与程序员的关系就如同GUI与用户的关系。API中的’P’实际上指的是’程序员’，而不是’程序’，它强调了所有API都是由程序员使用的事实。

Matthias 在他的Qt Quarterly 13 article about API design 中说他相信API应该很少并且是完整的，有清晰简单的语义，可凭直觉知道的，容易记忆而且能产生可读的代码。

数量最小化(Be minimal)

数量最小化的API拥有尽可能少的公有类，每个公有类的公有成员也很少。依次可使理解，记忆，调试，改变API更容易。

功能完整(Be complete)

完整的API包含了所有需要用到的功能。这和API的数量最小化有点冲突。另外，如果某个成员函数放到了错误的类里，需要使用此方法的用户就会找不到它。

清晰简单的语义(Have clear and simple semantics)

如同其他的设计，我们应该遵守意外最少的原则(principle of least surprise)，把常用的操作变得容易，很少用的操作不应该很显眼。不要把没必要通用的解决方案普遍化。例如，在Qt 3 中，QMimeSourceFactory 不应被命名为 QImageLoader。

直觉性强(Be intuitive)

API 应该有较强的直觉性，就像计算机里的其他内容，。不同的履历和背景导致了不同人有不同直觉。如果一个经验不很丰富的用户没有阅读文档就能搞懂某个API，明白此API构成的代码，说明此API直觉性较强。

容易记忆( Be easy to memorize)

为使API易于记忆，应选择一个具有一致性和精确性的命名约定。使用易于识别的模式和概念，避免使用缩写。

能写出可读代码(Lead to readable code)

代码只写一次，但是却被浏览，调试，改变很多次。可读性好的代码可能需要更长的时间来写，却能在产品的整个生命周期中节省时间。

最后，记住不同的用户使用不同部分的API。尽管记住简单得使用一个Qt类的示例更直接，最好还是告诉用户在使用API时阅读相关一下文档。

# 2.静态多态(Static Polymorphism)

相似的类应该有相似的API。运行时多态通过继承体系实现。然而多态也发生在设计阶段。例如，如果你用QProgressBar替换QSlider，或者是QString替换QByteArray，你会发现API的简洁性使替换如此容易。此所谓“静态多态”。

静态多态也使记忆API和编程模式更加容易。因此，一组有相似接口的相关类比每个类都有自己的一套接口更好。

总体上讲，在Qt中，比起没有强有力的理由实现的继承关系，我们跟喜欢依赖静态多态。这种做法可确保公有类较少，使刚学习Qt的用户认清路线。

好的静态多态

QDialogButtonBox与 QMessageBox 在按钮操作(addButton(), setStandardButtons()等等 )有相似的API，而没有继承某个”QAbstractButtonBox”的类。

糟糕的静态多态

QTcpSocket 与 QUdpSocket 都继承了 QAbstractSocket ，它们是两个行为非常不同的类。似乎没有什么人曾经或会去使用一个QAbstractSocket指针。

不能确定的静态多态

QBoxLayout 是 QHBoxLayout 与 QVBoxLayout 的基类，好处：可以在工具栏中使用QBoxLayout调用setOrientation() 使其变为水平/垂直。坏处：需要一个额外的类，并且有可能导致用户写出这样没什么意义的代码，((QBoxLayout *)hbox)->setOrientation(Qt::Vertical)。

# 3. 基于特性的API(Property-Based APIs )

新的Qt类倾向于基于特性的API，例如：

```cpp
QTimer timer;
timer.setInterval(1000);
timer.setSingleShot(true);
timer.start();
```

特性，是指此对象的任何属性——无论它是否是Q_PROPERTY。在实践中，用户应能够以任何顺序设置这些特性，也就是说，这些特性应该是正交的。例如，之前的代码可以写成：

```cpp
QTimer timer;
timer.setSingleShot(true);
timer.setInterval(1000);
timer.start();
```

【译者注：正交特性：改变某个特性而不会影响到其他的特性。《程序员修炼之道》中讲了一个关于正交性的直升飞机坠毁的例子。】

方便起见，也写成：

```cpp
timer.start(1000)；
```

类似地，对于QRegExp,

```cpp
QRegExp regExp;
regExp.setCaseSensitive(Qt::CaseInsensitive);
regExp.setPattern("***.*");
regExp.setPatternSyntax(Qt::WildcardSyntax);
```

为实现这种类型的API，最好不要过早构建目标。例如，在QRegExp的例子中，在不知道模式语法的时候，在setPattern()中编译"***.*"为时过早。

各种特性经常关联在一起。在上面的例子中，我们必须小心。思考一下当前风格提供的“默认的图标尺寸”和QToolButton的”iconSize”属性：

```cpp
toolButton->setStyle(otherStyle);
toolButton->iconSize();    // returns the default for otherStyle
toolButton->setIconSize(QSize(52, 52));
toolButton->iconSize();    // returns (52, 52)
toolButton->setStyle(yetAnotherStyle);
toolButton->iconSize();    // returns (52, 52)
```

需要提醒的是，一旦设置了 iconSize，它就一直保持设置状态；改变当前的风格不会改变此事。这样很好。有时候，重置某个特性也很有用，有两种方法：

（1）传入一个特殊值 (如 QSize(), -1, 或者 Qt::Alignment(0)) ，意味着重置

（2）提供一个明确的重置接口，如 resetFoo() 和 unsetFoo()对于iconSize,使用Qsize()就足够了。

在某些情况下，getters 返回的结果与设置的可能不同。例如，如果调用widget->setEnabled(true)，如果它的parent处于disabled状态，widget->isEnabled()仍然返回 false。这样是可行的，正是我们想要的(widget的父widget处于disabled状态，此widget也应该变为灰色，就好象也处于disabled状态一样，但是它会记得，其本身并没有处于disabled状态，正等待它的父widget变为enabled.)，但是诸如这样的特性必须被详细列入文档。

# 4.C++ API 规范(C++ Specifics)

指针与引用(Pointers vs. References)

最好的输出参数的类型是什么，指针还是引用？

```cpp
void getHsv(int *h, int *s, int *v) const
void getHsv(int &h, int &s, int &v) const
```

大多数C++书籍基于引用比指针“安全和优雅”的观点，推荐尽可能使用引用。相比之下，我们在开发Qt时更喜欢指针，因为使用指针可使用户代码可读性更好。比较下面两个例子：

```cpp
color.getHsv(&h, &s, &v);
color.getHsv(h, s, v);
```

仅有第一行代码充分显示h，s，v 很可能被此函数调用修改。

虚函数(Virtual Functions)

类的成员函数声明为virtual，一般是为了通过其子类实现此函数来定制函数的行为。将函数声明为virtual的目的是为了实现动态多态。如果在类外面没有人调用声明为virtual的函数，将其声明为virtual之前，你应该多加小心。

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

QTextEdit从Qt3 移植到Qt4 的时候，几乎所有的虚函数都被移除了。有趣的是(不过并不是没有预料到的)，没有人对此抱怨，为什么？因为Qt3根本没用到QTextEdit的多态行为。简单得说，没有理由去继承QTextEdit并重新实现这些函数，除非你自己调用了这些方法。如果你需要在你的应用程序，其中不属于Qt的部分实现多态，你会自己增加。
避免虚函数(Avoid virtual functions)

在Qt中，我们有很多理由尽量减少虚函数的数量。每一次对虚函数的调用会在函数调用图中插入一个未掌控的节点(某种程度上使结果无法预测)，使修改bug变得复杂。用户在重新实现的虚函数中能做很多事：

—— 发送事件　　

——发送信号　　

——从新进入事件循环(例如，通过打开一个模式文件对话框)　　

——删除对象(导致了delete this)

还有一些其他原因说明应避免过分使用虚函数：

（1）重载虚函数有难度。

（2）编译器很难优化虚函数或者让其变为inline函数。

（3）调用虚函数需要查找虚函数表，这比调用一个普通函数慢了2到3倍。

（4）虚函数使类很难按值复制(可以做到，但是非常混乱，不建议这样做)

经验告诉我们，没有虚函数的类一般有更少的bug，需要更少的维护成本。

一个重要的准则是除非我们作为工具集提供而且很多用户会调用某类的某个虚函数，否则不会这个函数设计成虚函数。

虚函数与复制(Virtualness vs. copyability)

包含虚函数的类必须把析构函数声明为虚函数，以防止基类析构时没有执行子类析构函数，导致内存泄漏。

如果要使某个类可以复制可以赋值，或者能够按值比较，应该需要一个拷贝构造函数，一个operator=() 和一个 operator==()。

```cpp
class CopyClass {
public:
    CopyClass();
    CopyClass(const CopyClass &other);
    ~CopyClass();
    CopyClass &operator=(const CopyClass &other);
    bool operator== (const CopyClass &other) const;
    bool operator!=(const CopyClass &other) const;

    virtual void setValue(int v);
};
```

如果有的类从CopyClass继承，未预料到的事就可能发生在代码中。一般情况下，如果没有虚成员函数和虚析构函数，就不能有依赖多态的子类。然而，如果存在虚成员函数和虚析构函数，这突然变成了要有子类去继承某个类的理由，而且也变的复杂了。最初容易获知只要简单得声明虚的操作符重载函数。但是如此下去会导致一篇混乱和不可读的代码。见如下例子：

```cpp
class OtherClass {
public:
    const CopyClass &instance() const; // what does it return? What should I assign it to?
};
```

常数(Constness)

C++ 的 关键词“const”表明了某些内容不可变或者没有副作用，它适用与简单的值，指针，指针所指的内容，或者类的成员函数。

然而，const并没有提供太大的价值——很多编程语言甚至没有类似”const”的关键词，但是却并没有因此产生问题。实际上，如果你不用函数重载，并删除你的C++代码中所有的”const”，它也几乎能编译通过并且正常运行。

让我们看一下与Qt的API设计相关的应用”const”的部分。

输入参数：const 类型的指针(Input arguments:const pointers)

参数是指针类型类型的const成员函数，它们的指针参数几乎总是const类型的。

若将函数声明为 const类型的(例如：bool func() const;)，意味着它既没有副作用，也不会改变对象的状态。那为什么它需要一个没有const限定的输入参数呢？记住const类型的函数通常被其他const类型的函数调用，所以不能传入非const的指针(没有用const_cast，应该尽量避免使用const_cast)。

以前：

```cpp
bool QWidget::isVisibleTo(QWidget *ancestor) const;
bool QWidget::isEnabledTo(QWidget *ancestor) const;
QPoint QWidget::mapFrom(QWidget *ancestor, const QPoint &pos) const;
```

QWidget 声明了许多参数本身可变的const类型的成员函数，这些函数可以修改传入的参数，不能修改对象自身。这样的函数通常也包含了const_cast转换。这些函数能最好接受的是const的参数。

之后：

```cpp
bool QWidget::isVisibleTo(const QWidget *ancestor) const;
bool QWidget::isEnabledTo(const QWidget *ancestor) const;
QPoint QWidget::mapFrom(const QWidget *ancestor, const QPoint &pos) const;
```

请注意，我们在QGraphicsItem中对此做了修正，但是QWidget 要等到 Qt 5:

```cpp
bool isVisibleTo(const QGraphicsItem *parent) const;
QPointF mapFromItem (const QGraphicsItem *item, const QPointF &point) const;
```

返回值：const类型的值(Return values:const values)

调用函数获得的非引用类型的值，称之为右值(R-value)。

不是类的对象的右值一般没有const限定(cv-unqualified type)。 虽然从语法上讲，用const限定这种类型也可以，但是却没有意义，因为这不改变任何与访问权限有关的内容。

【译者注：cv-qualified的类型(与cv-unqualified 相反) 是由const 或者volatile 或者volatile const限定的类型。】

大部分编译器在编译这样的代码时会提示警告信息。

若用”const”限定作为右值的类的对象，访问这个对象非const类型的成员函数或直接修改它的成员都是不可能的。

不加const可以去除以上限制，但随着作为右值的对象生命周期在分号出现时结束，这样做几乎没有必要。

示例：

```cpp
 struct Foo
 {
 void setValue(int v) { value h1. v; }
 int value;
 };

Foo foo()
 {
 return Foo();
 }

const Foo cfoo()
 {
 return Foo();
 }

int main()
 {
 // The following does compile, foo() is non-const R-value which
 // can't be assigned to (this generally requires an L-value)
 // but member access leads to a L-value:
 foo().value h1. 1; // Ok, but temporary will be thrown away at the end of the full-expression.

// The following does compile, foo() is non-const R-value which
 // can't be assigned to, but calling (even non-const) member
 // function is fine:
 foo().setValue(1); // Ok, but temporary will be thrown away at the end of the full-expression.

// The following does _not_compile, foo() is ''const'' R-value
 // with const member which member access can't be assigned to:
 cfoo().value h1. 1; // Not ok.

// The following does _not_compile, foo() is ''const'' R-value,
 // one cannot call non-const member functions:
 cfoo().setValue(1); // Not ok
 }
```

返回值：没有const的指针还是有const的指针(Return values:pointers vs. const pointers)

谈到const类型的函数应该返回非const的指针还是const指针的话题时，多数人发现“const 正确性”(const correctness)在C++中产生了分歧。问题源于const类型的成员函数，其本身不修改对象的状态，返回了对象的非const的成员的指针。仅仅是返回这样的指针不会影响对象的状态，也不改变函数的行为，但却给其他程序员修改对象状态的机会。下面的例子展示了调用返回值是没有const限定的指针的用const限定的函数，却能规避const特性的一种方式。

```cpp
QVariant CustomWidget::inputMethodQuery(Qt::InputMethodQuery query) const
{
moveBy(10, 10); // doesn't compile!
window()->childAt(mapTo(window(), rect().center()))->moveBy(10, 10); // compiles!
}
```

返回const指针的函数至少从一定程度上避免了这种副作用(也许是不期望的，没有预料到的)的产生。

若采用const-correct的方法，每个返回某个数据成员的指针(或多个数据成员的指针)的const类型的函数必须返回const的指针。在实践中，这种做法将导致没有用的API：

```cpp
QGraphicsScene scene;
// … populate scene

foreach (const QGraphicsItem *item, scene.items()) {
item->setPos(qrand() % 500, qrand() % 500); // doesn't compile! item is a const pointer
}
```

在Qt中，我们根据特定情况使用非const。我们找到了一个实用的方法：当返回const指针后，招致过分使用const_cast带来的问题多于滥用返回非const的指针时，我们会选择返回f非const的指针。

返回值：返回值还是返回const的引用(Return values: by value or const reference?)

若返回的是复制的对象，那么返回const引用可以更快；然而，以后对这个类的重构(refactor)将受限。我们可以任意改变Qt类在内存中的组织形式，但却不能在不破坏程序兼容性的情况下把返回值从“const QFoo &”变为”QFoo”。改变因此，除去个别速度非常重要而重构不是问题的情形(例如，QList::at())外，我们一般返回”QFoo”而不是”const QFoo &”。

Const 还是对象的状态(Const vs. the state of an object )

C++中有关Const 正确性(const coreectness)的问题就像vi 和emacs，因为这个问题在很多地方都存在分歧(比如包含指针的函数)。

但是通用的准则是const类型的成员函数不改变对象状态。“状态”的意思是“自身以及自身的行为”。这并不是说非const的成员函数要改变私有成员，而是这样的函数存在可见的副作用(visible side effects)。const类型的函数一般没有什么副作用，比如：

```cpp
QSize size = widget->sizeHint(); // const
widget->move(10, 10); // not const
```

某个widget负责绘制其他的内容。它的状态包括它的行为，因此也包括了它画出来的对象的状态。调用它的绘画行为必然会有副作用；它改变了它绘制的设备的外观(以及状态)。由此，用const限定paint()完全没有必要。QIcon的paint()也不需要是const的，没有人会在const类型函数的内部调用QIcon::paint()，除非他想显式的回避const这个特性。如果是前述情况，应使用const_cast。

```cpp
// QAbstractItemDelegate::paint is const
void QAbstractItemDelegate::paint(QPainter **painter, const QStyleOptionViewItem &option, const QModelIndex &index) const

// QGraphicsItem::paint is not const
void QGraphicsItem::paint(QPainter * painter, const QStyleOptionGraphicsItem **option, QWidget *widget h1\. 0)
```

如果const关键字不起作用，应该考虑将其移除而不是重载函数的non-const版本。

# 5.API语义和文档(API Semantics and Documentation)

如果传入了一个值为-1的参数，函数的行为是什么？警告、致命错误还是....有很多类似的问题...

API需要的是质量保证。第一个版本的API不可能是没问题的；必须对其进行[测试](http://lib.csdn.net/base/softwaretest "软件测试知识库")。阅读使用API的代码，发现用例并且检查代码的可读性。

其他的方法包括让其他人在有文档(有关类及其方法的文档)或没有文档辅助的情况下使用你的API。

# 6.命名的艺术(The Art of Naming)

命名很可能是设计API时最简单最重要的方面。各个类的名称如何确定？成员函数名称如何确定？

**通用的命名规则(General Naming Rules)**

有几个规则对所有的命名都适用。第一个是，之前已经说过的，不要使用缩写，即使是明显的缩写，比如把”previous”缩写成”prev”从长远来看并不值，因为用户必须记住缩写词的实际含义。

如果API本身一致性不好，事情就会越来越糟；例如，Qt3 中同时存在activatePreviousWindow()与fetchPrev()。恪守“不缩写”规则使建立一致性的API更容易。

另一个在设计类时重要但是更微妙的规则是应该保持子类名称空间的干净。在Qt3中，此项准则没有被一直追随。为了对此进行说明，我们以QToolButton为例。如果调用某个QToolButton的 name()、caption()、text()或者textLabel()，你期望获得什么？ 在Qt Designer试着研究一下QToolButton:

（1）name函数是从QObject继承而来，它返回的是内部的对象名称，可以被用来debug或者测试。　

　（2）caption函数继承自QWidget ，它返回的是窗口标题，对QToolButton来说毫无意义，因为它在创建的时候parent就存在了。

　（3）text函数继承自QButton，一般被Button使用，useTextLabel为true使不使用。　

　（4）textLabel是在QToolButton内部定义的，只有useTextLabel为true时，其内容才显示在Button上。

为了具有可读性，name在Qt4中叫做objectName，caption改为windowTitle，QToolButton中再也没有textLabel了。

当你找不到好的名称时，开始写文档是一种好好的寻找方式：尝试为类、方法、枚举类型、值等写文档，把写下的第一句作为启发。如果找不确切的名称，这说明这个东西不该存在。如果所有尝试都失败了，并且你认为不如发明一个新名称，你就知道”widget”，“event”,”focus”和”buddy”是如何产生的了。

**类的命名(Naming Classes)**

用把类的名称分组的方式替换为每个类单独命名的方法。例如，所有Qt4的了解模型(model-aware)的视图(view)类后缀都是View（QListView,QTableView,QTreeView）,相应的基于item的类后缀是Widget（QListWidget,QTableWidget,QTreeWidget）。

**枚举类型和值的命名(Naming Enum Types and Values)**

C++中枚举值没有类型(与[Java](http://lib.csdn.net/base/java "Java 知识库"),C#不同)，声明枚举类型时需要记住这一点。下面的例子说明了给枚举值起过于通用的名字的危害：

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

在最后一行，Insensitive是什么意思？(容易引起混淆) 。命名枚举类型的一个准则是在枚举值至少重复此枚举类型名称中的一个元素：

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

当对枚举值进行或运算并作为某种标志(flag)时，传统的做法是把或运算的结果保存在int型的值中，这不是类型安全的。Qt4提供了一个模板类，QFlags<T>，其中的T是枚举类型。为方便使用，Qt用typedef重新定义了QFlag类型， 所以可以用Qt::Alignment 代替QFlags<Qt::AlignmentFlag>。

习惯上，枚举类型命名为单数名词(因为它一次只能“持有”一个flag)，把可容纳多个”flag”的类型用复数命名，例如：

```cpp
enum RectangleEdge { LeftEdge, RightEdge, ... };
typedef QFlags<RectangleEdge> RectangleEdges;
```

在某性情形下，这种可容纳多个”flag”的类型名称为单数形式。而枚举类型的后缀变为 “Flag”:

```cpp
enum AlignmentFlag { AlignLeft, AlignTop, ... };
typedef QFlags<AlignmentFlag> Alignment;
```

**函数和参数的命名(Naming Functions and Parameters)**

函数命名的第一准则是可以从名称看出来此函数是否有副作用。在Qt3中，QString::simplifyWhiteSpace() 违反了此准则，因为它返回了一个QString 而不是按名称暗示的那样，改变调用它的QString对象。在Qt4中，此函数重命名为QString::simplified()。

虽然参数名称不会在使用API的代码中出现，但是它们给程序员提供了重要信息。因为现在的IDE都会在写代码时显示参数名称，在头文件中给参数起一个恰当的名称并在文档中使用相同的名称很值得。

**Bool类型的getter与setter的命名(Naming Boolean Getters, Setters, and Properties )**

为bool成员的获取函数(getter)和设置函数(setter)命名真痛苦。Getter应该叫做checked()还是isChecked()? scrollBarsEnabled() 或者areScrollBarEnabled()?

Qt4中，我们套用以下准则为getter命名：

（1）形容词以is-为前缀，例子：

　　isChecked(),

　　isDown() ,

　　isEmpty(),

　　isMovingEnabled()

（2）然而，修饰名词的形容词没有前缀：

　　scrollBarsEnabled(), 而不是 areScrollBarsEnabled()

（3）动词没有前缀，也不使用第三人称(-s)：

　　acceptDrops(), not acceptsDrops()

　　allColumnsShowFocus()

（4）名词一般没有前缀：

autoCompletion(), 而不是isAutoCompletion()

　　boundaryChecking()

（5） 有时，没有前缀容易混淆，我们会加上is-前缀：

　　isOpenGLAvailable(), 而不是 openGL()

　　isDialog(), 而不是 dialog()

　　 (一个叫做dialog()的函数，一般会被认为是返回 QDialog **。)

Setter的名称来源于getter，只是去掉了is-前缀，在前面加上了set；例如，setDown() 与setScrollBarsEnabled()。

# 7.避免常见陷阱(Avoiding Common Traps)

**简化的陷阱(The Convenience Trap)**

实现某样东西需要写的代码越少，API设计的越好这种观点是一种误解。应该记住代码只写一次，却被多次阅读和理解。例如：

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

**Boolean值的陷阱(The Boolean Parameter Trap )**

Bool类型的参数总是带来无法阅读的代码。给现有的函数增加一个bool型的参数几乎永远是一种错误的行为。仍以Qt为例，repaint()有一个bool类型的可选参数用于指定背景是否被擦出。可以写出这样的代码：

```cpp
widget->repaint(false);
```

初学者很可能是这样理解的，”不要重新绘制!”(Don’t repaint!)，能有多少Qt用户真心知道下面3行是什么意思：

```cpp
widget->repaint();
widget->repaint(true);
widget->repaint(false);
```

更好的API设计应该是这样的：

```cpp
widget->repaint();
widget->repaintWithoutErasing();
```

在Qt4中，我们通过移除了重新绘制(repaint)而不擦出widget的能力来解决了此问题。Qt4的双缓冲使这种特性被废弃。

还有更多的例子：

```cpp
widget->setSizePolicy(QSizePolicy::Fixed, QSizePolicy::Expanding, true);
textEdit->insert("Where's Waldo?", true, true, false);
QRegExp rx("moc_***.c??", false, true);
```

一种较为明显的解决方案是使用枚举值替代bool类型的值。我们在Qt4中的QString使用了此方法，对下面两种方式作一个比较：

```cpp
str.replace("%USER%", user, false);               // Qt 3
str.replace("%USER%", user, Qt::CaseInsensitive); // Qt 4
```

# 8.案例研究(Case Studies)  

**进度条(QProgressBar)**  

为了展示上文各种准则的实际应用。我们来学习一下Qt3中QProgressBar的API，并与Qt4中对应的API作比较。

在Qt3中：

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

以上API有点复杂且不一致；例如，reset()，setTotalSteps()，setProgress()紧密联系，而API中对此不明晰。

改善此API的关键是注意到QProgressBar与Qt4的QAbstractSpinBox及其子类QSpinBox,QSlider,QDail有相似之处。怎么做？把progress,totalSteps替换为minimum,maximum和value。增加一个valueChanged() 消息，再增加一个 setRange() 函数。

下一个发现是progressString, percentage 与 indicator其实是一回事：显示在进度条上的文字。通常这个文字是某个百分数，但是可通过setIndicator()设置为任何内容。以下是新的API：

```cpp
virtual QString text() const;
void setTextVisible(bool visible);
bool isTextVisible() const;
```

默认情况下，文字的内容是百分数，重写text()可以对此作改变。

Qt3的setCenterIndicator() 与 setIndicatorFollowsStyle() 是两个影响对齐的函数。他们可被一个setAlignment()函数代替：

```cpp
void setAlignment(Qt::Alignment alignment);
```

如果API用户未调用setAlignment()，那么对齐方式由风格决定。对于基于Motif的风格，文字内容在中间显示；对于其他风格，在右侧显示。

下面是已经改善的QProgressBar API:

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

**QAbstractItemModel**

有关模型/视图(model/view)的问题的细节在其他地方已经作了描述，但是有一个重要的总结是某个抽象的类不应该仅仅是联合(union)所有可能的子类的。这样的抽象基类几乎不是一个好的方案。QAbstractItemModel 就犯了这个错误——它其实是QTreeOfTablesModel，包含了一些复杂的API，但却被很多类继承。仅仅是增加抽象不会把API设计得更好。

**QImageSink**

Qt3有多个类用来逐渐加载图像并做成动画——QImageSource/Sink/QASyncIO/QASyncImageIO。由于他们完全可由变换的QLabel代替，所以这些类全都被移除了。我们获得的教训是不要通过增加抽象来应对某些模糊的未来情况。保持简洁，当这些情况出现时， 把它们纳入一个简单的系统要比纳入一个复杂的系统容易得多。

**Materials**：

[1] Little Manual of API Design[http://chaos.troll.no/~shausman/api-design/api-design.pdf](http://chaos.troll.no/~shausman/api-design/api-design.pdf)

[2] Qt Quarterly 13 article about API design [http://doc.qt.nokia.com/qq/qq13-apis.html](http://doc.qt.nokia.com/qq/qq13-apis.html)

[3] “The Pragmatic Programmer”[-《程序员修炼之道》](http://book.douban.com/subject/1152111/)(强烈推荐此书，绝对是必读之书)

转载本文请注明作者和出处[Gary的影响力]http://garyelephant.me，请勿用于任何商业用途！

Author: Gary Gao 关注互联网、分布式、高并发、自动化、软件团队
支持我的工作： [https://me.alipay.com/garygao](https://me.alipay.com/garygao)
