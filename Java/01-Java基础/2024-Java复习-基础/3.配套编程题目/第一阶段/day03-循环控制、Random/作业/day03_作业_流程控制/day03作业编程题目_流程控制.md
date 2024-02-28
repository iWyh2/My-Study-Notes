### day04作业编程题

### 题目1:

```java
需求:
	让用户依次录入三个整数，求出三个数中的最大值，并打印到控制台。【使用if】
	
实现步骤:
	
```

**您的代码**

```java
public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int num1 = Scanner.nextInt();
        int num2 = Scanner.nextInt();
        int num3 = Scanner.nextInt();
        int max1 = num1 > num2 ? num1 : num2;
        if (max1 > num3) {
            System.out.println("max num:" + max1);
        } else {
            System.out.println("max num:" + num3);
        }
    }
}
```



### 题目2:

```java
需求:
    1.根据程序员的工龄(整数)给程序员涨工资(整数),程序员的工龄和基本工资通过键盘录入
    2.涨工资的条件如下：
        [10-15)     +20000
        [5-10)      +10000
        [3~5)       +5000
        [1~3)       +3000        
     3.运行程序:
         请输入作为程序员的你的工作的工龄:10
         请输入作为程序员的你的基本工资为:60000
         程序运行后打印格式
         	"您目前工作了10年，基本工资为 60000元, 应涨工资 20000元,涨后工资 80000元"
     
         
实现步骤:
	
```

**您的代码**

```java
public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入作为程序员的你的工作的工龄: ");
        int workYear = sc.nextInt();
        System.out.println("请输入作为程序员的你的基本工资为: ");
        int moneyBegin = sc.nextInt();
        int moneyAfter = moneyBegin;
        int addMoney = 0;
        
        if (workYear >= 10 && workYear < 15) {
            moneyAfter += 20000;
            addMoney = 20000;
        } else if (workYear >= 5 && workYear < 10) {
            moneyAfter += 10000;
            addMoney = 10000;
        } else if (workYear >= 3 && workYear < 5) {
            moneyAfter += 5000;
            addMoney = 5000;
        } else {
            moneyAfter += 3000;
            addMoney = 3000;
        }
        
        System.out.println("您目前工作了"+workYear+"年，基本工资为"+moneyBegin+"元, 应涨工资"+addMoney+"元,涨后工资"+moneyAfter+"元");
    }
}
```



### 题目3:

```java
需求:
	打印出1到100之间的既是3的倍数又是5倍数的数字以及这些数字的和
实现步骤:	
```

##### 答案:

```java
public class Solution {
    public static void main(String[] args) {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            if (i % 3 == 0 && i % 5 == 0) {
                System.out.println(i);
                sum += i;
            }
        }
        System.out.println(sum);
    }
}
```



### 题目4:

```java
需求:
	从键盘上录入一个大于100的三位数,打印出100到该数字之间满足如下要求的数字,数字的个数,以及数字的和:
		1.数字的个位数不为7;
		2.数字的十位数不为5;
		3.数字的百位数不为3;
实现步骤:

```

##### 答案:

```java
public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入一个大于100的三位数");
        int num = sc.nextInt();
        
        if (num < 100) {
            System.out.println("傻逼");
            exit(0);
        }
        
        int count = 0;
        int sum = 0;
        for (int i = 100; i <= num; i++) {
            if (i % 10 != 7 && i / 10 % 10 != 5 && i / 100 % 10 != 3) { 
                System.out.println(i);
                count++;
                sum += i;
            }
        }
        
        System.out.println(count);
        System.out.println(sum);
    }
}
```



### 题目5:

```java
需求:      
	1.打印所有四位数中 个位 + 千位 == 百位 + 十位 的数字
    2.最后要打印符合条件的数字的总数量
   	3.打印格式如下:
		1010
        1021 
        1032
        1043 
        ....
        以上满足条件的四位数总共有 615 个
实现步骤:
	     
```

##### 答案:

```java
public class Solution {
    public static void main(String[] args) {
        int numCount = 0;
        for (int i = 1000; i < 10000; i++) {
            if ((i % 10 + i / 1000 % 10) == (i /100 % 10 + i / 10 % 10)) {
                System.out.println(i);
                numCount++;
            }
        }
        System.out.println("以上满足条件的四位数总共有 "+numCount+" 个");
    }
}
```



