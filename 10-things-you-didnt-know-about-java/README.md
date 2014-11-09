原文链接： [10 Things You Didn’t Know About Java](http://blog.jooq.org/2014/11/03/10-things-you-didnt-know-about-java/)

关于Java你可能不知道的10件事
===============================================

嗯，也许你写`Java`代码已经有些年头了。还记得吗这些吗：
那些年，它还叫做`Oak`；那些年，`OO`还是个热门话题；那些年，`C++`同学们觉得`Java`是没有出路的；那些年，`Applet`还风头正劲……

但我赌你下面的这些事你至少有一半还不知道。来聊聊这些让你有些惊讶的`Java`内部的事儿。

1. 其实不存在受检异常（`checked exception`）
---------------------------------------

是的！`JVM`并不感知这个，只有`Java`语言感知。

今天，大家都赞同，受检异常是个设计失误，`Java`语言的设计失误。正如 *Bruce Eckel* 在布拉格的[`GeeCON`会议上最后一页的演示](http://www.geecon.cz/speakers/?id=2)中说的，
`Java`之后语言都不会再有受检异常，甚至在`Java` 8中新式流`API`（`Streams API`）都不再拥抱受检异常
（[以`lambda`的方式来使用`IO`和`JDBC`，这个`API`用起来还是有些痛苦的](http://blog.jooq.org/2014/05/23/java-8-friday-better-exceptions/)。）

想证明`JVM`不感知受检异常？试试下面的这段代码：

```java
public class Test {
  
    // No throws clause here
    public static void main(String[] args) {
        doThrow(new SQLException());
    }
  
    static void doThrow(Exception e) {
        Test.<RuntimeException> doThrow0(e);
    }
  
    @SuppressWarnings("unchecked")
    static <E extends Exception>
    void doThrow0(Exception e) throws E {
        throw (E) e;
    }
}
```

不仅可以编译通过，并且也抛出了`SQLException`，你甚至都不需要用上`Lombok`的[`@SneakyThrows`](http://projectlombok.org/features/SneakyThrows.html)。

更多细节，可以在看看[这篇文章](http://blog.jooq.org/2012/09/14/throw-checked-exceptions-like-runtime-exceptions-in-java/)，或`Stack Overflow`上的[这个问题](http://stackoverflow.com/q/12580598/521799)。

2. 只有返回类型不同的方法重载
---------------------------------------

下面的代码不能编译，是吧？

```java
class Test {
    Object x() { return "abc"; }
    String x() { return "123"; }
}
```

是的！`Java`语言不允许一个类里有2个方法是『*重载一致*』的，而不会关心2个方法的`throws`子句或返回类型实际是不一样的。

但是等等！来看看来看看[`Class.getMethod(String, Class...)`](http://docs.oracle.com/javase/8/docs/api/java/lang/Class.html#getMethod-java.lang.String-java.lang.Class...-)方法的`Javadoc`：

> 注意，可能在一个类中会有多个匹配的方法，因为尽管`Java`语言禁止在一个类中多个方法签名相同只是返回类型不同的方法，但是`JVM`并不禁止。
这让`JVM`上可以更灵活地去实现各种语言特性。比如，可以用桥方法来实现方法的协变返回类型；桥方法和被重载的方法可以有相同的方法签名，但返回类型不同。

哦，这个说的通。事实上，当你这写下面的代码时，就发生了这样的情况：

```java
abstract class Parent<T> {
    abstract T x();
}
 
class Child extends Parent<String> {
    @Override
    String x() { return "abc"; }
}
```

查一下`Child`类生成的字节码：

```java
// Method descriptor #15 ()Ljava/lang/String;
// Stack: 1, Locals: 1
java.lang.String x();
  0  ldc <String "abc"> [16]
  2  areturn
    Line numbers:
      [pc: 0, line: 7]
    Local variable table:
      [pc: 0, pc: 3] local: this index: 0 type: Child
 
// Method descriptor #18 ()Ljava/lang/Object;
// Stack: 1, Locals: 1
bridge synthetic java.lang.Object x();
  0  aload_0 [this]
  1  invokevirtual Child.x() : java.lang.String [19]
  4  areturn
    Line numbers:
      [pc: 0, line: 1]
```

在字节码中，`T`实际上是`Object`类型。这很好理解。

合成的桥方法实际上是由编辑器生成的，因为在一些调用场景下，`Parent.x()`方法签名的返回类型期望是`Object`。

添加泛型而不生成这个桥方法，不可能做到二进制兼容。
所以，让`JVM`允许这个特性，可以愉快解决这个问题（实际是允许协变返回类型的方法中包含有副作用的逻辑）。
聪明不？嘿嘿。

你是不是想要扎入语言规范和内核？可以在[这里](http://stackoverflow.com/q/442026/521799)找到更多有意思的细节。

3. 这些写法都是二维数组！
---------------------------------------

```java
class Test {
    int[][] a()  { return new int[0][]; }
    int[] b() [] { return new int[0][]; }
    int c() [][] { return new int[0][]; }
}
```

是的，这是真的。尽管人肉解析器不能马上理解上面这些方法的返回类型，但都是一样的。下面的代码也类似：

```java
class Test {
    int[][] a = {{}};
    int[] b[] = {{}};
    int c[][] = {{}};
}
```

是不是觉得很2B？想象一下在上面的代码中使用[`JSR-308`或是`Java` 8的类型注解](https://jcp.org/en/jsr/detail?id=308)。
语法糖的数目要爆炸了吧！

```java
@Target(ElementType.TYPE_USE)
@interface Crazy {}
 
class Test {
    @Crazy int[][]  a1 = {{}};
    int @Crazy [][] a2 = {{}};
    int[] @Crazy [] a3 = {{}};
 
    @Crazy int[] b1[]  = {{}};
    int @Crazy [] b2[] = {{}};
    int[] b3 @Crazy [] = {{}};
 
    @Crazy int c1[][]  = {{}};
    int c2 @Crazy [][] = {{}};
    int c3[] @Crazy [] = {{}};
}
```

> 类型注解。这个设计引入的诡异大大超出了它解决的问题。

或换句话说：

> 在4周休假前的最后一个提交里，我写了这样的代码，然后。。。
![](hexhyZ8.jpg)  
【***译注***】画外音：然后，亲爱的同事你，就有得火救啦，哼，哼哼，哦哈哈哈哈~

找出上面用法的合适的使用场景，还是留给你作为一个练习吧。

4. 其实并没有条件表达式
---------------------------------------

所以，你认为自己知道什么时候使用条件表达式？让我告诉你，你不知道。大部分人会下面的2个代码段是等价的：

```java
Object o1 = true ? new Integer(1) : new Double(2.0);
```

等同于：

```java
Object o2;
 
if (true)
    o2 = new Integer(1);
else
    o2 = new Double(2.0);
```

让你失望了。来做个简单的测试吧：

```java
System.out.println(o1);
System.out.println(o2);
```

打印结果是：

```
1.0
1
```

哦！条件运算符会实现数值类型的转型，如果『需要』，这个『需要』有非常非常非常强的引号。你觉得下面的程序会不会抛`NullPointerException`：

```java
Integer i = new Integer(1);
if (i.equals(1))
    i = null;
Double d = new Double(2.0);
Object o = true ? i : d; // NullPointerException!
System.out.println(o);
```

关于这一条的更多的信息可以在[这里](http://blog.jooq.org/2013/10/08/java-auto-unboxing-gotcha-beware/)找到。

5. 其实并没有复合赋值运算符
---------------------------------------

是不是够诡异？来看看下面的2行代码：

```java
i += j;
i = i + j;
```

直觉上认为，2行代码是等价的，对吧？但结果即不是！`JLS`（`Java`语言规范）指出：

> 复合赋值运算符表达式 `E1 op= E2` 等价于 `E1 = (T)((E1) op (E2))`
> 其中`T`是`E1`的类型，并且`E1`只会求值一次。

这个做法太漂亮了，我引用[*Peter Lawrey*](https://twitter.com/PeterLawrey)在`Stack Overflow`上的[回答](http://stackoverflow.com/a/8710747/521799)来说明这点：

使用`*=`或`/=`作为例子可以方便说明其中的转型问题：

```java
byte b = 10;
b *= 5.7;
System.out.println(b); // prints 57

byte b = 100;
b /= 2.5;
System.out.println(b); // prints 40

char ch = '0';
ch *= 1.1;
System.out.println(ch); // prints '4'

char ch = 'A';
ch *= 1.5;
System.out.println(ch); // prints 'a'
```

为什么这个真有太有用了？如果我要在代码中，就地对字符做转型或是乘法。然后，你懂的……

6. 随机`Integer`
---------------------------------------

这个问题比迷题还迷题。先不要看解答，看看你能不能自己找出问题。运行下面的代码：

```java
for (int i = 0; i < 10; i++) {
  System.out.println((Integer) i);
}
```

…… 然后你可能得到下面的输出：

```
92
221
45
48
236
183
39
193
33
84
```

这怎么可能？！

. 

. 

. 

. 

. 

. 

. 我要剧透了…… 解答走起……

. 

. 

. 

. 

. 

. 

好吧，解答在这里(<http://blog.jooq.org/2013/10/17/add-some-entropy-to-your-jvm/>)，
这和用反射覆盖`JDK`的`Integer`缓存，然后使用自动打包解包（`auto-boxing`/`auto-unboxing`）相关。
别在家里这么玩！或换句话说，想想会有这样的状况（我再说一遍吧:）：

> 在4周休假前的最后一个提交里，我写了这样的代码，然后。。。
![](hexhyZ8.jpg)  
【***译注***】画外音：然后，亲爱的同事你，就有得火救啦，哼，哼哼，哦哈哈哈哈~

7. GOTO
---------------------------------------

这条是我的最爱。`Java`是有GOTO的！打上这行代码：

```java
int goto = 1;
```

结果是：

```java
Test.java:44: error: <identifier> expected
    int goto = 1;
       ^
```

这是因为`goto`是个[还未使用的关键字](http://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html)，保留了为以后可以用……

但这不是我要说的令人吃惊的内容。令人兴奋的是，你可以用`break`、`continue`和有标签的代码块来实现`goto`：

向前跳：

```java
label: {
  // do stuff
  if (check) break label;
  // do more stuff
}
```

对应的字节码是：

```java
2  iload_1 [check]
3  ifeq 6          // Jumping forward
6  ..
```

向后跳：

```java
label: do {
  // do stuff
  if (check) continue label;
  // do more stuff
  break label;
} while(true);
```

对应的字节码是：

```java
2  iload_1 [check]
3  ifeq 9
6  goto 2          // Jumping backward
9  ..
```

8. `Java`是有类型别名的
---------------------------------------

在别的语言中（比如，[`Ceylon`](http://blog.jooq.org/2013/12/03/top-10-ceylon-language-features-i-wish-we-had-in-java/)），可以方便地定义类型别名：

```java
interface People => Set<Person>;
```

这样定义的`People`可以和`Set<Person>`互换使用：

```java
People?      p1 = null;
Set<Person>? p2 = p1;
People?      p3 = p2;
```

在`Java`中，顶级（`top level`）是不能定义类型别名的。但可以在类级别、或方法级别定义。
如果对`Integer`、`Long`这样名字不满意，想更短的名字：`I`和`L`。很简单：

```java
class Test<I extends Integer> {
    <L extends Long> void x(I i, L l) {
        System.out.println(
            i.intValue() + ", " + 
            l.longValue()
        );
    }
}
```

上面的代码中，在`Test`类级别中`I`是`Integer`的别名，在`x`方法级别，`L`是`Long`的别名。可以这样来调用这个方法：

```java
new Test().x(1, 2L);
```

当然这个用法不严谨。在例子中，`Integer`、`Long`都是`final`类型，结果`I`和`L` *效果上*是个别名
（大部分情况下是。赋值兼容性只是单向的）。如果用的非`final`类型（比如，`Object`），还是使用原来的泛型参数类型的。

玩够了这些恶心的小把戏。现在来些真正的干货了！

9. 有些类型关系是不确定的
---------------------------------------

好，这条会很稀奇古怪，你先来杯咖啡，再集中精神来看。

看看下面的2个类型：

```java
// A helper type. You could also just use List
interface Type<T> {}
 
class C implements Type<Type<? super C>> {}
class D<P> implements Type<Type<? super D<D<P>>>> {}
```

类型`C`和`D`啥意思呢？










结论
---------------------------------------

一般来说，这只有`SQL`相关，是时候用下面的话来结束这往篇文章了：

> `Java`是个设备，只有她的能力可以延伸她的神秘。
