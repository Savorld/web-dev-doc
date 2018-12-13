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



