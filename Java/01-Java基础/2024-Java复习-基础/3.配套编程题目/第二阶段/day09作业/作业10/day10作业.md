##  day10作业

#### 一、选择题：

#####   1.下面哪个流类属于面向字符的输入流(D)    

A.BufferedWriter

B.FilelnputStream

D. ObjectInputStream

D.lnputStreamBeader

⒉.要从文件”file.txt”文件中读出第10个字节到变量c中，下列哪个正确(A)

```java
A.
FileInputStream in=new FileInputStream("file.txt");
in.skip(9); // skip() 跳过前几个字节数据
int c=in.read();
```

```java
B.
FileInputStream in=new FileInputStream("file.txt");
in.skip(10);// skip() 跳过前几个字节数据
int c=in.read();
```

```java
C.
FileInputStream in=new FileInputStream("file.txt");
int c=in.read();
```

```java
D.
FileInputStream in=new FileInputStream("file.txt");
in.skip(7);// skip() 跳过前几个字节数据
int c=in.readByte();
```

##### 3. 实现下列哪个接口，可以启用序列化功能( C   )

A. Runnable接口

B. Comparable接口

C. Serializable接口 

D. Comparator接口

##### 4.  下面的程序段创建了BufferedReader类的对象in，以便读取本机d盘my文件夹下的文件1.txt。File构造函数中正确的路径和文件名的表示是(  C  )

```java
File f=new File(填代码处);
file=new FileReader(f);
in=new BufferedReader(file);
```

A. "1.txt"         

B. "d:\\my\\1"      

C. "d:\\my\\1.txt"  

D. "d:\ my\1.txt"   

##### 5. 【多选题】下面关于IO流的说法正确的是(  ABC  )

A. IO流可以分为字节流和字符流

B. FileReader和FileWriter是专门用于读取和写入文本文件的

C. 字节流类有InputStream和OutputStream

D. 顶层类有Reader和Writer，他们都是接口

##### 6.【多选题】关于打印流PrintWriter说法正确的是(  ACD  ) 

A. 该流只操作数据目的，不操作数据源

B. 该流只操作数据源，不操作数据目的

C. 如果启用了自动刷新，该流调用println()方法时可以自动刷新

D. 该流是Writer的子类

##### 7. 关于对象的序列化说法错误的是(  B   ) 

A. 实现序列化的对象必须实现Serializable接口

B. 实现序列化的对象必须自定义序列号 

C. ObjectOutputStream中的writeObject()方法可以将对象写出

D. ObjectInputStream中的readObject()方法可以读取对象

##### 8. 【多选题】下面关于java中输入/输出流的说法正确的是( ABD ) 

A.FileInputStream与FileOutputStream类用来读、写字节流。

B. FileReader与FileWriter类用来读、写字符流。

C. File类既可以用来读文件，也可以用来写文件。  //仅仅是关联，读写是流的操作 

D. File类用来处理与文件相关的操作。

##### 9. 与InputStream流相对应的Java系统的标准输入对象是（A）

A.System.in 

B.System.out

C.System.err

D.System.exit() 

##### 10. 以下对于缓冲流描述错误的是（A） 

A. 缓冲流可以直接读写文件，所以完全不用普通流

B. 缓冲流是一种装饰模式，它本身不具备读写功能，读写依旧要依赖于普通流

C. 如果都准备数组读写数据的情况下，缓冲流效率同普通流相差无几

D. 缓冲流内置一个8192的数组，用来提高读写效率

------

#### 二、今日方法：

1. 字符流中所涉及到的类名以及方法名称：

   ```java
   答：
   ```

2. 字符缓冲流中的特殊方法名称以及作用：

   ```java
   答：
   ```

3. 转换流的类名：

   ```java
   答：
   ```

4. 打印流的类名以及方法名称：

   ```java
   答：
   ```

5. 数据流的类名以及方法名称：

   ```java
   答：
   ```

6. 序列化流的类名以及方法名称：

   ```java
   答：
   ```

------

#### 三、简答题：

1. 字符流有什么作用？能不能操作图片文件？会有什么问题？

   ```java
   答：
   ```

2. 什么是缓冲流？缓冲流能否直接读写文件？有什么好处？

   ```java
   答：
   ```

3. 转换流是字节流还是字符流？有什么作用？

   ```java
   答：
   ```

4. 打印流有什么作用？

   ```java
   答：
   ```

5. flush()和close()方法的区别？

   ```java
   答：
   ```

6. 什么是序列化和反序列化？需要注意什么？

   ```java
    答：
   ```
------

#### 四、排错题：

##### 排错题1：

