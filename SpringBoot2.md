



## SpringBoot2



### SpringBoot2核心技术



  配套笔记-语雀地址：https://yuque.com/atguigu/springboot 

#### 一、SpringBoot2入门



##### 1.1 版本要求

- Java8-Java14
- maven3.3以上
- idea 2019



##### 1.2 maven设置



###### 1.2.1 调整项目内的maven版本和maven库



- 选择自定义的maven版本库

![1617193853924](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617193853924.png)



- 修改maven数据源

修改maven开发包内的settings.xml文件，增加以下内容：

profiles标签内

~~~xml
	<profile>
		<id>jdk‐1.8</id> 
		<activation>
			<activeByDefault>true</activeByDefault> 
			<jdk>1.8</jdk> 
		</activation> 
		<properties> 
			<maven.compiler.source>1.8</maven.compiler.source>
			<maven.compiler.target>1.8</maven.compiler.target>
			<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
		</properties>
	</profile>
~~~



mirrors标签内：

~~~xml
	<mirror>
     <id>aliyunmaven</id>
     <mirrorOf>*</mirrorOf>
     <name>阿里云公共仓库</name>
     <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
     <mirror>
     <id>aliyunmaven</id>
     <mirrorOf>*</mirrorOf>
     <name>阿里云谷歌仓库</name>
     <url>https://maven.aliyun.com/repository/google</url>
    </mirror>
    <mirror>
     <id>aliyunmaven</id>
     <mirrorOf>*</mirrorOf>
     <name>阿里云阿帕奇仓库</name>
     <url>https://maven.aliyun.com/repository/apache-snapshots</url>
    </mirror>
    <mirror>
     <id>aliyunmaven</id>
     <mirrorOf>*</mirrorOf>
     <name>阿里云spring仓库</name>
     <url>https://maven.aliyun.com/repository/spring</url>
    </mirror>
    <mirror>
     <id>aliyunmaven</id>
     <mirrorOf>*</mirrorOf>
     <name>阿里云spring插件仓库</name>
     <url>https://maven.aliyun.com/repository/spring-plugin</url>
    </mirror>
~~~



##### 1.3 入门程序

创建maven工程

###### 1.3.1 导入依赖

~~~xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.3</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
~~~



###### 1.3.2 创建程序入口

~~~java
package org.csl.boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author maxcs
 */
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}
~~~

![1617194175979](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617194175979.png)



###### 1.3.3 创建业务程序



~~~java
/**
 * @author maxcs
 */
//@ResponseBody
//@Controller

@RestController
public class HelloController {


    @RequestMapping("/h1")
    public String handle01(){
        return "Hello, Spring Boot 2";
    }
}
~~~



**说明：**

- **RestController注解是ResponseBody和Controller的合并版注解使得该控制器方法的返回值内容将直接写入到RequestMapper配置的页面内**



###### 1.3.4 测试



运行程序入口。



###### 1.3.5 简化配置



修改tomcat端口：在Resources中创建`application.properties`

~~~properties
server.port=8888
~~~



###### 1.3.6 快速部署



在pom.xml中书写打包配置

~~~xml
    <!--将程序打包成jar-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
~~~



利用maven创建jar包

![1617194616868](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617194616868.png)



在target目录下可以寻找到对应的jar包，进入到对应的目录下：

在命令行使用`java -jar 包名`即可完成在命令窗口运行springboot

注意：

- 关闭被打成jar包的正在idea运行的项目（可能发生端口冲突）。
- 右键在cmd窗口属性栏取消快速编辑模式



##### 1.4 springboot配置原理



###### 1.4.1 依赖管理

~~~xml
依赖管理    
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
</parent>

他的父项目
 <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.4.RELEASE</version>
  </parent>

几乎声明了所有开发中常用的依赖的版本号,自动版本仲裁机制
~~~



利用dependencies标签导入对应的场景启动器spring-boot-stater-*的依赖就能产生对应背景的所有常用依赖



修改默认版本号

利用properties标签的xxx.version属性在当前项目的pom文件中重写配置

~~~xml
   <properties>
        <mysql.version>5.1.43</mysql.version>
    </properties>
~~~



使用场景：在spring-boot-dependencies中可以查看引入的对应jar包的版本号，如果有开发要求可以先查看开发启动器场景对应的jar包版本是否匹配，不匹配可以使用上述方式修改默认版本号。





###### 1.4.2 自动配置



1. 自动配置springMVC相关的如视图解析器ViewResolver，自动配置解决字符编码问题的CharacterEncoding，自动配置分发dispatcher

   

