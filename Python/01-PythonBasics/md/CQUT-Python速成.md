# Python速成 [CQUT版]

> "Life is short, you need Python!"



## Pyhton特点与发展

* 解释性

  Python语言写的程序不需要编译成二进制代码。Python解释器把源代码转换成称为字节码的中间形式，然后再把它翻译成计算机使用的机器语言并运行

* 简单易学

  Python是一种代表简单主义思想的语言。

  专注于解决问题而不是去搞明白语言本身。

  Python极其容易上手 

  Python有极其简单的语法

* 面向对象

* 免费开源与可移植性

  Python是FLOSS（自由/开放源码软件）之一。简单地说，可以自由地发布这个软件的拷贝、阅读它的源代码、对它做改动、把它的一部分用于新的自由软件中。

* 第三方库丰富

  支持所有主流关系型数据库：Oracle、Sybase、MySQL、PostgreSQL、Informix、SQLite

  支持非关系型数据库：mongodb

  从关系数据库映射到Python类（ORM）： SQLAlchemy 、SQLObject



Python特色：

1. 科学计算

   Python在科学计算上能力超强，堪与matlab比较。Python科学计算有numpy，scipy，matplotlib三剑客。其中，numpy负责数值计算，矩阵操作等；scipy负责常见的数学算法，插值、拟合等；matplotlib负责画图。

2. 网络爬虫

   网络爬虫的爬取、解析、存储三个基本流程。讲解了基于urllib、基于BeautifulSoup和基于Scrapy三种爬取方法，最后介绍了Selenium 和 PhantomJS使用抓取动态网页数据。

3. Web开发

   Web 应用框架（Web application framework）是一种开发框架，用来支持动态网站、网络应用程序的开发。web2py、Django、Flask等Web开发框架



