### Python难点理解
#### 概述
下文是本人在学习python过程中对一些难理解的概念的思考和理解过程，记录下来一是为了加强记和理解，二是如果对别人有帮助那更好
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
#### 编码与解码
对于我这样的新手来说python2.x的编码解码问题真是个坑，什么decode，encode，好乱好乱，怎么办呢，还是得自己搞懂的，这个世界你能靠的只有自己。
查了好多资料书籍，下面我试着来解释一下，有可能有些偏差，但是会好理解一点：

当我们要向对方表达某种意思的时候，就会用文字（当然有其他方法）写下来，当对方看到文字的时候，就会明白我们要表达的意思。

把要表达的意思写下来就是编码，对方看到文字后，会把通过眼睛接受的文字翻译成意思，然后就明白了要表达的东西，这就是解码

对应到Python中，“意思”就是unicode，文字就是str，因为文字有中文，英文，西班牙语等等，所以str就会有utf-8，ascii等，

因此unicode通过encode就会变成str，str通过decode就会变成unicode，一般我们以“utf-8”编码:unicode.encode('utf-8'),解码：str.decode('utf-8')

那为什么我们经常会遇到UnicodeEncodError：'ascii'code can't encode characters in position...呢：
这一般是发生在有中文的情况下，原因是当需要对str类型的字符串进行解码操作时，python会自动将其解码为unicode，而默认采用的是ascii来解码,举个例子：
``` javascript 
>>>a = u'hello' + 'ysf'
>>>a
u'helloysf'
```
这是在终端输入的代码，其中u‘hello’是unicode类型，‘ysf’是str类型，但是进行python在处理u'hello' + 'ysf'时却没有报错，最后还返回了u'helloysf'，这是为什么呢？原因就在于python内部先做了一步‘ysf’.decode('ascii'),将str类型解码成了unicode类型，这在没有中文的情况下是没错的，但是当有中文的时候，就有问题了，如下：
``` javascript 
>>>a = u'hello' + '中文'
Traceback (most recent call last):
  File "<input>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128) 
```
这是python在内部进行‘中文’.decode(‘ascii’）解码时出错了，因为ascii编码不包含中文，因此无法解码。按照如下方法改就不出错了
``` javascript
>>>a = u'hello' + '中文'.decode('utf-8')
>>>a
u'hello\u4e2d\u6587'
```
或者把系统默认编码方式改为‘utf-8’,如下，也就不会出错了
``` javascript
>>>import sys
>>>reload(sys)
<module 'sys' (built-in)>
>>>sys.setdefaultencoding('utf-8')
>>>a = u'hello' + '中文'
>>>a
u'hello\u4e2d\u6587'
```
最后在提一下，当源代码写在脚本文件中时，我们一般会用# -*- coding:utf-8 -*-去申明，而且要保存为utf-8编码，这样子Python解释器会按照‘utf-8’方式读取源代码，读取后，会转换为Unicode字符到内存里，编辑完成后，保存时在转换为‘utf-8’，因此如果要写入文件中，就要用encode（‘utf-8’）编码成utf-8类型。

#### 矩阵
学python基本语法用的几本书都没有提到矩阵，但网上面试题中有一提是杨氏矩阵查找，里面用到了矩阵，于是查了一些资料，原来python中是用列表嵌套列表的方法来表示矩阵的，以创建一个三行四列的简单矩阵为例：
``` javascript
>>>matrix = []
>>>for i in range(3):
       count = 1
       a = []
       for j in range(4):
           a.append(count)
           count += 1
       matrix.append(a)
    
>>>matrix
[[1, 2, 3, 4], [1, 2, 3, 4], [1, 2, 3, 4]]
```
这就创建了一个矩阵，矩阵的行数就是len（matrix），矩阵的列数就是len（matrix[0]），第m行第n列的值就是matrix[m-1][n-1],因为python是零索引的。

搞清楚了这个，现在做一提杨氏矩阵查找，题目：在一个m行n列矩阵中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

解题思路：首先定位到第一行第n列的那个数，也就是最右上角的那个数，如果要找的数比这个数大那么往下走，即看第2行第n列的数，反正，则往左走，即第一行第n-1列的数，直到找到要找的数为止，代码如下：
``` javascript
def find(matrix,value):
    m = len(matrix)-1
    n = len(matrix[0])-1
    a = 0
    b = n
    while a <= m and b>=0:
        compare  = matrix[a][b]
        if value == compare:
            return True
        elif value > compare:
            a += 1
        else:
            b -= 1
    return False
```