2. **包的扫描机制：默认扫描主程序及其以下所有子包的注解。**

   ![1617242473353](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617242473353.png)

   **指定需要扫描的包：在主程序中的@SpringBootApplication注解中配置`scanBasePackages`属性指定需要扫描的包**。因为该注解包含的底层的@Component注解，可以进行指定扫描的包。

   另：主程序中`SpringApplication.run(MainApplication.class,args);`返回的是一个IOC容器对象。

   

3. 各种配置都有默认值，配置都是映射到**MultipartProperties**类中在启动springboot的时候投入到IOC容器中





###### 1.4.3 底层注解



**@Configuration**：告诉IOC容器，被该注解标识的类是一个配置类（**本身也是一个组件是需要在扫描的时候纳入扫描范围的，扫描的内容将看情况注入到IOC容器**）；可以在类中使用@Bean注解为容器注册组件，同样的被@Bean注解标识的方法都会默认以单例模式被同时投入到IOC容器中



- 属性`proxyBeanMethod`

说明：在存在组件之间的依赖（如User对象中拥有一个Pet对象的情况下），`full模式`将该类标识为代理bean的方式进行创建，所以每次在外部调用控件的时候都会在容器中查询有没有对应的bean组件；`Lite模式`轻量模式将该类定义为非代理的模式，这种情况下，每次利用配置类调用方法获取对应的Bean组件（例中User、Pet），都会创建一个新的bean返回给调用方；



**Full与Lite的比较**：Full方式保证了每个组件都是以单例的模式进行创建并且在IOC容器创建的时候投入到其中，每次调用的时候都会从IOC容器中查询对应的Bean组件然后返回给调用方**也就是使用的都是同一个Bean组件**；Lite方式则不用每次都在IOC中查询，而是每次都创建一个新的Bean组件返回给调用方。**（Full牺牲时间换空间，Lite牺牲空间换时间）**

![1617246069758](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617246069758.png)



**@Import注解**：给容器中自动创建这两个类型的组件，默认组件名为全限定类名。（例如：@Import({User.class,DBHelpler})    ）



**@Conditional(xxx)注解**：条件装配，根据条件注入Bean。可用在类或方法上。

例如下图，只有容器中存在id为tom的Bean的时候才注入user01的Bean

![1617263770237](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617263770237.png)



**@ImportSource注解**：导入Spring配置文件

例：在配置类上@ImportSource("classpath:bean.xml")



###### 1.4.4  配置绑定



**第一种方式：**



**将配置文件里面的属性与Bean进行绑定**：`@ConfigurationProperties`

配置文件内写：

~~~properties
mycar.brand=BYD
mycar.price=10101
~~~



Bean类：

~~~java
@Component
@ConfigurationProperties(prefix = "mycar")
public class Car {

    private String brand;
    private double price;
~~~

以上省略了set、get、构造器和toString方法



注入测试：

~~~java
@RestController
public class HelloController {

    @Autowired
    Car car;

    @RequestMapping("/car")
    public Car car(){
        return car;
    }
~~~



**第二种方式：**

在配置类中配置注解；

适用场景：在使用第三方包的Bean的时候，**第三方类是只读的，且没有写Component注解将类注入容器**，则可以在配置类中写，@Configuration包含了@Componet注解。

![1617420326939](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617420326939.png)



###### 1.4.5 源码解析



三大核心注解：SpringBootConfiguration、EnableAutoConfiguration、ComponentScan



进入到SpringBootApplication注解下

![1617436943449](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617436943449.png)



- 进入**SpringBootConfiguration**注解

![1617437071576](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617437071576.png)

可以看到，这标识这是一个配置类



- 进入**EnableAutoConfiguration**注解

![1617437198093](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617437198093.png)



进入AutoConfigurationPackage

![1617437240664](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617437240664.png)



发现底层是一个Import注解导入一个`Registrar`类

![1617437274482](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617437274482.png)



再来看看Registrar类是干嘛的

![1617437349216](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617437349216.png)

它是用来获取当前类所在的包名





**总结**

![1617696736187](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617696736187.png)

修改`application.properties`



###### 1.4.6 开发技巧



1. 在配置文件配置`debug=true`：可以在日志看到spring容器启动时哪些bean注入哪些bean没有注入；positive生效，negative不生效

   ![1617697793620](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617697793620.png)

   

2. lombok插件

   利用注解省略set、get、toString、构造器方法

   

   - 依赖引入

   ~~~xml
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
           </dependency>
   ~~~

   下载插件lombok

   

