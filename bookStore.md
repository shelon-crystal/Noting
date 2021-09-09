[toc]





### 书城



#### 书城项目（六）



##### 6.1 登录信息显示



##### 6.2 注销用户

1. 销毁Session
2. 重定向到首页或登录页面



##### 6.3 表单重复提交

###### 6.3.1 重复提交的三种情况

1. 提交完表单之后，再次按F5，发起最后一次请求，造成重复提交。

   解决方法：在Servlet利用重定向进行跳转

2. 用户正常提交，由于网络延迟，迟迟未收到服务器响应，用户以为提交失败，多点了几次提交，造成表单重复提交。

   解决方法：

3. 用户正常提交服务器，服务器也没有延迟，提交完成之后，用户回退浏览器，重新提交，造成表单重复提交。



##### 6.5 验证码原理

![1601298558221](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601298558221.png)

###### 6.5.1 使用谷歌验证码解决表单重复提交问题

1. 导入谷歌验证码jar包（mykaptcha-2.3.2.jar）

2. 在web.xml中配置用于生成验证码的Servlet类（KaptchaServlet）

   ~~~xml
       <servlet>
           <servlet-name>KaptchaServlet</servlet-name>
           <servlet-class>com.google.code.kaptcha.servlet.KaptchaServlet</servlet-class>
       </servlet>
       <servlet-mapping>
           <servlet-name>KaptchaServlet</servlet-name>
           <url-pattern>/kaptcha.jpg</url-pattern>
       </servlet-mapping>
   ~~~

   

3. 在表单中使用img标签去显示验证码并使用

   ~~~jsp
   <img alt="" src="http://localhost:8080/bookStore/kaptcha.jpg" style="width: 100px; height: 30px">
   ~~~

   

4. 在服务器端Servlet获取谷歌生成的验证码和客户端发送过来的验证，比较使用

   通过Session查找验证码需要知道验证码的Session KEY，通过查询jar的的常量类得到

   ![1601300125968](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601300125968.png)

   

###### 6.5.2 验证码的切换

1. 给验证码图片设定id，并绑上单击事件。
2. 给验证码请求路径加上时间戳以识别不同的图片切换，以免浏览器调用缓存中的验证码图片导致无法切换。





---





##### 6.6 购物车模块实现

###### 6.6.1 模块图

以Session版本的实现方式为例，此版本不用与数据库交互，不需要建立dao层

![1601609632615](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601609632615.png)



###### 6.6.2 实体类

1. 购物车类

   由于使用Session存储，所有增删改方法都写到购物车的实体类中

   ~~~java
   /**
    *  购物车对象
    */
   public class Cart {
   //    private Integer totalCount;
   //    private BigDecimal totalPrice;
       private Map<Integer,CartItem> items = new LinkedHashMap<Integer,CartItem>();
   
       /**
        *  添加商品项
        * @param cartItem
        */
       public void addItem(CartItem cartItem){
           // 先查看购物车中是否已经添加过此商品，如果已经添加则数量累加，总金额更新，如果没有添加过，则直接放进集合中
           CartItem item = items.get(cartItem.getId());
           if (item == null){
               // 之前没添加过
               items.put(cartItem.getId(),cartItem);
           }else {
               // 已经添加过
               // 数量累加
               item.setCount(item.getCount() + 1);
               // 更新总金额
               item.setTotalPrice(item.getPrice().multiply(new BigDecimal(item.getCount())));
           }
       }
   
       /**
        *  删除商品项
        * @param id
        */
       public void deleteItem(Integer id){
           items.remove(id);
       }
   
       /**
        * 修改商品数目
        * @param id
        */
       public void updateItem(Integer id,Integer count){
           // 先查看购物车中是否有此商品，如果有，修改商品数量，更新总金额
   
           CartItem cartItem = items.get(id);
           if (cartItem != null){
               cartItem.setCount(count);
               cartItem.setTotalPrice(cartItem.getPrice().multiply(new BigDecimal(cartItem.getCount())));
           }
       }
   
       /**
        *  清空购物车
        * @param
        */
       public void clear(){
           items.clear();
       }
   
       public Integer getTotalCount() {
           Integer totalCount = 0;
   
           for (Map.Entry<Integer,CartItem> entry: items.entrySet()){
               totalCount += entry.getValue().getCount();
           }
   
           return totalCount;
       }
   
       public BigDecimal getTotalPrice() {
           BigDecimal totalPrice = new BigDecimal(0);
   
           for (Map.Entry<Integer,CartItem> entry: items.entrySet()){
               totalPrice = totalPrice.add(entry.getValue().getTotalPrice());
           }
   
           return totalPrice;
       }
   
       public Map<Integer,CartItem> getItems() {
           return items;
       }
   
       public void setItems(Map<Integer,CartItem> items) {
           this.items = items;
       }
   
       @Override
       public String toString() {
           return "Cart{" +
                   "totalCount=" + getTotalCount() +
                   ", totalPrice=" + getTotalPrice() +
                   ", items=" + items +
                   '}';
       }
   }
   ~~~

   

