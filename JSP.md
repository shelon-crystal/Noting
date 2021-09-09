JSP

[toc]



##### 九大内置对象





##### 四大域对象

1. pageContext		 当前jsp页面有效
2. request                  一次请求有效
3. session                  一次会话（打开浏览器至关闭浏览器期间）范围内有效
4. application             整个web工程启动期间有效（重新部署后失效）

pageContext < request < session < application 

##### 输出对象

*** **out：**out.write()**	（一般使用out）

1. out.write()           只能输出字符串型数据，对输入进来的数据的每个字符都进行 char 型转换输出
2. out.print()            可以输出任意类型数据，对输入进来的数据都进行String型转换，然后调用write()输出

    * 所以一般用out.print()方式进行输出



response：**response.getWriter().write()**

主要区别：

- 从结果上看，输出顺序不同，属于response的输出流优先于out输出流；

- 从底层看，在jsp代码执行完成之后，会out流会执行out.flush() 操作，将out缓冲区的数据追加写入到response缓冲区尾部，后再经过response输出到客户端

![1599183793357](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599183793357.png)



##### 常用标签

1. 静态包含(include标签)

特点：将被插入的jsp文件代码拷贝到include标签的位置

~~~jsp
<%@ include file="/include/footer.jsp" %>
~~~

案例：用于一般网站尾部插入的的版权、联系信息，只维护一份.jsp静态页面，利于修改



2. 动态包含(include标签)

~~~jsp
<jsp:include page="/include/footer.jsp">
        <jsp:param name="username" value="csl666"/>
        <jsp:param name="password" value="147258369"/>
</jsp:include>
~~~

特点：将包含的jsp翻译成java代码，再被翻译的调用页面中，采用一个语句调用被包含的页面执行输出

**注意：调用语句：也将当前页面的reques和response传入了被包含页面中**，被包含的页面与原页面公用一个缓冲区

org.apache.jasper.runtime.JspRuntimeLibrary.include(**request, response**, "/include/footer.jsp", out, false);









##### ServletContextListener 监听器

生存期：工程启动时创建，停止时销毁

使用步骤：

1. 编写类实现ServletContextListener

2. 实现两个回调方法

   ![1599188570821](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599188570821.png)

3. 到`web.xml`配置监听器

~~~xml
    <listener>
        <listener-class>org.studyCSL.Listener.MyServletContextListenImpl</listener-class>
    </listener>
~~~







##### EL表达式

格式：`${表达式}`

作用：替代jsp页面中的表达式脚本再jsp页面中进行数据的输出，主要输出域对象中的数据。

比较：相比于表达式脚本`<%=expression%>`，在表达式为空的时候，表达式脚本输出的时null，而EL表达式输出的则是空串 `""`

当四个域中有相同的key的时候，按照四个域从小到大的顺序去搜索，找到就输出。

~~~jsp
    <%
        pageContext.setAttribute("key","pageContext");
        request.setAttribute("key","request");
        session.setAttribute("key","session");
        application.setAttribute("key","application");
    %>
    ${key}
~~~



案例1：

使用EL表达式输出一个Bean中的数据

1. 在src包下创建一个Bean

2. 创建实例

   ~~~java
   
           Person per1 = new Person();
           per1.setName("11");
           per1.setPhones(new String []{"1313131","1515151"});
   
           List<String> cities = new ArrayList<String>();
           cities.add("beijing");
           cities.add("shanghai");
           cities.add("guangzhou");
           per1.setCities(cities);
   
           Map<String,Object> map = new HashMap<>();
           map.put("key1","value1");
           map.put("key2","value2");
           map.put("key3","value3");
           per1.setMap(map);
   
           pageContext.setAttribute("person",per1);
   
   ~~~

3. EL表达式输出

   ~~~jsp
       输出Person
       ${ person } <br/>
       Person.name =
       ${ person.name } <br/>
       Person.phone =
       ${ person.phones[0] } <br/>
       Person.cities =
       ${ person.cities } <br/>
       Person.map =
       ${person.map} <br/>
       map中某个key
       ${person.map.key1}
   ~~~

   

结果：

![1599207929703](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599207929703.png)



**输出的实质**：寻找对应属性的get方法，利用get方法进行输出

---





###### EL表达式运算



1. 关系运算：

![1599208346923](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599208346923.png)



2. 逻辑运算：

![1599208370776](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599208370776.png)



3. 算数运算

![1599208421587](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599208421587.png)



4. empty运算

判断表达式的值是否为空

~~~jsp
    <%
        // 1. 表达式值为Null的时候
        request.setAttribute("EMPTY",null);
        // 2. 值为空串的时候
        request.setAttribute("EMPTYSTR","");
        // 3. Object类型数组，长度为0
        request.setAttribute("OBJECT",new Object[]{});
        // 4. List集合，元素个数为0
        request.setAttribute("LIST",new ArrayList<>());
        // 5. map集合，元素个数为0
        request.setAttribute("MAP",new HashMap<>());
    %>

    ${ empty EMPTY} <br/>
    ${ empty EMPTYSTR} <br/>
    ${ empty OBJECT} <br>
    ${ empty LIST} <br>
    ${ empty MAP} <br>
~~~



5. 支持三元运算

   ![1599209802804](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599209802804.png)



6. 点运算和中括号运算

注：**当map中的key有特殊字符的时候可以使用中括号加`引号`包含key来输出key对应的value**

~~~jsp
<% 
Map<String,Object> map = new HashMap<String, Object>(); 
map.put("a.a.a", "aaaValue"); 
map.put("b+b+b", "bbbValue"); 
map.put("c-c-c", "cccValue"); 
request.setAttribute("map", map); 
%> 
${ map['a.a.a'] } <br>
${ map["b+b+b"] } <br> 
${ map['c-c-c'] } <br>
~~~





###### EL表达式通过定义Scope对象来获取内置对象中的数据

![1599275018257](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599275018257.png)

![1599275032755](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599275032755.png)

![1599275048654](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599275048654.png)



当不同的内置对象设置了相同的key之后，获取数据的方法

利用scope.key获取数据

~~~jsp
<body>
    <%
        pageContext.setAttribute("key1","pageContext1");
        pageContext.setAttribute("key2","pageContext2");
        request.setAttribute("key2","request");
    %>

    ${ pageScope.key1 }
    ${ pageScope.key2 }
    ${ requestScope.key2 }
</body>
~~~



pageContext

        request.getScheme()         获取请求协议
        request.getServerName()     获取服务器ip
        request.getServerPort()     服务器端口
        request.getContextPath()    获取工程路径
        request.getMethod()         获取请求方法
        request.getRemoteHost()     获取客户端 ip 地址
        session.getId()             获取session的id
