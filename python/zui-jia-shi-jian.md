# 最佳实践

## 代码风格

> 代码的阅读比编写更加频繁，高度的可读性是Python语言的设计核心。
>
> 当一名经验丰富的Python开发者（Pythonista）认为某段代码不 “Pythonic”时， 那么这段代码可能没有遵循通用的指导方针，也没有用最佳的（最可读的）方式来表达意图。

### 简单直接

```python
def make_complex(*args):
    x, y = args
    return dict(**locals())
```

在存在各种黑魔法的Python中，我们提倡最明确和直接的编码方式。

```python
def make_complex(x, y):
    return {'x': x, 'y': y}
```

### 每行一个声明

```python
print 'one'; print 'two'

if x == 1: print 'one'

if <complex comparison> and <other complex comparison>:
    # do something
```

复合语句（比如说列表推导）因其简洁和表达性受到推崇，但尽量不要在同一行代码中写多条独立的语句。

```python
print 'one'
print 'two'

if x == 1:
    print 'one'

cond1 = <complex comparison>
cond2 = <other complex comparison>
if cond1 and cond2:
    # do something
```

### 函数参数

有四种方式给函数传参：

#### 1.位置参数

位置参数是最简单的参数形式

```python
def send(message, to):
```

尽量不要用关键字来改变参数的顺序，这会降低可读性

```python
def send(to='world', message='hello'):
```

#### 2.关键字参数

非强制且有默认值，经常被用在传递给函数的可选参数中。 

```python
def send(message, to, cc=None, bcc=None):
```

可选的调用方法

```python
send('Hello', 'World', 'Cthulhu', 'God')
send('Hello again', 'World', bcc='God', cc='Cthulhu')
```

以上调用没有语法错误，但最后选择最接近函数定义的方法

```python
send('Hello', 'World', cc='Cthulhu', bcc='God') 
```

#### 3.**任意参数列表**

带有数目可扩展的位置参数的记号，通常用\*args，在以下这个函数体中，`args` 是一个元组，包含所有剩余的位置参数。

```python
def send(message, *args):
```

如果参数列表中的预期接收参数具有相同的属性，比如args只为了表示任意数量的消息接收者，尽量用列表参数代替

```python
def send(message, recipients):
```

调用时将更加明确

```python
send('hello', ['bob', 'sam', 'linda'])
```

#### 4.任意参数字典

如果函数要求一系列待定的命名参数，我们可以使用 `**kwargs` 的结构。在函数体中， `kwargs` 是一个 字典，它包含所有传递给函数但没有被其他关键字参数捕捉的命名参数。

```python
def send(message, **kwargs):
```

调用

```python
send('hello', to='bob', cc='God')
```

{% hint style="info" %}
这些传参方式的顺序是由简到繁，为了让代码更加易读易改，尽量使用简单的传参方式。
{% endhint %}

### 避免魔法方法

你或许应该知道Python提供了哪些魔法能让你达成各种目的，比如，它可以：

* 改变对象创建和实例化的方式
* 改变Python解释器导入模块的方式
* 甚至可能（如果需要的话也是被推荐的）在Python中嵌入C程序

然而，这样做会降低代码可读性，许多代码分析工具也可能无法理解这些“魔法”，所以尽量使用更加直接的方式完成你的需求。

### 私有属性的约定

Python和高度防御性的语言（如Java，拥有很多机制来预防错误的使用）有着非常大的不同，客户端能重写一个对象的属性和方法，为了避免混乱，Python社区选择依靠一组约定，来表明哪些元素不应该被直接访问。

私有属性的主要约定和实现是在所有的“私有”变量前加一个下划线。如果客户端代码打破了这条规则并访问了带有下划线的变量，那么因内部代码的改变而出现的任何问题，都是客户端代码的责任。

### 函数返回值

及早处理错误有助于扁平化代码结构

```python
def compute(a, b, c):
    if not a:
        return None
    if not b:
        return None
    x = a * b * c
    if not x:
        x = a + b + c
    return x    
```

以上代码有三个出口点，过多的出口点会让代码变得难以调试，所以应该尽量减少函数的出口点

### 习语（Idiom）