2. 商品项类

   ~~~java
   /**
    *  购物车的商品项实体类
    */
   public class CartItem  {
       private Integer id;
       private String name;
       private  Integer count;
       private BigDecimal price;
       private BigDecimal totalPrice;
   
       public CartItem() {
   
       }
   
       public CartItem(Integer id, String name, Integer count, BigDecimal price, BigDecimal totalPrice) {
           this.id = id;
           this.name = name;
           this.count = count;
           this.price = price;
           this.totalPrice = totalPrice;
       }
   
       public Integer getId() {
           return id;
       }
   
       public void setId(Integer id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public Integer getCount() {
           return count;
       }
   
       public void setCount(Integer count) {
           this.count = count;
       }
   
       public BigDecimal getPrice() {
           return price;
       }
   
       public void setPrice(BigDecimal price) {
           this.price = price;
       }
   
       public BigDecimal getTotalPrice() {
           return totalPrice;
       }
   
       public void setTotalPrice(BigDecimal totalPrice) {
           this.totalPrice = totalPrice;
       }
   
       @Override
       public String toString() {
           return "CartItem{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   ", count=" + count +
                   ", price=" + price +
                   ", totalPrice=" + totalPrice +
                   '}';
       }
   }
   ~~~

   

###### 6.6.3 CartServlet

说明：

1. 实现返回请求的页面：通过referer获取当前请求页面的url实现对加入购物车操作之后返回请求加入购物车的页面。
2. 每次都从Session中获取Cart购物车，如果存在则直接使用，不存在则新建放入Session。

~~~java
public class CartServlet extends BaseServlet {

    BookService bookService = new BookServiceImpl();

    /**
     *  加入购物车
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void addItem(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 1.获取请求的参数：商品编号
        int id = WebUtils.parseInt(req.getParameter("id"),0);
        // 2.调用bookService.queryBookById()：获取图书信息
        Book book = bookService.queryBookById(id);
        // 3.把图书信息转化成为CartItem商品项目
        CartItem cartItem = new CartItem(book.getId(),book.getName(),1,book.getPrice(),book.getPrice());
        // 4.调用Cart.addItem(CartItem) 添加商品项目
        // 从Session获取/判断之前是否有已经存在于session中的购物车
        Cart cart = (Cart) req.getSession().getAttribute("cart");
        if (cart == null){
            cart = new Cart();
            // 放进Session中
            req.getSession().setAttribute("cart",cart);
        }
        cart.addItem(cartItem);
        // 5.重定向回商品列表界面
        // Referer 在请求头中是请求页面的URL，可以作为添加购物车之后的跳回地址
        resp.sendRedirect(req.getHeader("Referer"));

        req.getSession().setAttribute("lastName",cartItem.getName());
    }

    /**
     * 删除商品项
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void deleteItem(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取商品编号
        int id = WebUtils.parseInt(req.getParameter("id"),0);

        // 获取购物车对象
        Cart cart = (Cart) req.getSession().getAttribute("cart");

        if (cart != null){
            // 执行删除
            cart.deleteItem(id);
            // 重定向回购物车展示页面
            resp.sendRedirect(req.getHeader("Referer"));
        }

    }

    /**
     *  修改商品数量
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void updateCount(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求的参数：商品编号、商品数量
        int id = WebUtils.parseInt(req.getParameter("id"),0);
        int count = WebUtils.parseInt(req.getParameter("count"),1);

        // 获取购物车对象
        Cart cart = (Cart) req.getSession().getAttribute("cart");

        if (cart != null){
            // 执行修改
            cart.updateItem(id,count);
            // 重定向回购物车展示页面
            resp.sendRedirect(req.getHeader("Referer"));
        }
    }

    /**
     *  清空购物车
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void clear(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取购物车对象
        Cart cart = (Cart) req.getSession().getAttribute("cart");

        if (cart != null){
            // 执行清空
            cart.clear();
            // 重定向回购物车展示页面
            resp.sendRedirect(req.getHeader("Referer"));
        }
    }
}

~~~



###### 6.6.4 页面联调

1. 遍历Session显示商品

   **注：都要判断购物车是否为空**

   ~~~jsp
   			<%--购物车为空的情况--%>
   			<c:if test="${ empty sessionScope.cart.items}">
   				<tr>
   					<td colspan="5"><a href="index.jsp">当前购物车为空！点击此处浏览商品。</a></td>
   				</tr>
   			</c:if>
   
   			<%--购物车非空，遍历Session--%>
   			<c:if test="${ not empty sessionScope.cart.items}">
   				<c:forEach items="${sessionScope.cart.items}" var="entry">
   					<tr>
   						<td>${entry.value.name}</td>
   						<td>
   							<input class="updateClass"
   								   bookId="${entry.value.id}"
   								   style="width: 80px" type="text" value="${entry.value.count}">
   						</td>
   						<td>${entry.value.price}</td>
   						<td>${entry.value.totalPrice}</td>
   						<td><a class="deleteItem" href="cartServlet?action=deleteItem&id=${entry.value.id}">删除</a></td>
   					</tr>
   				</c:forEach>
   			</c:if>
   ~~~

2. 页面按键事件

   - confirm：确认提示框，是-true，否-false
   - defaultValue：表单dom对象默认的value值
   - 属性class、id设置：class属性可为多个标签设置，id只能属于一个标签
   - this：表示响应当前事件标签的dom对象
   - location.href：指定跳转的页面

   ~~~jsp
   	<script type="text/javascript">
   		$(function () {
   			/*给删除按按钮绑上单击事件，提示确认删除商品*/
   			$("a.deleteItem").click(function () {
   				return confirm("你确定要删除【" + $(this).parent().parent().find("td:first").text() + "】吗？");
   			});
   
   			/*给清空购物车绑定单击事件*/
   			$("#clearCart").click(function () {
   				return confirm("你确定要清空购物车吗？");
   			});
   
   			/*给输入框绑定失去焦点事件*/
   			$("input.updateClass").change(function () {
   
   				// 获取商品名称
   				var name = $(this).parent().parent().find("td:first").text();
   				// 获取商品id（已在标签属性中定义）
   				var id = $(this).attr("bookId");
   				var count = this.value;
   				if (confirm("你确定要将【" + name + "】商品的数量修改为：" + count + "吗？")) {
   					location.href = "http://localhost:8080/bookStore/cartServlet?action=updateCount&count=" + count + "&id=" + id;
   				} else {
   					// defaultValue 表单项dom对象的默认value属性值
   					this.value = this.defaultValue;
   				}
   			});
   		});
   	</script>
   ~~~

   