   - 使用

   ~~~java
   /**
    * @author maxcs
    */
   @Data//set、get方法生成
   @ToString//toString方法生成
   @AllArgsConstructor//有参构造器
   @NoArgsConstructor//无参构造器
   
   @Component
   @ConfigurationProperties(prefix = "mycar")
   public class Car {
   
       private String brand;
       private double price;
   ~~~

   

   - 使用日志输出

   ![1617698928148](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617698928148.png)

   

3. 热部署

   

4. 



#### 二、SpringBoot2核心技术



##### 2.1 配置文件



###### 2.1.1 yaml的使用



概述：做以数据为中心的配置文件



基本语法：

- 缩进表示层级
- `#`表示注释
- `key: value`之间冒号`:`后有空格
- 大小写敏感
- value内容无需加引号，默认双引`""`，加单引会转义`\`，如 \n 会被输出成 \\\n



数据类型：

- 字面量： 单个的、不可再分的值。date、boolean、string、number、null

~~~yaml
k: v
~~~

- 对象： 键值对的集合。map、hash、set、`object` 

~~~yaml
单行：		
k: {k1:v1,k2:v2,k3:v3}

多行：
k:
  k1: v1
  k2: v2
  k3: v3
~~~



- 数组：array、list、queue

~~~yaml
单行：
k: [v1,v2]
多行：
k: 
- v1
- v2

~~~





- 创建实体类

~~~java
/**
 * @author maxcs
 */
@ConfigurationProperties(prefix = "person")
@Component
@Data
@ToString
public class Person {

    private String userName;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animal;
    private Map<String, Object> score;
    private Set<Double> salarys;
    private Map<String, List<Pet>> allPets;


}

@Data
public class Pet {

    private String name;
    private Double weight;

}

~~~



- 创建`application.yaml`

~~~yaml
person:
  userName: zhangsan
  boss: false
  birth: 2019/12/12 20:12:33
  age: 18
  pet:
    name: tomcat
    weight: 23.4
  interests: [篮球,游泳]
  animal:
    - jerry
    - mario
  score:
    english:
      first: 30
      second: 40
      third: 50
    math: [131,140,148]
    chinese: [first: 128,second: 136]
  salarys: [3999,4999,98,5999,99]
  allPets:
    sick:
      - {name: tom,weight: 33}
      - {name: jerry,weight: 47}
    health: [{name: mario,weight: 47}]


~~~



###### 2.1.2 配置注释处理器



解决yaml文件中配置时没有提示的问题。



pom.xml

~~~xml
        <!--配置注释处理器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
~~~



在打包部署的时候除去这个注释处理器

~~~xml
    <!--将程序打包成jar-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-configuration-processor</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
~~~



##### 2.2 Web开发



###### 2.2.1 springMVC配置文件



**静态资源访问**

- 默认静态资源目录： 类路径下 `/static` (or `/public` or `/resources` or `/META-INF/resources` 
- 访问方式：当前项目根路径/ + 静态资源名
- 访问优先级： `resources`>`static`>`public` 

原理：静态资源映射：/**，请求先经过controller，如果不能处理，再经过静态资源处理器，如果静态资源找不到就是404



示例：

设置与文件名同名的控制器

~~~java
/**
 * @author maxcs
 */
@RestController
public class HelloController {

    @RequestMapping("/megumi.jpg")
    public String hello(){
        return "aaasss";
    }
}
~~~



将静态资源文件（图片megumi.jpg）改名放入不同静态资源文件夹

![1617962241246](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617962241246.png)



启动springboot访问分别两个文件，注意这个**文件名和与控制器访问路径相同**

结果：

访问megumi.jpg

![1617962300014](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617962300014.png)

访问aaa.jpg

![1617962353324](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1617962353324.png)



**结论：可以看到，当没有设置静态资源访问的前提下，在访问静态资源的时候，控制器都会优先拦截，优先走控制器路径，如果控制器不能处理才会传播到静态资源管理器。**



###### 2.2.2 静态资源访问前缀和位置修改



在`application.yaml`修改访问前缀：

~~~yaml
spring:
  mvc:
    static-path-pattern: /res/**
  web:
    resources:
      static-locations: [classpath:/nres/]
~~~

- 再次使用localshot:8080/aaa.jpg访问时候需要加上前缀变成localhost:8080/res/aaa.jpg，并且清空浏览器缓存cookie。
- static-locations用于指定静态资源的位置，使用上例指定静态资源只在`/nres/`下去寻找静态资源，使用[]可以设置多个静态资源目录。





###### 2.2.3 静态资源访问原理分析



![1618023499907](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618023499907.png)



（1）传统方式：

通过获取`getStaticPathPattern()`和`getStaticLocations()`获取静态资源前缀和静态资源访问路径

- 进入`getStaticPathPattern`：

![1618023656848](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618023656848.png)

看看`StaticPathPattern`里有什么

![1618023718396](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618023718396.png)

可以看到`/**`表示默认前缀，也就是默认所有前缀



- 进入`getStaticLocations`

![1618046143365](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618046143365.png)



进入`staticLocations`

![1618046218348](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618046218348.png)



发现静态资源访问路径会默认访问类路径classpath下的/META-INF/resource、/resources/、/static/、/public/ 去寻找对应的静态资源



（2）webjar方式



为什么要引入webjar方式？

>通常对于web开发而言，我们常常需要导入一些静态资源，然而像`js、css、images`等静态资源版本管理是比较混乱的
>
>比如`Jquery、Bootstrap、Vue.js`等各个前端框架所依赖的各自组件的版本都不尽相同
>
>平时我们是将这些Web资源拷贝到Java的目录下，这种通过人工方式拷贝可能会产生版本误差，拷贝版本错误，前端页面就无法正确展示
>
>所以，是否有一种像后端管理jar包一样的解决方案呢？这就引入了今天要介绍WebJars



使用方式：



- 进入官方寻找对应资源的依赖 https://www.webjars.org/ ，以js为例

  ![1618046552246](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618046552246.png)

- 复制到项目的pom.xml的依赖中

- 在页面中使用/webjars/xxx即可引用到对应的包



原理分析：



查看引入的js的jar包

![1618046766772](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618046766772.png)



由源码的webjar方式可以看到，springboot会将符合`/webjars/**`结构的的资源映射成/META-INF/resources/webjars/，而我们引入的webjar资源的结构都是符合这个结构的。



- 测试访问js

测试路径：http://localhost:8080/webjars/jquery/3.6.0/jquery.js

![1618047015652](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618047015652.png)





###### 2.2.4 欢迎页与favicon设置



- 欢迎页

创建方式：

1. 在静态资源路径下创建index.xml，在方法`http://localhost:8080/`的时候会自动进入的index页面

   

   注意：在application.yaml中同时配置了静态资源前缀和静态资源路径的前提下，如果index页面在静态资源目录内，访问需要加上静态资源目录，如：

   http://localhost:8080/res/index.html

   

2. controller处理/index



- favicon



1. 将图片转换成favicon.ico格式

   https://tool.lu/favicon/

2. 将图片复制到static目录或resources或META-INF/resources/目录下即可使用

   如果不行则在页面head标签下添加：

   ~~~html
   <link rel="icon" type="image/x-icon" href="/META-INF/resources/favicon.ico">
   ~~~

3. 注意处理浏览器缓存和maven-clean

   ![1618151223238](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618151223238.png)



出现的问题：

- 直接访问localhost:8080会出现无法显示favicon.ico的情况，利用controller进行首页的跳转就可以正常显示自定义的favicon.ico

~~~java
@RestController
public class HelloController {

    @RequestMapping("/index.html")
    public String hello(){
        return "aass";
    }
}
~~~



- 把favicon.ico放在META-INF，首页放在static可以实现







###### 2.2.5 Rest映射详解与示例



REST风格



开启REST方式步骤



- 编写REST方式控制器

  ~~~java
      @RequestMapping(value = "/user",method = RequestMethod.GET)
      public String getUser(){
          return "GET-User";
      }
  
      @RequestMapping(value = "/user",method = RequestMethod.POST)
      public String saveUser(){
          return "POST-User";
      }
  
      @RequestMapping(value = "/user",method = RequestMethod.PUT)
      public String putUser(){
          return "PUT-User";
      }
  
      @RequestMapping(value = "/user",method = RequestMethod.DELETE)
      public String deleteUser(){
          return "DELETE-User";
      }
  ~~~

- 前端使用`<input name="_method" type="hidden" value="DELETE">`来控制请求方式

  ~~~html
      <form action="/user" method="get">
          <input value="REST-GET" type="submit">
      </form>
  
      <form action="/user" method="post">
          <input value="REST-POST" type="submit">
      </form>
  
      <form action="/user" method="post">
          <input name="_method" type="hidden" value="PUT">
          <input value="REST-PUT" type="submit">
      </form>
  
      <form action="/user" method="post">
          <input name="_method" type="hidden" value="DELETE">
          <input value="REST-DELETE" type="submit">
      </form>
  ~~~

- 开启REST风格识别配置

  ~~~yaml
  # 开启rest风格识别
  spring:
    mvc:
      hiddenmethod:
        filter:
          enabled: true
  ~~~



原理分析：

1. 在application.yaml配置REST识别之后会注入方法过滤器

   ![1618283898551](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618283898551.png)

   从上图可看，这个bean是默认不开启的。

   

2. 进入OrderedHiddenHttpMethodFilter继承的父类HiddenHttpMethodFilter

   ![1618284324886](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618284324886.png)

   可以看到**ALLOWED_METHOD=put、get、patch**

   

   ![1618284061118](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618284061118.png)

   - 页面每一次请求都会进入到这个过滤器，在判断没有发生错误之后，从请求中取出`value=_method`的参数值

   - 如果匹配到默认定义到的PUT和DELETE，则封装成新的HTTP请求转入到下一个过滤器或者到达控制器

     

3. 前端分析与总结

   ![1618284538257](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618284538257.png)

   总的来看，在前端将表单以post的方式发送到过滤器，然后利用参数`_method`作为隐藏参数来设置实际传入控制器的方法类型。

   

4. 控制器注解优化

   ~~~java
   //    @RequestMapping(value = "/user",method = RequestMethod.GET)
       @GetMapping("/user")
       public String getUser(){
           return "GET-User";
       }
   
   //    @RequestMapping(value = "/user",method = RequestMethod.POST)
       @PostMapping("/user")
       public String saveUser(){
           return "POST-User";
       }
   
   //    @RequestMapping(value = "/user",method = RequestMethod.PUT)
       @PutMapping("/user")
       public String putUser(){
           return "PUT-User";
       }
   
       @DeleteMapping("/user")
   //    @RequestMapping(value = "/user",method = RequestMethod.DELETE)
       public String deleteUser(){
           return "DELETE-User";
       }
   ~~~

5. 修改默认的_method

   - 建立配置类`boot.config.WebConfig`

   ~~~java
   /**
    * @author maxcs
    */
   @Configuration(proxyBeanMethods = false)
   public class WebConfig {
   
