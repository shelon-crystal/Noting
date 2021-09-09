#### XML概述

![1596207033863](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596207033863.png)

#### XML书写

头区域：

~~~html
<?xml version="1.0" encoding="utf-8" ?>
<!--
    xml文件声明
    version="1.0"
    encoding="utf-8"    文件本身编码
-->
~~~

XML内容：

```html
CDATA区：使一段文本不用xml引擎解析直接输出
<![CDATA[
        ]]>
```
~~~html
        <author>
            <![CDATA[
                <<<<<>>>>>
            ]]>
            Tom
~~~

#### 基于dom4j的XML 解析流程

1. 在`src`目录下建立要解析的xml文件（books.xml)

   ![1596204890250](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596204890250.png)

   要解析的内容如下：

   ~~~html
   <?xml version="1.0" encoding="utf-8" ?>
   
   <books>
       <book sn="SN1475-25"><!--表示多个图书信息-->
           <name>时间简史</name><!--name表示书名-->
           <author>史蒂芬霍金</author><!--author标志作者-->
           <price>46</price>
       </book>
       <book sn="SN1475-33"><!--表示多个图书信息-->
           <name>jQuery基础</name><!--name表示书名-->
           <author>Tom</author><!--author标志作者-->
           <price>33</price>
       </book>
   </books>
   ~~~

2. 在`src`目录下建立一个包org.studyCSL.pro用于存储封装标签的类Book.java和测试类dom4jTest.java

3. 在Book类中生成封装标签的对象属性值、以及set，get，toString方法

   ~~~java
   package org.studyCSL.pro;
   
   import java.math.BigDecimal;
   
   public class Book {
       private String sn;
       private String name;
       private String author;
       private Double price;
   
   //    生成get、set方法快捷键:alt + insert
   
       public Book() {
       }
   
       public Book(String sn, String name, String author, Double price) {
           this.sn = sn;
           this.name = name;
           this.author = author;
           this.price = price;
       }
   
       public String getSn() {
           return sn;
       }
   
       public void setSn(String sn) {
           this.sn = sn;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public String getAuthor() {
           return author;
       }
   
       public void setAuthor(String author) {
           this.author = author;
       }
   
       public Double getPrice() {
           return price;
       }
   
       public void setPrice(Double price) {
           this.price = price;
       }
   
       @Override
       public String toString() {
           return "Book{" +
                   "sn='" + sn + '\'' +
                   ", name='" + name + '\'' +
                   ", author='" + author + '\'' +
                   ", price=" + price +
                   '}';
       }
   }
   
   ~~~

   

4. 在xml中建立一个`lib`目录

   1. 将`dom4j`文件夹中`dom4j`依赖jar包（dom4j-1.6.1.jar）复制进lib目录

   2. 将jar包添加到类路径，并且调整jar包的level至`Module`级

      ![img](file://C:/Users/maxcs/AppData/Roaming/Typora/typora-user-images/1596205461284.png?lastModify=1596205659)

      

      ![1596205660477](file://C:/Users/maxcs/AppData/Roaming/Typora/typora-user-images/1596205660477.png?lastModify=1596205659)

      

5. 书写测试类dom4jTest.java （与写java测试类的方式一样测试方法名以`test`开头）

   1. 导入`Junit4`测试依赖的jar包至`lib`目录：hamcrest-core-1.3.jar、junit-4.12.jar，与dom4j同样的库处理方式处理这两个jar包

      ![1596206059306](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1596206059306.png)

6. 创建SAXReader输入流读取xml配置文件，生成document对象

   ~~~java
       @Test
       public void test1() throws DocumentException {
           //创建一个saxreader输入流读取xml配置文件，生成document对象
           SAXReader saxReader = new SAXReader();
           try {
               Document document = saxReader.read("src/books.xml");
               System.out.println(document);
           }catch (Exception e){
               e.printStackTrace();
           }
       }
   ~~~

7. 读取books.xml，生成Book类

   编程解析的四个步骤：

   1. 读取books.xml文件

   2. 通过document对象获取根元素 document.getRootElement();

   3. 通过根元素获取book标签对象rootElement.elements("book");

   4. 遍历，处理每一个book标签转换为Book类

      - elements() 和 element() 都是通过标签名查找子元素（elements获取的是标签List、element则是单个标签）
      - asXML()                         把标签对象转化为标签字符串
      - getText()                        方法可以获取标签中的文本内容
      - elementText()                可以直接获取指定标签名中的内容
      - attributeValue("sn")       获取标签属性值
      - 生成Book对象：new Book(snValue,nameText,authorText,Double.parseDouble(priceText))

      

   ~~~java
       @Test
       public void test2() throws DocumentException {
           // 1.读取books.xml文件
           SAXReader reader = new SAXReader();
           // 在Junit测试中，相对路径从模块名开始算
           Document document = reader.read("src/books.xml");
   
   
           // 2.通过document对象获取根元素
           Element rootElement = document.getRootElement();
   
           // 3.通过根元素获取book标签对象
               //elements() 和 element() 都是通过标签名查找子元素
           List<Element> books = rootElement.elements("book");
           
           // 4.遍历，处理每一个book标签转换为Book类
           for (Element book : books){
               // asXML() 把标签对象转化为标签字符串
               // System.out.println( book.asXML() );
               Element nameElement = book.element("name");
   
               // getText()方法可以获取标签中的文本内容
               String nameText = nameElement.getText();
               // elementText() 可以直接获取指定标签名中的内容
               String priceText = book.elementText("price");
               String authorText = book.elementText("author");
   
               // 获取属性值
               String snValue = book.attributeValue("sn");
   
               System.out.println(new Book(snValue,nameText,authorText,Double.parseDouble(priceText)));
           }
   
       }
   ~~~

   