##### 6.7 订单模块实现



###### 6.7.1 模块图

![1601822815305](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1601822815305.png)



###### 6.7.2 建表

~~~sql
USER book;

CREATE TABLE t_order(
	`order_id` VARCHAR(50) PRIMARY KEY,
	`create_time` DATETIME,
	`price` DECIMAL(11,2),
	`status` INT,
	`user_id` INT,
	FOREIGN KEY(`user_id`) REFERENCES t_user(`id`)

);

CREATE TABLE t_order_item(
	`id` INT PRIMARY KEY AUTO_INCREMENT,
	`name` VARCHAR(100),
	`count` INT,
	`price` DECIMAL(11,2),
	`total_price` DECIMAL(11,2),
	`order_id` VARCHAR(50),
	FOREIGN KEY(`order_id`) REFERENCES t_order(`order_id`)
);
~~~



###### 6.7.3 构建实体类



生成：有参构造、无参构造、toString（）

**Order.java** 订单

~~~java
    private String orderId;
    private Date createTime;
    private BigDecimal price;
    // 0未发货 1已发货 2已签收
    private Integer status=0;
    private Integer userId;
~~~



**OrderItem.java ** 订单项

~~~java
    private Integer id;
    private String name;
    private Integer count;
    private BigDecimal price;
    private BigDecimal totalPrice;
    private String orderId;