       /**
        *  用于修改方法类型过滤器属性名称
        * @return
        */
       @Bean
       public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
           HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
           methodFilter.setMethodParam("_m");
           return methodFilter;
       }
   }
   ~~~

   - 前端调用

     ~~~html
             <input name="_m" type="hidden" value="PUT">
     ~~~

   - 原理分析

     条件：父类HiddenHttpMethodFilter中存在队默认属性_method的set方法

     方式：由于这个父类过滤器对象是存在就不创建（@ConditionalOnMissingBean），所以建立一个用于修改`_method`的config配置类，利用Configuration注解扫描注入容器，在其中**构建HiddenHttpMethodFilter过滤器对象并且通过set方法修改其默认的`_method`属性名称**即可实现对默认`_method`的修改。

     ![1618370387600](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618370387600.png)



###### 2.2.6 请求映射原理（webmvc是怎么找到controller中对应的方法的）



概述：当页面的请求进来的时候，容器中的组件是如何对这个请求进行映射到对应的控制器方法上。



映射路径经过的主干类：

`DispatcherServlet、FrameworkServlet、HttpServletBean、HttpServlet`



层级关系：

![1618403891443](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618403891443.png) 



原生Servlet接收请求是通过invoke反射调用dopost和doget方法的，所以通过debug，请求会先进入继承了原生Servlet子类的FrameworkServlet

![1618404201795](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618404201795.png)

![1618405147139](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618405147139.png)

可以看到这个doGet方法调用了processRequest方法，step in它；发现在一系列get之后执行了一个**核心方法`doService`**，同样step in它。

![1618404326380](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618404326380.png)



发现是一个抽象方法

![1618404446459](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618404446459.png)



那么我们可以找FrameworkServlet子类去寻找它对应的实现，**程序将进入到DispatcherServlet**

![1618404533584](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618404533584.png)

同样，经过一系列处理之后执行了**核心方法`doDispatch`**，step in它；



**核心方法`doDispatch`**

**![1618404638771](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618404638771.png)**

在这个方法当中，我们终于发现了handler！在springboot中每一个操作都有一个或多个handler与之对应，图中最核心的就应该是`getHandler方法`了，这个方法可以为我们提供我们是如何找到访问的url对应的请求方法的实现（Determine handler for the current request）。step in它。





**getHandler**

![1618405237898](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618405237898.png)

这个for循环将为这个request在执行链中的size=5，五个mapping中寻找对应的handler。

这里有一个handlerMapping,这是一个处理器映射器，SpringMVC根据它中的映射规则知道了哪个请求应该由哪个处理器处理。默认的handlerMapping有5个，其中第一个RequestMappingHandlerMapping中保存了@RequestMapping和handler的映射规则。当我们的应用一启动，SpringMVC自动扫描所有的Controller并解析注解，把注解信息保存在handlerMapping里面。 然后通过循环遍历着5个handlerMapping,看哪个可以处理当前请求。



mapping中的方法路径

![1618405767514](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618405767514.png)



找到handler，标识出控制器的位置

![1618405521327](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618405521327.png)





![1618405575776](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618405575776.png)

另外，当路径为`/`的时候，则会寻找this.handlerMappings.1(WelcomexxxxMapping)



- 总结