```java
// 以下代码是否能正常运行？为什么？
public class Demo3 {
    public static void main(String[] args) {
        try {
            Reader fr = new FileReader("io-app2\\src\\itheima04.txt");
            // 创建一个字符缓冲输入流包装原始的字符输入流
            BufferedReader br = new BufferedReader(fr);
            String line; // 记住每次读取的一行数据
            while ((line = br.readLine()) != -1) {
                System.out.println(line);
            }
            br.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

```tex
答：
```

##### 排错题2：

```java
// 以下代码运行后，文件a.txt中是否会有数据？为什么？
public class Demo3 {
        public static void main(String[] args) throws FileNotFoundException {
        PrintWriter ps =  new PrintWriter(new FileOutputStream("day06-code/a.txt"));
        ps.println("123");
        ps.print(666);
        ps.write(123);
    }
}
```

```java
答：
```

##### 排错题3：

```java
// 以下代码运行后，country变量是否会有数据呢？
public class User implements Serializable {
    private String Name;
    private int age;
    // transient 这个成员变量将不参与序列化。
    private transient String passWord;
    // static 修饰的成员变量也无法被序列化
    private static String country;
	// 以下set/get/构造方法省略...
}

public class Demo4 {
    public static void main(String[] args) throws IOException {
        // 1、创建一个Java对象。
        User.country = "中国";
        User u = new User("张三", 32, "666888xyz");
        // 2、创建一个对象字节输出流包装原始的字节 输出流。
        ObjectOutputStream oos =
                new ObjectOutputStream(new FileOutputStream("io-app2/src/itheima11out.txt"));

        // 3、序列化对象到文件中去
        oos.writeObject(u); 
        oos.close();
        
        // 下面反序列化
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("io-app2/src/itheima11out.txt"));
       
        User u = (User) ois.readObject();
        // 请问打印出来的User对象中，是否有country的值？？？
        System.out.println(u);
        ois.close();
    }
}
```

```java
答：
```

------

#### 五、代码题：

##### 第一题：分析以下需求，并用代码实现

**训练目标**：

​	掌握java中字符流的基本使用，以及理解其在实际开发中的应用

**需求描述**：

​	编写一个程序，把一个目录里边的所有带.java文件拷贝到另一个目录中，拷贝成功后，把后缀名是.java的文件改为.txt文件。
（注意事项：是先拷贝，拷贝成功后才可以改后缀名的）

**实现提示**：

1. 用File对象封装目录
2. 通过listFiles()方法获取该目录下所有的文件或者文件夹的File对象数组
3. 遍历这个File数组，得到每一个File对象
4. 判断该File对象是否是文件
   - 如果是文件
   - 继续判断是否以.java结尾
     - 是：复制文件，再复制完成后改名
     - 否：不复制 

------

##### 第二题：	分析以下需求，并用代码实现	

**训练目标**：

​	掌握java中字符流的基本使用

**需求描述**：

​	实现一个验证程序运行次数的小程序，要求如下：
​		1.当程序运行超过3次时给出提示:本软件只能免费使用3次,欢迎您注册会员后继续使用
​		2.程序运行演示如下:
​        		第一次运行控制台输出: 欢迎使用本软件,第1次使用免费~   
​        		第二次运行控制台输出: 欢迎使用本软件,第2次使用免费~    
​        		第三次运行控制台输出: 欢迎使用本软件,第3次使用免费~
​        		第四次及之后运行控制台输出:本软件只能免费使用3次,欢迎您注册会员后继续使用~

****实现提示****：

	1. 程序运行3次，每次运行结果不同，所以控制台打印的数字，应该在写文件中
	2. 每次程序运行，读取文件中的数据，打印在控制台
	3. 再将数据+1后重新写回文件
	4. 每次读文件中的数据，需要判断数据是否超过3，超过3，则打印不同的结果

------

##### 第三题：【选做题】分析以下需求，并用代码实现

**训练目标**：

​	掌握java中字符流的基本使用

**需求描述**：

```java
// 项目根路径下有个questions.txt文件内容如下：
5+5     
150-25
155*155
2555/5  
// 要求：读取内容计算出结果，将结果写入到results.txt文件中
5+5=10
...
```

思考:如果说读取questions.txt文件的内容,算出计算结果,再写入questions.txt文件,即读和写的操作时针对同一个文件,应该如何操作 

**实现提示**：

1. 应该从文件中，把数据一行一行读出来
2. 拿到数据后，判断哪个不是数字，则使用不是数字的字符，切割字符串
3. 再把切割后的字符串，转成数字
4. 再匹配切割的字符的类型，进行相应的操作








