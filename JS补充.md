### JS补充

[toc]



##### js事件

绑定方式：

- 静态绑定：通过html标签事件属性之间赋予事件响应后的代码
- 动态绑定：通过js代码，得到标签的dom对象，通过dom对象名.事件名=function(){}

五大类：onload、onclick、onblur、onchange、onsubmit

~~~html
        onload      页面加载完成后。常用于页面初始化操作
        onclick     按钮点击响应
        onblur      输入框失去焦点后验证其输入内容是否合法
        onchange    下拉列表框、输入框发生改变后操作
        onsubmit    提交表单前，验证所有表单项是否合法
~~~

###### onload

静态在标签上指定事件类型，并且写上响应代码，或者在script里定义响应函数并调用

动态在script里获取页面对象dom，再通过对象名.事件类型的匿名函数来构造响应函数

~~~html
    <script type="text/javascript">
        //onload事件方法
        //静态注册
        function onLoadF() {
            alert('静态注册onload事件');
        }

        //动态注册
        window.onload = function () {
            alert('动态注册onload事件');
        }
    </script>
<!--静态绑定的onload-->
<body onload= "onLoadF();">

~~~



###### onclick

~~~html
    <script type="text/javascript">
        function onclickF() {
            alert('静态注册onclick事件');
        }

        window.onload = function () {
            //获取标签对象
            /*
            * Dom代表整个页面
            * document是js提供的一个对象
            * */
            var btn = document.getElementById("bt1001");
            btn.onclick = function () {
                alert('动态注册onclick事件');
            }
            //通过标签对象.事件名
        }
    </script>

    <button onclick="onclickF();">按钮1</button>
    <button id="bt1001">按钮2</button>
~~~



###### onblur

~~~html
    <script type="text/javascript">
        function onblurF() {
            console.log('静态失去焦点事件')
        }

        window.onload = function () {
            var pswObj = document.getElementById('password');
            pswObj.onblur = function () {
                console.log('动态注册焦点事件');
            }
        }
    </script>

    用户名：<input type="text" onblur="onblurF();"> <br/>
    密码：<input type="text" id="password"> <br/>
~~~



###### onchange

~~~html
    <script type="text/javascript">
        function selectF() {
            alert('change');
        }

        window.onload = function () {
            var selObj = document.getElementById('book');
            selObj.onchange = function () {
                alert('change');
            }
        }
    </script>

    请选择书目：
    <!--静态：<select onchange="selectF();">-->
    <select id="book">
        <option>--请选择--</option>
        <option>计算机网络</option>
        <option>计算机视觉</option>
        <option>网络安全</option>
        <option>算法大全</option>
        <option>Java</option>
    </select>
~~~



###### onsubmit



~~~html
    <script type="text/javascript">
        function onsubmitF() {

            alert('静态');
            return false;
        }

        window.onload = function () {
            var subObj = document.getElementById('form01');
            subObj.onsubmit = function () {
                alert('动态');
                return false;
            }
        }

        window.onload = function () {
            var subObj = document.getElementById('form02');
            subObj.onsubmit = function () {
                alert('动态');
                return false;
            }
        }

    </script>

    <!--静态注册通过onsubmit=return false来阻止有误的表单提交-->
    <form action="http://localhost:8080" method="get" onsubmit="return onsubmitF();">
        <input type="submit" value="静态注册"/>
    </form>

    <!--动态注册直接在动态script里面返回false就可以阻止表单提交-->
    <form action="http://localhost:8080" method="get" id="form01">
        <input type="submit" value="动态注册"/>
    </form>

    <form action="http://localhost:8080" method="get" id="form02">
        <input type="submit" value="11注册"/>
    </form>
~~~



##### Document对象

1. Document 对象代表整个 HTML 文档，可用来访问页面中的所有元素。

2. Document 对象是 Window 对象的一个部分，可通过 window.document 属性来访问。

3. window.document 是对document的只读访问。



##### Document对象的方法



###### getElementById()

getElementById() 方法可返回对拥有指定 ID 的第一个对象的引用。

也就是返回了一个标签对象，**可以通过标签对象.name/id/value 等方法对标签对象的属性进行访问。**