  请求流程： `FrameworkServlet.doGet()`->  `FrameworkServlet.processRequest()`->`DispatcherServlet.doService()` ->`doDispatch()`->`getHandler()`

  一个请求过来，首先到HttpServlet的doGet()方法，这个方法被FrameworkServlet重写，进而调用FrameworkServlet中的processRequest()方法，然后调用了DispatcherServlet实现的doService()方法，最后交给doDispatch()方法处理。



###### 2.2.7 常用参数注解



1. **@PathVariable**

   概述：

   路径变量：接收RequestMapping请求路径中的参数。

   

   使用方法：

   - 自定义基础类型接收路径参数

     ~~~java
         /**
          *  测试路径变量注解 @PathVariable
          * @param id
          * @return
          */
         @GetMapping("/car/{id}/owner/{username}")
         public Map<String,Object> getCar(@PathVariable Integer id,
                                          @PathVariable String username,
                                          @PathVariable Map<String,String> pv){
     
             Map<String,Object> map = new HashMap<String, Object>();
             map.put("id",id);
             map.put("username",username);
             map.put("pv",pv);
             return map;
         }
     ~~~

     

   - 利用Map接收路径参数

     同上。

     

     说明：使用map参数可以根据在RequestMapping上**所有**需要的参数名和访问路径中的值作为map的key和value存入

