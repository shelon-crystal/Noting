

### Filter

拦截请求：权限处理->特定用户只能访问特定的目录资源

![1602386112521](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602386112521.png)



#### 1.1 Filter的创建和使用



##### 1.1.1 Filter的创建

1. 在src下新建一个包，创建AdminFilter类继承Filter接口，并实现三个方法

   ![1602402255638](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602402255638.png)

   

   doFilter方法执行过滤

   ~~~java
   public class AdminFilter implements Filter  {
   
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
   
       }
   
       /**
        *  doFilter专门用于拦截请求，可以做权限检查
        * @param servletRequest
        * @param servletResponse
        * @param filterChain
        * @throws IOException
        * @throws ServletException
        */
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
   
           HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
           HttpSession session = httpServletRequest.getSession();
           Object user = session.getAttribute("user");
           if (user == null){
               // 禁止用户访问资源
               httpServletRequest.getRequestDispatcher("/login.jsp").forward(servletRequest,servletResponse);
               return;
           } else {
               // 让程序继续往下访问用户的目标资源
               filterChain.doFilter(servletRequest,servletResponse);
           }
       }
   
       @Override
       public void destroy() {
   
       }
   }
   ~~~

   



2. 在web.xml下配置Filter

   **pattern 与 servlet配置不同的地方：**

   配置拦截路径
           / 表示请求地址：http://ip:port/工程路径 映射到web目录
           /admin/* 表示：http://ip:port/工程路径/admin/*
           admin下的任何文件

   ~~~xml
       <filter>
           <!--该Filter的别名-->
           <filter-name>AdminFilter</filter-name>
           <filter-class>org.studyCSL.filter.AdminFilter</filter-class>
       </filter>
       <filter-mapping>
           <!--filter-name表示当前拦截路径给哪个filter用-->
           <filter-name>AdminFilter</filter-name>
           <!--配置拦截路径
           / 表示请求地址：http://ip:port/工程路径 映射到web目录
           /admin/* 表示：http://ip:port/工程路径/admin/*
           admin下的任何文件
           -->
           <url-pattern>/admin/*</url-pattern>
       </filter-mapping>
   ~~~

   

#### 1.2 Filter的生命周期



Filter的生命周期包含以下方法：

1. 构造器方法

2. init初始化方法

   1 和 2 在web工程启动的时候执行（Filter已经创建）

3. doFilter过滤器方法
   每次拦截请求都会执行

4. destroy销毁方法

   停止web工程的时候执行，同时也会销毁FIlter过滤器





#### 1.3 FilterConfig类



tomcat每次创建Filter的时候，同时创建一个FilterConfig类，该类包含了Filter配置文件的配置信息

![1602406308358](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602406308358.png)

作用：获取Filter过滤器的配置内容

1. 获取Filter名称filter-name的内容

2. 获取web.xml中Filter配置的 init-param 初始化参数

   ~~~xml
       <filter>
           <!--该Filter的别名-->
           <filter-name>AdminFilter</filter-name>
           <filter-class>org.studyCSL.filter.AdminFilter</filter-class>
           <init-param>
               <param-name>username</param-name>
               <param-value>root</param-value>
           </init-param>
           <init-param>
               <param-name>url</param-name>
               <param-value>jdbc:mysql://localhost:3306/test/</param-value>
           </init-param>
       </filter>
   ~~~

   

3. 获取ServletContext对象org.apache.catalina.core.ApplicationContextFacade@51c6d9e7

![1602406865941](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602406865941.png)









#### 1.4 FilterChain执行细节



![1602427066087](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602427066087.png)



Filter执行顺序为在web.xml中配置Filter的顺序

如果在最后一个Filter的中没有chain.doFilter() 则会直接返回执行上一个Filter的后置代码，可以得出结论：chain.doFilter是连接下一个Filter/资源的连接器，如果没有这个连接器，后续的回归代码不会执行。



#### 1.5 Filter拦截路径的三种配置方式



##### 1.5.1 精确匹配

~~~xml
<url-pattern>/target.jsp</url-pattern>
~~~

请求地址必须为：http://ip:port/工程路径/target.jsp



##### 1.5.2 目录匹配

~~~xml
<url-pattern>/admin/*</url-pattern>
~~~

请求地址必须为：http://ip:port/工程路径/admin/*



##### 1.5.3 后缀名匹配

~~~xml
<url-pattern>*.html</url-pattern>
~~~

请求地址必须为：请求地址必须以.html结尾才会拦截，**不用以斜杠开头**



**注：Filter只关系请求的地址是否匹配，不关心请求的资源是否存在。**