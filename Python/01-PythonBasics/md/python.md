# Python

## Python简介

### 作者

1991年，由<u>Gudio van Rossum（吉多· 范罗苏姆）</u>为了打发时间而写出

![](https://tse1-mm.cn.bing.net/th/id/OIP-C.Y-WaIPX1b7xlLZXnbOOVbAAAAA?w=230&h=185&c=7&r=0&o=5&pid=1.7)

### 特点

简单 易学 开发效率高 适用面广泛

### 应用场景

* 大数据
* 人工智能
* 后端开发
* 等等

------



## Python安装

前往[Python官网](https://www.python.org)下载并安装即可

cmd命令行输入

```cmd
python -V
```

若显示出安装的python版本即证明安装成功

欢迎进入编程世界😊

------



## PyCharm安装

pycharm由Jetbrains公司编写，是最专业也是使用最多的IDE

前往Jetbrains官网下载即可

破解码自行搜索，尊重知识产权

------



## 一. 基础语法

### 字面量

> 在代码中，被写来的固定的值，称之为字面量

python中，有六种常用数据的类型：

* 数字（Number）
  * 整数（int）：10，-10
  * 浮点数（float）：13.14，-13.14
  * 复数（complex）：4+5j，j结尾表示复数
  * 布尔（bool）：True，False
* 字符串（String）：用("")包裹的字符
* 列表（List）：有序的可变序列
* 元组（Tuple）：有序的不可变序列
* 集合（Set）：无序不重复集合
* 字典（Dictionary）：无序key-value集合

使用**print(字面量)**完成对各字面量的输出打印

### 注释

> 对代码进行解读说明
>

单行注释：

```python
# 这就是单行注释
```

多行注释：

```python
"""
	这就是多行注释
	你学会了吗？
"""
```

多行注释用于python文件，类，方法等

### 变量

> 在程序进行时，能存储运算结果或表示值的抽象概念

变量就是在程序运行时，记录数据的

```python
# 变量定义格式
变量名 = 变量值
```

变量的值是可变的

print语句输出多份内容：

```python
print(内容1,内容2,...,内容n)
```

### 数据类型

目前阶段主要接触三种类型：

* string
* int
* float

我们可以通过**type()**语句得到数据的类型

```python
type(被查看的数据)
# 结果为:
<class 'xxx'>
# 可以查看变量存储的数据类型
```

字符串的三种定义方式：

```python
# 1.双引号
"string"
# 2.单引号
'string'
# 3.三引号
"""string"""

# 三引号定义，使用变量接收就是字符串，不使用变量接收就是多行注释，支持换行操作
# 单引号可以包含双引号
# 双引号可以包含单引号
# 可使用转义字符 \？
```

字符串扩展

```python
# 字符串拼接：使用 + 即可
str = "str1" + "str2"
# 字符串无法和非字符串进行拼接

# 字符串格式化
"this is my %s" % name
# %表示要占位，s表示将后面变量变为字符串再填入占位的地方
# 多个变量占位，后面变量要用括号包裹，并按照占位顺序填入
"I'm %s, I'm %s" % (name, age)
# 后面变量是数字也可以占位，会将数字转为字符串
# 多种数据占位：%d（整数），%s（字符串），%f（浮点数）
```



### 数据类型转换

> 数据类型之间，可以相互转换

常用三种转换：

* int(x)：转整数
* str(x)：转字符串
* float(x)：转浮点数

注：

1. 任何类型，都可以通过str()转换成字符串
2. 字符串必须是真的数字，才可以字符串转数字

### 标识符

命名所用的名字

标识符命名规则：

* 内容限定：不可以数字开头

* 大小写敏感

* 不可使用关键字：

  ```python
  # python中的关键字
  ['False', 'None', 'True', 'and', 'as', 'assert', 'async', 'await', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally','for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
  ```

命名规范：

英文单词全小写，下划线式命名

### 运算符

算数运算符

赋值运算符

复合赋值运算符
