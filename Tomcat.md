### Tomcat及Servlet部署（IDEA）

[toc]



开启tomcat

- 双击bin下startup.bat
- dos进入bin目录D:\tools\CodingTools\Java\Tomcat\apache-tomcat-8.5.56\bin，输入命令catalina run

关闭tomcat

- 在服务器窗口按ctrl + c
- **双击bin下shutdown.bat**



改端口号(server.xml)

~~~html
    <Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
~~~



##### 部署方式

1. 将工程复制到`webapp`目录下
2. 在目录D:\tools\CodingTools\Java\Tomcat\apache-tomcat-8.5.56\conf\Catalina\localhost下写xml文件

~~~html
文件格式
<Context path="/abc" docBase="D:\tools\CodingTools\Java\Tomcat\book" />

<!--
Context：表示一个工程上下文
path：表示工程路径：/abc，在浏览器中调用工程文件的时候写/abc，就相当于调用docbase路径
docbase：表示工程目录在哪
-->
~~~

访问工程的方式就可以变为 http://127.0.0.1:8080/abc/index.html 



##### 页面访问

1. 拖拽页面至浏览器，地址： [file:///D:/tools/CodingTools/Java/Tomcat/apache-tomcat-8.5.56/webapps/book/index.html](file:///D:/tools/CodingTools/Java/Tomcat/apache-tomcat-8.5.56/webapps/book/index.html) 

   使用的协议：file，告诉浏览器读取后面的路径解析展示

2. 地址栏输入格式：http://ip:port/工程名/资源名

    http://localhost:8080/book/index.html 

   ![1596262216084](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596262216084.png)



##### ROOT工程访问

http://ip:port/					之后不加工程目录则默认访问ROOT工程

http://ip:port/工程名/ 		没有资源名，默认访问index.html页面(适用于所有工程目录)



##### 构建idea-tomcat动态工程

1. 配置tomcat

   ![1596263393259](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596263393259.png)

2. 创建工程

![1596263003919](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596263003919.png)

3. 在WEB-INF目录下创建lib目录

   ![1596286511275](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596286511275.png)



##### jar包的导入与移除

1. 第一种方法：复制进lib目录后add入库

   第二种方法：项目结构(project Structure)-在Libraries中新建Java库，选中被复制进lib目录中需要导入的jar包，确定后点击需要导入的模块目录，后点击应用；再到Artifacts中点击修复(fix中的add)

2. 移除出库

   项目结构(project Structure)-模块(Moduel)

   

   ![1596286948807](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596286948807.png)

   

   

   

   

##### 在IEDA中部署工程到tomcat执行

1. 修改web工程对应的tomcat运行实例名称-`与web工程名一致`

   ![1596288511277](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596288511277.png)

2. Development修改这个tomcat实例适应的范围，可以用于多个项目。

   

   ![1596288761746](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596288761746.png)

   

   

   - Application context 表示这个web工程的工程路径

   ![1596289637083](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596289637083.png)

3. 重启菜单选项

   ![1596289761352](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596289761352.png)



##### 修改模块的工程路径

修改Application context内容即可



##### 修改运行端口号

![1596290724023](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596290724023.png)



##### 修改时热部署

jsp页面有修改的时候通过刷新页面可以更新刚刚的修改内容

![1596290871381](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596290871381.png)

---



##### Servlet程序的实现

1. 编写类实现Servlet接口
2. 实现service方法
3. 到web.xml配置servlet程序访问地址



**遇到的问题**：报错500-HelloServlet has been compiled by a more recent version of the Java Runtime (class file version 55.0), this version of the Java Runtime only recognizes class file versions up to 52.0

大致意思是idea内置的jdk无法去跑这个版本的tomcat和servlet



解决办法：

1.  File ->  project Structure -> Project -> project SDK 

   改为自己安装的jdk目录，然后把项目语言级别调成 8

   ![1596297800480](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596297800480.png)

   

2.  File ->  project Structure ->  Modules ->  Dependencies  -> Module SDK 

   选择自己安装的jdk目录，回到source 把语言级别再调成 8 

   ![1596297821362](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596297821362.png)

   ![1596297768807](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596297768807.png)

   

3. File ->  project Structure -> SDKs

   选择应用1.8

   ![1596297898014](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596297898014.png)

   

4. file -> settings -> 构建执行部署 -> Compiler -> Java Compiler 

   取消use '--release' option for cross-compilation(Java 9 and later)的勾选

   选择project bytecode version 为 8

   ![1596298103931](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596298103931.png)















