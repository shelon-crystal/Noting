### Servlet 

[toc]

#### 通过实现Servlet接口，实现Servlet程序

1. src目录创建包，包内写Servlet是实现类

   对应代码:

   ~~~java
       public HelloServlet() {
           System.out.println("构造器方法");
       }
   
       @Override
       public void init(ServletConfig servletConfig) throws ServletException {
           System.out.println("初始化");
           // 1.获取Servlet程序别名，servlet-name的值
           System.out.println("HelloServlet 别名 ： " + servletConfig.getServletName());
   
           // 2.获取初始化参数init.param
           System.out.println("初始化参数:"  + servletConfig.getInitParameter("username"));
           System.out.println("初始化参数:"  + servletConfig.getInitParameter("url"));
   
           // 3.获取ServletContext对象
           System.out.println(servletConfig.getServletContext());
   
       }
   
       @Override
       public ServletConfig getServletConfig() {
           return null;
       }
   
       /**
        * service 专门用于处理请求与响应
        * @param servletRequest
        * @param servletResponse
        * @throws ServletException
        * @throws IOException
        */
       @Override
       public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
           System.out.println("Hello Servlet!");
           // 判断是get还是post方法
           // 1. 类型转换，将ServletRequest转换为他得子类 HttpServletRequest
           HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
   
           // 2.获取请求方式
           String method = httpServletRequest.getMethod();
   
           if("GET".equals(method)){
               doGet();
           } else if ("POST".equals(method)){
               doPost();
           }
       }
   
       /**
        *  get 请求 调用的函数
        */
       public void doGet(){
           System.out.println("get请求");
       }
   
       /**
        *  post 请求 调用的函数
        */
       public void doPost(){
           System.out.println("post请求");
       }
   
       @Override
       public String getServletInfo() {
           return null;
       }
   
       @Override
       public void destroy() {
           System.out.println("Destroy！");
       }
   }
   ~~~

   

2. 写web.xml，根据创建的类，填写配置文件

   1. Servlet标签

      servlet-name	给servlet程序起一个别名（一般是类名 如：HelloServlet）

      servlet-class	是Servlet程序的全类名

      ~~~xml
          <!--给tomcat配置servlet程序-->
          <servlet>
              <!--servlet-name给servlet程序起一个别名（一般是类名 如：HelloServlet）-->
              <servlet-name>HelloServlet</servlet-name>
              <!--是Servlet程序的全类名-->
              <servlet-class>org.studyCSL.pro.HelloServlet</servlet-class>
              <!--init-param初始化参数-->
              <init-param>
                  <!--参数名-->
                  <param-name>url</param-name>
                  <!--参数区-->
                  <param-value>jdbc:mysql://localhost:3306/test</param-value>
              </init-param>
          </servlet>
      ~~~

      

   2. Servlet-mapping标签

      作用：给servlet程序配置访问地址

      servlet-name 告诉服务器，当前配置的地址是给哪个servlet服务器使用

      url-pattern	配置访问地址
                  /   斜杠在服务器解析是表示地址为:http://ip:port/工程路径
                  /hello  表示http://ip:port/工程路径/hello

      ~~~xml
          <!--servlet-mapping标签给servlet程序配置访问地址-->
          <servlet-mapping>
              <!--servlet-name 告诉服务器，当前配置的地址是给哪个servlet服务器使用-->
              <servlet-name>HelloServlet</servlet-name>
              <!--url-pattern配置访问地址
                  /   斜杠在服务器解析是表示地址为:http://ip:port/工程路径
                  /hello  表示http://ip:port/工程路径/hello
              -->
              <url-pattern>/hello</url-pattern>
          </servlet-mapping>
      ~~~

      

##### 	get和post请求的分发处理

核心方法：httpServletRequest.getMethod()

~~~java
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("Hello Servlet!");
        // 判断是get还是post方法
        // 1. 类型转换，将ServletRequest转换为他得子类 HttpServletRequest
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;

        // 2.获取请求方式
        String method = httpServletRequest.getMethod();

        if("GET".equals(method)){
            doGet();
        } else if ("POST".equals(method)){
            doPost();
        }
    }
~~~





#### *通过继承HttpServlet，实现Servlet程序

实现doGet、doPost方法

~~~java
public class HelloServlet2 extends HttpServlet {

