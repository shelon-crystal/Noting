### CSS



[toc]



##### css基本语法

与html结合使用的三种方式：

1. ```html
   css与html结合方式1: 通过style属性
   <div style="border: 1px solid #ff4786;">div标签1</div>
   ```

2. ```html
   css与html结合方式2: 在head标签中使用style标签定义自己需要的css样式
   <head>
       <!--style标签专门用来定义css样式代码-->
       <style type="text/css">
           这是css注释方式
           div{
               border: 1px solid #ff4786;
           }
           span{
               border: 1px solid #ff4786;
           }
       </style>
   </head>
   ```

3. ```html
   css与html结合方式3:把css样式写成一个单独的css文件，再通过link标签引入
   <head>
   	<link rel="stylesheet" type="text/css" href="cssStyle.css">
   </head>
   ```

---



##### id选择器

~~~html
    <!--id选择器: 通过id属性选择样式使用-->
    <!--不能复用，对一个标签一个id-->
<head>
    <meta charset="UTF-8">
    <title>id选择器</title>
    <style>
        #id1001{
            color: blue;
            font-size: 20px;
            border: 1px solid yellow;
        }
        #id1002{
            color: red;
            font-size: 20px;
            border: 5px dotted blue;
        }
    </style>
</head>

    <div id="id1001">div标签1</div>
    <div id="id1002">div标签2</div>
~~~

---



##### class选择器

~~~html
    <!--class选择器：通过class属性选择样式使用-->
    <!--在style标签中定义的，可以在多个标签中复用-->
<head>
    <meta charset="UTF-8">
    <title>class选择器</title>
    <style type="text/css">
        .class01{
            color: blue;
            font-size: 30px;
            border: 1px solid yellow;
        }

        .class02{
            color: grey;
            font-size: 26px;
            border: 1px solid red;
        }
    </style>
</head>
    <div class="class01">div标签1_class01</div>
    <div class="class02">div标签1_class02</div>
    <span class="class01">span标签1</span>
    <span class="class02">span标签2</span>
~~~

---



##### 组合选择器

将id和class选择器结合起来

基本语法：

~~~html
组合选择器：让多个选择器公用一个代码
        选择器1，选择器2，选择器n{

        }
<head>
    <meta charset="UTF-8">
    <title>组合选择器</title>
    <style type="text/css">
        .class01,#id001{
            color: blue;
            font-size: 30px;
            border: 5px solid yellow;
            width: 50%;
            height: 50px;
            background: grey;
            /*div居中*/
            /*左边距，右边距*/
            margin-left: auto;
            margin-right: auto;
            /*文字居中*/
            text-align: center;

            /*超链接去下划线*/

        }
        a{
            text-decoration: none;
        }

        /*table样式*/
        table{
            /*设置边框*/
            border: 1px red solid;
            /*边框合并*/
            border-collapse: collapse;
        }

        td,th{
            border: 1px red solid;
        }
        
        /*列表去修饰*/
        ul{
            list-style: none;
        }
    </style>
</head>    

    <!--实例-->
    <!--修改class="class01"的div标签 和 id="id01"所有span标签-->
    <div class="class01" >div标签1_class02</div>
    <span id="id001" >span标签1</span><br/>

    <a href="http://baidu.com/" >百度</a><br/>


    <table>
        <tr>
            <td>1.1</td>
            <td>1.2</td>
            <td>1.3</td>
        </tr>
    </table>
    <br/>
    <ul>
        <li>111</li>
        <li>222</li>
        <li>333</li>
    </ul>
~~~





