



### JSON



#### 1.1 JSON概念



一种轻量级的数据交换格式

数据交换指客户端和服务器之间的业务数据的传递格式



#### 1.2 JSON的定义



##### 1.2.1 JSON结构

JSON由键值对组成，并且由花括号包围，键和值用冒号分隔，多组键值用逗号分隔。

JSON的定义：内含的数据类型可以有：整型，字符串型，布尔型，数组型，嵌套的JSON对象，JSON对象数组，对象数组等。

##### 1.2.2 JSON底层的数据类型

对象类型

~~~javascript
			// json的定义
			var jsonObj = {
				"key1":12,
				"key2":"abc",
				"key3":false,
				"key4":[11,"arr",false],
				"key5":{
					"key5_1":551,
					"key5_2":"hello"
				},
				"key6":[{
					"key6_1_1":"611",
					"key6_1_2":612
				},{
					"key6_2_1":"621",
					"key6_2_2":622
				}]

			};
~~~



#### 1.3 JSON的访问



##### 1.3.1 访问方式

`json对象.key`

~~~javascript
			// json的访问
			alert(jsonObj.key1);
			alert(jsonObj.key4);
			alert(jsonObj.key6);
~~~



#### 1.4 JSON对象与JSON字符串的相互转化



##### 1.4.1 常用方法

`JSON.stringify()`：将json对象转为json字符串

`JSON.parse()`：将json字符串专为json对象

注：

1. 一般要操作json中数据时，json对象格式

2. 客户端和服务器进行数据交换，json字符串



JSON转JSON字符串代码示例：

~~~javascript
var jsonObjString = JSON.stringify(jsonObj);
alert(jsonObjString);
~~~

![1602839327125](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602839327125.png)

 





#### 1.5 JSON在java中的使用



**核心方法**：`gson.toJson()`、`gson.fromJson()`

##### 1.5.1 JSON与JavaBean的互转

1. 导入第三方jar包`gson-2.2.4.jar`

~~~java
    public void test1(){
        Person person = new Person(1, "lihua");
        Gson gson = new Gson();
        // toJson方法可以将java对象转化为Json字符串
        String personJsonString = gson.toJson(person);
        System.out.println(personJsonString);
        // formJson将Json对象转化为java字符串
        gson.fromJson(personJsonString,Person.class);
    }
~~~



##### 1.5.2 List与JSON的互转



转换成Json字符串的时候方式与上文一样。

从字符串转换回来的时：

fromJson的第二个参数需要做出改变

1. 写一个类继承第三方类库中的反射方法TypeToken，继承的泛型填写要转化成的LIst的类型

   ~~~java
   public class PersonListType extends TypeToken<List<Person>> {
   }
   ~~~

2. 第二个参数传入该类的对象调用父类方法`.getType()`即可获取注入类型

   ~~~java
   //##### 1.5.2 List与JSON的互转
       @Test
       public void test2(){
           List<Person> personList = new ArrayList<>();
   
           personList.add(new Person(1,"handsome boy"));
           personList.add(new Person(2,"handsome girl"));
           personList.add(new Person(1,"handsome people"));
   
           Gson gson = new Gson();
   
           String personListJsonString = gson.toJson(personList);
           // 转化为Json数组
           // [{"id":1,"name":"handsome boy"},{"id":2,"name":"handsome girl"},{"id":1,"name":"handsome people"}]
           System.out.println(personListJsonString);
   
           // 当转换回来是个数组类型的时候
           List<Person> list = gson.fromJson(personListJsonString, new PersonListType().getType());
   
           System.out.println(list);
       }
   ~~~



##### 1.5.3 Map和JSON的互转

方式与上方一样，但是可以使用匿名内部类的方式获取Type而不用每次创建一个类去继承TypeToken

**获取注入类型：`TypeToken<Map<Integer,Person>>(){}.getType()`**

