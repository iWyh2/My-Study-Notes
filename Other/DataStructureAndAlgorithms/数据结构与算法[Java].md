# 数据结构与算法[Java☕]

> 💻 程序 = 数据结构 + 算法

# [前言]实际编程遇到的一些问题

* 你可以用单链表实现字符串的拼接、替换吗？
* 五子棋在保存在文件时，你能用稀疏数组来优化吗？
* 约瑟夫问题、修路问题、最短路径问题、汉诺塔、八皇后又怎样实现呢？
* 要了解和掌握数据结构，同时用算法+数据结构来解决实际问题

------



# 1. 线性结构与非线性结构

线性结构：也是最常用的

* 特点：数据元素之间一对一的线性关系
* 存储结构：
  * 顺序存储结构（顺序表）：存储的元素是连续的
  * 链式存储结构（链表）：存储的元素不一定连续，元素节点会存放相邻元素节点的地址信息
* 常见线性结构：数组、队列、链表、栈等



非线性结构：包括二维数组、多维数组、广义表、树、图等

* 特点：不是一对一的关系了，一对多，多对多

------



# 2. 稀疏数组

> 英文名：Sparse Array  也就是”稀疏矩阵“ 用于缩小程序规模

实际需求：编写的五子棋程序，需要有**存盘**和**续盘**功能

分析问题：棋盘=>二维数组，但如果棋盘上棋子数很少，那么大部分区域内存的为无效值=>用稀疏数组优化

## 2.1 基本介绍

当一个数组中大部分元素为0，或者为同一个值时，可用稀疏数组来保存该数组（保存数组的数组）

* 处理方法：

  1. 记录需要保存的数组**有几行几列**，**除了默认值有多少个不同的值**
     * 也就是稀疏数组的第一行记录的三个值，多少行、多少列、有几个不同值
  2. 把**具有不同值的元素的行列以及值**记录在一个**小规模的数组**（这就是稀疏数组）内，从而缩小程序的规模

* 格式案例

  ```java
  /*
  	0  0  0  22 0 0  15
  	0  11 0  0  0 17 0
  	0  0  0  -6 0 0  0
  	0  0  0  0  0 39 0
  	91 0  0  0  0 0  0
  	0  0  28 0  0 0  0
  	这是6 * 7的二维数组
  */
  转为稀疏数组
  /*
  			 行  列   值	
  	第一行[0]：6   7   8
  		 [1]：0   3   22
  		 [2]：0   6   15
  		 [3]：1   1   11
  		 [4]：1   5   17
  	 	 [5]：2   3   -6
  		 [6]：3   5   39
  	 	 [7]：4   0   91
  		 [8]：5   2   28
  	剩下的行 记录相应除了默认值的不同值在哪行哪列
  	可以记录相同的非默认值
  */
  ```

* 应用实例：

  * 使用稀疏数组保留类似的二维数组（棋盘，地图等）
  * 把稀疏数组存盘，并且可以重新恢复到原来的二维数组

* 实现思路分析：

  * 二维数组转稀疏数组：
    1. 遍历原始数组，得到有效数据个数 num
    2. 根据 num 创建稀疏数组spareArr int[num+1]*[3]，稀疏数组都是三列，记录行列值
    3. 将二维数组的有效数据存入到稀疏数组
  * 稀疏数组转二维数组
    1. 先读取稀疏数组的第一行，获取原来的二维数组多少行多少列，创建对应的二维数组
    2. 再读取后续行信息，在对应行列赋给相应值

## 2.2 代码实现