~~~jsp
<body>
    1. 协议： ${ pageContext.request.scheme } <br>
    2. 服务器 ip：${pageContext.request.serverName } <br>
    3. 服务器端口： ${pageContext.request.serverPort }<br>
    4. 获取工程路径： ${ pageContext.request.contextPath}<br>
    5. 获取请求方法： ${pageContext.request.method }<br>
    6. 获取客户端 ip 地址： ${pageContext.request.remoteHost }<br>
    7. 获取会话的 id 编号：${pageContext.session.id}<br>
</body>
~~~



param/paramValues

1. param 当参数username只有一个值的时候

   ~~~jsp
   ${ param.username } <br/>
   ~~~

2. paramValues 当参数例如username有多个值的时候   

   将指定的请求参数用以一个字符串数组存储，然后利用中括号运算获取

   ~~~jsp
       ${ paramValues.username[0] } <br/>
       ${ paramValues.hobby[0]  }
       ${ paramValues.hobby[1]  } <br/>
   ~~~

   

header/headerValues

1. header

   ~~~jsp
       请求头User-Agent：${ header["User-Agent"] } <br>
       请求头Connection：${ header.Connection }    <br>
   ~~~

2. headerValues

   ~~~jsp
       ${ headerValues['User-Agent'][0] }  <br>
   ~~~

   

cookie

~~~jsp
    ${ cookie }
    ${ cookie.JSESSIONID.name }
    ${ cookie.JSESSIONID.value }    <br>
~~~



initParam

1. 需要到.xml文件中配置初始化参数	

~~~xml
    <context-param>
        <param-name>username</param-name>
        <param-value>csl123</param-value>
    </context-param>
    <context-param>
        <param-name>password</param-name>
        <param-value>jdbc::mysql///test</param-value>
    </context-param>
~~~

2. 获取初始化参数

~~~jsp
    ${ initParam.username } <br>
    &lt;context-param&gt;:${ initParam.password }
~~~





---



###### JSTLcore核心库的使用



核心库的使用步骤

1. 将D:\tools\CodingTools\Java\idea\pag\尚硅谷JavaWeb_2020idea新版\笔记、代码、资料\资料\09-EL表达式&JSTL标签库\笔记\

   taglibs-standard-impl-1.2.1.jar

   taglibs-standard-spec-1.2.1.jar

2. 在WEB-INF下建立lib文件夹，复制jar包进lib

3. 添加入库



set标签

属性说明：

```jsp
    <c:set/>    往域中保存数据
    域对象.setAttribute(key,value)
    scope 属性设置保存到哪个域
            page 表示 pageContext
            request 表示 request
            session 表示 session
            application 表示 application
    var 属性设置 key
    value 属性设置 value
```
代码示例：

~~~jsp
    ${ requestScope.abc } <br>
    <c:set scope="request" var="abc" value="value1"/>
    ${ requestScope.abc } <br>
~~~





if标签

属性说明：

~~~jsp
           <c:if />
           if标签用于条件判断

           test 属性表示判断的条件
           如果test为振，表达式中间的语句就会输出
~~~

代码示例：

~~~jsp
    <c:if test="${ 12 == 12}">
        yes
    </c:if>
~~~







choose-when-otherwise标签

属性说明：

~~~jsp
        <c:choose> <c:when> <c:otherwise>
        作用：多路判断 类似于 switch case default
        choose开始选择判断
        when标签表示每一种情况
        otherwise标签表示其他情况


        注意：
        1.只要一个条件成立，其他条件就不会进行判断，不需要手动break
        2.在标签内部使用jsp注释
        3.when标签的父标签一定要是choose标签，如果要在其他标签中嵌套，则再写一个choose标签，之后再写when标签
~~~

代码示例：

~~~jsp
    <%
        request.setAttribute("key11",150);
    %>
    <c:choose>
        <c:when test="${requestScope.key11 > 190}">
            190++
        </c:when>
        <c:when test="${requestScope.key11 >= 200}">
            200++
        </c:when>
        <c:otherwise>
            <c:choose>
                <c:when test="${ requestScope.key11 <170 }">
                    170 --
                </c:when>
            </c:choose>
        </c:otherwise>
    </c:choose>

~~~





![1599469386837](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599469386837.png)





##### 文件上传

上传文件的HTTP协议内容

![1599790964180](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599790964180.png)

**注意：上传的表单必须以post请求的方式**



###### 使用apache提供的jar包实现（commons-fileupload-1.2.1.jar、commons-io-1.4.jar）

jar包路径：D:\tools\CodingTools\Java\idea\pag\尚硅谷JavaWeb_2020idea新版\笔记、代码、资料\资料\09-EL表达式&JSTL标签库\笔记\文件上传和下载

**注：这个jar包的方法只能解析多段数据，所以使用前要先判断数据格式**

常用到的方法：

![1599792875909](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599792875909.png)

![1599792955189](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599792955189.png)



使用实例：

1. 判断是否为多段数据
2. 创建FileItemFactory实现类对象，把该对象作为ServketFileUpload类对象的构造参数
3. 创建用于解析上传数据的工具类ServletFileUpload
4. 解析上传的数据，servletFileUpload.parseRequest(req)得到上传的表单List
5. 遍历表单项，用if分离出普通表单项和上传文件表单
6. 将上传的文件写入到指定的磁盘路径：fileItem.write(new File("path\\\\" + fileItem,getName()))

获取上传的文件内容：

~~~java
        ServletInputStream inputStream = req.getInputStream();
        byte [] buffer = new byte[1024000];
        int read = inputStream.read(buffer);
        System.out.println(new String(buffer,0,read));
        System.out.println("out");
~~~



代码示例：

~~~java
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        /*使用apache提供的jar包封装的输出流*/
        /*
        导包：fileupload、io包
         */

        // 1. 判断上传的数据是否是多段数据
        if (ServletFileUpload.isMultipartContent(req)){
            // 创建fileItemFactory工厂实现类
            FileItemFactory fileItemFactory = new DiskFileItemFactory();

            // 创建用于解析上传数据的工具类ServletFileUpload类
            ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);

            // 解析上传的数据
            try {
                List<FileItem> list = servletFileUpload.parseRequest(req);

                for (FileItem fileItem : list){

                    if(fileItem.isFormField()){
                        // 普通文件
                        System.out.println("name属性值：" + fileItem.getFieldName());
                        System.out.println("value属性值：" + fileItem.getString("UTF-8"));
                    }else {
                        // 上传的文件
                        System.out.println("name属性值：" + fileItem.getFieldName());
                        System.out.println("文件名" + fileItem.getName());
                        // 将上传的文件文件写入到指定路径
                        fileItem.write(new File("D:\\tools\\CodingTools\\Java\\idea\\pag\\尚硅谷JavaWeb_2020idea新版\\笔记、代码、资料\\资料\\09-EL表达式&JSTL标签库\\笔记\\文件上传和下载\\图片资源\\" + fileItem.getName()));
                    }
                }

            } catch (FileUploadException e) {
                e.printStackTrace();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
~~~



---





##### 文件下载

1. 读取要下载的文件名
2. 读取要下载的文件内容
3. 把下载的文件回传给客户端
4. 回传前，通过响应头告诉客户端返回的数据类型
5. 告诉客户端收到的数据是用于下载使用（还是使用响应头）



代码示例：

~~~java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        1. 读取要下载的文件名
        String downloadFileName = req.getParameter("fileName");
        System.out.println(downloadFileName);
//        2. 读取要下载的文件内容
        ServletContext servletContext = getServletContext();
        // 获取要下载的文件MIME类型
        String mimeType = servletContext.getMimeType("/file/" + downloadFileName);
        System.out.println("文件类型:" + mimeType);
//        4. 回传前，通过响应头告诉客户端返回的数据类型
        resp.setContentType(mimeType);
//        5. 告诉客户端收到的数据是用于下载使用（还是使用响应头）
        // 默认用于显示
        // attachment 附件形式
        // filename 为指定下载的文件名
        resp.setHeader("Content-Disposition","attachment;filename=" + downloadFileName);

        // 创建文件的输入流
        InputStream resourceAsStream = servletContext.getResourceAsStream("/file/" + downloadFileName);
        // 获取响应的输出，响应给用户
        OutputStream  outputStream = resp.getOutputStream();
//        3. 把下载的文件回传给客户端
        // 读取输入流中所有的数据，传输给输出流
        IOUtils.copy(resourceAsStream,outputStream);


    }

