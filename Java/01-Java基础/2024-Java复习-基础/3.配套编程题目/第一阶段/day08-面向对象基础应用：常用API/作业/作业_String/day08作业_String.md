### 一、选择题

##### 1.观察下列代码，求最终结果（C）

```java
String str1 = "Abc";
if(str1.equals("abc")){
  System.out.println("爱Java,爱生活~");
} else {
    System.out.println("爱生活,爱Java~");
}
```

A：什么也没有输出

B：爱Java,爱生活~

C：爱生活,爱Java~

D：运行报错

##### 2.观察下列代码，选出标号1和2的正确答案（A C）【多选题】

```java
char[] chs = {'h','e','l','l','o'};
String s1 = new String(chs);
String s2 = new String(chs);
String s3 = "hello";
String s4 = "hello";
//1
System.out.println((s1==s2)+","+(s1==s3)+","+(s3==s4));
//2
System.out.println((s1.equals(s2))+","+(s1.equals(s3))+","+(s3.equals(s4)));
```

A：fase,fase,true

B：false,false,false

C：true,true,true

D：true,true,false



##### 3.观察下列代码，选出标号1和2的正确答案（D B）【多选题】

```java
public static void main(String[] args) {
    String s1 = "abc";
    String s2 = "abc";
    String s3 = "ABC";
    String s4 = "a";
    String s5 = s4 + "bc";
    String s6 = "a" + "bc";
    //1
    System.out.println(s1.equals(s2)+" "+s1.equalsIgnoreCase(s3)+" "+s2.equals(s5)+" "+s3.equalsIgnoreCase(s5));
    //2
    System.out.println((s1==s2)+" "+(s1==s3)+" "+(s2==s5)+" "+(s2==s6));
    
}
```

A:  true false true true 

B:  true false false true 

C:  true true true true 

D:  true true true false 



##### 4.以下哪个选项可以获取字符串对象的长度（B）

A：public int length

B：public int length(){ ... }

C：public int size(){ ... }

D：public char charAt(int index){ ... }



##### 5.以下代码反转字符串逻辑正确的是（C ）

A:

```java
String s = "12345";
for (int i = s.length(); i >= 0; i--) {
	s+= s.charAt(i);
}
System.out.println(s);
```

B:

```java
String s = "12345";
for (int i = s.length()-1; i >= 0; i--) {
	s+= s.charAt(i);
}
System.out.println(s);
```

C:

```java
String s = "12345";
String s2 = "";
for (int i = s.length()-1; i >= 0; i--) {
	s2+= s.charAt(i);
}
s = s2;
System.out.println(s);
```

D:

```java
String s = "12345";
String s2 = "";
for (int i = 0; i < s.length(); i++) {
	s2+= s.charAt(i);
}
s = s2;
System.out.println(s);
```



##### 6.下面关于 String 的描述不正确的是（B D）【多选题】

A、equals()方法比较的是两个字符串的内容

B.、equals()方法比较的是两个字符串的地址和内容

C、charAt()返回的是指定的索引处的 char 值

D、length 属性代表的是字符串的长度



### 二、编程题

#### 1:需求	

```java
请定义一个方法用于判断一个字符串是否是对称的字符串，并在主方法中测试方法。

例如："abcba"、"上海自来水来自海上"均为对称字符串。

```

```java
public class Solution {
    public static boolean isSymmetrical(String str) {
        for (int i = 0, j = str.length() - 1; i != j; i++, j--) {
            if (str.charAt(i) != str.charAt(j)) {
                return false;
            }
        }
        return true;
    }
    
    public static boolean isSymmetrical2(String s) {
        //根据参数字符串对象s创建一个新的StringBuilder对象sb
        StringBuilder sb = new StringBuilder(s);
        //把StringBuilder对象中的内容反转
        sb.reverse();
        //把StringBuilder对象中的内容再转换成一个新的字符串
        String newStr = sb.toString();
        //如果是对称字符串,反转前后是一样的自然返回true
        //如果不是对称字符串,反转前后不一样的,自然返回false
        return newStr.equals(s);
    }
}
```



#### 2:需求

```java
请编写程序，由键盘录入一个字符串，统计字符串中英文字母和数字分别有多少个。
比如：Hello12345World中字母：10个，数字：5个。
```

```java
public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String str = sc.next();
        int lettersNum = 0;
        int digitsNum = 0;
        for (int i = 0; i < str.length(); i++) {
            if (str.charAt(i) >= 97 && str.charAt(i) <= 122 || str.charAt(i) >= 65 && str.charAt(i) <= 90) {
                lettersNum++;
            }
            if (str.charAt(i) >= 48 && str.charAt(i) <= 57) {
                digitsNum++;
            }
        }
        System.out.println(str+"中字母："+lettersNum+"个，数字："+digitsNum+"个。")
    }
}
```



#### 3:需求

```java
请编写程序，由键盘录入一个字符串，把字符串中的所有字母都变成大写后输出,再把字符串中的所有字母变成小写后输出。
比如：
    键盘录入字符串:Hello12345World~!#$
    控制台输出:
		转换后的大写字符串: HELLO12345WORLD~!#$
        转换后的小写字符串: hello12345world~!#$
要求:
	1.定义方法myToUpperCase,完成把方法参数字符串转换成大写字符串并返回转换后的结果
    2.定义方法myToLowerCase,完成把方法参数字符串转换成小写字符串并返回转换后的结果
    3.获取键盘录入的字符串后,分别调用以上两个方法,获取结果字符串并输出

```

```java
public class Solution {
    public static String myToUpperCase(String str) {
        String newString = "";
        for (int i = 0; i < str.length(); i++) {
            if (str.charAt(i) >= 97 && str.charAt(i) <= 122 ) {
                newString += (char)str.charAt(i) - 32;
            }
            newString += str.charAt(i);
        }
        return newString;
    }
    
    public static String myToLowerCase(String str) {
        String newString = "";
        for (int i = 0; i < str.length(); i++) {
            if (str.charAt(i) >= 65 && str.charAt(i) <= 90 ) {
                newString += (char)str.charAt(i) + 32;
            }
            newString += str.charAt(i);
        }
        return newString;
    }
}
```