      `http://localhost:8080/car/1/owner/zhangsan` 
   
     ![1618470956174](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618470956174.png)
     
     
     
   - 示例代码
   
     ~~~java
         /**
          *  测试路径变量注解 @PathVariable
          * @param id
          * @return
          */
         @GetMapping("/car/{id}/owner/{username}")
         public Map<String,Object> getCar(@PathVariable Integer id,
                                          @PathVariable String username,
                                          @PathVariable Map<String,String> pv,
                                         ){
     
             Map<String,Object> map = new HashMap<String, Object>();
             // 获取路径参数
             map.put("id",id);
             map.put("username",username);
             map.put("pv",pv);
     
         }
     }
     
     ~~~
   
     
   
   

2. **RequesHeader**

   获取请求头

   ~~~java
       /**
        *  测试路径变量注解 @RequesHeader
        * @param id
        * @return
        */
       @GetMapping("/car/{id}/owner/{username}")
       public Map<String,Object> getCar(@PathVariable Integer id,
                                        @PathVariable String username,
                                        @PathVariable Map<String,String> pv,
                                        @RequestHeader("User-Agent") String userAgent,
                                        @RequestHeader Map<String,String> headers){
   
           Map<String,Object> map = new HashMap<String, Object>();
           // 获取路径参数
           map.put("id",id);
           map.put("username",username);
           map.put("pv",pv);
   
   
           // 获取请求头
           map.put("userAgent",userAgent);
           map.put("headers",headers);
           return map;
   
   
       }
   }
   ~~~

   

3. **RequestParam**

   获取请求路径中的参数。

   参数形式：

   - **`@RequestParam Integer age,`**

     **`@RequestParam List<String> inters,`**

   - **`@RequestParam Map<String,String> params,`**

   ![1618492947805](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618492947805.png)

   

4. **CookieValue**

   获取页面的Cookie

   参数形式：

   - **`@CookieValue("csrftoken") String csrftoken`**

   - **`@CookieValue("csrftoken") Cookie cookie`**

   注意：要根据页面的Cookie的key来获取对应的cookie，图中是csrftoken

   ![1618493956460](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618493956460.png)

   ![1618494769110](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618494769110.png)

   ![1618494938232](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618494938232.png)

