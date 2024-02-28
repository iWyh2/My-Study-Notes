## 题目1

定义一个方法，该方法能够找出两个小数中的较小值并返回。在主方法中调用方法进行测试。

### 代码实现

```java
public class Solution {
    public static double findMin(double a, double b) {
        return a > b ? a : b;
    }
}
```



---

## 题目2:

定义一个方法，该方法能够找出三个整数中的最大值并返回。在主方法中调用方法测试执行。

### 代码实现

```java
public class Solution {
    public static int findMax(int a, int b, int c) {
        return c > (a > b ? a : b) ? c : (a > b ? a : b);
    }
}
```



## 题目3:

数字是有绝对值的，负数的绝对值是它本身取反，非负数的绝对值是它本身。请定义一个方法，方法能够得到小数类型数字的绝对值并返回。请定义方法并测试 

比如: 

​	小数数字 -6.6 的绝对值是 6.6 

​	小数数字 6.6 的绝对值是 6.6



### 代码实现

```java
public class Solution {
    public static double getAbsoluteValue(double num) {
        return num < 0.0 ? -num : num;
    }
}
```



