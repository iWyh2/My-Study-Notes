### 编程题

#### 1:需求:请定义一个交通工具(Vehicle)的类，其中有:    

	    属性：车的品牌(brand)，车的类型(type) ;
	
	    方法：启动方法(start()) 行驶的方法(run()) ;   
	
	   在测试类中通过对象完成成员变量和成员方法的使用
	
	要求: 
		1.成员变量private修饰,提供get/set方法,空参满参构造
		2.运行效果通过两种方式实现
			(1)空参构造创建对象,set方法赋值
			(2)满参构造直接创建对象

结果如下:	

```
保时捷保时捷开始启动
保时捷保时捷在高速公路上行驶
```

Vehicle类的内容

```java
public class Vehicle {
    String brand;
    String type;
    
    public Vehicle() {
        
    }
    
    public Vehicle(String brand, String type) {
        this.brand = brand;
        this.type = type;
    }
    
    public String getBrand() {
        return this.brand;
    }
    
    public void setBrand(String brand) {
        this.brand = brand;
    }
    
    public String getType() {
        return this.type;
    }
    
    public void setType(String type) {
        this.type = type;
    }
    
    public void start() {
        System.out.println(brand + "开始启动")
    }
    
    public void run() {
        System.out.println(brand + "在高速公路上行驶")
    }
}
```

测试类内容:

```java
public class Test {
    public static void main(String[] args) {
        Vehicle v1 = new Vehicle();
        v.setbrand("保时捷");
        Vehicle v2 = new Vehicle("保时捷", "跑车");
        v1.strat();
        v1.run();
    }
}
```



#### 2:请定义一个矩形(长方形)Rectangle类，其中有:           

	属性：
		长(length):int length
		宽(width): int width       
	
	方法：
		方法一用于求矩形的面积: 长 * 宽
			void area(): 方法内部直接打印该矩形的面积
			
		方法二用于求矩形的周长: (长 + 宽) * 2
	    	void size(): 方法内部直接打印该矩形的周长
	
	要求: 
		1.成员变量private修饰,提供get/set方法,空参满参构造
		2.运行效果通过两种方式实现
			(1)空参构造创建对象,set方法赋值
			(2)满参构造直接创建对象
	
	在测试类中通过对象完成成员变量和成员方法的使用
	运行效果:
		长为: 8, 宽为: 6 的矩形的面积为: 48
		长为: 8, 宽为: 6 的矩形的周长为: 28


​	 

Rectangle类的内容:

```java
public class Rectangle {
    int length;
    int width;
    
    public Rectangle() {}
    
    public Rectangle(int length, int width) {
        this.length = length;
        this.width = width;
    }
    
    public void setWidth(int width) {
        this.width = width;
    }
    
    public int getWidth() {
        return this.width;
    }
    
    public void setLength(int length) {
        this.length = length;
    }
    
    public int getLength() {
        return this.length;
    }
    
    public void area() {
        System.out.println("长为: "+this.length+", 宽为: "+this.width+" 的矩形的面积为: "+ width * length);
    }
    
    public void size() {
        System.out.println("长为: "+this.length+", 宽为: "+this.width+" 的矩形的周长为: "+ (width + length)*2);
    }
}
```

测试类的代码:

```java
public class Test {
    public static void main(String[] args) {
        Rectangle r1 = new Rectangle();
        r1.setLength(8);
        r1.setWidth(6);
        r1.size();
        r1.area();
        Rectangle r2 = new Rectangle(8, 6);
        r2.size();
        r2.area();
    }
}
```



#### 3.定义一个女朋友类。

	女朋友的属性包含：姓名，身高，体重。行为包含：洗衣服wash()，做饭cook()。另外定义一个用于展示三个属性值的show()方法。
	
	要求: 
		1.成员变量private修饰,提供get/set方法,空参满参构造
		2.运行效果通过两种方式实现
			(1)空参构造创建对象,set方法赋值
			(2)满参构造直接创建对象
			
	请在测试类中创建对象并给成员变量赋值，然后分别调用展示方法、洗衣服方法和做饭方法。打印效果如下：

```
我女朋友叫XXX,身高165.0厘米,体重130.0斤
XXX帮我洗衣服
XXX给我做饭
```

GirlFriend类的内容:

```java
public class GirlFriend {
    String name;
    double height;
    double weight;
    
    // ...
    
    public void wash() {
        System.out.println(this.name + "帮我洗衣服");
    }
    
    public void cook() {
        System.out.println(this.name + "给我做饭");
    }
    
    public void show() {
        System.out.println(this.name + ", 身高" + this.height + "厘米, 体重" + this.weight + "斤");
    }
}
```

测试类的内容:

```java

```

#### 4:需求

​	定义手机类，手机有品牌(brand),价格(price)和颜色(color)三个属性，有打电话call()和sendMessage()两个功能。

​	请定义出手机类，类中要有空参、有参构造方法，set/get方法。 

​	定义测试类，在主方法中使用空参构造创建对象，使用set方法赋值。

​	调用对象的两个功能，打印效果如下：

```
正在使用价格为3998元黑色的小米手机打电话....
正在使用价格为3998元黑色的小米手机发短信....
```

```java
public class Phone {
    String brand;
    double price;
    String color;
    
    public void call() {
        System.out.println("正在使用价格为"+price+"元黑色的"+brand+"手机打电话....");
    }
    
    public void sendMessage() {
        System.out.println("正在使用价格为"+price+"元黑色的"+brand+"手机发短信....");
    }
}
```

**测试类内容**

```java

```







#### 5.需求

   定义项目经理类Manager。

```
属性：姓名name，工号id，工资salary，奖金bonus。

行为：工作work()
```

   定义程序员类Coder。

```
属性：姓名name，工号id，工资salary。

行为：工作work()
```

要求：

```
1.按照以上要求定义Manager类和Coder类,属性要私有,生成空参、有参构造，set和get方法
2.定义测试类,在main方法中创建该类的对象并给属性赋值(set方法或有参构造方法)
3.调用成员方法,打印格式如下:		
	工号为888基本工资为15000奖金为6000的项目经理张三正在努力的做着管理工作,分配任务,检查员工提交上来的代码..... 
	工号为666基本工资为10000的程序员李四正在努力的写着代码......
```



**Manager类的内容**

```java
public class Manager {
	String name;
    int id;
    double salary;
    double bonus;
    
    public void work() {
        System.out.println("工号为"+id+"基本工资为"+salary+"奖金为"+bonus+"的项目经理"+name+"正在努力的做着管理工作,分配任务,检查员工提交上来的代码..... ");
    }
}
```

**Coder类的内容**

```java
public class Coder {
    String name;
    int id;
    double salary;
    
    public void work() {
        System.out.println("工号为"+id+"基本工资为"+salary+"的程序员"+name+"正在努力的写着代码..... ");
    }
}
```

**测试的代码**

```java

```