5. **RequestBody**

   获取请求体（POST表单的参数）

   **需要注意编码后的解码。**

   ~~~java
       @PostMapping("save")
       public Map<String,Object> save(@RequestBody String content) throws UnsupportedEncodingException {
           Map<String,Object> map1 = new HashMap<>();
   
           // 进行URL解码，因为在传入的时候会自动对URL进行UTF-8编码
           content = URLDecoder.decode(content,"UTF-8");
           map1.put("content",content);
           return map1;
       }
   ~~~

   

6. **RequestAttribute**

   获取在请求域中的值；

   用于在控制器方法之间的数据传输

   ![1618563162697](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1618563162697.png)

   

7. 矩阵变量MatrixVariable



###### 2.2.8 ServletAPI解析原理





###### 2.2.9 请求处理的Model、Map原理



Model、Map都是给request域中放数据，在测试中都可以使用原生request对象获取到对应的数据

![1630828170910](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1630828170910.png)

![1630828179584](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1630828179584.png)





###### 2.2.10 自定义参数类型



控制器参数含有自定义参数如Person等，探究springboot如何将页面中的数据注入到参数中

原理：一开始是找对应的参数解析器，然后判断是否是简单类型，不是则利用绑定工厂为创建web数据绑定器，然后新建一个自定义参数空类型（如JavaBean），利用**数据类型转换器**将request中的参数转换成与Person定义参数类型相同的参数，封装到对应的JavaBean中，因为打过来的http请求都是json或者String类型数据。



通过实现对应接口可以自定义数据类型转换器

![1630920668859](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1630920668859.png)



**实现自定义参数解析器**

在自定义的配置类中

![1630928539896](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1630928539896.png)



实现接口

~~~java
    /**
     *  自定义类型转换器
     * @return
     */
    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {
            @Override
            public void configurePathMatch(PathMatchConfigurer configurer) {
                UrlPathHelper urlPathHelper = new UrlPathHelper();
                urlPathHelper.setRemoveSemicolonContent(false);
                configurer.setUrlPathHelper(urlPathHelper);
            }

            @Override
            public void addFormatters(FormatterRegistry registry) {
                registry.addConverter(new Converter<String, Pet>() {

                    /**
                     * 转换用逗号分隔的字符串
                     * @param s
                     * @return
                     */
                    @Override
                    public Pet convert(String s) {
                        // 非空判断
                        if(!StringUtils.isEmpty(s)){
                            Pet pet = new Pet();
                            String[] strings = s.split(",");
                            pet.setName(strings[0]);
                            pet.setAge(Integer.parseInt(strings[1]));
                            return pet;
                        }
                        return null;
                    }
                });
            }
        };
    }
~~~



###### 2.2.11 springMVC控制器返回值原理





- 返回值处理器判断是否支持这种类型的返回值
- 返回值处理器调用handleReturnValue处理
- RequestResponseBodyMethodProcessor可以处理标注了@ResponseBody注解的
  - 如何将数据写为json
    - 内容协商：浏览器默认以请求头的方式告诉服务器能接收的内容类型

![1630984586096](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1630984586096.png)



###### 2.2.12 HttpMessageConverter消息转换器

![1630984604610](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1630984604610.png)





###### 2.2.13 内容协商原理



说明：根据客户端的**请求头中的Accept字段**，分析客户端能够接收的媒体类型数据，然后与服务器能够生产的媒体类型转换器比对，得到最佳的响应媒体类型转换器，也就是**把springMVC控制器中return的类型转化为客户端浏览器能够识别或者展示的类型**（从权重高到低排序，一般浏览器是xml权重0.9，其余`*/*`的类型为0.8）



~~~xml
        <!--xml格式内容协商依赖-->
        <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-xml</artifactId>
        </dependency>
~~~



![1631003821990](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1631003821990.png)



![1631005213721](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1631005213721.png)



**自定义内容协商**



- **基于请求参数的内容协商**

在yml文件中开启请求参数内容协商，在请求的时候添加上format=json或者format=xml，就可以完成参数内容协商。**前提是要添加上上面的xml内容协商依赖**

![1631088748936](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1631088748936.png)

**format字段只支持xml和json的协商**









- ***自定义内容协商转换器（基于请求头）**

