原文链接： [A practical introduction to functional programming](https://maryrosecook.com/blog/post/a-practical-introduction-to-functional-programming) - [Mary Rose](https://github.com/maryrosecook)，2015-08-10  

<img src="lambda.png" width="20%" hspace="2px" align="right" >

## 🍎 译序

本文是一篇手把手的函数式编程入门介绍，借助代码示例讲解细腻。但又不乏洞见，第一节中列举和点评了函数式种种让眼花缭乱的特质，给出了『理解函数式特质的指南针：函数式代码的核心特质就一条，**无副作用**』，相信这个指南针对于有积极学过挖过函数式的同学看来更是有相知恨晚的感觉。

希望看了这篇文章之后，能在学习和使用函数式编程的旅途中不迷路哦，兄die～

PS：本人是在《[Functional Programming, Simplified(Scala edition)](https://alvinalexander.com/scala/functional-programming-simplified-book)》了解到本文。这本书由浅入深循序渐进地对`FP`做了体系讲解，力荐！[书的豆瓣链接](https://book.douban.com/subject/30326807/)。  
翻译中肯定会有不足和不对之处，欢迎建议（[提交Issue](https://github.com/oldratlee/translations/issues)）和指正（[Fork后提交代码](https://github.com/oldratlee/translations/fork)）！

# 手把手介绍函数式编程：从命令式重构到函数式

有很多函数式编程文章讲解了抽象的函数式技术，也就是组合（`composition`）、管道（`pipelining`）、高阶函数（`higher order function`）。本文希望以另辟蹊径的方式来讲解函数式：首先展示我们平常编写的命令式而非函数式的代码示例，然后将这些示例重构成函数式风格。

本文的第一部分选用了简短的数据转换循环，将它们重构成函数式的`map`和`reduce`。第二部分则对更长的循环代码，将它们分解成多个单元，然后重构各个单元成函数式的。第三部分选用的是有一系列连续的数据转换循环代码，将其拆解成为一个函数式管道（`functional pipeline`）。

示例代码用的是`Python`语言，因为多数人都觉得`Python`易于阅读。示例代码避免使用`Python`范的（`pythonic`）代码，以便展示出对各种语言通用的函数式技术：`map`、`reduce`和管道。所有示例都用的是`Python 2`。

-------------------

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [理解函数式特质的指南针](#%E7%90%86%E8%A7%A3%E5%87%BD%E6%95%B0%E5%BC%8F%E7%89%B9%E8%B4%A8%E7%9A%84%E6%8C%87%E5%8D%97%E9%92%88)
- [不要迭代列表，使用`map`和`reduce`](#%E4%B8%8D%E8%A6%81%E8%BF%AD%E4%BB%A3%E5%88%97%E8%A1%A8%E4%BD%BF%E7%94%A8map%E5%92%8Creduce)
    - [`map`](#map)
    - [`reduce`](#reduce)
- [编写声明式代码，而非命令式](#%E7%BC%96%E5%86%99%E5%A3%B0%E6%98%8E%E5%BC%8F%E4%BB%A3%E7%A0%81%E8%80%8C%E9%9D%9E%E5%91%BD%E4%BB%A4%E5%BC%8F)
    - [使用函数](#%E4%BD%BF%E7%94%A8%E5%87%BD%E6%95%B0)
    - [消除状态](#%E6%B6%88%E9%99%A4%E7%8A%B6%E6%80%81)
- [使用管道](#%E4%BD%BF%E7%94%A8%E7%AE%A1%E9%81%93)
- [现在开始我们可以做什么？](#%E7%8E%B0%E5%9C%A8%E5%BC%80%E5%A7%8B%E6%88%91%E4%BB%AC%E5%8F%AF%E4%BB%A5%E5%81%9A%E4%BB%80%E4%B9%88)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

-------------------

<img src="compass-small.jpg" width="25%" hspace="2px" align="right" >

# 理解函数式特质的指南针

当人们谈论函数式编程时，提到了多到令人迷路的『函数式』特质（`characteristics`）：

- <a id="note_mark1"></a>人们会提到不可变数据[<sup>1</sup>](#note1)（`immutable data`）、一等公民的函数[<sup>2</sup>](#note2)（`first class function`）和尾调用优化[<sup>3</sup>](#note3)（`tail call optimisation`）。这些是**有助于函数式编程的语言特性**。
- <a id="note_mark4"></a>人们也会提到`map`、`reduce`、管道、递归（`recursing`）、柯里化[<sup>4</sup>](#note4)（`currying`）以及高阶函数的使用。这些是**用于编写函数式代码的编程技术**。
- <a id="note_mark5"></a>人们还会提到并行化[<sup>5</sup>](#note5)（`parallelization`）、惰性求值[<sup>6</sup>](#note6)（`lazy evaluation`）和确定性[<sup>7</sup>](#note7)（`determinism`）。这些是**函数式程序的优点**。

无视这一切。函数式代码的核心特质就一条：**无副作用**（`the absence of side effects`）。即代码逻辑不依赖于当前函数之外的数据，并且也不会更改当前函数之外的数据。所有其他的『函数式』特质都可以从这一条派生出来。在你学习过程中，请以此作为指南针。不要再迷路哦，兄die～

这是一个非函数式的函数：

```python
a = 0
def increment():
    global a
    a += 1
```

而这是一个函数式的函数：

```python
def increment(a):
    return a + 1
```

# 不要迭代列表，使用`map`和`reduce`

## `map`

`map`输入一个函数和一个集合，创建一个新的空集合，在原来集合的每个元素上运行该函数，并将各个返回值插入到新集合中，然后返回新的集合。

这是一个简单的`map`，它接受一个名字列表并返回这些名字的长度列表：

```python
name_lengths = map(len, ["Mary", "Isla", "Sam"])
print name_lengths
# => [4, 4, 3]
```

这是一个`map`，对传递的集合中的每个数字进行平方：

```python
squares = map(lambda x: x * x, [0, 1, 2, 3, 4])

print squares
# => [0, 1, 4, 9, 16]
```

这个`map`没有输入命名函数，而是一个匿名的内联函数，用`lambda`关键字来定义。`lambda`的参数定义在冒号的左侧。函数体定义在冒号的右侧。（隐式）返回的是函数体的运行结果。

下面的非函数式代码输入一个真实名字的列表，替换成随机分配的代号。

```python
import random

names = ['Mary', 'Isla', 'Sam']
code_names = ['Mr. Pink', 'Mr. Orange', 'Mr. Blonde']

for i in range(len(names)):
    names[i] = random.choice(code_names)

print names
# => ['Mr. Blonde', 'Mr. Blonde', 'Mr. Blonde']
```

（如你所见，这个算法可能会为多个秘密特工分配相同的秘密代号，希望这不会因此导致混淆了秘密任务。）

可以用`map`重写成：

```python
import random

names = ['Mary', 'Isla', 'Sam']

secret_names = map(lambda x: random.choice(['Mr. Pink',
                                            'Mr. Orange',
                                            'Mr. Blonde']),
                   names)
```

**练习1**：尝试将下面的代码重写为`map`，输入一个真实名字列表，替换成用更可靠策略生成的代号。

```python
names = ['Mary', 'Isla', 'Sam']

for i in range(len(names)):
    names[i] = hash(names[i])

print names
# => [6306819796133686941, 8135353348168144921, -1228887169324443034]
```

（希望特工会留下美好的回忆，在秘密任务期间能记得住搭档的秘密代号。）

我的实现方案：

```python
names = ['Mary', 'Isla', 'Sam']

secret_names = map(hash, names)
```

## `reduce`

`reduce`输入一个函数和一个集合，返回通过合并集合元素所创建的值。

这是一个简单的`reduce`，返回集合所有元素的总和。

```python
sum = reduce(lambda a, x: a + x, [0, 1, 2, 3, 4])

print sum
# => 10
```

`x`是迭代的当前元素。`a`是累加器（`accumulator`），它是在前一个元素上执行`lambda`的返回值。`reduce()`遍历所有集合元素。对于每一个元素，运行以当前的`a`和`x`为参数运行`lambda`，返回结果作为下一次迭代的`a`。

在第一次迭代时，`a`是什么值？并没有前一个的迭代结果可以传递。`reduce()`使用集合中的第一个元素作为第一次迭代中的`a`值，从集合的第二个元素开始迭代。也就是说，第一个`x`是集合的第二个元素。

下面的代码计算单词`'Sam'`在字符串列表中出现的次数：

```python
sentences = ['Mary read a story to Sam and Isla.',
             'Isla cuddled Sam.',
             'Sam chortled.']

sam_count = 0
for sentence in sentences:
    sam_count += sentence.count('Sam')

print sam_count
# => 3
```

这与下面使用`reduce`的代码相同：

```python
sentences = ['Mary read a story to Sam and Isla.',
             'Isla cuddled Sam.',
             'Sam chortled.']

sam_count = reduce(lambda a, x: a + x.count('Sam'),
                   sentences,
                   0)
```

这段代码是如何产生初始的`a`值？`'Sam'`出现次数的初始值不能是`'Mary read a story to Sam and Isla.'`。初始累加器用`reduce()`的第三个参数指定。这样就允许使用与集合元素不同类型的值。

为什么`map`和`reduce`更好？

1. 这样的做法通常会是一行简洁的代码。
1. 迭代的重要部分 —— 集合、操作和返回值 —— 以`map`和`reduce`方式总是在相同的位置。
1. 循环中的代码可能会影响在它之前定义的变量或在它之后运行的代码。按照约定，`map`和`reduce`都是函数式的。
1. `map`和`reduce`是基本原子操作。
    - 阅读`for`循环时，必须一行一行地才能理解整体逻辑。往往没有什么规则能保证以一个固定结构来明确代码的表义。
    - 相比之下，`map`和`reduce`则是一目了然表现出了可以组合出复杂算法的构建块（`building block`）及其相关的元素，代码阅读者可以迅速理解并抓住整体脉络。『哦～这段代码正在转换每个集合元素；丢弃了一些转换结果；然后将剩下的元素合并成单个输出结果。』
1. `map`和`reduce`有很多朋友，提供有用的、对基本行为微整的版本。比如：`filter`、`all`、`any`和`find`。

**练习2**：尝试使用`map`、`reduce`和`filter`重写下面的代码。`filter`需要一个函数和一个集合，返回结果是函数返回`True`的所有集合元素。

```python
people = [{'name': 'Mary', 'height': 160},
          {'name': 'Isla', 'height': 80},
          {'name': 'Sam'}]

height_total = 0
height_count = 0
for person in people:
    if 'height' in person:
        height_total += person['height']
        height_count += 1

if height_count > 0:
    average_height = height_total / height_count

    print average_height
    # => 120
```

如果上面这段代码看起来有些烧脑，我们试试不以在数据上操作为中心的思考方式。而是想一想数据所经历的状态：从人字典的列表转换成平均身高。不要将多个转换混在一起。每个转换放在一个单独的行上，并将结果分配一个有描述性命名的变量。代码工作之后，再合并缩减代码。

我的实现方案：

```python
people = [{'name': 'Mary', 'height': 160},
          {'name': 'Isla', 'height': 80},
          {'name': 'Sam'}]

heights = map(lambda x: x['height'],
              filter(lambda x: 'height' in x, people))

if len(heights) > 0:
    from operator import add
    average_height = reduce(add, heights) / len(heights)
```

# 编写声明式代码，而非命令式

下面的程序演示三辆赛车的比赛。每过一段时间，赛车可能向前跑了，也可能抛锚而原地不动。在每个时间段，程序打印出目前为止的赛车路径。五个时间段后比赛结束。

这是个示例输出：

```
-
--
--

--
--
---

---
--
---

----
---
----

----
----
-----
```

这是程序实现：

```python
from random import random

time = 5
car_positions = [1, 1, 1]

while time:
    # decrease time
    time -= 1

    print ''
    for i in range(len(car_positions)):
        # move car
        if random() > 0.3:
            car_positions[i] += 1

        # draw car
        print '-' * car_positions[i]
```

这份代码是命令式的。函数式版本则是声明式的，描述要做什么，而不是如何做。

## 使用函数

通过将代码片段打包到函数中，程序可以更加声明式。

```python
from random import random

def move_cars():
    for i, _ in enumerate(car_positions):
        if random() > 0.3:
            car_positions[i] += 1

def draw_car(car_position):
    print '-' * car_position

def run_step_of_race():
    global time
    time -= 1
    move_cars()

def draw():
    print ''
    for car_position in car_positions:
        draw_car(car_position)

time = 5
car_positions = [1, 1, 1]

while time:
    run_step_of_race()
    draw()
```

要理解这个程序，读者只需读一下主循环。『如果还剩下时间，请跑一步，然后画出线图。再次检查时间。』如果读者想要了解更多关于比赛步骤或画图的含义，可以阅读对应函数的代码。

没什么要再说明的了。**代码是自描述的。**

拆分代码成函数是一种很好的、简单易行的方法，能使代码更具可读性。

这个技术使用函数，但将函数用作子例程（`sub-routine`），用于打包代码。对照上文说的指南针，这样的代码并不是函数式的。实现中的函数使用了没有作为参数传递的状态，即通过更改外部变量而不是返回值来影响函数周围的代码。要确认函数真正做了什么，读者必须仔细阅读每一行。如果找到一个外部变量，必须反查它的源头，并检查其他哪些函数更改了这个变量。

## 消除状态

下面是赛车代码的函数式版本：

```python
from random import random

def move_cars(car_positions):
    return map(lambda x: x + 1 if random() > 0.3 else x,
               car_positions)

def output_car(car_position):
    return '-' * car_position

def run_step_of_race(state):
    return {'time': state['time'] - 1,
            'car_positions': move_cars(state['car_positions'])}

def draw(state):
    print ''
    print '\n'.join(map(output_car, state['car_positions']))

def race(state):
    draw(state)
    if state['time']:
        race(run_step_of_race(state))

race({'time': 5,
      'car_positions': [1, 1, 1]})
```

代码仍然是分解成函数。但这些函数是函数式的，有三个迹象表明这点：

1. 不再有任何共享变量。`time`与`car_position`作为参数传入`race()`。
1. 函数是有参数的。
1. 在函数内部没有变量实例化。所有数据更改都使用返回值完成。基于`run_step_of_race()`的结果，`race()`做递归调用[<sup>3</sup>](#note3)。每当一个步骤生成一个新状态时，立即传递到下一步。

让我们另外再来看看这么两个函数，`zero()`和`one()`：

```python
def zero(s):
    if s[0] == "0":
        return s[1:]

def one(s):
    if s[0] == "1":
        return s[1:]
```

`zero()`输入一个字符串`s`。如果第一个字符是`'0'`，则返回字符串的其余部分。如果不是，则返回`None`，`Python`函数的默认返回值。`one()`做同样的事情，但关注的是第一个字符`'1'`。

假设有一个叫做`rule_sequence()`的函数，输入一个字符串和规则函数的列表，比如`zero()`和`one()`：

1. 调用字符串上的第一个规则。
1. 除非`None`返回，否则它将获取返回值并在其上调用第二个规则。
1. 除非`None`返回，否则它将获取返回值并在其上调用第三个规则。
1. 等等。
1. 如果任何规则返回`None`，则`rule_sequence()`停止并返回`None`。
1. 否则，它返回最终规则的返回值。

下面是一些示例输入和输出：

```python
print rule_sequence('0101', [zero, one, zero])
# => 1

print rule_sequence('0101', [zero, zero])
# => None
```

这是命令式版本的`rule_sequence()`实现：

```python
def rule_sequence(s, rules):
    for rule in rules:
        s = rule(s)
        if s == None:
            break

    return s
```

**练习3**：上面的代码使用循环来实现。通过重写为递归来使代码更加声明式。

我的实现方案：

```python
def rule_sequence(s, rules):
    if s == None or not rules:
        return s
    else:
        return rule_sequence(rules[0](s), rules[1:])
```

# 使用管道

在上一节中，我们重写一些命令性循环成为调用辅助函数的递归。在本节中，将使用称为管道的技术重写另一类型的命令循环。

下面的循环对乐队字典执行转换，字典包含了乐队名、错误的所属国家和活跃状态。

```python
bands = [{'name': 'sunset rubdown', 'country': 'UK', 'active': False},
         {'name': 'women', 'country': 'Germany', 'active': False},
         {'name': 'a silver mt. zion', 'country': 'Spain', 'active': True}]

def format_bands(bands):
    for band in bands:
        band['country'] = 'Canada'
        band['name'] = band['name'].replace('.', '')
        band['name'] = band['name'].title()

format_bands(bands)

print bands
# => [{'name': 'Sunset Rubdown', 'active': False, 'country': 'Canada'},
#     {'name': 'Women', 'active': False, 'country': 'Canada' },
#     {'name': 'A Silver Mt Zion', 'active': True, 'country': 'Canada'}]
```

看到这样的函数命名让人感受到一丝的忧虑，命名中的`format`表义非常模糊。仔细检查代码后，忧虑逆流成河。在循环的实现中做了三件事：

1. `'country'`键的值设置成了`'Canada'`。
1. 删除了乐队名中的标点符号。
1. 乐队名改成首字母大写。

我们很难看出这段代码意图是什么，也很难看出这段代码是否完成了它看起来要做的事情。代码难以重用、难以测试且难以并行化。

与下面实现对比一下：

```python
print pipeline_each(bands, [set_canada_as_country,
                            strip_punctuation_from_name,
                            capitalize_names])
```

这段代码很容易理解。给人的印象是辅助函数是函数式的，因为它们看过来是串联在一起的。前一个函数的输出成为下一个的输入。如果是函数式的，就很容易验证。也易于重用、易于测试且易于并行化。

`pipeline_each()`的功能就是将乐队一次一个地传递给一个转换函数，比如`set_canada_as_country()`。将转换函数应用于所有乐队后，`pipeline_each()`将转换后的乐队打包起来。然后，打包的乐队传递给下一个转换函数。

我们来看看转换函数。

```python
def assoc(_d, key, value):
    from copy import deepcopy
    d = deepcopy(_d)
    d[key] = value
    return d

def set_canada_as_country(band):
    return assoc(band, 'country', "Canada")

def strip_punctuation_from_name(band):
    return assoc(band, 'name', band['name'].replace('.', ''))

def capitalize_names(band):
    return assoc(band, 'name', band['name'].title())
```

每个函数都将乐队的一个键与一个新值相关联。如果不变更原乐队，没有简单的方法可以直接实现。`assoc()`通过使用`deepcopy()`生成传入字典的副本来解决此问题。每个转换函数都对副本进行修改并返回该副本。

一切似乎都很好。当键与新值相关联时，可以保护原乐队字典免于被变更。但是上面的代码中还有另外两个潜在的变更。在`strip_punctuation_from_name()`中，原来的乐队名通过调用`replace()`生成无标点的乐队名。在`capitalize_names()`中，原来的乐队名通过调用`title()`生成大写乐队名。如果`replace()`和`title()`不是函数式的，则`strip_punctuation_from_name()`和`capitalize_names()`也将不是函数式的。

幸运的是，`replace()`和`title()`不会变更他们操作的字符串。这是因为字符串在`Python`中是不可变的（`immutable`）。例如，当`replace()`对乐队名字符串进行操作时，将复制原来的乐队名并在副本上执行`replace()`调用。Phew～有惊无险！

`Python`中字符串和字典之间在可变性上不同的这种对比彰显了像`Clojure`这样语言的吸引力。`Clojure`程序员完全不需要考虑是否会改变数据。`Clojure`的数据结构是不可变的。

**练习4**：尝试编写`pipeline_each`函数的实现。想想操作的顺序。数组中的乐队一次一个传递到第一个变换函数。然后返回的结果乐队数组中一次一个乐队传递给第二个变换函数。以此类推。

我的实现方案：

```python
def pipeline_each(data, fns):
    return reduce(lambda a, x: map(x, a),
                  fns,
                  data)
```

所有三个转换函数都可以归结为对传入的乐队的特定字段进行更改。可以用`call()`来抽象，`call()`传入一个函数和键名，用键对应的值来调用这个函数。

```python
set_canada_as_country = call(lambda x: 'Canada', 'country')
strip_punctuation_from_name = call(lambda x: x.replace('.', ''), 'name')
capitalize_names = call(str.title, 'name')

print pipeline_each(bands, [set_canada_as_country,
                    strip_punctuation_from_name,
                    capitalize_names])
```

或者，如果我们愿意为了简洁而牺牲一些可读性，那么可以写成：

```python
print pipeline_each(bands, [call(lambda x: 'Canada', 'country'),
                            call(lambda x: x.replace('.', ''), 'name'),
                            call(str.title, 'name')])
```

`call()`的实现代码：

```python
def assoc(_d, key, value):
    from copy import deepcopy
    d = deepcopy(_d)
    d[key] = value
    return d

def call(fn, key):
    def apply_fn(record):
        return assoc(record, key, fn(record.get(key)))
    return apply_fn
```

上面的实现中有不少内容要讲，让我们一点一点地来说明：

1. `call()`是一个高阶函数。高阶函数是指将函数作为参数，或返回函数。或者，就像`call()`，输入和返回2者都是函数。
1. `apply_fn()`看起来与三个转换函数非常相似。输入一个记录（一个乐队），`record[key]`是查找出值；再以值为参数调用`fn`，将调用结果赋值回记录的副本；最后返回副本。
1. `call()`不做任何实际的事。而是调用`apply_fn()`时完成需要做的事。在上面的示例的`pipeline_each()`中，一个`apply_fn()`实例会设置传入乐队的`'country'`成`'Canada'`；另一个实例则将传入乐队的名字转成大写。
1. 当运行一个`apply_fn()`实例，`fn`和`key`2个变量并没有在自己的作用域中，既不是`apply_fn()`的参数，也不是本地变量。但2者仍然可以访问。
    - 当定义一个函数时，会保存这个函数能闭包进来（`close over`）的变量引用：在这个函数外层作用域中定义的变量。这些变量可以在该函数内使用。
    - 当函数运行并且其代码引用变量时，`Python`会在本地变量和参数中查找变量。如果没有找到，则会在保存的引用中查找闭包进来的变量。就在这里，会发现`fn`和`key`。
1. 在`call()`代码中没有涉及乐队列表。这是因为`call()`，无论要处理的对象是什么，可以为任何程序生成管道函数。函数式编程的一大关注点就是构建通用的、可重用的和可组合的函数所组成的库。

完美！闭包（`closure`）、高阶函数以及变量作用域，在上面的几段代码中都涉及了。嗯，理解完了上面这些内容，是时候来个驴肉火烧打赏一下自己。🍣

最后还差实现一段处理乐队的逻辑：删除除名字和国家之外的内容。`extract_name_and_country()`可以把这些信息提取出来：

```python
def extract_name_and_country(band):
    plucked_band = {}
    plucked_band['name'] = band['name']
    plucked_band['country'] = band['country']
    return plucked_band

print pipeline_each(bands, [call(lambda x: 'Canada', 'country'),
                            call(lambda x: x.replace('.', ''), 'name'),
                            call(str.title, 'name'),
                            extract_name_and_country])

# => [{'name': 'Sunset Rubdown', 'country': 'Canada'},
#     {'name': 'Women', 'country': 'Canada'},
#     {'name': 'A Silver Mt Zion', 'country': 'Canada'}]
```

`extract_name_and_country()`本可以写成名为`pluck()`的通用函数。`pluck()`使用起来是这个样子：

> 【译注】作者这里用了虚拟语气『\*本\*可以』。  
> 言外之意是，在实践中为了更具体直白地表达出业务，可能不需要进一步抽象成`pluck()`。

```python
print pipeline_each(bands, [call(lambda x: 'Canada', 'country'),
                            call(lambda x: x.replace('.', ''), 'name'),
                            call(str.title, 'name'),
                            pluck(['name', 'country'])])
```

**练习5**：`pluck()`输入是要从每条记录中提取键的列表。试着实现一下。它会是一个高阶函数。

我的实现方案：

```python
def pluck(keys):
    def pluck_fn(record):
        return reduce(lambda a, x: assoc(a, x, record[x]),
                      keys,
                      {})
    return pluck_fn
```

# 现在开始我们可以做什么？

函数式代码与其他风格的代码可以很好地共存。本文中的转换实现可以应用于任何语言的任何代码库。试着应用到你自己的代码中。

想想特工玛丽、伊丝拉和山姆。转换列表迭代为`map`和`reduce`。

想想车赛。将代码分解为函数。将这些函数转成函数式的。将重复过程的循环转成递归。

想想乐队。将一系列操作转为管道。

　

-----------------------

**注：**

1. <a id="note1"></a>[^](#note_mark1) 不可变数据是无法更改的。某些语言（如`Clojure`）默认就是所有值都不可变。任何『变更』操作都会复制该值，更改副本然后返回更改后的副本。这消除了不完整模型下程序可能进入状态所带来的`Bug`。
1. <a id="note2"></a>[^](#note_mark1) 支持一等公民函数的语言允许像任何其他值一样对待函数。这意味着函数可以创建，传递给函数，从函数返回，以及存储在数据结构中。
1. <a id="note3"></a>[^](#note_mark1) 尾调用优化是一个编程语言特性。函数递归调用时，会创建一个新的栈帧（`stack frame`）。栈帧用于存储当前函数调用的参数和本地值。如果函数递归很多次，解释器或编译器可能会耗尽内存。支持尾调用优化的语言对整个递归调用序列重用同一个的栈帧。像`Python`这样没有尾调用优化的语言通常会限制函数递归的次数（如数千次）。对于上面例子中`race()`函数，因为只有5个时间段，所以是安全的。
1. <a id="note4"></a>[^](#note_mark4) 柯里化（`currying`）是指将一个带有多个参数的函数转换成另一个函数，这个函数接受第一个参数，并返回一个接受下一个参数的函数，依此类推所有参数。
1. <a id="note5"></a>[^](#note_mark5) 并行化（`parallelization`）是指，在没有同步的情况下，相同的代码可以并发运行。这些并发处理通常运行在多个处理器上。
1. <a id="note6"></a>[^](#note_mark5) 惰性求值（`lazy evaluation`）是一种编译器技术，可以避免在需要结果之前运行代码。
1. <a id="note7"></a>[^](#note_mark5) 如果每次重复执行都产生相同的结果，则过程就是确定性的。

-----------------------

![](lazy-small.jpg)