~~~



###### 6.7.4 构建dao以及Service



**dao（Order、OrderItem）默认实现所有接口方法，故此处省略接口类**

~~~java
public class OrderDaoImpl extends BaseDao implements OrderDao {

    @Override
    public int saveOrder(Order order) {
        String sql = "insert into t_order(`order_id`,`create_time`,`price`,`status`,`user_id`) values(?,?,?,?,?)";

        return update(sql,order.getOrderId(),order.getCreateTime(),order.getPrice(),order.getStatus(),order.getUserId());
    }
}
~~~



~~~java
public class OrderItemDaoImpl extends BaseDao implements OrderItemDao {
    @Override
    public int saveOrderItem(OrderItem orderItem) {
        String sql = "insert into t_order_item(`name`,`count`,`price`,`total_price`,`order_id`) values(?,?,?,?,?)";

        return update(sql,orderItem.getName(),orderItem.getCount(),orderItem.getPrice(),orderItem.getTotalPrice(),orderItem.getOrderId());
    }
}
~~~



**Service**

说明：

1. 生成唯一的订单编号：时间戳+用户id组成（System.currentTimeMillis() + "" + userId）
2. 生成订单结算后商品信息修改：在遍历购物车的每一项订单项入库之后，调用bookService服务修改库中对应商品的库存和销量
3. 修改商品前提：
   - 该商品存在
   - 形成订单的商品数量小于库存对应商品数量
4. 订单形成之后清空购物车，并返回订单号。



~~~java
public class OrderServiceImpl implements OrderService {

    private OrderDao orderDao = new OrderDaoImpl();
    private OrderItemDao orderItemDao = new OrderItemDaoImpl();
    private BookDao bookDao = new BookDaoImpl();
    /**
     *  生成订单服务
     * @param cart
     * @param userId
     * @return 返回订单号 orderId
     */
    @Override
    public String createOrder(Cart cart, Integer userId) {

        // 生成订单号：时间戳+用户id
        String orderId = System.currentTimeMillis() + "" + userId;
        // 先保存订单
        Order order = new Order(orderId,new Date(),cart.getTotalPrice(),0,userId);
        orderDao.saveOrder(order);
        // 再保存订单项

        // 遍历购物车每一个商品项转化为OrderItem
        for (Map.Entry<Integer, CartItem> entry :cart.getItems().entrySet()){
            // 获取每一个购物车中的商品项
            CartItem cartItem = entry.getValue();
            // 转换为每一个订单项
            OrderItem orderItem = new OrderItem(null,cartItem.getName(),cartItem.getCount(),cartItem.getPrice(),cartItem.getTotalPrice(),orderId);
            // 保存订单项到数据库
            orderItemDao.saveOrderItem(orderItem);

            // 更改库存、销量
            Book book = bookDao.queryBookById(cartItem.getId());
            Integer stock = book.getStock() - cartItem.getCount();
            if (stock < 0){
                return null;
            }
            book.setStock( stock );
            book.setSales( book.getSales() + cartItem.getCount());
            bookDao.updateBook(book);

        }
        // 清空购物车
        cart.clear();

        return orderId;
    }
}
~~~