~~~java
//##### 1.5.3 Map和JSON的互转
    @Test
    public void test3(){
        Map<Integer, Person> personMap = new HashMap<>();

        personMap.put(1,new Person(1,"Json"));
        personMap.put(2,new Person(2,"Peter"));
        personMap.put(3,new Person(3,"Tom"));

        // 转Json字符串
        Gson gson = new Gson();
        String personMapJsonString = gson.toJson(personMap);
        System.out.println(personMapJsonString);

        // Json字符串转Map，使用匿名内部类的方式获取Type
        Map<Integer,Person> personMap2 = gson.fromJson(personMapJsonString,new TypeToken<Map<Integer,Person>>(){}.getType());
        System.out.println(personMap2);
        System.out.println(personMap.get(1));

    }
}
~~~







---



### AJAX

(Asychronous Javasrcipt And XML 异步Javascript和XML)



#### 2.1 概述

是一种浏览器通过js异步发起请求，局部更新页面的技术，地址的URL不会发生改变，页面除发起Ajax请求对应的部分，其他部分都不会发生改变。

同步：代码的执行之间存在时序关系，从上往下顺序执行

异步：类似于先来先服务，浏览器不会等待服务器响应Ajax的数据再执行其他部分代码，执行块之间是存在固定的时序关系。谁先响应就谁先获取到交互数据。







#### 2.2 原生AJAX

![1602990033101](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602990033101.png)





#### 2. 3 *JQuery版AJAX



##### 2.3.1 `$.ajax` 

参数：

- url ：表示请求的地址
- type：表示请求的类型GET或者POST
- data：表示发送给服务器的数据（请求参数，一般传入Servlet）
  - name=value&name=value
  - {key,value}
- success：请求成功，响应的回调函数
- dataType：响应的数据类型（服务器返回的数据类型），指定从Servlet返回过来的数据类型是什么，这样可以省去用data传过来的json字符串转换成json对象的步骤。
  - text：纯文本
  - xml：表示xml数据
  - json：表示json对象



代码示例

~~~javascript
				// ajax请求
				$("#ajaxBtn").click(function(){
					
					$.ajax({
						url:"http://localhost:8080/15_json_ajax_i18n/ajaxServlet",
						// data:"action=jQueryAjax",
						data:{action:"jQueryAjax"},
						type:"GET",
						/*参数是服务器返回的数据，可以直接调用*/
						success:function (data) {
							/*先将回传的数据转换成json对象*/
							// var personDataObj = JSON.parse(data);
							$("#msg").html("Ajax->编号：" + data.id + "," + "姓名：" + data.name);
						},
						dataType:"json"
					});

				});
~~~



##### 2.3.2 `$.get`与`$.post`

参数：

- url ：表示请求的地址
- data：表示发送给服务器的数据（请求参数，一般传入Servlet）
- callback：请求成功，响应的回调函数
- type：Servelt返回的数据类型



代码示例：

~~~javascript
				// ajax--get请求
				$("#getBtn").click(function(){

					$.get("http://localhost:8080/15_json_ajax_i18n/ajaxServlet",
						"action=jQueryGet",
						function (data) {
							$("#msg").html("Get->编号：" + data.id + "," + "姓名：" + data.name);
						},
						"json");

			});
				
				// ajax--post请求
				$("#postBtn").click(function(){
					// post请求
					$.post("http://localhost:8080/15_json_ajax_i18n/ajaxServlet",
							"action=jQueryPost",
							function (data) {
								$("#msg").html("Post->编号：" + data.id + "," + "姓名：" + data.name);
							},
							"json");

				});
~~~



##### 2.3.3 $.getJSON

- url ：表示请求的地址
- data：表示发送给服务器的数据（请求参数，一般传入Servlet）
- callback：请求成功，响应的回调函数



##### 2.3.4 表单序列化



**核心方法：`.serialize()`**

该方法可以将表单中的所有表单项的内容都获取到，并以name=value&name=value的形式进行拼接，自动生成表单项转化为拼接参数，然后加入到data参数中

**注：在传入时要补加拼接符`&`**

"action=jQuerySerialize**&**" + $("#form01").serialize()

代码示例：

