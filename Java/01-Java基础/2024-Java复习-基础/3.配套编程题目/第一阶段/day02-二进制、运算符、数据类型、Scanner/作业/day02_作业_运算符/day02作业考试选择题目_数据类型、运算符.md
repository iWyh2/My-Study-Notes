

1.下列代码的运行结果是（ C）

```java
public static void main(String[] args){
    float x = 3.999F;
    float y = 2.000001F;
    System.out.println((int)x);
    System.out.println((int)y);
}
```

 

|      | `A: 4 2`     |
| ---- | ------------ |
|      | `B: 4 3`     |
|      | **`C: 3 2`** |
|      | `D: 3 3`     |





2.下列代码的运行结果是（D）

```java
public static void main(String[] args){
    int x = 3;
    float y = x;
    System.out.println(y);
}
```

 

|      | `A: 4`   |
| ---- | -------- |
|      | `B: 4.0` |
|      | `C: 3`   |
|      | `D: 3.0` |



3.下列代码的运行结果是（ C  ）

```java
public static void main(String[] args) {
    int i = 7;
    int j = 2;
    System.out.println(i/j);
}
```

 

|      | `A: 1` |
| ---- | ------ |
|      | `B: 2` |
|      | `C: 3` |
|      | `D: 4` |



4.下列代码的运行结果是（ B  ）

```java
public static void main(String[] args){
    /*
    	先算 3 + 4 的结果:7
    	再算7 + "黑马"的结果:"7黑马"
    	再算"7黑马" + 5的结果:"7黑马5"
    	最后算"7黑马5"+6的结果: "7黑马56"
    */
    System.out.println(3 + 4 + "黑马" + 5 + 6);
}
```

 

|      | `A: 7黑马11`  |
| ---- | ------------- |
|      | `B: 7黑马56`  |
|      | `C: 34黑马11` |
|      | `D: 34黑马56` |



5.下列代码的运行结果是（  B ）

```java
public class Test03 {
    public static void main(String[] args) {                        
        int j = 2;
        j++; 
        System.out.println(j); 
    }
}
```

 

|      | `A: 2` |
| ---- | ------ |
|      | `B: 3` |
|      | `C: 4` |
|      | `D: 5` |



6.下列代码的运行结果是（C  ）

```java
public class Test03 {
    public static void main(String[] args) {                        
        int j = 10;
        j += 5;
        System.out.println(j);
    }
}
```

 

|      | `A:  5` |
| ---- | ------- |
|      | `B: 10` |
|      | `C: 15` |
|      | `D: 20` |



```
7.在java 语句中，运算符&&实现（B）
```

|      | `A: 逻辑或`    |
| ---- | -------------- |
|      | `B: 逻辑与 `   |
|      | `C: 逻辑非`    |
|      | `D: 逻辑相等 ` |



```
8.对下列表达式值的判断正确的一个是（C）
```

|      | `A: 23<43的值为false ` |
| ---- | ---------------------- |
|      | `B: 18>=19的值为true ` |
|      | `C: 12<=12的值为true`  |
|      | `D: 66!=22的值为false` |



```
9.打印下列布尔值b结果为真(true)的是（B）
```

|      | `A: boolean b = 10 > 'a';`              |
| ---- | --------------------------------------- |
|      | `B: boolean b = 'a' > 20;`              |
|      | `C: boolean b = !true;`                 |
|      | `D: boolean b = ((3 < 5) && (4 > 10));` |



```
10.以下关于运算符说法正确的有（ B ）
```

|      | `A: && 短路与，符号左边是true,右边不参与计算 ` |
| ---- | ---------------------------------------------- |
|      | `B: + 可以用来做加法运算，也可以当连接符`      |
|      | `C: % 表示取整，/ 表示取余 `                   |
|      | `D: ++i就是先变量参与操作，之后变量做自增`     |



```
11.设int x=6,y=10,则y>=6&&x<=y的值为（A）
```

|      | `A: true ` |
| ---- | ---------- |
|      | `B: 10  `  |
|      | `C: false` |
|      | `D: 6`     |



```
12.下面哪个表达式可用得到x和y两个数中的较大值 ( B )
```

|      | `A: x>y?y:x;`    |
| ---- | ---------------- |
|      | `B: x<y?y:x;   ` |
|      | `C: x>y?x:x;`    |
|      | `D: x==y?y:x;`   |



```
13.下面的逻辑表达式中合法的是（C）
```

|      | `A: (7+8)&&(9-5)`      |
| ---- | ---------------------- |
|      | `B: (9 * 5)||(9 * 7) ` |
|      | `C: 9>6&&8<10  `       |
|      | `D: (9%4)&&(8*3) `     |



14.执行完下列程序的运行结果是（B   ）

```java
int a, b, c;
a = 1;
b = 2;
c = (a + b) > 3 ? a++ : b++;
```

 

|      | `A: a的值是1，b的值是1` |
| ---- | ----------------------- |
|      | `B: a的值是1，b的值是3` |
|      | `C: a的值是1，b的值是2` |
|      | `D: c的值是false`       |



```
15.整型变量a,b的值定义如下：（ D  ）   

int a=3;   

int b=4; 

则表达式 (++a)==b的值为：
```

|      | `A: 4`     |
| ---- | ---------- |
|      | `B: false` |
|      | `C: 3`     |
|      | `D: true`  |



16.下面的代码运行的结果是？(   C)

```java
int i = 128,j=130;
i = (i>j?128:130);
j = (i<j?128:130);
System.out.println("i:"+i+"j:"+j)
```

 

|      | `A:  i:128j:130` |
| ---- | ---------------- |
|      | `B: i:130j:128`  |
|      | `C:  i:130j:130` |
|      | `D:  i:128j:128` |



```
17.【多选】下面代码是强制类型转换的有（ AC  ）
```

|      | `A: byte b = (byte)128;` |
| ---- | ------------------------ |
|      | `B: int i = 128;`        |
|      | `C: int d = (int) 3.14;` |
|      | `D:  double d = 3.14F;`  |