编程习语，说得简单些，就是写代码的方式，采用习语的Python代码通常被称为 _Pythonic_

#### 解包（Upacking）

如果知道一个列表或者元组的长度，可以将其解包并为它的元素取名。

```python
#enumerate()会对list中的每一项提供包含两个元素的元组
for index, item in enumerate(some_list):
    # 使用index和item做一些工作
```

{% hint style="info" %}
使用 [`enumerate()`](https://docs.python.org/3/library/functions.html#enumerate) 函数比手动维护计数有更好的可读性。而且，它对迭代器进行了优化。
{% endhint %}

```python
# 交换变量
a, b = b, a
```

```python
# 嵌套解包
a, (b, c) = 1, (2, 3)
```

#### 忽略变量的记号

如果您需要赋值（比如解包）但不需要这个变量，请使用 `__`:

```python
filename = 'foobar.txt'
basename, __, ext = filename.rpartition('.')
```

{% hint style="info" %}
许多Python风格指南建议使用单下划线的 "`_`" 来表示废弃变量。问题是， "`_`" 常用作[`gettext()`](https://docs.python.org/3/library/gettext.html#gettext.gettext)函数的别名，也被用在交互式命令行中记录最后一次操作的值。所以，使用双下划线更清晰，避免和其它地方混淆。
{% endhint %}

#### 创建N个元素的列表

```python
four_nones = [None] * 4
```

#### 创建含N个列表的列表

用`*`将会创建含N个指向同一个列表的列表，如果需要N个独立的列表，请使用列表解析

```python
four_lists = [[] for __ in xrange(4)]
```

#### 列表转字符串

一般在空字符串上使用`str.join()`

```python
letters = ['s', 'p', 'a', 'm']
word = ''.join(letters)
```

#### 在集合中查找元素

一般用`in`关键字

```python
s = set(['s', 'p', 'a', 'm'])
l = ['s', 'p', 'a', 'm']

def lookup_set(s):
    return 's' in s

def lookup_list(l):
    return 's' in l
```

在列表中查找将比在元组和字典中查找耗时，因为元组和字典是可哈希的，项的哈希值会直接告诉Python去哪找匹配项，而列表需要依次查看每一项直到找到匹配项。如果你的场景满足下列条件：

* 集合体中包含大量的项
* 将在集合体中重复地查找项
* 没有重复的项

使用元组或字典是更好的选择，如果集合规模很小，或者不会频繁查找，那么建立哈希带来的开销经常会大于改进搜索的收益。

#### 变量的真值测试

```python
if attr == True:
    print 'True!'

if attr == None:
    print 'attr is None!'
```

不需要明确地比较一个值是True，或者None，或者0

```python
# 检查值
if attr:
    print 'attr is truthy!'

# 或者做相反的检查
if not attr:
    print 'attr is falsey!'

# 明确检测None
if attr is None:
    print 'attr is None!'
```

#### 访问字典元素

```python
d = {'hello': 'world'}
if d.has_key('hello'):
    print d['hello']    # 打印 'world'
else:
    print 'default_value'
```

不要使用 `dict.has_key()` 方法。取而代之，使用 `x in d` 语法，或者 将一个默认参数传递给 [`dict.get()`](https://docs.python.org/3/library/stdtypes.html#dict.get)

```python
d = {'hello': 'world'}

print d.get('hello', 'default_value') # 打印 'world'
print d.get('thingy', 'default_value') # 打印 'default_value'

# Or:
if 'hello' in d:
    print d['hello']
```

#### 修改列表

```python
# 过滤大于 4 的元素
a = [3, 4, 5]
for i in a:
    if i > 4:
        a.remove(i)
```

永远不要在列表迭代的过程中删除列表元素，如果有其它变量引用原始列表，这样可能会产生错误，如果一定要这样做，可以用切片赋值或者`filter()`

```python
# 修改原始列表的内容
sequence[::] = [value for value in sequence if value != x]
# 或者
sequence[::] = (value for value in sequence if value != x)
# 或者
sequence[::] = filter(lambda value: value != x, sequence)
```

```python
# 推导创建了一个新的列表对象
filtered_values = [value for value in sequence if value != x]
# 或者 (2.x)
filtered_values = filter(lambda i: i != x, sequence)

# 生成器不会创建新的列表
filtered_values = (value for value in sequence if value != x)
# 或者 (3.x)
filtered_values = filter(lambda i: i != x, sequence)
# 或者 (2.x)
filtered_values = itertools.ifilter(lambda i: i != x, sequence)
```

#### 修改列表元素的值

```python
# 所有的列表成员都加 3
a = [3, 4, 5]
b = a                     # a 和 b 都指向一个列表独享

for i in range(len(a)):
    a[i] += 3             # b[i] 也改变了
```

如果两个或多个变量引用相同的列表，则修改其中一个变量意味着将修改所有变量。创建一个新的列表对象并保留原始列表对象会更安全。

```text
a = [3, 4, 5]
b = a

# 给变量 "a" 赋值新的列表，而不改变 "b"
a = [i + 3 for i in a]
# 或者 (Python 2.x):
a = map(lambda i: i + 3, a)
# 或者 (Python 3.x)
a = list(map(lambda i: i + 3, a))
```

#### 文件读取

```python
f = open('file.txt')
a = f.read()
print a
f.close()
```

使用 `with open` 语法来读取文件，因为它能确保关闭文件，即使是在 `with` 的区块中抛出了异常

```python
with open('file.txt') as f:
    for line in f:
        print line
```

#### 行的拼接

```python
my_very_big_string = """For a long time I used to go to bed early. Sometimes, \
    when I had put out my candle, my eyes would close so quickly that I had not even \
    time to say “I’m going to sleep.”"""

from some.deep.module.inside.a.module import a_nice_function, another_nice_function, \
    yet_another_nice_function
```

如果使用反斜杠，这会破坏代码，而且可能有意想不到的结果。更好的方案是使用括号，左边用一个未闭合的括号开头，Python 解释器会把行的结尾和后面的行连接起来直到括号闭合，中括号和大括号同样适用。

```python
my_very_big_string = (
    "For a long time I used to go to bed early. Sometimes, "
    "when I had put out my candle, my eyes would close so quickly "
    "that I had not even time to say “I’m going to sleep.”"
)

from some.deep.module.inside.a.module import (
    a_nice_function, another_nice_function, yet_another_nice_function)

```

#### **Python**之禅

```python
>>> import this
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!

Python之禅 by Tim Peters

优美胜于丑陋（Python以编写优美的代码为目标）
明了胜于晦涩（优美的代码应当是明了的，命名规范，风格相似）
简洁胜于复杂（优美的代码应当是简洁的，不要有复杂的内部实现）
复杂胜于凌乱（如果复杂不可避免，那代码间也不能有难懂的关系，要保持接口简洁）
扁平胜于嵌套（优美的代码应当是扁平的，不能有太多的嵌套）
间隔胜于紧凑（优美的代码有适当的间隔，不要奢望一行代码解决问题）
可读性很重要（优美的代码是可读的）
即便假借特例的实用性之名，也不可违背这些规则（这些规则至高无上）
不要包容所有错误，除非您确定需要这样做（精准地捕获异常，不写 except:pass 风格的代码）
当存在多种可能，不要尝试去猜测
而是尽量找一种，最好是唯一一种明显的解决方案（如果不确定，就用穷举法）
虽然这并不容易，因为您不是 Python 之父（这里的 Dutch 是指 Guido ）
做也许好过不做，但不假思索就动手还不如不做（动手之前要细思量）
如果您无法向人描述您的方案，那肯定不是一个好方案；反之亦然（方案测评标准）
命名空间是一种绝妙的理念，我们应当多加利用（倡导与号召）
```

Python优雅风格的示例，请见 [Python之禅示例](http://artifex.org/~hblanks/talks/2011/pep20_by_example.pdf)

#### PEP8

[**PEP 8**](https://www.python.org/dev/peps/pep-0008) 是Python的代码风格指南，可以在 [pep8.org](http://pep8.org/) 上获得高质量的、一致的PEP 8版本。让您的 Python 代码遵循 PEP 8 ，这有助于与其他开发人员的协作

