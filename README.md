### Python难点理解
#### 概述
下文是本人在学习python过程中对一些难理解的概念的思考和理解过程，因此语言基本是大白话，没有太多专业术语，目的只有一个，便于理解。
#### 装饰器
装饰器:用于在不改变原函数的情况下，给原函数扩张功能
解释：
我们先编写一个简单的打印字符串的函数，如下：
``` javascript 
def print_ysf():
  print 'ysf'
```
调用函数print_ysf：
``` javascript 
>>> print_ysf()
ysf
```
这个函数只有一个功能就是打印出ysf,但我现在要想在打印ysf之前先打印出hello，单不改变print_ysf函数，那怎么办呢，这个时候就要用到装饰器，
我先把具体用法写下来，再来解释.
``` javascript
def  decorator(func):
  def print_hello():
    print 'hello'
    return func()
  return print_hello
 
@decorator
def print_ysf（）:
  print 'ysf'
```
调用函数print_ysf
``` javascript
>>>print_ysf()
hello
ysf
```
神奇吧，这就是装饰器的功能，在没有改变print_ysf函数的情况下，增加了print_ysf函数的功能，下面我来解释一些其运行原理：
那我们现在看这个函数：
``` javascript
def  decorator(func):
  def print_hello():
    print 'hello'
    return func()
  return print_hello
 
@decorator
def print_ysf（）:
  print 'ysf'
```
首先记住一点：**函数只有在调用时才被执行**，因此乍一看改函数什么都没有被执行，但是因为有@decorator就不一样了

当python解释器扫到@decorator时会执执行如下步骤：

1.将@decorator下面的函数，即print_ysf函数作为decorator函数的参数，也就是decorator（print_ysf）

2.执行decorator（print_ysf）函数

3.现在执行decorator（print_ysf）函数，我们发现decorator（print_ysf）还有一个print_hello()函数，但因为没有被调用，因此不执行，
继续往下，那么其实就是执行了 return print_hello

4.将返回的print_hello函数赋给给@decorator下面的函数print_ysf，即print_ysf = print_hello

5.然后我们再命令行输入print_ysf（），其实相当于执行了print_hello（），那么就会按照print_hello函数的内部代码，
先print 'hello',然后return func（），而func就是decorato函数通过@decorator传进来的参数，即原本的print_ysf函数，于是就又执行了原本的
print_hello函数，因此会执行print ‘ysf’

6.所以最终输出为:
``` javascript
hello
ysf
```