    /**
     *  get请求调用
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("HelloServlet2 doGet()");
    }


    /**
     *  post请求调用
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("HelloServlet2 doPost()");
    }
}public class HelloServlet2 extends HttpServlet {

    /**
     *  get请求调用
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("HelloServlet2 doGet()");
    }


    /**
     *  post请求调用
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("HelloServlet2 doPost()");
    }
}
~~~



#### Servlet - GenericServlet - HttpServlet关系

HttpServlet抽象类实现了service方法，并实现了请求的分发处理

![1596445940769](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596445940769.png)

#### 通过创建Servlet程序

右键所要创建的包，点击创建

![1596442694082](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596442694082.png)

创建完需要去web.xml配置路径





#### ServletConfig 类

Servlet配置信息类

三大作用

1. 获取Servlet程序别名，servlet-name的值

2. 获取初始化参数init.param

   在web.xml 设置初始化参数（在对应servlet程序的servlet标签内部的子标签<init-pram>）

   ~~~xml
       <servlet>
           <servlet-name>HelloServlet</servlet-name>
           <servlet-class>org.studyCSL.pro.HelloServlet</servlet-class>
           <!--init-param初始化参数-->
           <init-param>
               <!--参数名-->
               <param-name>url</param-name>
               <!--参数区-->
               <param-value>jdbc:mysql://localhost:3306/test</param-value>
           </init-param>
       </servlet>
   ~~~

   

3. 获取ServletContext对象

~~~java
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("初始化");
        // 1.获取Servlet程序别名，servlet-name的值
        System.out.println("HelloServlet 别名 ： " + servletConfig.getServletName());

        // 2.获取初始化参数init.param
        System.out.println("初始化参数:"  + servletConfig.getInitParameter("username"));
        System.out.println("初始化参数:"  + servletConfig.getInitParameter("url"));

        // 3.获取ServletContext对象
        System.out.println(servletConfig.getServletContext());

    }
~~~



##### 在Servlet的其他函数中使用Config对象

- 获得当前Servlet程序的Config对象：getServletConfig()

* 在重写`init`中加入 `super.init(config)` ，config对象才能在其他函数中调用

~~~java
    @Override
    public void init(ServletConfig config) throws ServletException {
        // 加了 super.init(config) ，config对象才能在其他函数中调用
        super.init(config);
    }
~~~



#### ServletContext类

1. ServletContext是一个接口，它表示一个Servlet上下文对象
2. **一个web工程只有一个**ServletContext对象
3. ServletContext是一个域对象
4. 生存期：**在工程部署时创建，工程停止时销毁。**

- 域对象：想Map一样存取数据的对象，这里指存取数据的操作范围



|        | 存数据         | 取数据         | 删除数据          |
| ------ | -------------- | -------------- | ----------------- |
| Map    | put()          | get()          | remove()          |
| 域对象 | setAttribute() | getAttribute() | removeAttribute() |



##### ServletContext类的四个作用

1. 获取 web.xml 中配置的上下文参数 context-param

2. 获取当前工程路径 ：/工程路径

   示例：

   ~~~java
   servletContext.getContextPath();
   ~~~

   结果：

   ~~~java
   当前工程路径：/servlet
   ~~~

   

3. 获取工程部署后在服务器硬盘上的绝对路径

   斜杠在服务器解析是表示地址为:http://ip:port/工程路径
   `映射IDEA工程代码里的web目录`

   ```java
   servletContext.getRealPath("/")
   ```

   - **上方法获取到的结果：D:\tools\CodingTools\Java\idea\idea2020\JavaWeb\out\artifacts\servlet_war_exploded\***

   - **是Servlet工程部署在磁盘具体的目录**

     ![1596466345654](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596466345654.png)

     

     

   - **WEB-INF中存着工程目录中src目录下的.java文件所编译成为的.class字节码文件**和**工程目录中的Servlet配置文件web.xml**

     ![1596466485853](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596466485853.png)

     ![1596466505365](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596466505365.png)

     

   

   ![1596466059946](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596466059946.png)

   C:\Users\maxcs\.IntelliJIdea2019.3\system\tomcat\Tomcat_8_5_56_JavaWeb_2

   上路径是IDEA整合tomcat之后Tomcat被拷贝的一些副本内容，在该目录下：

   1. 有tomcat运行需要的配置文件和其他内容

   2. 在Catalina的localhost中有一个对工程文件路径的映射`.xml`文件

      ![1596466254082](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596466254082.png)

   

   

   示例代码：

   ~~~java
    //D:\tools\CodingTools\Java\idea\idea2020\JavaWeb\out\artifacts\servlet_war_exploded
           System.out.println("工程部署的路径是：" + servletContext.getRealPath("/"));
   
           // D:\tools\CodingTools\Java\idea\idea2020\JavaWeb\out\artifacts\servlet_war_exploded\css
           System.out.println("工程下css目录的结对路径：" + servletContext.getRealPath("/css"));
   
           //D:\tools\CodingTools\Java\idea\idea2020\JavaWeb\out\artifacts\servlet_war_exploded\images\1.jpg
           System.out.println("工程下images目录中1.jpg的结对路径：" + servletContext.getRealPath("/images/1.jpg"));
   ~~~

   

   **注：获取其他目录路径的时候，要目录下有文件IDEA才会在磁盘中建立那个目录，否则只是可以获取目录，但是具体目录不存在。**

   

   

4. 像Map一样存取数据

   在一个Servlet中存数据，在这次工程停止之前都不会销毁，并且为所有Servlet程序共享。

   contextServlet1.java

   ~~~java
       protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           // 获取 ServletContext
           ServletContext servletContext = getServletContext();
   
           // 以键值对的形式存数据
           servletContext.setAttribute("key1","value1");
   
           System.out.println("context1 key1数据对的值：" + servletContext.getAttribute("key1"));
       }
   ~~~

   contextServlet2.java

   ~~~java
       protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           ServletContext servletContext = getServletContext();
           System.out.println("context2 key1数据对的值：" + servletContext.getAttribute("key1"));
       }
   ~~~

   

   调用结果：

   ![1596468914933](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596468914933.png)

   



#### HTTP协议



##### HTTP请求协议内容

###### GET请求HTTP协议内容

1. 请求行
2. 请求头:    key-value

![1596508288953](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596508288953.png)



###### POST请求HTTP协议内容

1. 请求行
2. 请求头
3. 请求体：发送给服务器的数据

![1596511073889](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596511073889.png)



###### 常用的GET请求操作

1. form标签，method=get
2. a标签
3. link标引入css
4. Script标签引入js文件
5. img标签引入图面
6. iframe标签引入页面
7. 在浏览器地址栏输入地址后敲回车



###### 常用POST请求

1. form标签，method=post



##### 响应的HTTP协议

1. 响应行

   1. 响应协议、版本号
   2. 响应状态码
   3. 响应状态描述符

2. 响应头

   1. key-value

      空行

      ![1596512640165](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596512640165.png)

3. 响应体：回传给客户端的数据

   ~~~html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
   <body>
       <form action="http://localhost:8080/servlet/hello" method="post">
           <input type="hidden" name="action" value="login"/>
           <input type="hidden" name="username" value="root"/>
           <input type="submit">
       </form>
   
   </body>
   </html>
   ~~~

   

##### 常用响应码

200		请求成功

302		请求重定向

404		请求服务器已经收到了，但是请求的数据不存在（如地址错误）

500		服务器已收到请求，服务器内容错误（如代码错误）



##### 常用的MIME流媒体类型

![1596513561930](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596513561930.png)





#### HTTPServlet

##### URI 与 URL

首先需要知道 URI 和 URL 的全称：

URL：Universal Resource Locator，即统一资源定位符

URI：Universal Resource Identifier，即统一资源标识符

他们之间的关系为：

URI是URl的更高层次的抽象，是一种字符串文本标准；

即，URI是URL的父类，URI是请求服务器的路径，定义这么一个资源，而URL同时要说明要如何访问这个资源（即http://）



##### HTTPServlet的常用方法

1. 获取请求资源路径	req.getRequestURI()

2. 获取请求统一资源定位符     req.getRequestURL()

3. 获取客户端ip    req.getRemoteHost()

   **注：在localhost的地方换成本机的ipv4可以获取与本机在同一局域网下的ip地址**

4. 获取请求头    req.getHeader("User-Agent")

5. 获取请求方式    req.getMethod()

   ```java
   protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       // 1. 获取请求资源路径  /09_servlet_plus/requestAPIServlet
       System.out.println("URI:" + req.getRequestURI());
   
       // 2. 获取请求统一资源定位符  http://localhost:8080/09_servlet_plus/requestAPIServlet
       System.out.println("URL:" + req.getRequestURL());
   
       // 3. 获取客户端ip   ip:0:0:0:0:0:0:0:1
       // 在localhost的地方换成本机的ipv4可以获取与本机在同一局域网下的ip地址：http://192.168.1.104:8080/09_servlet_plus/requestAPIServlet
       // 要同一个网络才能获取ip ->为啥？
       System.out.println("ip->" + req.getRemoteHost());
   
       // 4. 获取请求头
       // Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.105 Safari/537.36
       System.out.println("User-Agent:" + req.getHeader("User-Agent"));
   
       // 5. 获取请求方式
       System.out.println("请求方式:" + req.getMethod());
   
   }
   ```

6. 获取请求参数    

   req.getParameter("username")

   req.getParameterValues("hobby")--->获取多个参数值，例如复选框的值

   ~~~java
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
           String username = req.getParameter("username");
           String password = req.getParameter("password");
           String[] hobby = req.getParameterValues("hobby");
           System.out.println("用户名：" + username + "\n" + "密码:" + password);
           System.out.println("兴趣爱好：" + Arrays.asList(hobby));
       }
   ~~~

   post请求乱码问题解决：**在获取请求参数之前调用	req.setCharacterEncoding("UTF-8");**

   ~~~java
       @Override
       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
           // 解决Post请求中文乱码
           // 要在获取请求参数之后调用才有效
           req.setCharacterEncoding("UTF-8");
           String user_name = req.getParameter("username");
           String password = req.getParameter("password");
           String[] hobby = req.getParameterValues("hobby");
           System.out.println("用户名：" + user_name + "\n" + "密码:" + password);
           System.out.println("兴趣爱好：" + Arrays.asList(hobby));
       }
   ~~~

   




#### 请求的转发

从一个Servlet程序转发客户端请求到另外一个Servlet程序

特点：

1. 地址栏不发生变化

2. 只有一次性请求

3. 请求Servlet共享Request域中的数据

4. **可以转发到WEB-INF目录下（也就是进入到WEB-INF下的文件），直接在地址栏访问WEB-INF是不允许的**

   例：访问WEB-INF下的form.html

   ```java
   RequestDispatcher requestDispatcher = req.getRequestDispatcher("/WEB-INF/form.html");
   ```

5. 不可以转发到当前工程以外的地址

   原因：地址栏是以 http://localhost:8080/09_servlet_plus/servlet1 开头，

   以百度为例： http://localhost:8080/09_servlet_plus/servlet1/www.baidu.com 无法访问

![1596595228623](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596595228623.png)



关键代码：

Servlet1 

1. 检查请求参数
2. 封装需要传入下一个Servlet的参数===>setAttribute("key","value");
3. 设置转向下一个Servlet

~~~java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.检查请求参数
        String username = req.getParameter("username");
        System.out.println("Servlet1 查看参数：" + username);

        // 2.参数封装，传入下一个Servlet
        req.setAttribute("user_name",username + "s");

        // 3.转向下一个Servlet
        /**
         *  下一个路径必须以斜杠开头
         *
         */
        RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");