Python资源大全（Github网址）：[GitHub - jobbole/awesome-python-cn: Python资源大全中文版，包括：Web框架、网络爬虫、模板引擎、数据库、数据可视化、图片处理等，由「开源前哨」和「Python开发者」微信公号团队维护更新。](https://github.com/jobbole/awesome-python-cn)

> print("Hello World!")

------



## Python版本与IDE选择

版本选择Python3.x，前往官网下载：**http://python.org/download**

IDE选择PyCharm

------



## 编写Python代码

### 1.文本编辑器

创建一个.py文件，在文件中编写python代码，然后保存

然后打开cmd，输入文件名.py运行脚本即可

### 2.Python Shell（编写交互式代码）

打开cmd，输入python即可进入

将会看到“>>>”符号

### 3.PyCharm

使用成熟的IDE编写

------



## Python概述

1. **Python是动态编译语言，变量的使用只需赋值**
2. **Python 是弱类型语言**

------



## Python数据类型

### 常量，变量，标识符

#### 常量

```python
# 在程序运行过程中，其值不能改变的量称为常量。
#    （1）整型常量： -123、20
#    （2）实型常量：3.14、0.15、-2.0
#    （3）字符串常量：‘Python’、“Very Good！”
#    （4）布尔型常量：True、False
#    （5）复数类型常量：3+2.5j
```

#### 变量

```python
# 在Python中，不需要事先声明变量名及其类型，类型是在运行过程中自动决定的，直接赋值即可创建各种类型的变量。
#    变量在程序中使用变量名表示，变量名必须是合法的标识符，并且不能使用Python关键字。
# 例如：
     >>>x=5
     >>>type(x)
    <class 'int'>
#    采用内置函数type()返回变量x的类型int
	 >>>string=”Hello World!”
#    创建了字符型变量string，并赋值为Hello World!。
# 注意：Python是一种动态类型语言，即变量的类型可以随时变化。
# 例如：
    >>>x=5
    >>>type(x)
    <class 'int'>
#   -----------------------------------------------------
    >>>x="Hello World! "
    >>>type(x)
    <class 'str'>
#   -----------------------------------------------------
# 当创建了字符串类型变量x之后，之前创建的整型变量x就自动失效了。
```

#### 标识符

```python
# 合法的标识符由字母、数字和下划线的序列，且必须由字母或下划线开头，自定义的标识符不能与关键字同名。
#    合法的标识符: x， a1， wang，num_1， radius， 1， PI
#    不合法的标识符: a.1， 1sum， x+y， !abc， 123， π， 3-c
#    Python中，单独的下划线（_）用于表示上一次运算的结果。
# 例如：
    >>>20
    20
    >>>_*10
    200

# 标识符的命名习惯：
#（1）变量名和函数名中的英文字母一般用小写，以增加程序的可读性。
#（2）见名知义：通过变量名就知道变量值的含义。一般选用相应英文单词或拼音缩写的形式，如求和用sum，而尽量不要使用简单代数符号，如x、y、z等。
#（3）尽量不要使用容易混淆的单个字符作为标识符，例如数字0和字母o，数字1和字母l。
#（4）开头和结尾都使用下划线的情况应该避免，因为Python中大量采用这种名字定义了各种特殊方法和变量。
```

### 基本数据类型

**在程序设计语言中，都是采用数据类型来描述程序中的数据结构、数据的表示范围和数据在内存中的存储分配等**

* 简单类型
  * 整型
  * 复数
  * 布尔
  * 浮点型 \ 实型
* 序列
  * 列表
  * 元组
  * 字符串
* 其他类型
  * 字典
  * 集合

#### 整型数据

```python
# 整型数据即整数，不带小数点，可以有正号或者负号。在Python3.x中，整型数据在计算机内的表示没有长度限制，其值可以任意大。
#   （1）十进制整数。
#   （2）二进制常数。以0b为前缀，其后由0和1组成。如：0b1001表示二进制数1001，即(1001)2。
#   （3）八进制整数。以0o为前缀，其后由0至7的数字组成。如：0o 456表示八进制数456，即(456)8。
#   （4）十六进制整数。以0x或0X开头，其后由0至9的数字和a至f字母或A至F字母组成。如：0x7A表示十六进制数7A，即(7A)16

#【例】整型常量。
>>> 0xff
255
-----------------------------------------------------
>>> 2017
2017
-----------------------------------------------------
>>> 0b10011001
153
-----------------------------------------------------
>>> 0b012
SyntaxError: invalid syntax
-----------------------------------------------------
>>> -0o11
-9
```

#### 浮点型数据

```python
# 实数又称浮点数，一般有两种表示形式：
# （1）十进制小数形式。由数字和小数点组成（必须有小数点），如：1.2、.24、0.0等，浮点型数据允许小数点后没有任何数字，表示小数部分为0，如2.表示2.0.
# （2）指数形式。用科学计数法表示的浮点数，用字母e（或E）表示以10为底的指数，e之前为数字部分，之后为指数部分。如，123.4e3和123.4E3均表示123.4×103。
#    注意：e（或E）前面必须有数字，后面必须是整数。
#    15e2.3       e3      .e3

# 对于实型常量，Python 3.x默认提供17位有效数字的精度。

# 例如：
    >>> 1234567890012345.0
    1234567890012345.0
    -----------------------------------------------------
    >>> 12345678900123456789.0
    1.2345678900123458e+19
    -----------------------------------------------------
    >>> 15e2
    1500.0
    -----------------------------------------------------
    >>> 15e2.3
    SyntaxError: invalid syntax
```

#### 字符型数据

```python
#    在Python中定义一个字符串可以用用一对单引号、双引号或者三引号进行界定，且单引号、双引号和三引号还可以相互嵌套，表示复杂的字符串。
# 例如：
    >>> “Let's go”
    “Let's go”
    -----------------------------------------------------
    >>> s=“'Python' Program”
    >>> s
    “'Python' Program”

```

#### 转义字符

| 字符形式 | 含    义                           |
| -------- | ---------------------------------- |
| **\n**   | 回车换行，将当前位置移到下一行开头 |
| **\t**   | 横向跳到下一制表位置（Tab）        |
| \b       | 退格，将当前位置退回到前一列       |
| \r       | 回车，将当前位置移到当前行开头     |
| \f       | 走纸换页，将当前位置移到下页开头   |
| \\\      | 反斜线符"\\"                       |
| **\\'**  | 单引号符                           |
| **\\"**  | 双引号符                           |
| **\ddd** | 1～3位8进制数所代表的字符          |
| **\xhh** | 1～2位16进制数所代表的字符         |

```python
# 使用转义字符时要注意：
#（1）转义字符多用于print()函数中。
#（2）转义字符常量，如'\n'，'\x86'等只能代表一个字符。
#（3）反斜线后的八进制数可以不用0开头。如'\101'代表字符常量'A'，     '\134'代表字符常量'\'。
#（4）反斜线后的十六进制数只能以小写字母x开头，不允许用大写字母X或0x开头。

# 【例】 转义字符
 a=1
 b=2
 c='\101'
 print("\t%d\n%d%s\n%d%d\t%s"%(a,b,c,a,b,c))
# 运行结果：
	1
2A
12	A
```

#### 布尔型数据

```python
# Python的布尔类型有两个值：True和False，分别表示逻辑真和逻辑假。
#【例】布尔型数据示例。
 >>> type(True)
 <class 'bool'>
 -----------------------------------------------------
 >>> True==1
 True
 -----------------------------------------------------
 >>> False==0
 True

# 布尔类型还可以与其他数据类型进行逻辑运算，Python规定：0、空字符串、None为False，其他数值和非空字符串为True
 >>> 1>2
 False
 -----------------------------------------------------
 >>> False > -1
 True
 >>> 0 and False
 0
 -----------------------------------------------------
 >>> None or True
 True
 -----------------------------------------------------
 >>> "" or 1
 1
```

#### 复数类型数据

```python
# 复数由两部分组成：实部和虚部。复数的形式为：实部+虚部j。
# 例如：
  >>> x=3+5j   #x为复数
  >>> x.real   #查看复数实部
  3.0
  -----------------------------------------------------
  >>> x.imag   #查看复数虚部
  5.0
  -----------------------------------------------------
  >>> y=6-10j   #y为复数
  >>> x+y       #复数相加
  (9-5j)
```

### 运算符与表达式

#### 运算符

```python
# Python语言的运算符按照它们的功能可分为：
#（1）算术运算符（ +、–、*、/、**、//、％ ）。
#（2）关系运算符（ >、<、>=、<=、= =、!= ）。
#（3）逻辑运算符（and、or、not ）。
#（4）位运算符（ <<、>>、~、|、^、& ）。
#（5）赋值运算符（ =、复合赋值运算符 ）。
#（6）成员运算符（ in、not in ）。
#（7）同一运算符（ is、is not ）。
#（8）下标运算符（ [ ] ）。
#（9）其他（ 如函数调用运算符（））。

#  Python中除法有两种：/和//，在Python3.x分别表示除法和整除运算
  >>> 3/5
  0.6
  -----------------------------------------------------
  >>> 3//5
  0
  -----------------------------------------------------
  >>> -3.0//5
  -1.0
  -----------------------------------------------------
  >>> 3.0//-5
  -1.0

# **运算符实现乘方运算，其优先级高于*和/。
# 例如： 
 >>> 2**3
 8
 -----------------------------------------------------
 >>> 2**3.5
 11.313708498984761
 -----------------------------------------------------
 >>> 4*3**2
 36

# 运算符的多重含义：
 >>> 3*5      #整数相乘运算
 15
 -----------------------------------------------------
 >>> 'a'*10     #字符串重复运算
 'aaaaaaaaaa'

# %运算符表示对整数和浮点数的取模运算。
# 由于受浮点数精确度的影响，计算结果会有误差。
 >>> 5%-3
 -1
 -----------------------------------------------------
 >>> 10.5%2.1           #浮点数取模运算
 2.0999999999999996    #结果有误差
 
# 赋值运算符和赋值表达式
# 1. 赋值运算符
#    赋值运算符用“=”表示。
# 2. 赋值表达式
# 变量 = 表达式
#    等号的左边必须是变量，右边是表达式。
  >>>y=2
  >>>x=(y+2)/3       
  >>>x
  1.3333333333333333
```

#### 表达式

```python
# 算术表达式
#    例如：
#    3+a*b/5-2.3+′b′

# 数据转换
#    在Python中，同一个表达式允许不同类型的数据参加运算，这就要求在运算之前，先将这些不同类型的数据转换成同一类型，然后再进行运算
#【例】自动类型转换。
 >>> 10/4*4
 10.0
 >>> type(10/4*4)
 <class 'float'>
```

当自动类型转换达不到转换需求时，可以使用类型转换函数，将数据从一种类型强制（或称为显式）转换成另一种类型

| 函数           | 功能描述                                        |
| -------------- | ----------------------------------------------- |
| int(x)         | 将x转换为整数                                   |
| **float(x)**   | 将x转换为浮点数                                 |
| **complex(x)** | 将x转换为复数，其中实部为x，虚部为0             |
| complex(x,y)   | 将x、y转换为复数，其中实部为x，虚部为y          |
| **str**(x)     | 将x转换为字符串                                 |
| **chr**(x)     | 将一个整数转换为一个字符，整数为字符的ASCII编码 |
| **ord**(x)     | 将一个字符转换为它的ASCII编码的整数值           |
| **hex(x)**     | 将一个整数转换为一个十六进制字符串              |
| **oct**(x)     | 将一个整数转换为一个八进制字符串                |
| **eval**(x)    | 将字符串str当做有效表达式求值，并返回计算结果   |

```python
# Python的赋值和一般的高级语言的赋值有很大的不同，它是引用赋值。看下面的代码1：例如：
  >>> a = 5
  >>> b = 8
  >>> a = b    
#   执行a=5和b=8之后a指向的是5，b指向的是8，当执行a = b的时候，b把自己指向的地址（也就是8的内存地址）赋给了a，那么最后的结果就是a和b同时指向了8。
```

#### 多变量赋值

```python
# 多变量赋值
#（1）链式赋值
#    在Python中，可通过链式赋值将同一个值赋给多个变量的，一般形式为：
  >>>x=y=5
  >>>x

#（2）多变量并行赋值
#    变量1,变量2,…,变量n=表达式1,表达式2, …,表达式n
#    变量个数要与表达式的个数一致，其过程为：首先计算表达式右边n个表达式的值，然后同时将表达式的值赋给左边的n个变量。例如：
 >>>x,y,z=2,5,8
 >>>x
 2
 -----------------------------------------------------
 >>>y
 5
 -----------------------------------------------------
 >>>z
 8
# 例如：
   >>>x=20
   >>>x,x=3,x*3
   >>>x
   60
#   采取并行赋值，可以使用一条语句就可以交换两个变量的值：x,y=y,x
```

#### 复合赋值运算符

```python
# 赋值运算符“＝”与7种算术运算符（+、-、*、/、//、**、％）和5种位运算符（>>、<<、&、^、|）结合构成12种复合的赋值运算符。结合方向为自右至左。
# 例如：
    a+=3      	    	等价于     	a=a+3
    a*=a+3    	    	等价于     	a=a*(a+3)
    a%=3       	等价于     	a=a%3

# 注意：“a*=a+3”与“a=a*a+3”是不等价的，“a*=a+3”等价于“a=a*(a+3）”，这里括号是必需的。

#【例】复合的赋值运算符。
 >>> a=3
 >>> b=5
 >>> a+=b
 >>> a
 8
 -----------------------------------------------------
 >>> a>>=2
 >>> a
 2
 -----------------------------------------------------
 >>> a*=a+3
 >>> a
 10
```

#### 关系运算符

| **运算符** | **含义**     | **优先级**                                           | **结合性** |
| ---------- | ------------ | ---------------------------------------------------- | ---------- |
| **>**      | **大于**     | **这些运算符的优先级相同，但比下面的运算符优先级低** | **左结合** |
| **>=**     | **大于等于** | **这些运算符的优先级相同，但比下面的运算符优先级低** | **左结合** |
| **<**      | **小于**     | **这些运算符的优先级相同，但比下面的运算符优先级低** | **左结合** |
| **<=**     | **小于等于** | **这些运算符的优先级相同，但比上面的运算符优先级高** | **左结合** |
| **==**     | **等于**     | **这些运算符的优先级相同，但比上面的运算符优先级高** | **左结合** |
| **!=**     | **不等于**   | **这些运算符的优先级相同，但比上面的运算符优先级高** | **左结合** |
| **<>**     | **不等于**   | **这些运算符的优先级相同，但比上面的运算符优先级高** | **左结合** |

```python
# 关系运算符的优先级：{ >、>=、<、<=}→{==、!= 、<> }
#    在Python中，真用“True”表示，假用“False”表示。
  >>> x,y,z=2,3,5
  >>> x>y
  False
  >>> x<y<z    
  True
```

```python
# 注意：浮点数的相等。在计算机中，浮点数是实数的近似值。执行一系列浮点数的运算后，可能会发生四舍五入的情况。
# 例如：
  >>> x=123456
  >>> y=-111111
  >>> z=1.2345678
  >>> a=(x+y)+z
  >>> b=x+(y+z)
  >>> a
  12346.2345678
  >>> b
  12346.234567799998
```

#### 逻辑运算符

not and or

优先级：not > and > or

not右结合性

and or左结合性

```python
# 逻辑表达式和短路运算
#（1）对于与运算a and b
#  如果a为真，继续计算b，b将决定最终整个表达式的真值，所以，结果为b的值。
#  如果a为假，无需计算b，就可以得知整个表达式的真值为假，所以，结果为a的值。例如：
  >>>True and 0               >>> False and 12
  0                           False
#（2）对于或运算a or b
#   如果a为真，无需计算b，就可得知整个表达式的真值为真，所以结果为a的值。
#   如果a为假，继续计算b，b将决定整个表达式最终的值，所以结果为b的值
# 例如：
    >>> True or 0
    True
    >>> False or 12
    12
    >>> False or 12 or 0
    12
```

#### 成员运算符

**成员运算符用于判断一个元素是否在某一个序列中，或者判断一个字符是否属于这个字符串等，运算结果仍是逻辑值**

| **运算符** | **含义** | **优先级** | **结合性** |
| ---------- | -------- | ---------- | ---------- |
| **in**     | **存在** | **相同**   | **左结合** |
| not in     | 不存在   | **相同**   | **左结合** |

```python
 >>> 'a' in 'abcd'
 True
 >>> 'ac' in 'abcd'
 False

# not in 运算符用于在指定的序列中查找某个值是否不存在，不存在返回True，存在返回False
 >>> 'a' not in 'bcd'
  True
 >>> 3 not in [1,2,3,4]
  False
```

#### 同一性运算符

**同一性运算符用于测试两个变量是否指向同一个对象，其运算结果是逻辑值**

| **运算符** | **含义**   | **优先级** | **结合性** |
| ---------- | ---------- | ---------- | ---------- |
| **is**     | **相同**   | **相同**   | **左结合** |
| **is not** | **不相同** | **相同**   | **左结合** |

```python
# is检查用来运算的两个变量是否引用同一对象，如果相同返回True，不相同则返回False。
# is not检查用来运算的两个变量是否不是引用同一对象，如果不是同一个对象返回True，否则返回False。
>>> x=y=2.5
>>> z=2.5
>>> x is y
True
>>> x is z
False
>>> x is not z
True
```

| 运算符       | 结合性   |
| ------------ | -------- |
| ()           | 从左至右 |
| **           | 从左至右 |
| *、/、%、//  | 从左至右 |
| +、-         | 从左至右 |
| <、<=、>、>= | 从左至右 |
| ==、!=、<>   | 从左至右 |
| is、not is   | 从左至右 |
| in、not in   | 从左至右 |
| not          | 从右至左 |
| and          | 从左至右 |
| or           | 从左至右 |

：优先级从高到低

### 序列

一系列连续值，它们通常是相关的，并且按一定顺序排列

• **引用元素：序列名＋[位置编号]**

• 第1个元素的位置编号为0

• 第i个元素是c[i-1]

序列也可以从尾部访问:

• **最后一个元素是** **c[-1]**

• 倒数第i个元素是c[-i]

位置编号----也称“下标”或“索引”，是整数或整数表达式

#### 列表

• 是Python中内置数据类型，是一个元素的有序集合

• **一个列表中的数据类型可以各不相同**

• **列表中的每一个数据称为元素**

• 其所有元素用逗号分割并放在一对中括号“[”和“]”中

**例如：**

**[1,2,3,4,5]**

**['Python', 'C','HTML','Java','Perl ']**

**['wade',3.0,81,[ 'bosh','haslem']]**

列表基本操作：

1.列表的创建

使用赋值运算符“=”将一个列表赋值给变量即可创建列表对象

```python
>>>a_list = ['physics', 'chemistry',2017, 2.5]

>>>b_list = ['wade',3.0,81,[ 'bosh','haslem']]

>>>c_list = [1,2,(3.0,'hello world!')]

>>>d_list = []
```

2.列表元素读取

方法为：**列表名[索引]**

```python
>>>a_list= ['physics', 'chemistry',2017, 2.5,[0.5,3]]

>>>a_list[1]

'chemistry'

>>> a_list[-1]

[0.5, 3]

>>> a_list[5]

Traceback (most recent call last):

 File "<pyshell#9>", line 1, in <module>

  a_list[5]

IndexError: list index out of range
```

3.列表切片

切片操作的方法是：列表名[开始索引:结束索引:步长]

a_list= ['physics', 'chemistry',2017, 2.5,[0.5,3]]

```python
>>>a_list[1:3]

['chemistry', 2017]

>>> a_list[1:-1]

['chemistry', 2017, 2.5]

>>> a_list[:3] 

['physics', 'chemistry', 2017]

>>> a_list[1:]

['chemistry', 2017, 2.5, [0.5, 3]]

>>> a_list[:]

['physics', 'chemistry', 2017, 2.5, [0.5, 3]]

>>>a_list[::2]

['physics', 2017, [0.5, 3]]
```

4.增加元素

（1）使用“+”运算符将一个新列表添加在原列表的尾部

**a_list= ['physics', 'chemistry',2017, 2.5,[0.5,3]]**

```python
>>> id(a_list)  #获取列表a_list的地址

49411096

>>> a_list=a_list+[5]

>>> a_list

['physics', 'chemistry', 2017, 2.5, [0.5, 3], 5]

>>> id(a_list)   #获取添加元组时候a_list的地址

49844992
```

（2）使用列表对象的append()方法向列表尾部添加一个新的元素

```python
>>>a_list.append('Python')

>>>a_list

['physics', 'chemistry', 2017, 2.5, [0.5, 3], 5, 'Python']
```

（3）使用列表对象的**extend()**方法将一个新列表添加在原列表的尾部

```python
>>>a_list.extend**([2017,'C'])

>>>a_list

['physics', 2017, 'chemistry', 2.5, [0.5, 3], 5, 'Python', 2017, 'C']
```

（4）使用列表对象的*insert()*方法将一个元素插入到列表的指定位置

```python
>>> a_list.insert(3,3.5)

>>> a_list

['physics', 2017, 'chemistry', 3.5, 2.5, [0.5, 3], 5, 'Python', 2017, 'C']
```

5.删除元素

**['physics', 2017, 'chemistry', 3.5, 2.5, [0.5, 3], 5, 'Python', 2017, 'C']**

（1）使用**del**命令删除列表中指定位置的元素

```python
>>> del a_list[2]

>>> a_list

['physics', 2017, 3.5, 2.5, [0.5, 3], 5, 'Python', 2017, 'C']

#del命令也可以直接删除整个列表

>>> b_list=[10,7,1.5]

>>> b_list

[10, 7, 1.5]

>>> del b_list

>>> b_list

Traceback (most recent call last):

 File "<pyshell#42>", line 1, in <module>

  b_list

NameError: name 'b_list' is not defined
```

（2）使用列表对象的**remove()**方法删除首次出现的指定元素

```python
>>>a_list.remove(2017)

>>> a_list

['physics', 3.5, 2.5, [0.5, 3], 5, 'Python', 2017, 'C']

>>> a_list.remove(2017)

>>> a_list

['physics', 3.5, 2.5, [0.5, 3], 5, 'Python', 'C']

>>> a_list.remove(2017)

Traceback (most recent call last):

 File "<pyshell#30>", line 1, in <module>

  a_list.remove(2017)

ValueError: list.remove(x): x not in list
```

列表常用函数：

1.cmp()

格式：cmp(列表1,列表2)

```python
>>>cmp([1,2,5],[1,2,3])
1
>>>cmp([1,2,3],[1,2,3])
0
>>>cmp([123, 'Bsaic'],[ 123, 'Python'])
-1
#在Python 3.x中，不再支持cmp()函数，可以直接使用关系运算符来比较数值或列表。
#例如：
>>> [123,'Bsaic']>[ 123,'Python']
False
>>> [1,2,3]==[1,2,3]
True
```

2.len()

```python
#格式：len(列表)
#功能：返回列表中的元素个数。
>>> a_list= ['physics', 'chemistry',2017, 2.5,[0.5,3]]
>>> len(a_list)
5
>>> len([1,2.0,'hello'])
3
```

3.max()与min()

```python
#格式：max(列表)，min(列表)
>>> a_list=['123', 'xyz', 'zara', 'abc']
>>> max(a_list)
'zara'
>>> min(a_list)
'123'
```

4.sum()

```python
#格式：sum(列表)
#功能：对数值型列表的元素进行求和运算，对非数值型列表运算则出错

>>> a_list=[23,59,-1,2.5,39]
>>> sum(a_list)
122.5
>>> b_list=['123', 'xyz', 'zara', 'abc']
>>> sum(b_list)
Traceback (most recent call last):
  File "<pyshell#11>", line 1, in <module>
    sum(b_list)
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

5.sorted()

```python
#功能：对列表进行排序，默认是按照升序排序。该方法不会改变原列表的顺序。
>>> a_list=[80, 48, 35, 95, 98, 65, 99, 95, 18, 71]
>>> sorted(a_list)
[18, 35, 48, 65, 71, 80, 95, 95, 98, 99]
>>>a_list
[80, 48, 35, 95, 98, 65, 99, 95, 18, 71]
#降序排序: 在sorted()函数的列表参数后面增加一个reverse参数，让其值等于True则表示降序排序，等于Flase表示升序排序
>>> a_list=[80, 48, 35, 95, 98, 65, 99, 95, 18, 71]
>>> sorted(a_list,reverse=True)
[99, 98, 95, 95, 80, 71, 65, 48, 35, 18]
>>> sorted(a_list,reverse=False)
[18, 35, 48, 65, 71, 80, 95, 95, 98, 99]
```

6.sort()

```python
#功能：对列表进行排序，排序后的新列表会覆盖原列表，默认为升序排序。
>>> a_list=[80, 48, 35, 95, 98, 65, 99, 95, 18, 71]
>>> a_list.sort()
>>> a_list 
[18, 35, 48, 65, 71, 80, 95, 95, 98, 99]    
          
#降序排序: 在sort()方法中增加一个reverse参数
>>> a_list=[80, 48, 35, 95, 98, 65, 99, 95, 18, 71]
>>> a_list.sort(reverse=True)
>>> a_list
[99, 98, 95, 95, 80, 71, 65, 48, 35, 18]
>>> a_list.sort(reverse=False)
>>> a_list
[18, 35, 48, 65, 71, 80, 95, 95, 98, 99]
```

7.reverse()

```python
#格式：list.reverse()
#功能：对list列表中的元素进行翻转存放，不会对原列表进行排序。
>>> a_list=[80, 48, 35, 95, 98, 65, 99, 95, 18, 71]
>>> a_list.reverse()
>>> a_list                 
[71, 18, 95, 99, 65, 98, 95, 35, 48, 80]
```

方法总结：

| **方法**                     | **功能**                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| **list.append(obj)**         | **在列表末尾添加新的对象**                                   |
| **list.extend(seq)**         | **在列表末尾一次性追加另一个序列中的多个值**                 |
| **list.insert(index,  obj)** | **将对象插入列表**                                           |
| **list.index(obj)**          | **从列表中找出某个值第一个匹配项的索引位置**                 |
| **list.count(obj)**          | **统计某个元素在列表中出现的次数**                           |
| **list.remove(obj)**         | **移除列表中某个值的第一个匹配项**                           |
| **list.pop(obj=list[-1])**   | **移除列表中的一个元素（默认最后一个元素），并且返回该元素的值** |
| **sort()**                   | **对原列表进行排序**                                         |
| **reverse()**                | **反向存放列表元素**                                         |
| **cmp(list1,  list2)**       | **比较两个列表的元素**                                       |
| **len(list)**                | **求列表元素个数**                                           |
| **max(list)**                | **返回列表元素的最大值**                                     |
| **min(list)**                | **返回列表元素的最小值**                                     |
| **list(seq)**                | **将元组转换为列表**                                         |
| **sum(list)**                | **对数值型列表元素求和**                                     |

#### 元组

元组（tuple）的定义形式与列表类似，区别在于定义元组时所有元素放在一对圆括号“(”和“)”中

**例如：**

**(1,2,3,4,5 )**

**('Python', 'C','HTML','Java','Perl ')**

元组的基本操作：

1.元组的创建

```python
#赋值运算符“=”将一个元组赋值给变量即可创建元组对象。
>>>a_tuple=('physics', 'chemistry',2017, 2.5) 
>>>b_tuple=(1,2,(3.0,'hello world!'))
>>>c_tuple=('wade',3.0,81,[ 'bosh','haslem'])
>>>d_tuple=()

#创建只包含一个1个元素的元组方法：
>>> x=(1)
>>> x
1
>>> y=(1,)
>>> y
(1,)      
>>> z=(1,2)
>>> z
(1, 2)
```

2.读取元素

```python
#方法为：元组名[索引]
>>> a_tuple= ('physics', 'chemistry',2017, 2.5)
>>> a_tuple[1]
'chemistry'
>>> a_tuple[-1]
2.5
>>> a_tuple[5]
Traceback (most recent call last):
  File "<pyshell#14>", line 1, in <module>
    a_tuple[5]
IndexError: tuple index out of range
```

3.元组切片

```python
#元组也可以进行切片操作，方法与列表类似。对元组切片可以得到一个新元组。
a_tuple= ('physics', 'chemistry',2017, 2.5)

>>> a_tuple[1:3]
('chemistry', 2017)
>>> a_tuple[::3]
('physics', 2.5)
```

4.检索元素

```python
a_tuple= ('physics', 'chemistry',2017, 2.5)
#（1）使用元组对象的index()方法可以获取指定元素首次出现的下标。
>>> a_tuple.index(2017)     
2
>>> a_tuple.index('physics',-3)    
Traceback (most recent call last):
  File "<pyshell#24>", line 1, in <module>
    a_tuple.index('physics',-3)
ValueError: tuple.index(x): x not in tuple   
#（2）使用元组对象的count()方法统计元组中指定元素出现的次数。
>>> a_tuple.count(2017)
1
>>> a_tuple.count(1)
0
#（3）使用in运算符检索某个元素是否在该元组中。
>>> 'chemistry' in a_tuple
True
>>> 0.5 in a_tuple
False
```

5.删除元组

```python
#使用del语句删除元组，删除之后对象就不存在了，再次访问会出错。

>>> del a_tuple
>>> a_tuple
Traceback (most recent call last):
  File "<pyshell#30>", line 1, in <module>
    a_tuple
NameError: name 'a_tuple' is not defined
```

#### 列表与元组的区别和转换

1.区别

**不同点在于列表是可变序列**

**与列表相比，元组具有以下优点。**

**（1）元组的处理速度和访问速度比列表快。如果定义了一系列常量值，主要对其进行遍历或者类似用途，而不需要对其元素进行修改，这种情况一般使用元组。可以认为元组对不需要修改的数据进行了“写保护”，可以使代码更安全。**

**（2）作为不可变序列，元组（包含数值、字符串和其他元组的不可变数据）可用作字典的键，而列表不可以充当字典的键，因为列表是可变的**

2.转换

```python
>>> a_list= ['physics', 'chemistry',2017, 2.5,[0.5,3]]
>>> tuple(a_list) #列表转元组
('physics', 'chemistry', 2017, 2.5, [0.5, 3])
>>> type(a_list)      
<class 'list'>          
>>> b_tuple=(1,2,(3.0,'hello world!'))
>>> list(b_tuple) #元组转列表
[1, 2, (3.0, 'hello world!')]
>>> type(b_tuple) 
<class 'tuple'>
# 这是不会改变原来的数据类型的，需要时可以创建一个新的变量接收转化结果
```

3.元组的应用

```python
#利用元组来一次性给多个变量赋值。
>>> v = ('Python', 2, True)
>>> (x,y,z)=v
>>> x
'Python'
>>> y
2
>>> z
True
```

#### 字符串

Python中的字符串用一对单引号（'）或双引号（"）括起来

```python
>>> 'Python'	
'Python'
>>> "Python Program"
'Python Program'

# 如果字符串占据了几行，但却想让Python保留输入时使用的准确格式，例如行与行之间的回车符、引号、制表符或者其他信息都保存下来，则可以使用三重引号
>>> '''Python is an "object-oriented" 
open-source programming language'''
'Python is an "object-oriented"\n open-source programming language'
```

字符串基本操作：

1.字符串的创建

```python
# 使用赋值运算符“=”将一个字符串赋值给变量即可创建字符串对象。
>>> str1="Hello"
>>> str1
"Hello"
>>> str2='Program \n\'Python\''	
>>> str2
"Program \n'Python'"
```

2.字符串元素读取

```python
# 方法为：字符名[索引]
>>> str1[0]
'H'
>>> str1[-1]
'o'
```

3.字符串切片

```python
# 方法：字符串名[开始索引:结束索引:步长] 
>>> str="Python Program"
>>> str[0:5:2]	
'Pto'
>>> str[:]             
'Python Program'
>>> str[-1:-20]          
''                     
>>> str[-1:-20:-1] # 字符串逆置
'margorP nohtyP'
```

4.字符串连接

```python
# 使用运算符“+”，将两个字符串对象连接起来
>>> "Hello"+"World"
'HelloWorld'
>>> "P"+"y"+"t"+"h"+"o"+"n"+"Program"
'PythonProgram'
# 将字符串和数值类型数据进行连接时，需要使用str()函数将数值数据转换成字符串，然后再进行连接运算。
>>> "Python"+str(3)
'Python3'
```

5.字符串重复

```python
# 字符串重复操作使用运算符“*”，构建一个由字符串自身重复连接而成的字符串对象。例如：
>>> "Hello"*3
'HelloHelloHello'
>>> 3*"Hello World!"
'Hello World!Hello World!Hello World!'
```

6.字符串关系运算

```python
#（1）单字符字符串的比较
# 单个字符字符串的比较是按照字符的ASCII码值大小进行比较。例如：
>>> "a"=="a"
True
>>> "a"=="A"
False
>>> "0">"1"
False
#（2）多字符字符串的比较
# 例如：
>>> "abc"<"abd"
True
>>> "abc">"abcd"
False
>>> "abc"<"cde"
True
>>> ""<"0"
True
# 注意：空字符串（""）比其他字符串都小，因为它的长度为0
```

7.成员运算

```python
# 字符串使用in或not in运算符判断一个字符串是否属于另一个字符串。

# 字符串1 [not] in 字符串2
>>> "ab" in "aabb"
True
>>> "abc" in "aabbcc"
False
>>> "a" not in "abc"
False
```

字符串常用方法：

```python
# 1.子串查找
str.find(substr,[start,[,end]])
>>> s1="beijing xi'an tianjin beijing chongqing"
>>> s1.find("beijing")
0
>>> s1.find("beijing",3)
22
>>> s1.find("beijing",3,20)
-1

# 2. 字符串替换
#字符串替换replace()方法的一般形式为：
#	str.replace(old,new(,max))
>>> s2 = "this is string example. this is string example."
>>> s2.replace("is", "was") 
'thwas was string example. thwas was string example.'
>>> s2 = "this is string example. this is string example."
>>> s2.replace("is", "was",2)
'thwas was string example. this is string example.'

# 3. 字符串分离
#		str.split([sep])
# sep 表示分隔符，默认以空格作为分隔符。若参数中没有分隔符，则把整个字符串作为列表的一个元素，当有参数时，以该参数进行分割。
>>> s3="beijing,xi'an,tianjin,beijing,chongqing"
>>> s3.split(',')                    
['beijing', "xi'an", 'tianjin', 'beijing', 'chongqing']    
>>> s3.split('a')                     
["beijing,xi'", 'n,ti', 'njin,beijing,chongqing']
>>> s3.split()                      ["beijing,xi'an,tianjin,beijing,chongqing"]

# 4. 字符串连接
#       sep.join(sequence)
# 其中sep表示分隔符，可以为空，sequence是要连接的元素序列。
>>> s4=["beijing","xi'an","tianjin", "chongqing"]
>>> sep="-->"
>>> str=sep.join(s4)          #连接列表元素
>>> str                       #输出连接结果
"beijing-->xi'an-->tianjin-->chongqing"
>>>s5=("Hello","World")
>>>sep=""
>>> sep.join(s5)               #连接元组元素
'HelloWorld'
```

### 字典

```python
# 通过任意键信息查找一组数据中值信息的过程叫映射，Python语言中通过字典实现映射。Python语言中的字典可以通过大括号({})建立，建立模式如下：
# {<键1>:<值1>, <键2>:<值2>, … , <键n>:<值n>}
# 其中，键和值通过冒号连接，不同键值对通过逗号隔开

>>>Dcountry={"中国":"北京", "美国":"华盛顿", "法国":"巴黎"}
>>>print(Dcountry)
{'中国': '北京', '法国': '巴黎', '美国': '华盛顿'}
# 字典打印出来的顺序与创建之初的顺序不同，各个元素并没有顺序之分
```

字典常用操作：

1.字典的创建

```python
# （1）使用“=”将一个字典赋给一个变量
>>> a_dict={'Alice':95,'Beth':82,'Tom':65.5,'Emily':95}
>>> a_dict    
{'Emily': 95, 'Tom': 65.5, 'Alice': 95, 'Beth': 82}
>>> b_dict={}
>>> b_dict
{}

# （2）使用内建函数dict()
>>>c_dict=dict(zip(['one', 'two', 'three'], [1, 2, 3]))  
>>>c_dict
{'three': 3, 'one': 1, 'two': 2}
>>>d_dict = dict(one = 1, two = 2, three = 3)
>>>e_dict = dict([('one', 1),('two',2),('three',3)])
>>>f_dict = dict((('one', 1),('two',2),('three',3)))
>>> g_dict= dict()
>>> g_dict
{} 

# （3）使用内建函数fromkeys()
# 一般形式：dict.fromkeys(seq[, value]))
>>> h_dict={}.fromkeys((1,2,3),'student’) 
>>> h_dict
{1: 'student', 2: 'student', 3: 'student'}
>>> i_dict={}.fromkeys((1,2,3))
>>> i_dict
{1: None, 2: None, 3: None}
>>> j_dict={}.fromkeys(())    
>>> j_dict
{}
```

2.字典元素的读取

```python
# （1）使用下标的方法
>>>a_dict={'Alice':95,'Beth':82,'Tom':65.5,'Emily':95}
>>> a_dict['Tom']
65.5
>>> a_dict[95]
Traceback (most recent call last):
  File "<pyshell#32>", line 1, in <module>
    a_dict[95]
KeyError: 95

# （2）使用get方法获取执行键对于的值
# 一般形式：dict.get(key, default=None)
# default是指指定的“键”值不存在时，返回的值
>>> a_dict.get('Alice')
95
>>> a_dict.get('a','address')   
'address'
>>> a_dict.get('a')
>>> print(a_dict.get('a'))
None
```

3.字典元素的添加与修改

```python
# （1）使用 “字典名[键名]=键值”形式
>>> a_dict['Beth']=79         
>>> a_dict
{'Alice': 95, 'Beth': 79, 'Emily': 95, 'Tom': 65.5}
>>> a_dict['Eric']=98          
>>> a_dict
{'Alice': 95, 'Eric': 98, 'Beth': 79, 'Emily': 95, 'Tom': 65.5}

# （2）使用update()方法
>>> a_dict={'Alice': 95, 'Beth': 79, 'Emily': 95, 'Tom': 65.5}
>>> b_dict={'Eric':98,'Tom':82}
>>> a_dict.update(b_dict)       
>>> a_dict
{'Alice': 95, 'Beth': 79, 'Emily': 95, 'Tom': 82, 'Eric': 98}
```

4.字典元素的删除

```python
# （1）使用del命令删除字典中指定“键”对应的元素
>>> del a_dict['Beth']
>>> a_dict
{'Alice': 95, 'Emily': 95, 'Tom': 82, 'Eric': 98}
>>> del a_dict[82]
Traceback (most recent call last):
  File "<pyshell#56>", line 1, in <module>
    del a_dict[82]
KeyError: 82

# （2）使用pop()方法删除并返回指定“键”的元素
>>> a_dict.pop('Alice')
95
>>>a_dict
{Emily': 95, 'Tom': 82, 'Eric': 98}

# （3）使用popitem()方法，随机元素
>>> a_dict.popitem()
('Emily', 95)
>>> a_dict
{'Tom': 82, 'Eric': 98}

# （4）使用clear()方法
>>> a_dict.clear()
>>> a_dict
{}

# （5）使用del命令删除整个字典
>>> del a_dict
>>> a_dict
Traceback (most recent call last):
  File "<pyshell#68>", line 1, in <module>
    a_dict
NameError: name 'a_dict' is not defined
```

字典的遍历：

1.遍历字典关键字

```python
# 一般形式：dict.keys()
a_dict={'Alice': 95, 'Emily': 95, 'Tom': 82, 'Eric': 98}
>>> a_dict.keys()
dict_keys(['Tom', 'Emily', 'Beth', 'Alice'])
```

2.遍历字典的值

```python
# 一般形式：dict.values()
a_dict={'Alice': 95, 'Emily': 95, 'Tom': 82, 'Eric': 98}
>>> a_dict.values()
dict_values([65.5, 95, 79, 95])
```

3.遍历字典元素

```python
# 一般形式：dict.items()
{'Alice': 95, 'Emily': 95, 'Tom': 82, 'Eric': 98}
>>> a_dict.items()
dict_items([('Tom', 65.5), ('Emily', 95), ('Beth', 79), ('Alice', 95)])
```

### 集合

```python
# 集合（set）是一组对象的集合，是一个无序排列的、不重复的数据集合体。
# 用一对“ { } ”进行界定:
#  			s={0,1,2,3,4}
```

集合常用操作：

1.集合的创建

```python
# （1）使用“=”将一个集合赋给一个变量
>>> a_set={0,1,2,3,4,5,6,7,8,9}
>>> a_set
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
>>> b_set={1,3,3,5}     //重复元素
>>> b_set
{1, 3, 5}

# （2）使用集合对象的set()方法创建集合
>>> b_set=set(['physics', 'chemistry',2017, 2.5]) 
>>> b_set
{2017, 2.5, 'chemistry', 'physics'}
>>> c_set=set(('Python', 'C','HTML','Java','Perl '))
>>> c_set
{'Java', 'HTML', 'C', 'Python', 'Perl '}
>>> d_set=set('Python') 
>>> d_set
{'y', 'o', 't', 'h', 'n', 'P'}

# （3）使用frozenset()方法创建一个冻结的集合
>>> e_set=frozenset('a')     
>>> a_dict={e_set:1,'b':2}
>>> a_dict
{frozenset({'a'}): 1, 'b': 2}
>>> f_set=set('a')
>>> b_dict={f_set:1,'b':2}     
Traceback (most recent call last):
  File "<pyshell#9>", line 1, in <module>
    b_dict={f_set:1,'b':2}
TypeError: unhashable type: 'set'
```

2.访问集合

```python
# 使用in或者循环遍历访问元素
>>> b_set=set(['physics', 'chemistry',2017, 2.5])
>>> b_set
{'chemistry', 2017, 2.5, 'physics'}
>>> 2.5 in b_set
True
>>> 3 in b_set
False
>>> for i in b_set:print(i,end=' ')
chemistry 2017 2.5 physics
```

3.删除集合

```python
# 使用del命令
>>> a_set={0,1,2,3,4,5,6,7,8,9}
>>> a_set
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
>>> del a_set
>>> a_set
Traceback (most recent call last):
  File "<pyshell#66>", line 1, in <module>
    a_set
NameError: name 'a_set' is not defined
```

4.更新集合

```python
b_set={'chemistry', 2017, 2.5, 'physics'}
# （1）使用add()方法：s.add(x)
>>> b_set.add('math')
>>> b_set
{'chemistry', 2017, 2.5, 'math', 'physics'}

# （2）使用update()方法：s.update(s1,s2,…,sn)
>>> s={'Phthon','C','C++'}
>>> s.update({1,2,3},{'Wade','Nash'},{0,1,2})
>>> s
{0, 1, 2, 3, 'Phthon', 'Wade', 'C++', 'Nash', 'C'}
```

5.删除集合中的元素

```python
# （1）使用remove()方法：s.remove(x)
>>> s={0, 1, 2, 3, 'Phthon', 'Wade', 'C++', 'Nash', 'C'}
>>> s.remove(0)
>>> s
{1, 2, 3, 'Phthon', 'Wade', 'C++', 'Nash', 'C'}
>>> s.remove('Hello')
Traceback (most recent call last):
  File "<pyshell#45>", line 1, in <module>
    s.remove('Hello')
KeyError: 'Hello'

# （2）使用discard ()方法：s. discard(x)
s={1, 2, 3, 'Phthon', 'Wade', 'C++', 'Nash', 'C'}
>>> s.discard('C')
>>> s
{1, 2, 3, 'Phthon', 'Wade', 'C++', 'Nash'}
>>> s.discard('abc')
>>> s
{1, 2, 3, 'Phthon', 'Wade', 'C++', 'Nash'}

# （3）使用pop()方法删除任意一个元素
s={1, 2, 3, 'Phthon', 'Wade', 'C++', 'Nash', 'C'}
>>> s.pop()
1
>>> s
{2, 3, 'Phthon', 'Wade', 'C++', 'Nash'}

# （4）使用clear()方法删除集合中所有元素
>>> s.clear()
>>> s
set() 
```

集合常用运算：

1.交集

```python
# 方法：s1&s2&…&sn
>>> {0,1,2,3,4,5,7,8,9}&{0,2,4,6,8}
{8, 0, 2, 4}
>>> {0,1,2,3,4,5,7,8,9}&{0,2,4,6,8}&{1,3,5,7,9}
set() 
```

2.并集

```python
# 方法： s1|s2|…|sn
>>> {0,1,2,3,4,5,7,8,9}|{0,2,4,6,8}
{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
>>> {0,1,2,3,4,5}|{0,2,4,6,8}
{0, 1, 2, 3, 4, 5, 6, 8}
```

3.差集

```python
# 方法： s1-s2-…-sn
>>> {0,1,2,3,4,5,6,7,8,9}-{0,2,4,6,8}
{1, 3, 5, 9, 7}
>>> {0,1,2,3,4,5,6,7,8,9}-{0,2,4,6,8}-{2,3,4}
{1, 5, 9, 7}
```

4.对称差集

```python
# 方法：s1^s2^…^sn
>>> {0,1,2,3,4,5,6,7,8,9}^{0,2,4,6,8}
{1, 3, 5, 7, 9}
>>> {0,1,2,3,4,5,6,7,8,9}^{0,2,4,6,8}^{1,3,5,7,9}
set()
```

5.集合的比较

```python
# ==、！=、<、<=、>、>=
```

------



## Python程序结构

任何程序均可以由“顺序”、“选择”和“循环”这三种基本结构通过有限次的组合与嵌套来描述。
任何程序设计语言均由这三种基本结构组成，其差别在于具体语法的形式不同，而原理是相同的。

### 顺序结构

数据的输入和输出：

```python
# 一般一个程序分为三步进行：输入原始数据、进行计算处理和输出运算结果 。
# （1）标准输入
input() # input( [提示字符串] )
# 在Python2.x中，函数返回结果的类型由输入时使用的界定符来决定。
>>>x=input("Please enter your input: ")
Please enter your input: 5    
>>>x=input("Please enter your input: ")
Please enter your input: '5'    
>>>x=input("Please enter your input: ")
Please enter your input: [1,2,3]    
>>>x=input("Please enter your input: ")
Please enter your input: (1,2,3)

#如果要输入数值类型数据，可以使用类型转换函数将字符串转换为数值。
>>> x=int(input(“please input your input:”))
please input your input:5 
>>>print(type(x))
<class'int'>
# 说明：x接受的是字符串5，通过int（）函数将字符串转换为整型。

# （2）标准输出
# 在Python2.x中使用的是print语句输出
# 在Python3.x中使用的是print（）函数输出

# print()函数的一般形式为：
print([输出项1，输出项2，....,输出项n][,sep=分隔符][,end=结束符])
# 说明：输出项之间用逗号分隔，没有输出项时输出一个空行。sep表示输出时候各个输出项之间的分隔符，end表示输出时的结束符。
# print函数从左求出至右各输出项的值，并将各输出项的值依次显示在屏幕的同一行上。
>>>x,y=2,3
>>>print(x,y)
2 3
>>>print(x,y,sep=':')
2:3
>>>print(x,y,sep=':',end='%)
2:3%

# (3)格式化输出
# 1.字符串格式化%
 # 格式控制字符串%（输出项1，输出项2，...输出项）
 #（1）常规字符：包括可显示的字符和用转义字符表示的字符。
 #（2）格式控制符：以%开头的一个或多个字符，以说明输出数据的类型、形式、长度、小数位数等。
# 例如： 
print（“sum=%d”%x）

# print（）格式化说明：
# i \ d : 十进制格式
# o : 八进制格式
# x \ X: 十六进制格式
# c: 一个字符格式
# s: 字符串格式
# f: 实数格式
# e \ E: 指数格式
# g \ G: e和f中较短一种格式

# 2.附加格式说明符
# m ：域宽，十进制整数，用以描述输出数据所占宽度。
# n： 附加域宽，十进制整数，用于指定实型数据小数部分的输出位数。
# —：输出数据左对齐，默认为右对齐
# +：输出正数时，也以+号开头
# #：作为o，x的前缀时，输出结果前面加上前导符号0，,0x

>>>year = 2017
>>>month = 1
>>>day = 28
>> print('%04d-%02d-%02d'%(year,month,day)) 
2017-01-28    
 >>>value = 8.123
>> print('%06.2f'%value)
008.12 
>>>print('%d'%10) 
10
>>>print('%o'%10)
12
```

### 选择结构

简单的if语句：

```python
# 形式：
if 表达式:
    语句

# 例如：将小写字母转换为大写字母
c=input("Please input character:")
if c>='a' and c<='z':
         c=ord(c)-32
print("%c\n"%c)

```

双分支选择：

```python
# 形式： 
if 表达式:
    语句A
else:
	语句B

# 例如：求输入的两个整数的最小值
x,y=eval(input(‘请输入x、y:’))
if a<b:
        min=a
else:
        min=b
printf(“The minimum is %d”%min)
```

多分支选择：

```python
# 形式：
if exp1 :
    选择体1
elif  exp2:
    选择体2
……
elif expn: 
     选择体n
else:
     选择体n+1

```

选择结构嵌套 -> if语句的嵌套：如果if的内嵌语句中又使用了一个if语句**，**则构成if语句的嵌套

### 循环结构

while循环：

```python
# 特点：先判断，再执行(当型循环)
# while 语句的语法格式：
while exp:
     循环体
# 注意：
 # 循环体可能一次也不执行。
 # 循环体可为任意类型语句。
 # 退出while循环的情况:
  # exp不成立
  # 循环体内遇break, return, exit等。

# 在while语句中使用else子句
while exp:
     循环体
else:
      语句
# Python可以在循环语句中使用else子句，即构成了while.......else循环结构

# 例如：
sum=0
x=input(“请输入正整数:")
while x>=0:
	sum=sum+x
	x=input(“请输入正整数:")
    
printf("sum=", sum)

# 例如：
x=int(input(“Please input  x :”);
if x>=0 and x<99999;
i=x
n=0
while  i>0:
      i=i//10
      n=n+1
      a=x%10
      print(“%d是%d位数，它的个位上数字是%d”%(x, n,a))
else:
      print(“输入错误！”)

```

for循环：

```python
# 1.for语句一般形式：
for 目标变量 in 序列对象:
    循环体语句
# 说明：
# for语句的首行定义了目标变量和遍历的序列对象，后面是需要重复执行的语句块。语句块中的语句要向右缩进，且缩进量要一致
# 注意：
# （1）for语句是通过遍历任意序列的元素来建立循环的。
# （2）for 语句也支持一个可选的else块，一般格式如下：
for 目标变量 in 序列对象:
    语句块
else:
    语句

# 2.range对象在for循环中的应用
# range函数返回的是可迭代对象，range（）函数的一般格式为：
range（[start,]end[,step]）
# rang ()函数共有三个参数，start和step是可选的，start表示开始，默认值为0，end表示结束，step表示每次跳跃的间距，默认值为1。函数的功能是从一个start参数的值开始，到end参数的值结束的数字序列。

# 传递一个参数的range()函数：参数为end
>>> for i in range(5):
	print(i)
0
1
2
3
4
# 传递两个参数的range()函数：参数为start和end
>>> for i in range(2,4):
	print(i)
2
3
# 传递三个参数的range()函数：参数为start，end，step
>>> for i in range(2,20,3):
	print(i)
2
5
8
11
14
17

# 例如：判断这个数是否为素数
m=int（input(“请输入要判断的正整数m”)）
flag=1
for i in range(2,m):
	if m%i==0:
		flag=0
		i=m    
if flag==1:
	print(“%d 是素数”%m)
else:
	print(“%d不是素数”%m)        
```

循环嵌套：

```python
# 循环的嵌套，又称多层循环，应用于多个变量同时变化的情况

# 说明：1. 外层循环可包含两个以上内循环
#      2. 嵌套的循环的执行流程

# 注意：
# while、for循环语句可以并列,也可以相互嵌套,但要层次清楚,不能出现交叉
# 多重循环程序执行时，外层循环每执行一次，内层循环都需要循环执行多次。
# 循环语句while和for可以相互嵌套。在使用循环嵌套时，

# 应注意以下几个问题：
# （1）外层循环和内层循环控制变量不能同名，以免造成混乱。
# （2）循环嵌套的缩进在逻辑上一定要注意，以保证逻辑上的重要性。
# （3）循环嵌套不能交叉，即在一个循环体内必须完整地包含另一个循环

# 例如：输出九九乘法表
for i in range (1,10,1):
	for j in range(1,i+1,1)：
	    print(“%d*%d=%2d  ”%(j,i,i*j),end=” ”)
	print(“”)

```

循环控制语句：

```python
# 有时候我们需要在循环体中提前跳出循环，或者在某种条件满足时，不执行循环体中的某些语句而立即从头开始新的一轮循环，这时就要用到循环控制语句break、continue和pass语句

# （1）break语句
# 一般格式
	break
# 功能：
# break语句用在循环体内，迫使所在循环立即终止，即跳出所在循环体，继续执行循环结构后面的语句。

# （2）continue语句
# 一般格式
	continue
# 功能：
# 在循环语句中强行提前结束本次循环,而不是终止循环。

# （3）pass语句
# pass语句是一个空语句，它不做任何操作，代表一个空操作，在特别的时候用来保证格式或是语义的完整性。
# 例如下面的循环语句：
for i in range(5):
       pass
# 该语句的确会循环5次，但是除了循环本身之外，它什么也没做。
```

------



## Python函数

**函数是一组实现某一特定功能的语句集合，是可以重复调用、功能相对独立完整的程序段**

使用函数的好处：

**①** **程序结构清晰，可读性好。**

**② 减少重复编码的工作量。**

**③ 可多人共同编制一个大程序，缩短程序设计周期，提高程序设计和调试的效率**

函数分类：
1.从用户使用角度：

* 库函数 / 标准函数：由系统提供在程序前导入该函数原型所在的模块

  使用库函数应注意：

  1、函数功能

  2、函数参数的数目和顺序，及各参数意义和类型

  3、函数返回值意义和类型

* 用户自定义函数

2.从参数传递的角度：

* 有参函数
* 无参函数

函数的定义与调用：

```python
# 定义一般形式：
def 函数名([形式参数表]):
    函数体
    [return 表达式]
# 函数定义时需要注意：
# 采用def 关键字定义函数，不需要指定返回值的类型；
# 函数的参数不限，不需要指定参数类型；
# 参数括号后面的冒号“:”必不可少；
# 函数体相对于def关键字必须保持一定的空格缩进；
# return语句是可选的；
# 允许定义函数体为空的函数

# 函数的调用：
# 一般形式：
    函数名([实际参数表])
# 说明:
# 实参可以是常量、变量、表达式、函数等，但在进行函数调用时必须有确定的值。
# 函数的实参和形参应在个数、类型和顺序上一一对应。
# 对于无参函数，调用时实参表列为空，但( )不能省。
# 在Python中不允许前向引用，即在函数定义之前，不允许调用该函数。

# 例
def getMax(a,b,c):
     if a>b:
          max=a
     else:
          max =b
     if(c>m):
          max =c
     return max
n = getMax(a,b,c)
```

函数的参数和函数的返回值

```python
# 形式参数：定义函数时函数名后面括号中的变量名
# 实际参数：调用函数时函数名后面括号中对应的参数
# 说明
# 实参可以是常量、变量和表达式，但必须在函数调用之间有确定的值。
# 形参与实参个数相同
# 形参定义时编译系统并不为其分配存储空间，也无初值；只有在函数调用时，临时分配存储空间，接受来自实参的值；函数调用结束，内存空间释放。

# 参数传递方式
# 1.单向的值传递
# 实参和形参之间是单向的值传递。在函数调用时，将各实参表达式的值计算出来，赋给形参变量。因此，实参与形参必须类型相同或赋值兼容，个数相等，一一对应。在函数调用中，即使实参为变量，形参值的改变也不会改变实参变量的值。
# 实参和形参占用不同的内存单元

# 2.传地址方式
# 函数调用时，将实参数据的存储地址作为参数传递给形参
# 传递列表类型数据为参数也就是传地址

# 例
# 传值：
def swap(a, b):
     a,b=b,a
     print("a=",a,"b=",b)

x,y=eval(input("input x,y:"))
swap(x,  y)
print("x=",x,"y=",y)
# 传址：
def swap(a_list):
     a_list[0],a_list[1]=a_list[1],a_list[0]
     print("a_list[0]=",a_list[0],"a_list[1]=",a_list[1])

x_list=[3,5]
swap(x_list)
print("x_list[0]=",x_list[0],"x_list[1]=",x_list[1])
```

```python
# 函数的返回值
# 指函数被调用、执行完后，返回给主调函数的值。
# 函数的返回语句
# 一般形式：
return  表达式
# 功能：使程序控制从被调用函数返回到调用函数中，同时把返回值带给调用函数
# 说明：
# 函数内可有多条返回语句。
# 如果没有return语句，会自动返回NONE；如果有return语句，但是return后面没有表达式也返回NONE
```

函数的递归调用

```python
# 在函数的执行过程中又直接或间接调用该函数本身 	
# 1.直接递归调用
# 在函数中直接调用函数本身
# 2.间接递归调用
# 在函数中调用其它函数，其它函数又调用原函数

# 递归算法的两个基本特征
# 1.递推归纳
# 将问题转化为比原问题小的同类规模，归纳出一般递推公式. 故所处理的对象要有规律地递增或递减
# 2.递归终止
# 当规模小到一定的程度应该结束递归调用，逐层返回常用条件语句来控制何时结束递归 

# 例：递归方法求n!
def fac(n):
   if  n==0:
        f=1
   else:
        f=fac(n-1)*n;
   return f

n=int(input("please input n: "))
f=fac(n)
print("%d!=%d"%(n,f))

# 总结
# 执行过程（两个阶段）
#    第一阶段：逐层调用，调用函数自身
#    第二阶段：逐层返回，返回到调用该层的位置
# 递归调用是多重嵌套调用的一种特殊情况
# 调用的深度：调用的层数
```

经典汉诺塔问题

```python
# 设有三座塔座（A、B、C），在一个塔座（设为A）上有64个盘片，盘片不等，按大盘在下，小盘在上的顺序依次叠放。现要将A塔上的盘片借助于B塔，移到C塔上并保持同样顺序叠排，移动盘片时必须遵守以下规则：
# （1）每次只能移动一个圆盘；
# （2）圆盘可以插在A、B、C任意一个塔座上；
# （3）任何时候都不能将一个较大的圆盘放到较小的圆盘之上。

# 问题分析：
# （1）n=1时，直接将其从A－>C；
# （2）n>1时，只要先将前n-1个借助C从A－>B,那么可以把第n个直接从A－>C;
# （3）如何将剩下的n-1个圆盘遵守规则借助A从B－>C，问题性质同（2）;

# 若将n个盘片按规定从A塔移至C塔，移动步骤可分为三步：
# 把A塔上的n-1个盘片借助C移动到B塔
# 把第n个盘片从A塔移至C塔
# 把B塔上的n-1个盘片借助A塔移至C塔

# 以递归算法实现
# n：盘片数
# x：源塔
# y：借助塔
# z：目标塔

count=0
def hanoi(n,x,y,z):
   global count
   if n==1:
        count+=1
        move(count,x,z)
   else:
       hanoi(n-1,x,z,y);                 
       count+=1
       move(count,x,z)
       hanoi(n-1,y,x,z);               
       
def move(n,x,y):
    print("step %d: Move disk form %c to %c"%(count,x,y))

m=int(input("Input the number of disks:"))
print("The steps to moving %d disks:"%m)
hanoi(m,'A','B','C')
```

插入排序

```python
def insert_sort(array):
  for i in range(1, len(array)):
    if array[i - 1] > array[i]:
        temp = array[i]
        index = i
        while index > 0 and array[index - 1] > temp:
            array[index] = array[index - 1]     
            index -= 1
        array[index] = temp
```

变量的作用域

```python
# 当程序中有多个函数时，定义的每个变量只能在一定的范围内访问，称之为变量的作用域。
# 按作用域划分，将变量分为局部变量和全局变量。

# 1.局部变量
# 在一个函数内或者语句块内定义的变量称为局部变量。局部变量的作用域仅限于定义它的函数体或语句块中。
# 例：
def fun1(a):
   x=a+10
   ……             
def fun2(a,b):
   x,y=a,b

# 2.全局变量
# 在所有函数之外定义的变量称为全局变量，它可以在多个函数中被引用。
# 例：
x = 30
def func():
        global x
        print('x的值是', x)
        x = 20
        print('全局变量x改为', x)
func()
print('x的值是', x)
# global关键字显示地告诉解释器 x 为全局变量，然后会在函数外面寻找 x 的定义
# global关键字的作用是可以使得一个局部变量为全局变量
```

### 模块

```python
# 将一些常用的功能单独放置到一个文件中，方便其他文件来调用，这些文件即为模块。
# 从用户的角度看，模块也分为标准库模块和用户自定义模块
```

标准库模块

```python
# 标准库模块是Python自带的函数模块。
# 	文本处理
# 	文件处理
# 	操作系统功能
# 	网络通信
# 	网络协议
```

**用户自定义模块**

```python
# 用户建立一个模块就是建立扩展名为.py的Python文件
def printer(x):
    print(x)
# 将以上程序代码保存成.py文件，例如module.py
```

导入模块

```python
# 导入模块就是给出一个访问模块提供的函数、对象和类的方法。

# （1）引入模块
       import 模块
# （2）引入模块中的函数
       from 模块名 import 函数名
# （3）引入模块中的所有函数
       from 模块名 import *
```

Python库的导入

```python
# Python启动后，默认情况下它并不会将它所有的功能都加载（也称之“导入”）进来，使用某些模块（或库，一般不做区分）之前必须把这些模块加载进来，这样就可以使用这些模块中的函数。模块把一组相关的函数或类组织到一个文件中，一个文件即是一个模块

# （1）库的常规导入
# 常规导入是最常使用的导入方式，导入方式如下所示：
import 库名
# 在导入模块时，还可以重命名这个模块，如下所示：
import sys as system
# 既可以按照和以前“sys.方法”的方式调用模块的方法，也可以用“system.方法”的方式调用模块的方法。

# （2）使用from语句导入
# 很多时候只需要导入一个模块或库中的某个部分，这时候可通过联合使用import和from来实现这个目的：
from math import sin
# 上面这行代码可以让我们直接调用sin：
>>> from math import sin
>>> sin(0.5)  #计算0.5弧度的正弦值
0.479425538604203
# 也可以一次导入多个函数：
>>> from math import sin, exp, log
# 也可以直接导入math库中的所有函数，导入方式如下所示：
>>> from math import *
```

### 扩展库的安装（pip命令）

当前，pip已成为管理Python扩展库和模块的主流方式，使用pip不仅可以查看本机已安装的Python扩展库和模块，还支持Python扩展库和模块的安装、升级和卸载等操作。pip命令的常用方法如表所示：

| pip命令示例               | 描述                 |
| ------------------------- | -------------------- |
| pip install xxx           | 安装xxx模块          |
| pip list                  | 列出已安装的所有模块 |
| pip install --upgrade xxx | 升级xxx模块          |
| pip uninstall xxx         | 卸载xxx模块          |

```python
# 使用pip命令安装Python扩展库，需要保证计算机联网，然后在命令提示符环境中通过pip install xxx进行安装，这里分两种情况：
# 如果Python安装在默认路径下 ，打开控制台直接输入：pip install 扩展库或模块名即可。
# 如果Python安装在非默认环境下，在控制台中，需进入到pip.exe所在目录（位于Scripts文件夹下），然后再输入“pip install 扩展库或模块名”即可。
```

