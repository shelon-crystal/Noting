### HTML

[toc]



##### HTML书写规范

```html
<!DOCTYPE html><!--约束，声明-->
<head><!--头标区包括：title标签，css样式，js代码-->
    <meta charset="UTF-8">
    <title>标题</title>
</head>
<body>
   页面主体内容
</body>
```



##### 页面标签

属性：

~~~html
基本属性：<body bgcolor="green">
事件属性：<body onclick="alert('nice buddy！')">
~~~

双标签：

~~~html
<button>按钮</button>
~~~



##### Font

参数（属性）：`color，face，size`

```html
<font color="red" face="宋体" size="5">
```

##### 特殊字符

字符实体：例如：`<，>，&`等，有对应的编码

```html
<!--写 "<>" -->    我是&lt;br&gt;标签
<!--写空格-->    你好&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;世界
```

##### 标题标签

属性`align`指定对齐方式,默认左对齐

```html
<!--分六级标题-->
<h1 align="left">标题1</h1>
<h2>标题2</h2>
<h3>标题3</h3>
<h4>标题4</h4>
<h5>标题5</h5>
<h6>标题6</h6>
```



##### 超链接

src/href路径
        相对路径
        `.`表示当前文件所在目录
        `..`表示当前文件所在上一级目录

`target`指定跳转方式，默认当前页面跳转_self。

~~~html
    <!--target指定跳转页面方式-->
    <!--在本页面跳转-->
    <a href="http://baidu.com/">百度</a><br>
    <a href="http://baidu.com/" target="_self">百度_self</a><br>
    <!--创建新页面跳转-->
    <a href="http://baidu.com/" target="_blank">百度_blank</a>
~~~



##### 列表标签

分无序列表和有序列表

列表域`<ul></ul>`

列表项`<li></li>`

~~~html
    <!--无序列表-->
    <!--unordered list-->
    <ul>
        <li>a</li><!--list item-->
        <li>b</li>
        <li>c</li>
        <li>d</li>
    </ul>

    <!--有序列表-->
    <!--ordered list-->
    <ol>
        <li>a</li><!--list item-->
        <li>b</li>
        <li>c</li>
        <li>d</li>
    </ol>
~~~



##### 图片标签

`src` 文件路径

`width` 宽度

`height` 高度

`border` 边缘像素宽度

`alt` 文件不存在，用以代替图片显示的text内容

~~~html
    <!--图片标签img
        src 文件路径
        width 宽度
        height 高度
        border 边框像素宽度
        alt 表示文件找不到文件，用来代替显示的文本内容
    -->
    <!--src路径
        相对路径
        .表示当前文件所在目录
        ..表示当前文件所在上一级目录

        绝对路径
        http://ip:port/工程名/资源路径
    -->
    <img src="../images/1.jpg" width="300" height="420" border="1" alt="not found">
~~~



##### table标签

单元表格：基本表格、跨行跨列表格

表格基本属性

~~~html
    <!--
    border 边框
    width 宽度
    height 高度
    align 表格对齐方式
    cellspacing 单元格间距
    -->
~~~



单元格属性

```html
    <!--
    <tr>为行标签 table row
    <td>为单元格标签
    <th>表头标签 table head
    <b>为加粗标签
    align 单元格对齐方式
    -->
```


~~~html
    <table align="center" border="10" width="300" height="300" cellspacing="8">
        <tr>
            <td align="center"><b>1.1 </b></td>
            <!--相当于上方语句 table head-->
            <th>1.2 </th>
            <td>1.3 </td>
        </tr>

        <!--
        跨行跨列表格
        colspan 跨列
        rowspan 跨行
        -->
        <tr>
            <!--跨列2-->
            <td colspan="2">2.1 </td>
            <td>2.2 </td>
            <td>2.3 </td>
        </tr>
        <tr>
            <td rowspan="2">3.1 </td>
            <td>3.2 </td>
            <!--2行2列-->
            <td colspan="2" rowspan="2">3.3 </td>
        </tr>
    </table>
~~~

输出形式：

![1595215742612](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1595215742612.png)





##### iframe标签

作用：在页面开一个小窗口放页面

iframe 和 a 标签组合使用：

1. 在iframe标签中使用name属性  
2. 在 a 标签的target属性上设置iframe的name属性，让a标签的内容显示到iframe里面

~~~html
    新页面<br/><br/>
    <iframe src="5.超链接.html" name="abc"></iframe>
    <br/>
    <ul>
        <li><a href="标题标签.html" target="abc">标题标签.html</a> </li>
        <li><a href="font标签.html" target="_blank">font标签.html</a> </li>
    </ul>