        // 可以把请求转发到WEB-INF下的文件
        // RequestDispatcher requestDispatcher = req.getRequestDispatcher("/WEB-INF/form.html");

        requestDispatcher.forward(req,resp);


    }
~~~



Servlet2

~~~java
 @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.检查请求参数
        String username = req.getParameter("username");
        System.out.println("Servlet2 查看参数：" + username);

        // 2.获得Servlet1 封装的参数值
        Object user_name = req.getAttribute("user_name");
        System.out.println(user_name);

        // 3.执行本Servlet2业务
        System.out.println("Servlet2业务");
    }
~~~









#### base标签作用

设置页面相对路径工作时参照的地址，在例如a标签调用本页面的相对路径的之后直接取base标签的值作为本页面的相对路径进行回退等操作。



例：利用请求转发的方式转到目标页面后正确地回退

因为利用Servlet进行跳转之后，地址栏会变成xml文件中mapping成的地址，以此地址在a标签中利用`..`进行路径回溯会出现 http://localhost:8080/09_servlet_plus/forwardC 回退两次，变成http://localhost:8080/index.html 的情况，所有在需要回退的页面里用base标签设定好用于回退的参考相对路径。

参考代码：

Servlet代码：Forward.java

~~~java
public class ForwardC extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("ForwardC 请求转发:");
        req.getRequestDispatcher("/a/b/c.html").forward(req,resp);
    }
}
~~~