```java
/**
 * @author iWyh2
 * 实现稀疏数组
 * 模拟棋盘：1-->黑子 2-->白子 0-->默认无子
 * 棋盘size：11*11
 */
public class SparseArray {
    public static void main(String[] args) {
        int[][] chessArr = new int[11][11];
        chessArr[1][2] = 1;
        chessArr[2][3] = 2;
        chessArr[4][5] = 2;
        //print chessArr
        for (int[] row : chessArr) {
            for (int data : row) {
                System.out.printf("%d\t",data);//格式化输出（C语言风格）
            }
            System.out.println();//无内容默认打印换行符换行
        }
        System.out.println("-------------------------------------");

        //二维数组转稀疏数组
        //先遍历并获取有效数据个数 num
        int num = 0;
        for (int[] row : chessArr) {
            for (int data : row) {
                if (0 != data) {
                    num++;
                }
            }
        }
		//System.out.println(num);//打印看下是否对
        //创建稀疏数组 num+1是因为稀疏数组必须由第一行存储原始数组信息 稀疏数组永远是3列
        int[][] sparseArray = new int[num + 1][3];
        sparseArray[0][0] = 11;
        sparseArray[0][1] = 11;
        sparseArray[0][2] = num;
        //再次遍历原数组获取有效数据的行列信息，要有索引，所以不要用增强for
        int row = 1;//稀疏数组开始记录有效数据的行数 --第二行
        int col = 0;//稀疏数组的列数
        for (int i = 0; i < 11; i++) {//i -> row
            for (int j = 0; j < 11; j++) {//j -> col
                if (0 != chessArr[i][j]) {
                    sparseArray[row][col] = i;
                    sparseArray[row][col+1] = j;
                    sparseArray[row][col+2] = chessArr[i][j];
                    row++;
                }
            }
        }
        //print sparseArray
        for (int[] rows : sparseArray) {
            for (int data : rows) {
                System.out.printf("%d\t",data);//格式化输出（C语言风格）
            }
            System.out.println();//无内容默认打印换行符换行
        }
        System.out.println("-------------------------------------");

        //稀疏数组转二维数组
        //先读取稀疏数组的第一行信息
        //再读取有效数据的信息
        int[][] chessArr2 = new int[sparseArray[0][0]][sparseArray[0][1]];//11*11
        for (int i = 1; i <= num; i++) {
            chessArr2[sparseArray[i][0]][sparseArray[i][1]] = sparseArray[i][2];
        }
        //print sparseArray
        for (int[] rows : chessArr2) {
            for (int data : rows) {
                System.out.printf("%d\t",data);//格式化输出（C语言风格）
            }
            System.out.println();//无内容默认打印换行符换行
        }
        //磁盘操作 将稀疏数组存入磁盘，再从磁盘读取出来
    }
}
```

------



# 3. 队列

> 英文名：Queue
>
> 日常生活中的一些需要排队的业务就是一个简单队列

## 3.1 基本介绍

队列是一个**有序列表**，可以用**数组**或者**链表**来实现

遵循**先入先出，后入后出**的原则

也就是，**从尾加数据，从头取数据**

## 3.2 数组模拟队列

数组模拟队列：队列本身是有序列表

* maxSize：队列的最大容量
* front：标记队首，随着数据的输出而改变，空队列时为-1
* rear：标记队尾，随着数据的增加而改变，空队列时为-1

存入数据：addQueue思路

* 首先判断队列是否满了

* 将尾指针后移，rear+1，当font==rear（也就是队列为空）或者队列未满
* 若尾指针rear小于最大下标(maxSize-1)，那么就将数据存入rear指向的位置，否则队满不存

获取数据：getQueue思路

* 首先判断是否为空，不然会有越界风险
* 不为空才可以获取数据
* 取数据之前首先要将头指针后移（也就是先自增）
* 然后获取对应位置的数据即可

### 3.2.1 数组模拟队列代码实现