~~~html
    <script type="text/javascript">

        function onclickF() {
            var userObj = document.getElementById('user1001');

            /* 获取输入框中的内容 */
            var userName = userObj.value;

            /*正则表达式*/
            var patt = /^\w{5,12}$/;

            /* test()测试某个字符串是否是匹配某个规则 */
            if (patt.test(userName)){
                alert('用户名合法');
            }else {
                alert('用户名不合法');
            }
        }
    </script>

<body>
    用户名：<input type="text" id="user1001"/>
    <button onclick="onclickF()">校验</button>
</body>
~~~



###### getElementByName

~~~html
            // 1.document.getElementsByName()根据 指定name属性返回多个同名标签对象数组
            // 2.集合中元素的顺序是在页面中从上到下的顺序
~~~

示例代码：

~~~html
    <script type="text/javascript">
        // 全选
        function checkAll() {
            // document.getElementsByName()根据 指定name属性返回多个同名标签对象数组
            // 集合中元素的顺序是在页面中从上到下的顺序
            var hobbies = document.getElementsByName('hobby');

            // checked 表示复选框状态，选中则true，不选中则false;可读可写
            for(var index in hobbies){
                hobbies[index].checked = true;
            }

        }
        // 全不选
        function NotcheckAll() {
            var hobbies = document.getElementsByName('hobby');

            for(var index in hobbies){
                if(hobbies[index].checked === true){
                    hobbies[index].checked = false;
                }
            }
        }
        // 反选
        function resCheck() {
            var hobbies = document.getElementsByName('hobby');

            for(var index in hobbies){
                hobbies[index].checked = !hobbies[index].checked;
                // if(hobbies[index].checked) {
                //     hobbies[index].checked = false;
                // }else {
                //     hobbies[index].checked = true;
                // }
            }
        }
    </script>
</head>
<body>
    兴趣爱好:
    <input type="checkbox" name="hobby" value="cpp"> C++
    <input type="checkbox" name="hobby" value="Java"> Java
    <input type="checkbox" name="hobby" value="JavaScript"> JavaScript
    <br/>
    <button onclick="checkAll()">全选</button>
    <button onclick="NotcheckAll()">全不选</button>
    <button onclick="resCheck()">反选</button>

</body>
~~~



###### getElementByTagName

```html
按照指定标签名进行查询并返回集合
```

示例代码：

~~~html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        // 全选
        function checkAll() {
            // 按照指定标签名进行查询并返回集合
            var inputObjs = document.getElementsByTagName("input");
            for(var index in inputObjs){
                inputObjs[index].checked = true;
            }
        }

    </script>
</head>
<body>
    兴趣爱好:
    <input type="checkbox"  value="cpp"> C++
    <input type="checkbox"  value="Java"> Java
    <input type="checkbox"  value="JavaScript"> JavaScript
    <br/>
    <button onclick="checkAll()">全选</button>
</body>
~~~



###### createElement

1. 创建标签对象
2. 写入标签内容
3. 放入document.body

注：innerHTML中的文本内容也被js封装成一个对象

~~~html
    <script type="text/javascript">
            window.onload = function () {
                var divObj = document.createElement("div");
                divObj.innerHTML = "Hello world";
                // 上面语句等价于下方语句
                var textNode = document.createTextNode("Hello World");
                divObj.appendChild(textNode);
        }
    </script>
~~~



示例代码：

~~~html
![8OR%]POHR41N`K}G0%L%Q%L](D:\application\social\QQ\userData\820211665\FileRecv\MobileFile\Image\8OR%]POHR41N`K}G0%L%Q%L.png)    <script type="text/javascript">
            /*因为document.body.appendChild(divObj);是语句，应该放到页面加载完成之后执行才能显示*/
            window.onload = function () {
            // 创建标签
            var divObj = document.createElement("div");
            divObj.innerHTML = "Hello world";

            // 放进body里面
            // 添加子元素
            document.body.appendChild(divObj);
        }

    </script>
~~~



正则表达式：查阅文档

>D:\tools\CodingTools\Java\idea\pag\尚硅谷JavaWeb_2020idea新版\笔记、代码、资料\资料\02-javascript\笔记\w3cschool菜鸟教程 新版本.chm

浏览器脚本->Javascript->Javascript RegExp对象(regular expression)



##### 标签对象化属性

![1595519552396](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1595519552396.png)



##### 节点

节点的概念相当于**标签**

![1595519599705](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1595519599705.png)