`c.html`

~~~html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!--设置页面相对路径工作时参照的地址
        href 为参照路径-->
    <base href="http://localhost:8080/09_servlet_plus/a/b/">
</head>
<body>
    这是a/b/c.html页面<br/>
    <!--相当于当前路径都参照 http://localhost:8080/09_servlet_plus/a/b/c.html../../index.html-->
    <!--经过回退 http://localhost:8080/09_servlet_plus/index.html-->
    <a href="../../index.html">跳回首页</a>
</body>
~~~



index.html

~~~html
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
    web 下的 index.html<br/>
    <a href="a/b/c.html">a/b/c.html</a> <br/>
    <a href="http://localhost:8080/09_servlet_plus/forwardC">请求转发a/b/c.html</a>
</body>
~~~



#### 相对路径与绝对路径



##### 相对路径

`.`			表示当前目录

`..`		  表示上一级目录

资源名	 表示当前目录/资源名

##### 绝对路径

http://ip:port/工程路径/资源名





#### 斜杠的不同意义

`/`被浏览器解析	地址是：http://ip:port/

1. ~~~html
   <a href="/"></a>
   ~~~



`/`被服务器解析	地址是：http://ip:port/工程路径

1. ~~~html
   <url-pattern>/requestAPIServlet</url-pattern>
   ~~~