1. 在项目中的converter包内新建MyConverter.java类，实现HttpMessageConverter接口，实现对应的converter方法

   ~~~java
   package com.example.demo.converter;
   
   import com.example.demo.bean.Person;
   import org.springframework.http.HttpInputMessage;
   import org.springframework.http.HttpOutputMessage;
   import org.springframework.http.MediaType;
   import org.springframework.http.converter.HttpMessageConverter;
   import org.springframework.http.converter.HttpMessageNotReadableException;
   import org.springframework.http.converter.HttpMessageNotWritableException;
   
   import java.io.IOException;
   import java.io.OutputStream;
   import java.util.List;
   
   /**
    * 自定义converter,内容协商
    * @author maxcs
    */
   public class MyMessageConverter implements HttpMessageConverter<Person> {
       @Override
       public boolean canRead(Class<?> aClass, MediaType mediaType) {
           return false;
       }
   
       @Override
       public boolean canWrite(Class<?> aClass, MediaType mediaType) {
           // 如果返回的是Person类型就能写
           return aClass.isAssignableFrom(Person.class);
       }
   
       /**
        *  服务器统计所有MessageConverter都能写出哪些内容类型
        * @return
        */
       @Override
       public List<MediaType> getSupportedMediaTypes() {
           // 用于与浏览器请求头的Accept字段发送的内容类型相匹配
           return MediaType.parseMediaTypes("application/x-guigu");
       }
   
       @Override
       public Person read(Class<? extends Person> aClass, HttpInputMessage httpInputMessage) throws IOException, HttpMessageNotReadableException {
           return null;
       }
   
       @Override
       public void write(Person person, MediaType mediaType, HttpOutputMessage httpOutputMessage) throws IOException, HttpMessageNotWritableException {
           // 自定义协议数据的写出
           String data = person.getUserName() + ";" + person.getAge() + ";" + person.getBirth();
   
           //用输出流写出去
           OutputStream body = httpOutputMessage.getBody();
           body.write(data.getBytes());
       }
   }
   
   
   ~~~

   

2. 在配置类WebConfig中的实现WebMvcConfigurer方法中重载extendMessageConverters方法，添加1中写好的converter

   ~~~java
       /**
        *  自定义springMVC的配置
        * @return
        */
       @Bean
       public WebMvcConfigurer webMvcConfigurer(){
           return new WebMvcConfigurer() {
   
               /**
                * 添加自定义内容协商转换器
                * @param converters
                */
               @Override
               public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
                   converters.add(new MyMessageConverter());
               }
   
   ~~~

   

3. 在底层中converter会自动添加到服务器能够生产的converter列表中与发进来的请求头中Accept字段匹配（客户端浏览器能够接收的内容类型）。

4. 在postman中测试请求，postman取消勾选左边的多选框，不使用默认的请求头字段，然后在最下面添加自己想要的请求头字段，在图中，**Accept发送的类型要与自定义converter中getSupportedMediaTypes方法添加的类型名一致**



PS：用浏览器不能测试，因为在浏览器当前页面没有设置发送这种内容类型的代码。

![1631102660170](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1631102660170.png)





- **扩展以参数/请求头的形式对自定义内容协商转换器进行适配**





**自定义参数内容协商策略媒体类型和请求头内容协商策略媒体类型**

其实就是新建Map映射，用于映射自定义转换器的名字，再加上请求头类型

~~~java
    /**
     *  自定义springMVC的配置
     * @return
     */
    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {

            /**
             *  自定义参数内容协商策略
             *  默认的format只支持json、xml
             * @param configurer
             */
            @Override
            public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {

                // 基于请求头的内容协商策略
                HashMap<String, MediaType> mediaTypes = new HashMap<>();// 构建内容协商映射
                mediaTypes.put("json", MediaType.APPLICATION_JSON);
                mediaTypes.put("xml",MediaType.APPLICATION_XML);
                // 这样 format=gg 就找找到名为 x-guigu 的自定义的内容类型转换器
                // 其实就是建立 gg->x-guigu的映射
                mediaTypes.put("gg",MediaType.parseMediaType("application/x-guigu"));
                // 指定哪些参数对应哪些媒体类型
                ParameterContentNegotiationStrategy parameterStrategy = new ParameterContentNegotiationStrategy(mediaTypes);

                // 修改参数key名 ff=json   ff=xml...
                // parameterStrategy.setParameterName("ff");

                // 基于请求头的内容协商策略
                    // 直接注册一个请求头内容协商策略
//                configurer.mediaType("gg",MediaType.parseMediaType("application/gg"));
                HeaderContentNegotiationStrategy headStrategy = new HeaderContentNegotiationStrategy();
                configurer.strategies(Arrays.asList(parameterStrategy,headStrategy));


            }

~~~



![1631107261447](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1631107261447.png)

![1631108803487](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1631108803487.png)







**Ps：还可与通过直接注册和yml设置的方式来进行策略内容类型的添加**

~~~yml
 #     添加媒体类型
      media-types: application/gg
~~~