~~~



###### 解决中文下载名乱码

IE、Chrome：URLEncoder

```java
URLEncoder.encode(downloadFileName,"UTF-8")
```

Firefox：Base64

请求头需要做出的修改：

![1599832627271](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599832627271.png)

~~~java
resp.setHeader("Content-Disposition","attachment;filename==?UTF-8?B?" + new BASE64Encoder.encoder(downloadFileName).getBytes("UTF-8") + "?=";
~~~



分立演示

~~~java
public class Base64 {
    public static void main(String[] args) throws IOException {
        String content = "编码内容";
        // 创建base64编码器
        BASE64Encoder base64Encoder = new BASE64Encoder();

        String encodedString = null;

            // 执行编码操作
        encodedString = base64Encoder.encode(content.getBytes("UTF-8"));


        System.out.println(encodedString);

        BASE64Decoder base64Decoder = new BASE64Decoder();
        // 解码
        byte [] bytes = base64Decoder.decodeBuffer(encodedString);

        String str = new String(bytes,"UTF-8");

        System.out.println(str);
    }
}
~~~





**注：`ctrl+shift+r` 鼠标左键点击要查找的目录或文件实现批量替换和查找**

![1599917801706](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1599917801706.png)













---



##### 书城项目（三）



1. 将页面改成jsp格式，统一修改方法：左键点击要修改的目录pages-> `ctrl + shift + R` 

2. 创建common包，将页面的公共部分写成一个jsp文件，例如head、footer、menu等等，在原页面中用一个静态include标签包含common包里的公共jsp

3. 书写动态的base跳转路径，包括在head.jsp内

   head.jsp示例

   ~~~jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <%
       String basePath = request.getScheme()
               + "://" + request.getServerName()
               + ":" + request.getServerPort()
               + request.getContextPath()
               + "/";
   %>
   <!--base标签固定相对路径跳转的结果-->
   <base href="<%=basePath%>">
   <link type="text/css" rel="stylesheet" href="static/css/style.css">
   <script type="text/javascript" src="static/script/jquery-1.7.2.js"></script>
   ~~~

4. 反射优化if-else结构

5. 优化形成BaseServlet类

6. 数据封装和BaseUtils类

   主要用于解决请求参数很多，getParameter()和new User() 代码行过于冗长和反复调用的问题

7. 数据封装和抽取BaseUtils

   7.1 导入第三方类库：commons-beanutils-1.8.0.jar、commons-logging-1.1.1.jar

   7.2 使用req.getParameterMap()获取参数

   ​	底层是一个Map，获取传入的参数键值对

   ​	action = [regist]
   ​	username = [csl777]
   ​	password = [123456]
   ​	repwd = [123456]
   ​	email = [sawda@qq.com]
   ​	code = [abcde]

   7.3 在Utils包内写一个WebUtils类去封装注入的操作

   ~~~java
   public class WebUtils {
       /**
        * 把Map中的值注入到对应的JavaBean中
        * @param value
        * @param bean
        * 参数中传 Map不传HttpServlet原因
        * 在dao、service、Web层都没有HttpServlet的API
        * Map在dao、service、Web
        */
       public static <T> T copyParamToBean(Map value, T bean){
           // 利用BeanUtils注入参数
           try {
               System.out.println("注入之前" + bean);
               /**
                *  把所有请求的参数都注入到user对象中
                */
               BeanUtils.populate(bean,value);
               System.out.println("注入之后" + bean);
           } catch (Exception e) {
               e.printStackTrace();
           }
           return bean;
       }
   }
   ~~~

   7.4 实际上通过**`User user = WebUtils.copyParamToBean(req.getParameterMap(),new User())`**方法注入

   ​	注入原理：在获取参数的键值对的时候，通过键key查找并调用对应的Bean(User)中的set方法，查找通过拼接set和key，如**set 、key：id，拼接成：setId()**.

8. ss

9. 







---



##### 书城项目（四）

###### 利用EL表达式代替表达式脚本实现回显功能

~~~jsp
${ empty requestScope.msg ?"请输入用户名和密码":requestScope.msg}
<%--<%=request.getAttribute("msg")==null?"请输入用户名和密码":request.getAttribute("msg")%>--%>

<input class="itxt" type="text" placeholder="请输入用户名"
autocomplete="off" tabindex="1" name="username"
<%--设置回显--%>
value="${requestScope.username}"
<%--value=<%=request.getAttribute("username")==null?"":request.getAttribute("username")%>"--%> id="username" /> <br />
~~~







##### 书城项目（五）

1. ###### 图书模块（建库->Bean->Dao->Service->Web）

   1.1 编写图书模块数据库表

   sql文件路径：D:\tools\CodingTools\Java\idea\pag\尚硅谷JavaWeb_2020idea新版\笔记、代码、资料\资料\11-项目第五阶段-图书模块\笔记\sql.sql

   

   1.2 编写图书模块JavaBean

   1.3 编写图书模块的Dao和测试Dao

   1.4 编写图书模块的Service和测试Service

   1.5 编写图书模块的Web层和页面联调测试

   ​	1.5.1 编写查询所有图书功能

   ~~~java
       protected void list(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           // 1. 查询全部图书
           List<Book> bookList = bookService.queryBooks();
           // 2. 保存到request域中
           req.setAttribute("book",bookList);
           // 3. 请求转发到pages/manager/book_manager.jsp页面(把数据发送过去)
           req.getRequestDispatcher("/pages/manager/book_manager.jsp").forward(req,resp);
   
       }
   ~~~

   

   ​	修改页面跳转路径

   ~~~jsp
       <%--将action类型传入BaseDao，然后转换成post类型请求，进入dopost方法体，通过反射调用BookServlet里的list方法--%>
       <a href="manager/bookServlet?action=list">图书管理</a>
   ~~~

   

   ​	修改配置.xml文件

   ~~~xml
       <servlet>
           <servlet-name>BookServlet</servlet-name>
           <servlet-class>org.studyCSL.web.BookServlet</servlet-class>
       </servlet>
       <servlet-mapping>
           <servlet-name>BookServlet</servlet-name>
           <url-pattern>/manager/bookServlet</url-pattern>
       </servlet-mapping>
   ~~~

   **注：改成/manager/bookServlet的原因是用于区分用户和后台人员**

   ![1600593585212](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1600593585212.png)

   

   

   ​	在book_manager.jsp中调用JSTL标签库遍历所有图书

   ~~~jsp
   			<%--利用JSTL标签库对request中的数据进行遍历显示--%>
   			<c:forEach items="${requestScope.page.items}" var="book">
   				<tr>
   					<td>${book.name}</td>
   					<td>${book.price}</td>
   					<td>${book.author}</td>
   					<td>${book.sales}</td>
   					<td>${book.stock}</td>
   					<td><a href="manager/bookServlet?action=getBook&id=${book.id}&pageNum=${requestScope.page.pageNum}">修改</a></td>
   					<td><a class="deleteClass" href="manager/bookServlet?action=delete&id=${book.id}&pageNum=${requestScope.page.pageNum}">删除</a></td>
   				</tr>
   			</c:forEach>
   ~~~
   

​	这里的${requestScope.page.pageNum}应用在分页函数，将当前页面编号传入Servlet，在Servlet程序中调用查询来显示分页。





​	1.5.2 添加图书功能实现

​	实现模型图：

​	![1600600078068](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1600600078068.png)

​	

​	在Servlet中的三个步骤：

 1. 获取请求参数
   
 2. 调用BookService.addBook()保存图书
   
 3. 跳转到图书列表界面
   
       **跳转方式选择重定向，不选择请求转发，因为请求转发之后，在利用f5功能键之后回产生表单重复提交的问题，导致重复添加内容。**
   
       重定向的`"/"`只表示当http://ip:port/，在重定向路径的时候需要加上页面工程路径

   ~~~java
       protected void add(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           // 1.获取请求参数
           Book book = WebUtils.copyParamToBean(req.getParameterMap(),new Book());
   
           // 2.调用BookService.addBook()保存图书
           bookService.addBook(book);
   
           // 3.跳转到图书列表界面
           // 按下f5存在表单重复提交的bug
   //        req.getRequestDispatcher("/manager/bookServlet?action=list").forward(req,resp);
           resp.sendRedirect(req.getContextPath() + "/manager/bookServlet?action=list");
       }
   
   ~~~

   

   ​	1.5.3 图书删除功能实现

   	1. 获取请求删除的图书id
        	2. 调用bookService.deleteBookById()
        	3. 重定向到图书列表管理界面

   ​	实现步骤：

   ~~~java
       protected void delete(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           // 1.获取请求删除的图书id
           int id = WebUtils.parseInt(req.getParameter("id"),0);
           // 2.调用bookService.deleteBookById()
           bookService.deleteBookById(id);
           // 3.重定向到图书列表管理界面
           resp.sendRedirect(req.getContextPath() + "/manager/bookServlet?action=list");
   
   
       }
   ~~~

   ​	

   ​	在页面层需要为删除键加入单击事件，让用户确认删除信息	

   ​	**注：因为a标签的class返回false的时候，默认不执行跳转操作，则不会进入Servlet程序执行删除，保证用户在点击取消按键的时候不执行跳转操作。**

   ~~~javascript
   	<script type="text/javascript">
   		$(function () {
   			// 在事件的function函数中有一个this对象，表示当前正在响应事件的dom对象
   			// 给删除的a标签绑定单击事件，用于删除的确认提示操作
   			$("a.deleteClass").click(function () {
   				/**
   				 *  confirm确认其实看函数
   				 *  返回true表示点击确认，返回false表示点击取消
   				 */
   				return confirm("你确定要删除【" + $(this).parent().parent().find("td:first").text() +"】?");
   
   
   			})
   		});
   	</script>
   ~~~

   

   ​	1.5.4 更新图书信息操作实现

   

   ​	具体实现模块图![1600675720616](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1600675720616.png)



​		分两部分完成：

​			第一部分：在点击修改按钮时候，将被点击的图书信息回显到修改界面。

​			- 从页面获取图书信息(id)：

​			在图书管理页面的修改的a标签上加入其对应的图书的id，然后转入Servlet去执行查询服务，得到图书信息之后，将图书信息利用请求转发到book_edit.jsp页面去显示。

~~~jsp
<td><a href="manager/bookServlet?action=getBook&id=${book.id}">修改</a></td>
~~~

​			- 将从数据库得到的需要修改的图书信息请求转发。

~~~java
    protected void getBook(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.获取请求的参数图书编号
        int id = WebUtils.parseInt(req.getParameter("id"),0);
        // 2.调用bookService.queryBookById查询图书
        Book book = bookService.queryBookById(id);
        // 3.保存图书到request域中
        req.setAttribute("book",book);
        // 4.请求转发到pages/manager/book_edit.jsp页面
        req.getRequestDispatcher("/pages/manager/book_edit.jsp").forward(req,resp);
    }
~~~

​			注：由于添加操作和修改操作使用的是同一个jsp页面，所以需要判断设置调用这个jsp的是添加操作还是修改操作

​			解决方案：

			1. 在添加和修改的a标签内设置多一个参数，method=add / method=update，然后在提交表单的隐藏域中使用param.method EL内置对象来设置表单action的值在Servlet中的响应函数。（注入隐藏域，a标签中的参数EL内置对象param会保存参数method）



​			第二部分：点击修改之后，执行修改操作，并返回到图书管理界面显示图书信息。

~~~java
    protected void update(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.获取请求参数，封装为Book对象
        Book book = WebUtils.copyParamToBean(req.getParameterMap(), new Book());
        // 2.调用bookService.updateBook(book);修改图书
        bookService.updateBook(book);
        // 3.重定向回图书列表管理界面
        resp.sendRedirect(req.getContextPath() + "/manager/bookServlet?action=list");
    }
~~~





2. ###### 图书分页

2.1 分页实现结构图：

![1600698013567](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1600698013567.png)



2.2 实体类（Page）：

本质：为了封装一整块图书信息，而封装成为的新实体类

~~~java
    public static final Integer PAGE_SIZE = 4;
    // 当前页码
    private Integer pageNum;
    // 总页码
    private Integer pageTotal;
    // 当前页显示数量
    private Integer pageSize = PAGE_SIZE;
    //总记录数
    private Integer pageTotalCount;
    // 当前页数据
    private List<T> items;
    // 分页条的请求地址
    private String url;
~~~



2.3 dao层

封装对数据库的操作：

利用limit的数据库查询语句

~~~java
    @Override
    public Integer queryForPageTotalCount() {
        String sql = "select count(*) from t_book";
        Number count = (Number) queryForSingleValue(sql);
        return count.intValue();

    }

    @Override
    public List<Book> queryForPageItems(int begin, int pageSize) {
        String sql = "select `id`, `name`, `author`, `price`, `sales`, `stock`, `img_path` imgPath from t_book limit ?,?";
        return queryForList(Book.class,sql,begin,pageSize);
    }
~~~



2.4 service层

具体实现了对一个分页的各项参数的计算方法

总页码：总记录数除以每页要显示的记录数，然后做取余判断，如果余数不为0，则增加多一页用于显示剩下的记录

当前页数据开始索引（begin）：主要用于页面跳转的显示，

​	计算方法为（当前页码(pageNum)-1）* 每页显示的记录数（pageSize）

**设置当前页码说明：**设置页码需要边界，以免用户跳转页码越界。

​	操作：对Page实体类对象中的set方法进行调整

~~~java
    public void setPageNum(Integer pageNum) {
        /*数据边界的有效检查*/
        if (pageNum < 1){
            pageNum = 1;
        }
        if (pageNum > pageTotal){
            pageNum = pageTotal;
        }
        this.pageNum = pageNum;
    }
~~~



分页服务的具体实现方法

~~~java
    @Override
    public Page<Book> page(int pageNum, int pageSize) {
        Page<Book> page = new Page<Book>();

        // 设置每页显示的数量
        page.setPageSize(pageSize);
        // 求总记录数
        Integer pageTotalCount = bookDao.queryForPageTotalCount();
        // 设置总记录数
        page.setPageTotalCount(pageTotalCount);
        // 求总页码
        Integer pageTotal = pageTotalCount / pageSize;
        if (pageTotalCount % pageSize > 0){
            pageTotal+=1;
        }
        // 设置总页码
        page.setPageTotal(pageTotal);

        // 设置当前页码
        page.setPageNum(pageNum);

        // 求当前页数据的开始索引
        int begin = (page.getPageNum() - 1) * pageSize;
        // 求当前页数据
        List<Book> items = bookDao.queryForPageItems(begin,pageSize);
        // 设置当前页数据
        page.setItems(items);

        return page;

    }
~~~



2.5 web与Servlet层

BookServelt：主要用于后台数据的显示、增删查改

**注意：**

重定向的`"/"`仅表示http://ip:port/

add函数细节说明：

1. 在函数内部获取的pageNum参数是由添加图书时传入的参数，参数值为：requestScope.page.pageTotal，即页面总数，目的是为了在添加图书操作完成之后，在重定向的时候把页面定位到所有页面的尾部，方便用户观看到刚刚添加的图书。

~~~java
public class BookServlet extends BaseServlet{

    private BookService bookService = new BookServiceImpl();

    /**
     *  处理分页功能
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void page(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.获取请求参数pageNum 和 pageSize
        // defaultValue 表示用户没有点击页码的时候默认显示第一页，因为此时req获取的为null
        int pageNum = WebUtils.parseInt(req.getParameter("pageNum"),1);
        int pageSize = WebUtils.parseInt(req.getParameter("pageSize"),Page.PAGE_SIZE);
        // 2.调用bookService.page(pageNum,pageSize)获取Page对象
        Page<Book> page = bookService.page(pageNum,pageSize);

        // 设置page的请求地址
        page.setUrl("manager/bookServlet?action=page");
        // 3.保存Page对象到Request域中
        req.setAttribute("page",page);
        // 4.请求转发到pages/manager/book_manager.jsp页面
        req.getRequestDispatcher("/pages/manager/book_manager.jsp").forward(req,resp);
    }


    /**
     *  添加图书
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void add(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        int pageNum = WebUtils.parseInt(req.getParameter("pageNum"),0);
        pageNum+=1;
        // 1.获取请求参数
        Book book = WebUtils.copyParamToBean(req.getParameterMap(),new Book());

        // 2.调用BookService.addBook()保存图书
        bookService.addBook(book);

        // 3.跳转到图书列表界面
        // 按下f5存在表单重复提交的bug
//        req.getRequestDispatcher("/manager/bookServlet?action=list").forward(req,resp);
        resp.sendRedirect(req.getContextPath() + "/manager/bookServlet?action=page&pageNum=" + pageNum);
    }

    /**
     *  删除图书
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void delete(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.获取请求删除的图书id
        int id = WebUtils.parseInt(req.getParameter("id"),0);
        // 2.调用bookService.deleteBookById()
        bookService.deleteBookById(id);
        // 3.重定向到图书列表管理界面
        resp.sendRedirect(req.getContextPath() + "/manager/bookServlet?action=page&pageNum=" + req.getParameter("pageNum"));


    }

    /**
     *  获取图书信息，请求转发到修改页面显示
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void getBook(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.获取请求的参数图书编号
        int id = WebUtils.parseInt(req.getParameter("id"),0);
        // 2.调用bookService.queryBookById查询图书
        Book book = bookService.queryBookById(id);
        // 3.保存图书到request域中
        req.setAttribute("book",book);
        // 4.请求转发到pages/manager/book_edit.jsp页面
        req.getRequestDispatcher("/pages/manager/book_edit.jsp").forward(req,resp);
    }

    /**
     *  更新图书信息
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void update(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.获取请求参数，封装为Book对象
        Book book = WebUtils.copyParamToBean(req.getParameterMap(), new Book());
        // 2.调用bookService.updateBook(book);修改图书
        bookService.updateBook(book);
        // 3.重定向回图书列表管理界面
        resp.sendRedirect(req.getContextPath() + "/manager/bookServlet?action=page&pageNum=" + req.getParameter("pageNum"));
    }

    /**
     *  获取图书列表，主要用于manager层的获取图书
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void list(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1. 查询全部图书
        List<Book> bookList = bookService.queryBooks();
        // 2. 保存到request域中
        req.setAttribute("book",bookList);
        // 3. 请求转发到pages/manager/book_manager.jsp页面(把数据发送过去)
        req.getRequestDispatcher("/pages/manager/book_manager.jsp").forward(req,resp);

    }
}

~~~



功能外调用类（WebUtils）：

方法说明：

1. copyParamToBean（Map value, T bean）方法：在Servlet程序获取到页面输入的数据的时，将页面的参数注入，形成一个Bean对象用于调用服务层。
2. parseInt（String strInt,int defaultValue）方法：将字符串转换成Int数据，第二个参数用于实现不能转换的情况自动返回一个默认值，默认值用传入的参数指定，例子：当传入的字符串（图书价格查询的上界）为空时，返回默认值（在调用时设定为无穷大，即从最低价格，不设上限地查询图书信息并且分页显示）。

~~~java
public class WebUtils {
    /**
     * 把Map中的值注入到对应的JavaBean中
     * @param value
     * @param bean
     * 参数中传 Map不传HttpServlet原因
     * 在dao、service、Web层都没有HttpServlet的API
     * Map在dao、service、Web
     */
    public static <T> T copyParamToBean(Map value, T bean){
        // 利用BeanUtils注入参数
        try {
            System.out.println("注入之前" + bean);
            /**
             *  把所有请求的参数都注入到user对象中
             */
            BeanUtils.populate(bean,value);
            System.out.println("注入之后" + bean);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return bean;
    }

    /**
     *  将字符串转化成Int类型数据
     * @param strInt
     * @param defaultValue
     * @return
     */

    public static int parseInt(String strInt,int defaultValue){
        try {
            return Integer.parseInt(strInt);
        } catch (Exception e) {
//            e.printStackTrace();
        }
        return defaultValue;
    }
}

~~~





ClientBookServlet：主要用于客户端的分页数据显示，并且与前端的部分查询和分页查询显示按钮交互，需要新增一个Servlet

~~~java
public class ClientBookServlet extends BaseServlet {
    private BookService bookService = new BookServiceImpl();

    /**
     *  在前端index区处理分页功能
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void page(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        // 1.获取请求参数pageNum 和 pageSize
        // defaultValue 表示用户没有点击页码的时候默认显示第一页，因为此时req获取的为null
        int pageNum = WebUtils.parseInt(req.getParameter("pageNum"),1);
        int pageSize = WebUtils.parseInt(req.getParameter("pageSize"), Page.PAGE_SIZE);
        // 2.调用bookService.page(pageNum,pageSize)获取Page对象
        Page<Book> page = bookService.page(pageNum,pageSize);

        // 设置page的请求地址
        page.setUrl("client/bookServlet?action=page");

        // 3.保存Page对象到Request域中
        req.setAttribute("page",page);
        // 4.请求转发到pages/manager/book_manager.jsp页面
        req.getRequestDispatcher("/pages/client/index.jsp").forward(req,resp);
    }

    /**
     *  根据价格查询响应的图书信息，并显示到index.jsp
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void pageByPrice(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        // 1.获取请求参数pageNum 和 pageSize
        // defaultValue 表示用户没有点击页码的时候默认显示第一页， 因为此时req获取的为null
        int pageNum = WebUtils.parseInt(req.getParameter("pageNum"),1);
        int pageSize = WebUtils.parseInt(req.getParameter("pageSize"), Page.PAGE_SIZE);
        // 获取价格区间
        int min = WebUtils.parseInt(req.getParameter("min"),0);
        int max = WebUtils.parseInt(req.getParameter("max"),Integer.MAX_VALUE);


        // 2.调用bookService.page(pageNum,pageSize)获取Page对象
        Page<Book> page = bookService.pageByPrice(pageNum,pageSize,min,max);

        StringBuffer urlCon = new StringBuffer("client/bookServlet?action=pageByPrice");
        // 如果有最小价格，追加到url参数中
        if (req.getParameter("min") != null){
            urlCon.append("&min=" + req.getParameter("min"));
        }
        // 如果有最大价格，追加到url参数中
        if (req.getParameter("max") != null){
            urlCon.append("&max=" + req.getParameter("max"));
        }
        // 设置page的请求地址
        page.setUrl(urlCon.toString());

        // 3.保存Page对象到Request域中
        req.setAttribute("page",page);
        // 4.请求转发到pages/manager/book_manager.jsp页面
        req.getRequestDispatcher("/pages/client/index.jsp").forward(req,resp);
    }

}

~~~







2.x 抽取分页条

- 把page分页动作的请求地址作为PageBean的属性，在Servlet中设置其请求地址即可抽取出请求地址，之后如果需要修改请求地址或者扩展即可在对应的Servlet中修改。

- 把`manager/bookServlet?action=page` ====> 

```java
${requestScope.page.url}
```

**(client/bookServlet?action=page同理)**



把分页条抽取成common包内的page_nav.jsp放入分页条

~~~jsp
<%--分页条的开始--%>
<div id="page_nav">
    <%--大于首页才显示--%>
    <c:if test="${requestScope.page.pageNum > 1 }">
        <a href="${requestScope.page.url}&pageNum=1">首页</a>
        <a href="${requestScope.page.url}&pageNum=${requestScope.page.pageNum - 1}">上一页</a>
    </c:if>
    <%--页码输出的开始--%>
    <%--规定能跳转的页码数为4（除当前页码不可点击）--%>
    <c:choose>
        <%--情况1：如果总页码小于等于5，页码范围是1-总页码--%>
        <c:when test="${requestScope.page.pageTotal <= 5}">
            <c:set var="begin" value="1"/>
            <c:set var="end" value="${requestScope.page.pageTotal}"/>
        </c:when>
        <%--情况2：总页码数大于5的--%>
        <c:when test="${requestScope.page.pageTotal > 5}">
            <c:choose>
                <%--情况2.1：当前页码为1-3的时候--%>
                <c:when test="${requestScope.page.pageNum <= 3}">
                    <c:set var="begin" value="1"/>
                    <c:set var="end" value="5"/>
                </c:when>
                <%--情况2.2：当前页码为最后三页的时候--%>
                <%--页码范围：总页码减4到总页码--%>
                <c:when test="${requestScope.page.pageNum >= requestScope.page.pageTotal - 3}">
                    <c:set var="begin" value="${requestScope.page.pageTotal - 4}"/>
                    <c:set var="end" value="${requestScope.page.pageTotal}"/>
                </c:when>
                <%--情况2.3：      3 4 【5】 6 7--%>
                <%--页码范围为当前页码减2 到 当前页码加2--%>
                <c:otherwise>
                    <c:set var="begin" value="${requestScope.page.pageNum - 2}"/>
                    <c:set var="end" value="${requestScope.page.pageNum + 2}"/>
                </c:otherwise>
            </c:choose>
        </c:when>
    </c:choose>
    <%--输出页码--%>
    <c:forEach begin="${begin}" end="${end}" var="i">
        <c:if test="${i == requestScope.page.pageNum}">
            【${i}】
        </c:if>
        <c:if test="${i != requestScope.page.pageNum}">
            <a href="${requestScope.page.url}&pageNum=${i}">${i}</a>
        </c:if>
    </c:forEach>



    <%--页码输出的结束--%>

    <%--如果已经是最后一夜，则补显示下一页--%>
    <c:if test="${requestScope.page.pageNum < requestScope.page.pageTotal}">
        <a href="${requestScope.page.url}&pageNum=${requestScope.page.pageNum + 1}">下一页</a>
        <a href="${requestScope.page.url}&pageNum=${requestScope.page.pageTotal}">末页</a>
    </c:if>

    共${requestScope.page.pageTotal}页，${requestScope.page.pageTotalCount}条记录
    到第<input value="${param.pageNum}" name="pn" id="pn_input"/>页
    <input id="searchPageBtn" type="button" value="确定">
    <script type="text/javascript">
        $(function () {
            $("#searchPageBtn").click(function () {
                var pageNum = $("#pn_input").val();

                if(pageNum < 1 || pageNum > ${requestScope.page.pageTotal}){
                    alert("超过搜索范围，请重新输入");
                    return false;
                }
                //javaScript 提供了一个location地址栏对象
                // 这个对象有一个属性href可以获取浏览器地址栏中的地址

                location.href = "${pageScope.basePath}${requestScope.page.url}&pageNum=" + pageNum;

            });
        });
    </script>


</div>
<%--分页条的结束--%>
~~~



2. 6主页面价格区间的搜索

实现的模块调用图：

![1600932105930](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1600932105930.png)

建立dao层和建立service层的方式与实现分页显示的方法基本一致







##### Cookie

本质：**键值对**

Cookie(String name, String value){

​	Construct a cookie with specified name and value.

}

定义：

1. Cookie是服务器通知客户端保存键值对的一种技术。
2. 客户端有了Cookie之后，每次请求都发送给服务器，数据由浏览器保存，后发送给服务器。
3. 每个Cookie大小不超过4KB。



###### Cookie的创建

~~~java
    protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
        Cookie cookie = new Cookie("key1","value1");
        resp.addCookie(cookie);
        Cookie cookie1 = new Cookie("key2","value2");
        resp.addCookie(cookie1);
        resp.getWriter().write("Cookie创建成功");
    }
~~~



###### Cookie的获取

流程图：

![1601130653009](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601130653009.png)

实现方法：

因为Cookies[] 没有点计算获取内部的Cookie信息，只能通过equal加for遍历的方式去获取指定的Cookie。

1. 封装查询函数(package：org.studyCSL.utils)

   ~~~java
   public class CookieUtils {
       /**
        * 找对应的Cookie对象
        * @param name  Cookie的键
        * @param cookies   从客户端获取的所有Cookie
        * @return 查找结果
        */
       public static Cookie findCookie(String name, Cookie[] cookies){
           if (name == null || cookies == null || cookies.length == 0){
               return null;
           }
   
           for (Cookie cookie : cookies) {
               if (name.equals(cookie.getName())){
                   return cookie;
               }
           }
           return null;
       }
   }
   ~~~

   

2. 获取指定Cookies[] 以及 Cookie

   ~~~java
       protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
   
           Cookie[] cookies = req.getCookies();
   
           // 获取全部的Cookie
           for (Cookie cookie : cookies) {
               // cookie.getName()     方法获取Cookie的名称
               // cookie.getValue()    方法返回Cookie的值
               resp.getWriter().write("Cookie[" + cookie.getName() + "=" + cookie.getValue() + "]" + "<br/>");
           }
   
           // 获取指定的Cookie
           Cookie iwantCookie = CookieUtils.findCookie("key1", cookies);
   //        for (Cookie cookie : cookies) {
   //
   //            if ("key1".equals(cookie.getName())){
   //                iwantCookie = cookie;
   //                break;
   //            }
   //
   //        }
           if (iwantCookie != null){
               resp.getWriter().write("找到Cookie[" + iwantCookie.getName() + "=" + iwantCookie.getValue() + "]" + "<br/>");
           }
   
   
       }
   ~~~

   



###### Cookie值的修改

Cookie的修改包含两种方式(updateCookie)：

1. 创建一个新的同名（同key）的Cookie，把value设置成想要的值，发送到客户端去覆盖。

   ~~~java
           // 修改方案一：创建一个同名Cookie覆盖
   
           Cookie cookie = new Cookie("key1", "newValue");
   
           // 通知客户端保存Cookie
           resp.addCookie(cookie);
   
           resp.getWriter().write("Update Successfully");
   ~~~

   

2. 利用Utils里的findCookie方法获取指定name的Cookie，之后利用cookie.setValue的方式覆写cookie的value，同样修改完之后都要add给客户端保存。

   ~~~java
           // 修改方案二：用查找方法获取要修改的Cookie对象，在调用setValue赋予新的value
   
           // set的方法是不支持中文的字符串传入的
           Cookie cookie = CookieUtils.findCookie("key1",req.getCookies());
   
           if (cookie != null){
               cookie.setValue("newValue2");
               resp.addCookie(cookie);
           }
   ~~~



在客户端的显示效果：

![1601130617362](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601130617362.png)



**注意：**如果修改的内容包括特殊字符或者中文要利用BASE64进行编码解码

![1601131084707](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601131084707.png)



###### 在谷歌浏览器和火狐浏览器上查看Cookie

谷歌：

![1601131200041](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601131200041.png)

火狐：





###### Cookie的生存期

设置方法：setMaxAge() 

1. 正数：在指定的秒数后过期
2. 负数：浏览器关闭就消失
3. 零：马上删除Cookie

默认值：会话级Cookie（Session级）



1. 在关闭浏览器之后消失：

   ~~~java
       protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
   
           Cookie cookie = new Cookie("default", "default");
   
           resp.addCookie(cookie);
           // 设置存活时间
           cookie.setMaxAge(-1);
       }
   ~~~