###### 6.7.5 OrderServlet与Web页面联调



说明：

1. 在创建订单之前进行用户登录检查，若当前Session中无用户信息，请求转发到用户登录界面。
2. 将创建订单完成之后的订单号存入Session在页面中调用显示给用户。

**OrderServlet.java**

~~~java
public class OrderServlet extends BaseServlet{

    OrderService orderService = new OrderServiceImpl();
    /**
     *  生成订单
     * @param req
     * @param resp
     * @throws ServletException
     * @throws IOException
     */
    protected void createOrder(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取Cart购物车对象
        Cart cart = (Cart) req.getSession().getAttribute("cart");
        // 获取UserId
        User loginUser = (User) req.getSession().getAttribute("user");
        if (loginUser == null){
            req.getRequestDispatcher("/pages/user/login.jsp").forward(req,resp);
            return;
        }
        Integer userId = loginUser.getId();
        // 调用OrderService的CreateOrder(cart,userId)
        String orderId = orderService.createOrder(cart, userId);
        if (orderId == null){
            return;
        }
        // 将订单号保存再Session中给订单显示页面调用显示
        req.getSession().setAttribute("orderId",orderId);

        // 请求转发到/pages/cart/checkout.jsp
        resp.sendRedirect(req.getContextPath() + "/pages/cart/checkout.jsp");
    }
}

~~~





----







#### 书城项目（八）



##### 8.1 拦截manager部分的请求

~~~java
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {

        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;

        Object user = httpServletRequest.getSession().getAttribute("user");

        if (user == null){
            httpServletRequest.getRequestDispatcher("/pages/user/login.jsp").forward(servletRequest,servletResponse);
        }  else {
            filterChain.doFilter(servletRequest,servletResponse);
        }
    }
~~~



配置多个拦截路径，因为在请求页面的时候，地址栏的地址不仅仅至少page/manager可配置多个拦截路径



![1602490234758](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602490234758.png)

~~~xml
    <filter>
        <filter-name>ManagerFilter</filter-name>
        <filter-class>org.studyCSL.filter.ManagerFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>ManagerFilter</filter-name>
        <url-pattern>/pages/manager/*</url-pattern>
        <url-pattern>/manager/bookServlet</url-pattern>
    </filter-mapping>
~~~



##### 8.2 使用Filter和ThreadLocal组合管理事务



###### 8.2.1 ThreadLocal



作用：解决多线程的数据安全问题

使用：给当前线程关联一个数据（可以是普通变量、对象、数组、集合等等）

特点：

1. 可以为当前线程关联一个数据（像Map一样存储数据，key为当前线程名）
2. 每一个ThreadLocal对象只能为当前线程关联一个数据，如果要为当前线程管理多个数据，则需要使用多个ThreadLocal对象实例
3. 每个ThreadLocal对象实例定义的时候，一般是static类型
4. ThreadLocal中保存数据，在线程销毁之后，由JVM自动释放



理解：为当前线程绑定一个数据，设置成员对象ThreadLocal，调用`.set`和`.get`方法为线程绑定数据，如果多次绑定，则只会绑定为线程绑定最后一次绑定的数据。





###### 8.2.2 使用ThreadLocal确保所有的操作都使用同一个Connection来实现事务管理



情况：可能会出现在保存订单之后，出现错误中止了之后的订单项在数据库中的添加和图书信息的修改，即目的要实现事务回滚。



调用Service的时候，Service层与其他层之间是存在于同一个线程内部（在Service类中创建了Dao层的对象，所以ThreadLocal变量可以在存在与同一个线程中的类实例中传递，Dao与BaseDao同理）



1. 在JdbcUtils里创建ThreadLocal类实例，保存数据库连接池的Connection

2. 修改获取连接的方式，从首先从ThreadLocal中获取连接，如果没有连接则创建一个连接，并保存set到ThreadLocal对象中，然后设置为手动提交事务

   ~~~java
       public static Connection getConnection() {
   
           Connection conn = conns.get();
   
           if (conn == null){
               try {
                   conn = dataSource.getConnection();// 从数据库连接池里获取连接
   
                   // 保存到ThreadLocal对象中
                   conns.set(conn);
   
                   conn.setAutoCommit(false); //设置为手动管理事务
               }catch (Exception e ){
                   e.printStackTrace();
               }
           }
   
           return conn;
       }
   ~~~

3. 分别设立两个方法分别用于提交和回滚事务

   **说明：**conns.remove();  移除ThreadLocal以免Tomcat线程池溢出，导致内存泄漏。

   ~~~java
       /**
        *  提交事务并关闭释放连接
        */
       public static void commitAndClose(){
           Connection connection = conns.get();
           if (connection != null){
               // 如果不为空则说明之前使用连接操作过数据库
   
               try {
                   // 提交事务
                   connection.commit();
               } catch (SQLException e) {
                   e.printStackTrace();
               } finally {
                   // 关闭连接
                   try {
                       connection.close();
                   } catch (SQLException e) {
                       e.printStackTrace();
                   }
               }
           }
           //
           conns.remove();
       }
   
       /**
        *  回滚事务并关闭释放连接
        */
       public static void rollbackAndClose(){
           Connection connection = conns.get();
           if (connection != null){
               // 如果不为空则说明之前使用连接操作过数据库
   
               try {
                   // 回滚事务
                   connection.rollback();
               } catch (SQLException e) {
                   e.printStackTrace();
               } finally {
                   // 关闭连接
                   try {
                       connection.close();
                   } catch (SQLException e) {
                       e.printStackTrace();
                   }
               }
           }
           //
           conns.remove();
       }
   
   ~~~