2. ~~~java
   servletContext.getRealPath("/");
   ~~~

3. ~~~java
   request.getRequestDispatch("/");
   ~~~

4. **特殊情况**：请求重定向

   ~~~java
   response.sendRediect("/");
   ~~~

   得到的是：http://ip:port/



---



#### HttpServletResponse



##### 输出方式

1. 字节流	getOutputStream();	常用于下载-二进制数据
2. 字符流    getWriter();      常用于回传字符串

注：两个流同时只能用一个



##### 给客户端回传字符串数据

PrintWriter writer = resp.getWriter();
writer.write("内容：response's content");

关键代码：

~~~java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        // 方式一：
        // 默认编码：ISO-8859-1
        // 解决中文乱码
        // 设置 服务器 的字符集为 UTF-8
        //resp.setCharacterEncoding("UTF-8");

        // 通过响应头，设置浏览器也使用UTF-8
        //resp.setHeader("Content-Type","text/html;charset=UTF-8");

//        方式2：
        // 同时设置客户端和浏览器使用UTF-8
        // 必须在获取流对象之前调用才有效
        resp.setContentType("text/html;charset=UTF-8");


        PrintWriter writer = resp.getWriter();
        writer.write("内容：response's content");


    }
~~~



###### 解决中文乱码问题

页面默认编码：ISO-8859-1

注：必须在获取流对象之前调用才有效



方式一：分别设置服务器、浏览器的编码

resp.setCharacterEncoding("UTF-8");

resp.setHeader("Content-Type","text/html;charset=UTF-8");



方式二：同时设置客户端和浏览器使用UTF-8	

resp.setContentType("text/html;charset=UTF-8");



#### 请求重定向

![1596636499518](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596636499518.png)



方式一：

在response1中分别设置响应状态码302和响应头的**Location为新地址**

~~~java
        // 方法一
        // 1. 设置响应状态码
        resp.setStatus(302);
       // 2. 设置响应头
       resp.setHeader("Location","http://localhost:8080/09_servlet_plus/response2");
~~~

方式二：

直接调用sendRedirect，默认设置状态码302

~~~java
resp.sendRedirect("http://localhost:8080/09_servlet_plus/response2");
~~~



~~~java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("Response1 ");

//        // 方法一
//        // 1. 设置响应状态码
//        resp.setStatus(302);
//        // 2. 设置响应头
//        resp.setHeader("Location","http://localhost:8080/09_servlet_plus/response2");

        // 方法二
        resp.sendRedirect("http://localhost:8080/09_servlet_plus/response2");
    }
~~~





![1596636955523](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596636955523.png)



特点：

1. 地址栏会发生变化
2. 两次请求（两次从客户端到服务器，服务器到客户端的过程）
3. 不共享Request对象（Response1 的Request不作为参数传入Response2）
4. 不能访问WEB-INF下的资源
5. 可以访问工程外的资源（因为地址栏不受限了sendRedirect之后可以填任意的地址）