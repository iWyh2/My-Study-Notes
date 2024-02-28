### 编程题一

```java
需求:
	身高是具有遗传性的，子女的身高和父母的身高有一定的关系。假定，父母和子女的身高遗传关系如下：
	儿子身高（厘米）＝(父亲身高＋母亲身高) ×1.08÷2
	女儿身高（厘米）＝(父亲身高×0.923＋母亲身高) ÷2
现有父亲身高177CM,母亲身高165CM。求子女身高分别预计为多少？
```



##### 您的代码:

```java
public class Solution {
    public static void main(String[] args) {
        double fatherHeight = 177.00;
        double motherHeight = 165.00;
        double sonHeight = (fatherHeight + motherHeight) * 1.08 / 2;
        double daughterHeight = (fatherHeight * 0.932 + motherHeight) / 2;
    }
}
```



### 编程题二

```java
需求:
	定义一个int类型的变量,初始化值为1234,求这个数的个位,十位,百位,千位分别是多少?
        
运行效果:
	1234的个位是4,十位是3,百位是2,千位是1
```



##### 您的代码:

```java
public class Solution {
    public static void main(String[] args) {
        int num = 1234;
        int one = 1234 % 10;
        int two = 1234 / 10 % 10;
        int three = 1234 / 100  % 10;
        int four = 1234 / 1000 % 10;
        System.out.println("1234的个位是"+one+",十位是"+two+",百位是"+three+",千位是"+four);
    }
}
```

**以下图形中给出了解题思路**

![1596528591750](img/1596528591750.png)

### 题目三:

```
某外卖商家的菜品单价如下:
    1.鱼香肉丝每份24元,油炸花生米每份8元,米饭每份3元。
    2.优惠方式:
        总金额大于100元,总金额打9折,其它无折扣        
    3.需求:
        小明购买了3分鱼香肉丝,3份花生米,5份米饭,最终需要付多少钱?
```



##### 你的代码:

```java
public class Solution {
    public static void main(String[] args) {
        double totalMoney = 3 * 24 + 3 * 8 + 5 * 3;
        totalMoney = totalMoney > 100 ? totalMoney * 0.9 : totalMoney;
    }
}
```



### 题目四:

```
需求:
	动物园里有两只老虎，已知两只老虎的体重分别为180kg、200kg，
	请用程序实现判断两只老虎的体重是否相同。
```



##### 你的代码:

```java
public class Solution {
    public static void main(String[] args) {
        double tiger1 = 180.0;
        double tiger2 = 200.0;
        // boolean isequal = tiger1 == tiger2 ? true : false;
        System.out.println("两只老虎的体重是否相同: " + (tiger1 == tiger2));
    }
}
```



### 题目五:

```java
需求:
	一座寺庙里住着三个和尚，已知他们的身高分别为150cm、210cm、165cm，
    请开发程序，可以通过键盘输入这三个和尚的身高，并计算出出这三个和尚的最高身高。
```



##### 你的代码:

```java
public class Solution {
    public static void main(String[] args) {
        double height1;
        double height2;
        double height3;
        Scanner sc = new Scanner(System.in);
        height1 = sc.nextDouble();
        height2 = sc.nextDouble();
        height3 = sc.nextDouble();
        double maxHeight1 = height1 > height2 ? height1 : height2;
        double maxHeight2 = height3 > maxHeight1 ? height3 : maxHeight1;
    }
}
```



