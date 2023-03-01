# Swagger

## 介绍

生成各种格式的接口文档，以及在线接口调试页面

需要按照Swagger的规范去定义接口以及接口的相关信息，再通过Swagger衍生出来的一系列工具

[官网](https://swagger.io/)



直接使用Swagger是很繁琐的

knife4j是为Java MVC框架继承Swagger生成API文档的增强解决方案

```xml
<dependency>
	<groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>3.0.2</version>
</dependency>
```

------



## 使用

操作步骤：

1. 导入knife4j的maven坐标
2. 导入knife4j的相关配置类
3. 设置静态资源，否则接口文档页面无法访问
4. 在项目中的LoginCheckFilter中设置不需要处理的请求路径



常用注解：

| 注解               | 说明                                                     |
| ------------------ | -------------------------------------------------------- |
| @Api               | 用在请求类上（Controller），表示对类的说明               |
| @ApiModel          | 用在实体类上，表示一个返回响应数据的信息                 |
| @ApiModelProperty  | 用在实体类的属性上，描述响应类的属性                     |
| @ApiOperation      | 用在请求类的方法上，说明方法的用途，作用                 |
| @ApiImplicitParams | 用在请求类的方法上，表示一组参数的说明                   |
| @ApiImplicitParam  | 用在@ApiImplicitParams注解中，指定一个请求参数的各个方面 |



