### jQuery



#### 基本概念

写法流程：

1. 在script标签引入js库
2. 检测是否引进
3. 书写jQuery代码

注：jQuery的运行代码要在页面加载完成之后才能执行，故应放**在$(function () {}内部书写**

示例代码：

~~~html
<body>
	<!--body里面都是一个个的script标签域-->

	<!--引进js库-->
	<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
	<!--检测是否引进-->
	<script type="text/javascript">
		/*检测是否有值*/
		// alert($);

		$(function () { //页面加载完成之后，相当于window.onload = function(){}

			// 相当于document.getElementById("btnId");
			var $btnObj = $("#btnId");
			// 绑定点击事件
			$btnObj.click(function () {
				alert("jQuery单击事件");
			})
		})
	</script>


	<button id="btnId">SayHello</button>

</body>
~~~



**`$`的本质：是一个jQuery函数**

---



#### jQuery核心函数

1. 传入参数为[函数]时：在文档加载完成后执行这个函数

2. 传入参数为[HTML字符串]时：根据这个字符串创建元素节点对象

3. 传入参数为[选择器字符串]时：根据这个字符串查找元素节点对象

   ```html
    $("#id");   id选择器，根据id查询标签对象
    $("标签名");   指定标签名查询标签对象
    $(".class属性值");  类型选择器，根据class属性查询标签对象
    $("*");  选择所有元素
    $("标签名,#id,.class属性值");  构成组合选择查询
   ```

4. 传入参数为[DOM对象]时：将DOM对象包装为jQuery对象返回

示例代码：

~~~html
<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
<script type="text/javascript">

	//核心函数的4个作用
	// 1.传入参数为[函数]时：在文档加载完成后执行这个函数
    $(function () {
        alert("页面加载完成后，自动调用");

        // 复制标签进去的时候应先创建一个空串，把容放进去。

        var $ss = $("    <div>\n" +
            "        <span>div-span1</span>\n" +
            "        <span>div-span2</span>\n" +
            "    </div>").appendTo("body");
    });

</script>
~~~



**jQuery对象的本质**：Dom对象的数组 + JQuery一系列功能函数

注：JQuery对象属性/方法和Dom对象属性方法不能互相使用



#### jQuery与DOM对象相互转换

1. dom转jQuery：$(dom对象);
2. jQuery转dom：jQuery对象[下标]

![1595690486198](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1595690486198.png)



---

#### jQuery选择器



##### 基本选择器

基本用法：利用jQuery核心函数对标签进行获取



##### 层次选择器

基本函数：

~~~html
ancestor descendant  ：在给定的祖先元素下匹配所有的后代元素		$("form input");
parent > child 		   ：取得parent元素直接的child节点	$("form > input");
prev + next 		   ：匹配所有紧接在 prev 元素后的 next 元素 $("label + input")
prev ~ siblings 	   ：匹配 prev 元素之后的所有 siblings 元素
~~~

**注：$(document).ready(function(){} 是 $(function () {});的全写版。**

示例代码：

~~~html
		<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
		<script type="text/javascript">
			$(document).ready(function(){
				// 上写法是$(function () {}); 的全写版本

				//1.选择 body 内的所有 div 元素 
				$("#btn1").click(function(){
					$("body div").css("background", "#bbffaa");
				});

				//2.在 body 内, 选择div子元素  
				$("#btn2").click(function(){
					$("body > div").css("background", "#bbffaa");
				});

				//3.选择 id 为 one 的下一个 div 元素 
				$("#btn3").click(function(){
					$("#one + div").css("background", "#bbffaa");
				});

				//4.选择 id 为 two 的元素后面的所有 div 兄弟元素
				$("#btn4").click(function(){
					$("#two ~ div").css("background", "#bbffaa");
				});
			});
		</script>
~~~



##### 基本过滤选择器

基本方法：

~~~html
	:first			获取匹配的第一个元素				$("li:first");
	:last			获取匹配的最后一个元素
	:not(selector)	获取所有与selector匹配的所有元素	$("input:not(:checked)");
	:even			获取索引值为偶数的元素，从0开始计数，对应表格的1、3、5行	$("tr:even");
	:odd 			获取索引值为奇数的元素，从0开始计数，对应表格的2、4、6行	$("tr:odd");
	:eq(index)		匹配一个给定索引值的元素			$("tr:eq(1)");		equal
	:gt(index) 		匹配所有大于给定索引值的元素		$("tr:gt(1)");		giant
	:lt(index) 		匹配所有小于给定索引值的元素		$("tr:lt(1)");		less than
	:header			匹配如h1，h2之类的标题元素		$(:header);
	:animated		匹配正在执行动画的元素			$(:animated);
~~~

示例代码：

~~~html
		<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
		<script type="text/javascript">
			$(document).ready(function(){
				function anmateIt(){
					$("#mover").slideToggle("slow", anmateIt);
				}
				anmateIt();
			});
			
			$(document).ready(function(){
				//1.选择第一个 div 元素  
				$("#btn1").click(function(){
					$("div:first").css("background", "#bbffaa");
				});

				//2.选择最后一个 div 元素
				$("#btn2").click(function(){
					$("div:last").css("background", "#bbffaa");
				});

				//3.选择class不为 one 的所有 div 元素
				$("#btn3").click(function(){
					$("div:not(.one)").css("background", "#bbffaa");
				});

				//4.选择索引值为偶数的 div 元素
				$("#btn4").click(function(){
					$("div:even").css("background", "#bbffaa");
				});

				//5.选择索引值为奇数的 div 元素
				$("#btn5").click(function(){
					$("div:odd").css("background", "#bbffaa");
				});

				//6.选择索引值为大于 3 的 div 元素
				$("#btn6").click(function(){
					$("div:gt(3)").css("background", "#bbffaa");
				});

				//7.选择索引值为等于 3 的 div 元素
				$("#btn7").click(function(){
					$("div:eq(3)").css("background", "#bbffaa");
				});

				//8.选择索引值为小于 3 的 div 元素
				$("#btn8").click(function(){
					$("div:lt(3)").css("background", "#bbffaa");
				});

				//9.选择所有的标题元素
				$("#btn9").click(function(){
					$(":header").css("background", "#bbffaa");
				});

				//10.选择当前正在执行动画的所有元素
				$("#btn10").click(function(){
					$(":animated").css("background", "#bbffaa");
				});
			});
		</script>
	</head>
	<body>	
~~~





##### 内容过滤选择器

基本方法：

~~~html
			:contains(text)		匹配包含给定文本的元素	$("div:contains('John')")
			:empty				匹配所有不包含子元素或者文本的空元素	$("td:empty")
			:has(selector)		匹配 含有 选择器所匹配的元素的元素	$("div:has(p)").addClass("test");
			 					*拿到的元素是div而不是p，因为div含有p，按照规则，返回div
			:parent 			匹配含有子元素或者文本的元素
~~~



示例代码：

~~~html
		<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
		<script type="text/javascript">
			$(document).ready(function(){
				function anmateIt(){
					$("#mover").slideToggle("slow", anmateIt);
				}
	
				anmateIt();				
			});
			$(document).ready(function(){
				//1.选择 含有文本 'di' 的 div 元素
				$("#btn1").click(function(){
					$("div:contains('di')").css("background", "#bbffaa");
				});

				//2.选择不包含子元素(或者文本元素) 的 div 空元素
				$("#btn2").click(function(){
					$("div:empty").css("background", "#bbffaa");
				});

				//3.选择含有 class 为 mini 元素的 div 元素
				$("#btn3").click(function(){
					$("div:has('.mini')").css("background", "#bbffaa");
				});

				//4.选择含有子元素(或者文本元素)的div元素
				$("#btn4").click(function(){
					$("div:parent").css("background", "#bbffaa");
				});
			});
		</script>
~~~



##### 属性过滤选择器

基本方法：

~~~html
[attribute] 			匹配 包含 给定属性的元素	$("div[id]")
[attribute=value] 		匹配给定的属性 是 某个特定值的元素	$("input[name='newsletter']").attr("checked", true);
[attribute!=value]		匹配给定的属性 不是 某个特定值的元素
[attribute^=value] 		匹配给定的属性是以某些值 开始 的元素	$("input[name^='news']")
[attribute$=value] 		匹配给定的属性是以某些值 结尾 的元素
[attribute*=value] 		匹配给定的属性是以 包含 某些值的元素
[attrSel1][attrSel2][attrSelN]	复合属性选择器，需要同时满足多个条件时使用。$("input[id][name$='man']")
	
~~~



示例代码：

~~~html
<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
<script type="text/javascript">
	$(function() {
		//1.选取含有 属性title 的div元素
		$("#btn1").click(function() {
			$("div[title]").css("background", "#bbffaa");
		});
		//2.选取 属性title值等于'test'的div元素
		$("#btn2").click(function() {
			$("div[title='test']").css("background", "#bbffaa");
		});
		//3.选取 属性title值不等于'test'的div元素(*没有属性title的也将被选中)
		$("#btn3").click(function() {
			$("div[title!='test']").css("background", "#bbffaa");
		});
		//4.选取 属性title值 以'te'开始 的div元素
		$("#btn4").click(function() {
			$("div[title^='te']").css("background", "#bbffaa");
		});
		//5.选取 属性title值 以'est'结束 的div元素
		$("#btn5").click(function() {
			$("div[title$='est']").css("background", "#bbffaa");
		});
		//6.选取 属性title值 含有'es'的div元素
		$("#btn6").click(function() {
			$("div[title*='es']").css("background", "#bbffaa");
		});
		
		//7.首先选取有属性id的div元素，然后在结果中 选取属性title值 含有'es'的 div 元素
		$("#btn7").click(function() {
			$("div[id][title*='es']").css("background", "#bbffaa");
		});
		//8.选取 含有 title 属性值, 且title 属性值不等于 test 的 div 元素
		$("#btn8").click(function() {
			$("div[title][title!='test']").css("background", "#bbffaa");
		});
	});
</script>
~~~



##### 表单对象属性过滤选择器

主要方式：按照标签的type返回，返回type="input" 的标签

~~~html

		:input 		
		:text 		
		:password 	
		:radio 		
		:checkbox 	
		:submit 	
		:image 		
		:reset 		
		:button 	
		:file 		
		:hidden 	
		
		表单对象的属性
		:enabled 		
		:disabled	使标签变得不可用，类似于不可操作	$("input:disabled");
		:checked	匹配所有选中的被选中元素(复选框、单选框等，不包括select中的option)
		:selected	匹配所有选中的option元素	$("input:checked");
~~~



示例代码：

~~~html
		<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
		<script type="text/javascript">
			$(function(){
					
					
				//1.对表单内 可用input 赋值操作
				$("#btn1").click(function(){
					// val()可操作表单项value属性值
					$("input:enabled").val("New Value");
				});
				//2.对表单内 不可用input 赋值操作
				$("#btn2").click(function(){
					$().val("New Value Too");
				});
				//3.获取多选框选中的个数  使用size()方法获取选取到的元素集合的元素个数
				$("#btn3").click(function(){
					alert($(":checkbox:checked").size())
				});
				//4.获取多选框，每个选中的value值
				$("#btn4").click(function(){
					var str = "";
					var eles = $(":checkbox:checked");
					console.log(eles);




					for(var i=0;i<eles.size();i++){
						str += "【"+$(eles[i]).val()+"】";
					}
					alert(str)
				});
				//5.获取下拉框选中的内容  
				$("#btn5").click(function(){
					var str = "";
					//注意这个选择器的特殊，因为select里面的option是真正的被选择项，
					//所以 :selected 选择器和 select[name='test']选择器的关系是子父关系
					//必须按照基本选择器选择后代的方法选
					var els = $("select option:selected");
					console.log(els);
					for(var i=0;i<els.size();i++){
						str += "【"+$(els[i]).val()+"】";
					}
					alert(str)
				});
			})	
		</script>
~~~



**注：**

1. each方法是jQuery对象提供用来遍历元素的方法
2. function中有一个this对象，这个this对象就是当前遍历到的dom对象
3. eles指代的是一个jQuery对象

~~~html
<script>
					eles.each(function () {
						alert(this);
					});
</script>
~~~



##### 元素筛选方法

理解：对之前选择器的另外一种写法：以方法的形式去筛选标签，方法的内容可以写上筛选标签所需的条件、表达式



基本方法：

~~~html
	过滤
	类似于:eq(),:first
	eq(index|-index) 	获取第N个元素	$("p").eq(1)
	first() 			获取第一个元素	$('li').first()
	last() 				获取最后个元素	$('li').last()
	hasClass(class)
	filter(expr|obj|ele|fn) 	筛选出与指定表达式匹配的元素集合。
	is(expr|obj|ele|fn)1.6*		根据选择器、DOM元素或 jQuery 对象来检测匹配元素集合，
	 							如果其中至少有一个元素符合这个给定的表达式就返回true

	has(expr|ele) 				功能和:has一样
	not(expr|ele|fn) 			功能和:not一样
	slice(start,[end])
	
	查找
	children([expr]) 			功能与 parent > ch一样
	closest(expr,[con]|obj|ele)1.6*   
	find(expr|obj|ele) 			功能与 ancestor descendant一样
	next([expr]) 				功能与 prev + next 一样
	nextall([expr]) 			功能与prev ~ siblings 一样
	nextUntil([exp|ele][,fil])1.6* 	查找当前元素之后所有的同辈元素，直到遇到匹配的那个元素为止。
	parent([expr]) 				取得一个包含着所有匹配元素的 唯一 父元素的元素集合
	parents([expr])				取得一个包含着所有匹配元素的祖先元素的元素集合
	parentsUntil([exp|ele][,fil])1.6*  
	prev([expr]) 				返回当前元素的 上一个 兄弟(同级)元素
	prevall([expr]) 			查找当前元素之前所有的同辈元素
	prevUntil([exp|ele][,fil])1.6* 	查找当前元素之前所有的同辈元素，直到遇到匹配的那个元素为止
	siblings([expr]) 			取得一个包含匹配的元素集合中每一个元素的所有唯一同辈元素的元素集合
	
	串联
	add(expr|ele|html|obj[,con]) 	
~~~

>详见jQuery文档

示例代码：

~~~html
		<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
		<script type="text/javascript">
			$(document).ready(function(){
				function anmateIt(){
					$("#mover").slideToggle("slow", anmateIt);
				}
				anmateIt();

				
				//(1)eq()  选择索引值为等于 3 的 div 元素
				$("#btn1").click(function(){
					$("div").eq(3).css("background-color","#bfa");
				});
				//(2)first()选择第一个 div 元素
				 $("#btn2").click(function(){
					 //first()   选取第一个元素
					$("div").first().css("background-color","#bfa");
				});
				//(3)last()选择最后一个 div 元素
				$("#btn3").click(function(){
					//last()  选取最后一个元素
					$("div").last().css("background-color","#bfa");
				});
				//(4)filter()在div中选择索引为偶数的
				$("#btn4").click(function(){
					//filter()  过滤   传入的是选择器字符串
					$("div").filter(":even").css("background-color","#bfa");
				});
				 //(5)is()判断#one是否为:empty或:parent
				//is用来检测jq对象是否符合指定的选择器
				$("#btn5").click(function(){
					alert($("#one").is("empty"));
				});
				
				//(6)has()选择div中包含.mini的
				$("#btn6").click(function(){
					//has(selector)  选择器字符串    是否包含selector
					$("div").has(".mini").css("background-color","#bfa");
				});
				//(7)not()选择div中class不为one的
				$("#btn7").click(function(){
					//not(selector)  选择不是selector的元素
					$("div").not(".one").css("background-color","#bfa");
				});
				//(8)children()在body中选择所有class为one的div子元素
				$("#btn8").click(function(){
					//children()  选出所有的子元素
					$("body").children("div.one").css("background-color","#bfa");
					// $("body").children("one").filter("div").css("background-color","#bfa");
				});
				
				
				//(9)find()在body中选择所有class为mini的div元素
				$("#btn9").click(function(){
					//find()  选出所有的后代元素
					$("body").find("div.mini").css("background-color","#bfa");
				});
				//(10)next() #one的下一个div
				$("#btn10").click(function(){
					//next()  选择下一个兄弟元素
					$("#one").next("div").css("background-color","#bfa");
				});
				//(11)nextAll() #one后面所有的span元素
				$("#btn11").click(function(){
					//nextAll()   选出后面所有的元素
					$("#one").next("span").css("background-color","#bfa");
				});
				//(12)nextUntil() #one和span之间的元素
				$("#btn12").click(function(){
					//
					$("#one").nextUntil("span").css("background-color","#bfa")
				});
				//(13)parent() .mini的父元素
				$("#btn13").click(function(){
					$(".mini").parent().css("background-color","#bfa");
				});
				//(14)prev() #two的上一个div
				$("#btn14").click(function(){
					//prev()  
					$("#two").prev("div").css("background-color","#bfa")
				});
				//(15)prevAll() span前面所有的div
				$("#btn15").click(function(){
					//prevAll()   选出前面所有的元素
					$("span").prevAll("div").css("background-color","#bfa")
				});
				//(16)prevUntil() span向前直到#one的元素
				$("#btn16").click(function(){
					//prevUntil(exp)   找到之前所有的兄弟元素直到找到exp停止
					$("span").prevUntil("#one").css("background-color","#bfa")
				});
				//(17)siblings() #two的所有兄弟元素
				$("#btn17").click(function(){
					//siblings()    找到所有的兄弟元素，包括前面的和后面的
					$("#two").siblings().css("background-color","#bfa")
				});
				
				
				//(18)add()选择所有的 span 元素和id为two的元素
				$("#btn18").click(function(){

					// $("span").add("#two").css("background-color","#bfa");
					//   $("span,#two,.mini,#one")
					$("span").add("#two")
							.add(".mini").add("#one").css("background-color","#bfa");
					
				});
				


			});
			
			
		</script>
~~~



**注：class为one的div子元素写法：div.one**



##### dom属性操作

基本方法：主要是处理dom对象中的数据，比如获得输入框中用户输入的数据用 `$domObj.val()`等.

~~~html
/**
HTML代码/文本/值
html([val|fn])    a.html()取出a的html值    a.html(val)  让a的html值变为val
 	可以设置起始标签和结束标签中的内容，跟dom属性的innerHTML一样
text([val|fn]) 	  a.text()取出a的text值    a.text(val)  让a的文本值变为val
 	可以设置起始标签和结束标签中的文本，跟dom属性的innerText一样
val([val|fn|arr]) a.val()  取出a的val值（input）   a.val(v)  设置a的value值为v 
	可以设置和获取表单项的value属性值


属性
attr(name|pro|key,val|fn)  
 1、a.attr('name')取出a的name值
 2、a.attr("name","username")把a的name值设置为username
 3、不推荐操作 checked、readOnly、selected、disabled
 4、可操作非标准属性，如程序员为标签自定义的属性：abc，ccc等
 
removeAttr(name) 
a.removeAttr('class')    移除a的class属性


prop(name|pro|key,val|fn)1.6+ 
 1、a.prop('id')  取出a的id值
 2、a.prop('id',"bj")  设置a的id值为bj
 3、只推荐操作 checked、readOnly、selected、disabled
 4、checked的返回和操作值为 true 和 false

removeProp(name)1.6+
a.removeProp('class') 移除a的class属性
~~~





##### dom增删改操作

基本方法：

~~~html
文档处理
内部插入

appendTo(content) 	   a.appendTo(b);  把a加到b子元素末尾里面				  添加到最后面
prependTo(content)	   a.prependTo(b); 把a添加到b所有子元素前面里面    			  添加到最前面
括号内可以加选择器找元素

外部插入
insertAfter(content) 	a.insertAfter(b);  把a插入到b的后面	ba
insertBefore(content) 	a.insertBefore(b); 把a插入到b的前面	ab


替换
replaceWith(content|fn) a.replaceWith(b)  把a用b替换
replaceAll(selector) 	a.replaceAll(b)	  用a替换所有的b

删除
empty() 				a.empty()   把a掏空，把a标签内的所有内容
remove([expr]) 			a.remove(b)  所有的a，是b的话就会删除	a.remove()删除a
~~~



示例代码：

~~~html
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>DOM增删改</title>
		<link rel="stylesheet" type="text/css" href="style/css.css" />
		<script type="text/javascript" src="../script/jquery-1.7.2.js"></script>
		<script type="text/javascript">

            
			$(function(){
			
				
				$("#btn01").click(function(){
					//创建一个"广州"节点,添加到#city下[appendTo()]
					//子节点.appendTo(父节点)
					$("<li>广州</li>").appendTo( $("#city") );
					
					
				});
				
				
				$("#btn02").click(function(){
					//创建一个"广州"节点,添加到#city下[prependTo()]
					$("<li>广州</li>").prependTo( $("#city") );
					
				});
				
				
				
				$("#btn03").click(function(){
					//将"广州"节点插入到#bj前面[insertBefore()]
					//前边.insertBefore(后边的)
					$("<li>广州</li>").insertBefore( $("#bj") );
					
				});
				
				
				$("#btn04").click(function(){
					//将"广州"节点插入到#bj后面[insertAfter()]
					//后边.insertAfter(前边的)
					$("<li>广州</li>").insertAfter( $("#bj") );
					
				});
				
				$("#btn05").click(function(){
					//使用"广州"节点替换#bj节点[replaceWith()]
					//被替换的.replaceWith()
					$("#bj").replaceWith( "<li>广州</li>" );

					
				});
				
				$("#btn06").click(function(){
					//使用"广州"节点替换#bj节点[replaceAll()]
					//新的节点.replaceAll(旧的节点)
					$("<li>广州</li>").replaceAll( $("#bj") );
					
				});
				
				$("#btn07").click(function(){
					//删除#rl节点[remove()]
					//$("ul").remove("#rl");
					//$("#rl").remove();


					$("#rl").remove();
				});
				
				$("#btn08").click(function(){
					//掏空#city节点[empty()]
					$("#city").empty();
					
				});
				
				$("#btn09").click(function(){
					//读取#city内的HTML代码
					$("#city").each(function () {
						alert(this.innerHTML);
					});
				});
				
				$("#btn10").click(function(){
					//设置#bj内的HTML代码
					$("#bj").text("Hello");
					
				});
				
				
				
			});
			
		
		</script>
		
	</head>
~~~



##### css样式操作

~~~html
CSS
css(name|pro|[,val|fn])       读写匹配元素的样式属性。 
								a.css('color')取出a元素的color
								a.css('color',"red")设置a元素的color为red

CSS 类

addClass(class|fn) 			为元素添加一个class值;<div class="mini big">
removeClass([class|fn]) 	删除元素的class值；传递一个具体的class值，就会删除具体的某个class
							a.removeClass()：移除所有的class值
~~~



示例代码：

~~~html
<script type="text/javascript" src="script/jquery-1.7.2.js"></script>
<script type="text/javascript">
	

	$(function(){

		
		var $divEle = $('div:first');
		
		$('#btn01').click(function(){
			//addClass() - 向被选元素添加一个或多个类
			$divEle.addClass("redDiv blackDiv");
		});
		
		$('#btn02').click(function(){
			//removeClass() - 从被选元素删除一个或多个类 
			$divEle.removeClass()
		});

		
		$('#btn03').click(function(){
			//toggleClass() - 对被选元素进行添加/删除类的切换操作 
			//切换就是如果具有该类那么删除，如果没有那么添加上
			$divEle.toggleClass("redDiv");
		});
		
		$('#btn04').click(function(){
			//offset() - 返回第一个匹配元素相对于文档的位置。
			var os = $divEle.offset();
			//注意通过offset获取到的是一个对象，这个对象有两个属性top表示顶边距，left表示左边距
			alert("顶边距："+os.top+" 左边距："+os.left);
			
			//调用offset设置元素位置时，也需要传递一个js对象，对象有两个属性top和left
			//offset({ top: 10, left: 30 });
			 $divEle.offset({
				 top:50,
				 left:60
			 }); 
		});
		
	})
</script>
~~~



##### 动画效果

基本方法：

~~~html
		基本
		show([speed,[easing],[fn]]) 	显示隐藏的匹配元素
		hide([speed,[easing],[fn]]) 	将可见的元素隐藏
		toggle([speed],[easing],[fn])	

		滑动
		slideDown([spe],[eas],[fn])
		slideUp([speed,[easing],[fn]])
		slideToggle([speed],[easing],[fn]) 

		淡入淡出
		fadeIn([speed],[eas],[fn]) 		淡入，慢慢地可见
		fadeOut([speed],[eas],[fn]) 	淡出，慢慢地消失
		fadeTo([[spe],opa,[eas],[fn]]) 	在指定时长内慢慢将透明度修改到指定的值：0透明 1全部可见
		fadeToggle([speed,[eas],[fn]])



		1. 第一个参数都可添加参数，以毫秒为单位
		2. 第二个参数是动画的回调函数，动画完成之后自动调用的函数

~~~



示例代码：

~~~html
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>Untitled Document</title>
		<link href="css/style.css" type="text/css" rel="stylesheet" />
		<script type="text/javascript" src="script/jquery-1.7.2.js"></script>
	
<script type="text/javascript">

		$(function(){
			//显示   show()
			$("#btn1").click(function(){
				$ ("#div1").show(1000,function () {
					alert("finished");
				});
			});		
			//隐藏  hide()
			$("#btn2").click(function(){
				$ ("#div1").hide(1000,function () {
					alert("finished");
				});
			});	
			//用一个按键切换/隐藏，切换   toggle()
			$("#btn3").click(function(){
				$ ("#div1").toggle(1000);
			});	
			
			//淡入，慢慢地可见   fadeIn()
			$("#btn4").click(function(){
				$ ("#div1").fadeIn(1000);
			});	
			//淡出，慢慢地消失  fadeOut()
			$("#btn5").click(function(){
				$ ("#div1").fadeOut(1000);
			});	
			
			//淡化到  fadeTo()
			$("#btn6").click(function(){
				$ ("#div1").fadeTo(2000,0.5,function () {
					alert("finished");
				});
			});	
			//淡化切换  fadeToggle()
			$("#btn7").click(function(){
				$ ("#div1").fadeToggle(1000);
			});	
		});
</script>
</head>
~~~