4. 在BaseDao中设置如果SQL出现异常则直接将异常抛出到类外给Servlet等类中的catch块捕获，然后回滚事务

   ~~~java
       /**
        *  update 方法用来执行 insert/update/delete语句
        * @return  返回-1，执行失败，其他表示影响的行数
        */
       public int update(String sql,Object ... args){
           Connection connection = JdbcUtils.getConnection();
           // 包围快捷键 ctrl alt t
           try {
              return queryRunner.update(connection,sql,args);
           } catch (SQLException e) {
               e.printStackTrace();
               throw new RuntimeException(e);
           }
       }
   ~~~

5. 在OrderServlet中的修改

   给OrderService.xxx() 绑上try-catch块

   （要对每一个Service.xxx() 用try-catch块包围，效率很低，后面将会在Service层用FIlter过滤提高）

   设置如果不会到异常就直接回滚并关闭连接池，如果没有异常则提交。

   ~~~java
           // 调用OrderService的CreateOrder(cart,userId)
           String orderId = null;
           try {
               orderId = orderService.createOrder(cart, userId);
   
               JdbcUtils.commitAndClose();// 提交事务
           } catch (Exception e) {
               // 在捕获到ServiceImpl中的异常之后回滚刚刚创建的订单
               JdbcUtils.rollbackAndClose();// 回滚事务
               e.printStackTrace();
           }
   ~~~

   





###### 8.2.3 使用FIlter给所有Service方法加上try-catch来管理事务



**实现原理**

![1602588440543](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602588440543.png)





1. 构建TransactionFilter：

~~~java
public class TransactionFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        try {
            filterChain.doFilter(servletRequest,servletResponse);
            // 提交事务
            JdbcUtils.commitAndClose();
        } catch (Exception e) {
            // 回滚事务
            JdbcUtils.rollbackAndClose();
            e.printStackTrace();
        }
    }

    @Override
    public void destroy() {

    }
}
~~~





2. 在xml文件中的配置：

**注意：`/*`表示当前工程下所有请求**