2. 指定秒数后过期（以格林尼治时间为起始点）10s

   ~~~java
       protected void life10(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
           Cookie cookie = new Cookie("key3Life10", "value3");
           // 单位:s
           cookie.setMaxAge(10);
           resp.addCookie(cookie);
           resp.getWriter().write("设置10s生存期Cookie");
   
   
       }
   ~~~

   

生存期在Chrome里对应的参数

![1601132016742](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601132016742.png)





###### 删除Cookie

1. 先找到要删除的Cookie
2. 非空判断
3. 调用删除，然后addCookie到客户端

~~~java
    /**
     *  删除Cookie
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void deleteNow(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{

        // 先找到要删除的Cookie
        Cookie cookie = CookieUtils.findCookie("key2", req.getCookies());
        // 非空判断
        if (cookie != null){
            // 调用setMaxAge(0); 马上删除
            cookie.setMaxAge(0);
            // 调用resp.addCookie(cookie);
            resp.addCookie(cookie);

            resp.getWriter().write("删除成功！");
        }
    }
~~~



浏览器的header信息：

![1601132683187](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601132683187.png)





###### Cookie有效路径Path设置

作用：Cookie的Path属性可以有效地过滤哪些Cookie可以发送给服务器，哪些不发。

方法：Path属性通过请求的地址来进行有效地过滤

举例：

![1601195677168](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601195677168.png)

**注：**满足上一级路径的Cookie同样也能通过过滤



示例代码：

~~~java
    protected void testPath(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException{
        Cookie cookie = new Cookie("path1","path1");
        // 得到：工程路径/abc
        // 如果不设置Path则默认为工程路径 req.getContextPath()
        cookie.setPath(req.getContextPath() + "/abc");
        resp.addCookie(cookie);

        resp.getWriter().write("创建了一个带有Path的Cookie");

    }
~~~



直接set看不到的原因：浏览器自动过滤掉Path不是当前Path的Cookie，要进入到设置的Cookie对应的url下才能看见刚刚设置的Cookie

![1601196265572](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601196265572.png)

![1601196299565](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601196299565.png)





在html页面调用的交互：

~~~html
<body>
	<iframe name="target" width="500" height="500" style="float: left;"></iframe>
	<div style="float: left;">
		<ul>
			<li><a href="cookieServlet?action=createCookie" target="target">Cookie的创建</a></li>
			<li><a href="cookieServlet?action=getCookie" target="target">Cookie的获取</a></li>
			<li><a href="cookieServlet?action=updateCookie" target="target">Cookie值的修改</a></li>
			<li>Cookie的存活周期</li>
			<li>
				<ul>
					<li><a href="cookieServlet?action=defaultLife" target="target">Cookie的默认存活时间（会话）</a></li>
					<li><a href="cookieServlet?action=deleteNow" target="target">Cookie立即删除</a></li>
					<li><a href="cookieServlet?action=life10" target="target">Cookie存活10秒</a></li>
				</ul>
			</li>
			<li><a href="cookieServlet?action=testPath" target="target">Cookie的路径设置</a></li>
			<li><a href="" target="target">Cookie的用户免登录练习</a></li>
		</ul>
	</div>
</body>
~~~



###### Cookie免用户名登录

免密登录也类似，多一个参数。

1. 从页面获取用户名和密码
2. 调用查询检查用户名是否存在，密码是否正确
3. 如正确则创建用户名Cookie，设定有限生存期，通知客户端保存
4. 在页面端的用户名输入端Cookie利用EL表达式获取Cookie中的key.value，如有值Cookie存在，显示，如无则不显示。



实现框图：

![1601197207173](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601197207173.png)



**Servlet**

~~~java
public class LoginServlet extends BaseServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");
        String password = req.getParameter("password");

        // 写死固定
        if ("csl777".equals(username) && "123456".equals(password)){
            // 登录成功
            Cookie cookie = new Cookie("username", username);
            // 一周的Cookie
            cookie.setMaxAge(60 * 60 * 24 *7);
            // 登录成功后将Cookie发送给客户端保存
            resp.addCookie(cookie);
            System.out.println("登录成功");
        }else {
            System.out.println("登录失败");
        }
    }
}
~~~



**web**

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="http://localhost:8080/12_cookie_session/loginServlet" method="get">
        用户名：<input type="text" name="username" value="${cookie.username.value}"/><br/>
        密码：<input type="password" name="password"/><br/>
        <input type="submit" value="登录"/>
    </form>
</body>
</html>
~~~



结果：

![1601198216439](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601198216439.png)







---



##### Session

概述：

![1601213584878](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601213584878.png)



###### Session的创建



~~~java
    /**
     *  创建Session
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void createOrGetSession(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 创建和获取Session会话对象
        HttpSession session = req.getSession();
        // 判断当前Session会话是否是新创建出来的
        boolean isNew = session.isNew();
        // 获取Session会话的唯一标识
        String id = session.getId();

        resp.getWriter().write("得到的SessionID=" + id + "<br/>");
        resp.getWriter().write("是否为新创建的：" + isNew + "<br/>");
    }
~~~



###### Session域数据的存取

~~~java
   /**
     *  往Session中保存数据
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void setAttribute(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("从Session保存了数据");
        req.getSession().setAttribute("key1","value1");
    }

    /**
     *  从Session中获取数据
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void getAttribute(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object attribute = req.getSession().getAttribute("key1");
        resp.getWriter().write("从Session中取出的key数据" + attribute);
    }
~~~



###### Session生命周期控制



**超时**

默认的超时时长：1800s=30min=0.5h

- 在tomcat中的位置

C:\Users\maxcs\.IntelliJIdea2019.3\system\tomcat\Tomcat_8_5_56_JavaWeb_8\conf\web.xml中

~~~xml
  <session-config>
    <session-timeout>30</session-timeout>
  </session-config>
~~~

修改为其他值：

1. 集体修改：在当前工程的web.xml中按以上方式配置即可修改当前工程所有Session的超时时长。
2. 单独修改：req.getSession().setMaxInactiveInterval(20);



**超时的实质：**

![1601217072184](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601217072184.png)



设定超时参数

1. 正数
2. 负数：永不超时
3. 零：马上删除Session
4. 令当前Session马上超时：public void invalidate()



示例代码：

~~~java
    protected void deleteNow(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取Session对象
        HttpSession session = req.getSession();

        // 设置马上超时
        session.invalidate();

        resp.getWriter().write("Session已设置为超时");
    }


    protected void life3s(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取session对象
        HttpSession session = req.getSession();
        // 设置当前session3s后超时
        session.setMaxInactiveInterval(3);

        resp.getWriter().write("当前Session已设置3s超时");
    }


    protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取Session默认超时时长

        int maxInactiveInterval = req.getSession().getMaxInactiveInterval();
        resp.getWriter().write("默认超时时长为：" + maxInactiveInterval + "s");
    }
~~~





###### Session-Cookie的关系

服务器每次创建Session会话的时候，都会创建一个Cookie对象，这个Cookie对象的key永远都是：**JSESSIONID**，值是新创建出来的Session的ID值

![1601218186457](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601218186457.png)



**Session是基于Cookie实现的。**

![1601218858326](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601218858326.png)



调用界面：

~~~html
<head>
<meta http-equiv="pragma" content="no-cache" />
<meta http-equiv="cache-control" content="no-cache" />
<meta http-equiv="Expires" content="0" />
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Session</title>
	<base href="http://localhost:8080/12_cookie_session/">
<style type="text/css">

	ul li {
		list-style: none;
	}
	
</style>

</head>
<body>
	<iframe name="target" width="500" height="500" style="float: left;"></iframe>
	<div style="float: left;">
		<ul>
			<li><a href="sessionServlet?action=createOrGetSession" target="target">Session的创建和获取（id号、是否为新创建）</a></li>
			<li><a href="sessionServlet?action=setAttribute" target="target">Session域数据的存储</a></li>
			<li><a href="sessionServlet?action=getAttribute" target="target">Session域数据的获取</a></li>
			<li>Session的存活</li>
			<li>
				<ul>
					<li><a href="sessionServlet?action=defaultLife" target="target">Session的默认超时及配置</a></li>
					<li><a href="sessionServlet?action=life3s" target="target">Session3秒超时销毁</a></li>
					<li><a href="sessionServlet?action=deleteNow" target="target">Session马上销毁</a></li>
				</ul>
			</li>
			<li><a href="" target="target">浏览器和Session绑定的原理</a></li>
		</ul>
	</div>
</body>
~~~