~~~

显示结果：

![1595335598764](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1595335598764.png)



##### *表单

~~~html
    input type=text 文本输入框 value设置默认输入内容
    input type=password 密码输入框 value设置默认输入内容
    input type=radio 单选框 name指定项目分组 checked指定默认值
    input type=checkbox 复选框
    input type=reset 重置按钮   value指定修改按钮上的文本
    input type=submit 提交按钮  value指定修改按钮上的文本
    input type=button 按钮  value指定修改按钮上的文本
    input type=file 文件上传域
    input type=hidden 隐藏域   不需要用户看见的信息，一起提交

    select 下拉列表框
    option 为列表项，selected指定默认选中

    textarea 多行文本框
        rows 指定可显示的行数
        cols 指定行有多长
        起始标签和结束标签之中是默认值
~~~



~~~html
    <form>
        <h1 align="center">用户注册</h1>
        <table border="1" align="center">
            <tr>
                <td>用户名称:</td>
                <td>
                    <input type="text" value="默认值">
                </td>
            </tr>

            <tr>
                <td>用户密码:</td>
                <td><input type="password" value="abc"></td>
            </tr>

            <tr>
                <td>确认密码:</td>
                <td><input type="password" value="abc"></td>
            </tr>

            <tr>
                <td>性别:</td>
                <td><input type="radio" name="sex" checked="checked"/>男<input type="radio" name="sex"/><br/>女</td>
            </tr>

            <tr>
                <td>兴趣爱好:</td>
                <td>
                    <input type="checkbox" checked="checked"/>Java
                    <input type="checkbox"/>JavaScript
                </td>
            </tr>

            <tr>
                <td>国籍:</td>
                <td>
                    <select>
                    <option>---请选择国籍---</option>
                    <option selected="中国">中国</option>
                    <option>美国</option>
                    </select>
                </td>
            </tr>

            <tr>
                <td>自我评价:</td>
                <td>
                    <textarea rows="10" cols="50">
                        默认值
                    </textarea>
                </td>
            </tr>

            <tr>
                <td><input type="reset" value="复位"/></td>
                <td align="right"><input type="submit"/></td>
            </tr>


        </table>

<!--        <input type="button" value="按钮x"><br/>
        <input type="file" /><br/>
        <input type="hidden" name="code" value="147258369"/><br/>-->
    </form>
~~~



显示结果：

![1595335747346](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1595335747346.png)



##### 表单的提交

~~~html
        action 属性指定提交的服务器地址
        method 指定提交方式   默认get，

        数据没有提交给服务器的情况：
            1.属性没有带name属性
            2.单选、复选、下拉列表中的option标签都要添加value属性
            3.表单项不在form标签中
        get
		  请求地址格式:action[+?+请求参数]
           请求参数格式:name=value & sex=boy
           数据长度受限(超过100字符的部分去除)
           不安全
        post
           地址栏只有action服务器地址

~~~



~~~html
<form action="http://localhost:8080" method="post">
    <input type="hidden" name="code" value="147258369"/>
    <h1 align="center">用户注册</h1>
    <table border="1" align="center">
        <tr>
            <td>用户名称:</td>
            <td>
                <input type="text" name="username" value="默认值">
            </td>
        </tr>

        <tr>
            <td>用户密码:</td>
            <td><input type="password" name="password" value="abc"></td>
        </tr>

        <tr>
            <td>确认密码:</td>
            <td><input type="password" name="ensurepwd" value="abc"></td>
        </tr>

        <tr>
            <td>性别:</td>
            <td>
                <input type="radio" name="sex" checked="checked" value="boy"/>男
                <input type="radio" name="sex" value="girl"/>女
            </td>
        </tr>

        <tr>
            <td>兴趣爱好:</td>
            <td>
                <input type="checkbox" name="hobby" checked="checked" value="Java"/>Java
                <input type="checkbox" name="hobby" value="JavaScript"/>JavaScript
            </td>
        </tr>

        <tr>
            <td>国籍:</td>
            <td>
                <select name="nation">
                    <option>---请选择国籍---</option>
                    <option selected="中国" value="CN">中国</option>
                    <option value="USA">美国</option>
                </select>
            </td>
        </tr>

        <tr>
            <td>自我评价:</td>
            <td>
                    <textarea rows="10" cols="50" name="desc">
                        默认值
                    </textarea>
            </td>
        </tr>

        <tr>
            <td><input type="reset" value="复位"/></td>
            <td align="right"><input type="submit" value="上交"/></td>
        </tr>


    </table>
</form>
~~~