~~~java
				// ajax请求
				$("#submit").click(function(){
					// 把参数序列化
					// alert($("#form01").serialize());
$.getJSON("http://localhost:8080/15_json_ajax_i18n/ajaxServlet",
          "action=jQuerySerialize&" + $("#form01").serialize(),
          function (data) {
						$("#msg").html("Serialize->编号：" + data.id + "," + "姓名：" + data.name);
					});
				});
~~~







-----



### i18n



![1603096102189](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603096102189.png)



#### 3.2 通过请求头实现国际化



##### 3.2.1 语言设置

在浏览器语言设置调整优先语言，排在前面的权重大，Locale对象优先获取权重q值最大的语言

![1603113094911](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603113094911.png)



##### 3.2.2 设置资源包

`i18n_en_US.properties`

~~~java
username=username
password=password
sex=sex
age=age
regist=regist
boy=boy
girl=girl
email=email
reset=reset
submit=submit
~~~



`i18n_zh_CN.properties`

~~~java
username=用户名
password=密码
sex=性别
age=年龄
regist=注册
boy=男
girl=女
email=邮箱
reset=重置
submit=提交
~~~



##### 3.2.3 在页面获取资源包并使用

`ResourceBundle i18n = ResourceBundle.getBundle("i18n", locale);`会获取i18n打开的`.properties`文件

~~~jsp
	<%
		/*从请求头获取Locale信息*/
		Locale locale = request.getLocale();
		System.out.println(locale);
		// 获取资源包
		ResourceBundle i18n = ResourceBundle.getBundle("i18n", locale);
	%>
~~~



在页面中使用`ResourceBundle.getString(key)`写入表达式脚本，key值为.properties文件中的键值对的key

~~~jsp
		<table>
		<form>
			<tr>
				<td><%=i18n.getString("username")%></td>
				<td><input name="username" type="text" /></td>
			</tr>
			<tr>
				<td><%=i18n.getString("password")%></td>
				<td><input type="password" /></td>
			</tr>
			<tr>
				<td><%=i18n.getString("sex")%></td>
				<td><input type="radio" /><%=i18n.getString("boy")%><input type="radio" /><%=i18n.getString("girl")%></td>
			</tr>
			<tr>
				<td><%=i18n.getString("email")%></td>
				<td><input type="text" /></td>
			</tr>
			<tr>
				<td colspan="2" align="center">
				<input type="reset" value="<%=i18n.getString("reset")%>" />&nbsp;&nbsp;
				<input type="submit" value="<%=i18n.getString("submit")%>" /></td>
			</tr>
			</form>
		</table>
~~~



#### 3.3 通过语言类型选择实现国际化



实现：给两个a标签带上参数country=value跳回原本页面，if判断locale = Locale.US设置语言类型

~~~jsp
	<a href="i18n.jsp?country=cn">中文</a>|
	<a href="i18n.jsp?country=usa">english</a>
~~~



~~~java
	<%
		/*从请求头获取Locale信息*/
		Locale locale = null;
		String country = request.getParameter("country");
		if ("cn".equals(country)){
			locale = Locale.CHINA;
		} else if ("usa".equals(country)){
			locale = Locale.US;
		} else {
			locale = request.getLocale();
		}

//		locale = request.getLocale();
//		System.out.println(locale);
		// 获取资源包
		ResourceBundle i18n = ResourceBundle.getBundle("i18n", locale);
	%>
~~~





#### 3.4 通过JSTL标签库ftm实现国际化



在a标签设置参数：

~~~jsp
	<a href="i18n_fmts.jsp?locale=zh_CN">中文</a>|
	<a href="i18n_fmts.jsp?locale=en_US">english</a>
~~~

步骤：

1. 使用标签设置Locale信息

   ~~~jsp
   	<%--1 使用标签设置Locale信息--%>
   	<fmt:setLocale value="${param.locale}" />
   ~~~

2. 使用标签设置baseName

   ~~~jsp
   	<%--2 使用标签设置baseName--%>
   	<fmt:setBundle basename="i18n"/>
   ~~~

3. 使用标签输出国际化信息

   ~~~jsp
   <fmt:message key="regist" />
   <fmt:message key="username" />
   ~~~

   **key的值为设置的i18n资源文件的键值**