~~~xml
    <filter>
        <filter-name>TransactionFilter</filter-name>
        <filter-class>org.studyCSL.filter.TransactionFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>TransactionFilter</filter-name>
        <!--/*表示当前工程下所有请求-->
        <url-pattern>/*</url-pattern>
    </filter-mapping>
~~~





3. 修改BaseServlet中的异常处理，将异常抛出。

   原因：在所有底层的方法中，都要将异常抛出给FIlter捕获。

   抛出方式：throw new RuntimeException(e)

   ~~~java
       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
           String action = req.getParameter("action");
           try {
               // 获取相应业务的方法反射对象
               // 方法参数，第一个是方法名，第二个是方法的返回值类型
               // public Method getDeclaredMethod(String name, Class<?>... parameterTypes)
               Method method = this.getClass().getDeclaredMethod(action,HttpServletRequest.class,HttpServletResponse.class);
   
               // 调用目标业务方法
               // protected void org.studyCSL.web.UserServlet.login(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
               // throws javax.servlet.ServletException,java.io.IOException
               method.invoke(this,req,resp);
           } catch (Exception e)  {
               e.printStackTrace();
               throw new RuntimeException(e);// 把异常抛给Filter过滤器
           }
       }
   ~~~

   



###### 8.3 创建并配置用于产生错误后跳转的页面



1. 在FIlter的try-catch块中设置抛出错误信息

   ~~~java
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           try {
               filterChain.doFilter(servletRequest,servletResponse);
               // 提交事务
               JdbcUtils.commitAndClose();
           } catch (Exception e) {
               // 回滚事务
               JdbcUtils.rollbackAndClose();
               e.printStackTrace();
               throw new RuntimeException(e);// 将错误抛给Tomcat显示友好的页面
           }
       }
   ~~~

2. 创建error目录并设置几个error跳转页面

   ![1602598116260](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1602598116260.png)

3. 在web.xml中配置相应错误对应的页面路径

   说明：

   **<error-code>标签表示错误类型**

   **<location>标签表示产生错误后跳转的页面路径**

   ~~~xml
       <!--配置错误页面标签，配置服务器出错之后，自动跳转的页面-->
       <error-page>
           <!--error是错误类型-->
           <error-code>500</error-code>
           <!--location表示要跳转的页面路径-->
           <location>/pages/error/error500.jsp</location>
       </error-page>
   
       <error-page>
           <!--error是错误类型-->
           <error-code>404</error-code>
           <!--location表示要跳转的页面路径-->
           <location>/pages/error/error404.jsp</location>
       </error-page>
   ~~~

   






---







#### 书城项目（九）



##### 9.1 利用AJAX请求验证用户名是否可用

![1603012454780](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1603012454780.png)



步骤：

1. 为用户名输入框绑定失去焦点事件

2. $.getJSON()将数据传入Servlet并做出响应

   ~~~javascript
   			// 给用户名输入框绑定单击事件，让其调用AJAX请求
   			$("#username").blur(function () {
   
   				// 获取用户名
   				var username = this.value;
   
   				$.getJSON("http://localhost:8080/bookStore/userServlet",
   				          "action=ajaxExistUsername&username=" + username,
   				          function (data) {
   								if (data.checkResult){
   									$("span.errorMsg").text("用户名已存在");
   								}else {
   									$("span.errorMsg").text("用户名可用");
   								}
   						  });
   			});
   ~~~

3. Servlet内：获取请求参数。调用Service查询用户名是否存在。将结果封装成Map对象再转化为JSON回传客户端

   **回传客户端：resp.getWriter().write(json);**

   ~~~java
       protected void ajaxExistUsername(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           // 获取请求参数username
           String username = req.getParameter("username");
           // 调用Service
           boolean checkResult = userService.existUsername(username);
           // 将结果封装成为Map对象
           Map<String,Object> resultMap = new HashMap<>();
           resultMap.put("checkResult",checkResult);
   
           //转成JSON
           Gson gson = new Gson();
           String json = gson.toJson(resultMap);
           // 回传客户端
           resp.getWriter().write(json);
       }
   ~~~





##### 9.2 使用AJAX请求修改添加商品到购物车