```java
public class ArrayQueue {
    //存储数据所用数组
    private final int[] data;
    //队列的最大容量
    private final int maxSize;
    //队列的头指针
    private int front;
    //队列的尾指针
    private int rear;

    //创建队列时的初始化 -- 只需要传入最大容量
    public ArrayQueue(int maxSize) {
        this.maxSize = maxSize;
        this.data = new int[maxSize];
        this.front = -1;//队列头的前一个数据
        this.rear = -1;//队列尾的后一个位置，也就是最后一个元素
    }

    //判断是否为空
    public boolean isEmpty() {
        return front == rear;//如果头尾指针一致，那就是为空队列
    }

    //判断队列是否存满
    public boolean isFull() {
        return rear == maxSize - 1;//如果尾指针为最大容量-1那就是满了
    }

    //存入数据
    public void add(int element) {
        if(!isFull()) {
            rear++;//首先将尾指针后移
            this.data[rear] = element;
        }
    }

    //获取数据
    public int get() {
        if(isEmpty()) {
            throw new RuntimeException("队列为空!\n");
        }
        front++;//首先将头指针后移
        return this.data[front];
    }

    //展示队列里面所有数据
    public void show() {
        if (isEmpty()) {
            System.out.println("队列为空\n");
        }
        System.out.print("[ ");
        //视觉上显示获取完并删除掉了，其实并没有，掩耳盗铃
        for (int i = this.front + 1; i <= this.rear; i++) {
            System.out.printf("%d ", this.data[i]);
        }
        System.out.print("]\n");
    }

    //显示队列的头元素
    public void showFirst() {
        if (isEmpty()) {
            System.out.println("对列为空\n");
        }
        System.out.printf("headElement: %d\n",this.data[front+1]);
    }
}
```

### 3.2.2 问题分析

* 这个数组模拟的队列，里面的数组只能使用一次，没办法复用
* 当获取到了数据之后，数组没有删除掉那个数据，依然还在数组内
* 所以想要重复利用数组内的空间，我们需要将数组闭环。
* 也就是形成环形队列

## 3.3 环形队列

对数组模拟出来的队列进行了优化 使数组的空间可以复用

### 3.3.1 思路分析

1. 调整**front**含义：**指向队列的第一个元素**，也就是初始值为0
2. 调整**rear**含义：**指向队列的最后一个元素的后一个位置**，也就是初始值也为0，并且，这里希望**数组内的空间会剩余一个出来**，这作为一个约定
3. **队列满**的条件：**（rear+1）%  maxSize == front**
4. **队列为空**的条件：**rear == front**
5. 队列中有效数据个数：（rear + maxSize - front）%  maxSize

### 3.3.2 数组模拟循环队列代码实现

```java
public class CircleArrayQueue {
    //存储数据所用数组
    private final int[] data;
    //队列的最大容量
    private final int maxSize;
    //队列的头指针
    private int front;
    //队列的尾指针
    private int rear;

    //创建队列时的初始化 -- 只需要传入最大容量
    public CircleArrayQueue(int maxSize) {
        this.maxSize = maxSize;
        this.data = new int[maxSize];
        this.front = 0;//队列的第一个元素
        this.rear = 0;//队列的最后一个元素的后一个位置
    }

    //判断是否为空
    public boolean isEmpty() {
        return front == rear;//如果头尾指针一致，那就是为空队列
    }

    //判断队列是否存满
    public boolean isFull() {
        return (rear+1)%maxSize == front;
    }

    //存入数据
    public void add(int element) {
        if(!isFull()) {
            this.data[rear] = element;//只要未满，那么就先把元素存到rear当前指向的位置，然后再自增rear
            rear = (rear + 1) % maxSize;//循环的变
        }
    }

    //获取数据
    public int get() {
        if(isEmpty()) {
            throw new RuntimeException("队列为空!\n");
        }
        int value = this.data[this.front];//先取出数据保存
        this.front = (front+1)%maxSize;//再自增
        return value;
    }

    //获取队列的大小，也就是存了多少值
    public int size() {
        return (rear+maxSize - front)%maxSize;//rear+maxSize的意思是防止rear跑到了front的前面，相减为负数，取不到大小
    }

    //展示队列里面所有数据
    public void show() {
        if (isEmpty()) {
            System.out.println("队列为空\n");
        }
        System.out.print("[ ");
        int size = this.size();
        for (int i = this.front; i < front+size; i++) {
            System.out.printf("%d ", this.data[i%maxSize]);//取模循环取出
        }
        System.out.print("]\n");
    }

    //显示队列的头元素
    public void showFirst() {
        if (isEmpty()) {
            System.out.println("对列为空\n");
        }
        System.out.printf("headElement: %d\n",this.data[front]);
    }
}
```

------

