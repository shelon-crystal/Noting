MYSQL



功能实现步骤

1. 建立数据库表

2. 编写数据库表对应的JavaBean

   1. 在pojo包下建立User
   2. 建立库表属性，alt insert 生成无参构造、有参构造、set/get方法、toString方法

   ~~~java
   package org.studyCSL.pojo;
   
   public class User {
       private Integer id;
       private String username;
       private String password;
       private String email;
   
       public User() {
       }
   
       public User(Integer id, String username, String password, String email) {
           this.id = id;
           this.username = username;
           this.password = password;
           this.email = email;
       }
   
       public Integer getId() {
           return id;
       }
   
       public void setId(Integer id) {
           this.id = id;
       }
   
       public String getUsername() {
           return username;
       }
   
       public void setUsername(String username) {
           this.username = username;
       }
   
       public String getPassword() {
           return password;
       }
   
       public void setPassword(String password) {
           this.password = password;
       }
   
       public String getEmail() {
           return email;
       }
   
       public void setEmail(String email) {
           this.email = email;
       }
   
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", username='" + username + '\'' +
                   ", password='" + password + '\'' +
                   ", email='" + email + '\'' +
                   '}';
       }
   }
   
   ~~~

   

3. 编写工具类（JdbcUtils）

   导入druid、mysql-connector包

   **注：Connection 依赖的包 import java.sql.Connection;**

   1. 与顶层包（org.studyCSL.pro）同级写配置文件jdbc.properties

      ~~~java
      username=root
      password=mysql123
      url=jdbc:mysql://localhost:3306/book?useUnicode=true&characterEncoding=utf8
      driverClassName=com.mysql.jdbc.Driver
      initialSize=5
      maxActive=10
      ~~~

      

   2. 构造连接数据库函数

      - 建立Properties对象
      - 读取配置文件，获得输入流InputStream
      - 从流中加载(load)数据
      - 创建数据库连接池dataSource
      - 利用连接池getConnection获取数据库连接

      ~~~java
      package org.studyCSL.utils;
      
      import com.alibaba.druid.pool.DruidDataSource;
      import com.alibaba.druid.pool.DruidDataSourceFactory;
      
      
      import java.io.InputStream;
      import java.sql.Connection;
      import java.sql.SQLException;
      import java.util.Properties;
      
      public class JdbcUtils {
      
      
          private static DruidDataSource dataSource;
          static {
      
              try {
      
                  Properties properties = new Properties();
                  // 读取配置文件 "jdbc.properties"
                  InputStream resourceAsStream = JdbcUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
                  // 从流中加载数据
                  properties.load(resourceAsStream);
                  // 创建数据库连接池
                  dataSource = (DruidDataSource) DruidDataSourceFactory.createDataSource(properties);
      
              } catch (Exception e) {
                  e.printStackTrace();
              }
      
      
          }
      
          /**
           * 获取数据库连接
           * @return
           */
          public static Connection getConnection() {
      
              Connection conn = null;
      
              try {
                  conn = dataSource.getConnection();
              }catch (Exception e ){
                  e.printStackTrace();
              }
              return conn;
          }
      
          /**
           *  关闭数据库连接
           */
          public static void close(Connection conn ){
      
              if (conn != null){
                  try {
                      conn.close();
                  } catch (SQLException e) {
                      e.printStackTrace();
                  }
              }
          }
      
      
      }
      
      ~~~

   **测试：**

   导入测试包junit4-12.jar、hamcrest-core-1.3.jar

   ctrl shift t 生成测试对象类至test包

   ~~~java
   public class JdbcUtilsTest {
   
       /**
        *  测试数据库连接情况
        */
       @Test
       public void testJdbc(){
           System.out.println(JdbcUtils.getConnection());
       }
   }
   ~~~

   

4. 编写BaseDao类

   导入DbUtils包（common-dbUtils-1.3.jar）操作数据库

   对数据库增删改查的函数进行统一定义

   **args：代表不定长参数**

   **Class<T> type：利用泛型，可以让这个dao接受任何JavaBean**

   ~~~java
   package org.studyCSL.dao.impl;
   
   import org.apache.commons.dbutils.QueryRunner;
   import org.apache.commons.dbutils.ResultSetHandler;
   import org.apache.commons.dbutils.handlers.BeanHandler;
   import org.apache.commons.dbutils.handlers.BeanListHandler;
   import org.apache.commons.dbutils.handlers.ScalarHandler;
   import org.studyCSL.utils.JdbcUtils;
   
   import javax.management.Query;
   import java.sql.Connection;
   import java.sql.ResultSet;
   import java.sql.SQLException;
   import java.util.List;
   
   public abstract class BaseDao {
       //使用DbUtils操作数据库
       // 导包
   
       private QueryRunner queryRunner = new QueryRunner();
   
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
           }finally {
               JdbcUtils.close(connection);
           }
           return -1;
       }
   
       /**
        *  查询返回 一个 JavaBean对象的sql语句
        * @param type  返回的对象类型
        * @param sql
        * @param args
        * @param <T>   返回类型的泛型
        * @return
        */
       public <T> T queryForOne(Class<T> type, String sql ,Object ...args){
           Connection connection = JdbcUtils.getConnection();
           try {
               return queryRunner.query(connection,sql,new BeanHandler<T>(type),args);
           } catch (SQLException e) {
               e.printStackTrace();
           }finally {
               JdbcUtils.close(connection);
           }
           return null;
       }
   
       /**
        *  查询返回多个Javabean语句
        * @param type
        * @param sql
        * @param args
        * @param <T>
        * @return
        */
   
       public <T>List<T> queryForList(Class<T> type, String sql ,Object ...args){
   
           Connection con = JdbcUtils.getConnection();
           try {
               return queryRunner.query(con,sql,new BeanListHandler<>(type),args);
           } catch (SQLException e) {
               e.printStackTrace();
           }finally {
               JdbcUtils.close(con);
           }
           return null;
       }
   
       /**
        *  执行返回一行一列的sql，单独拿一个值
        * @param sql
        * @param args sql对应的参数值
        * @return
        */
       public Object queryForSingleValue(String sql ,Object ...args){
   
           Connection connection = JdbcUtils.getConnection();
           try {
               return queryRunner.query(connection,sql, new ScalarHandler(),args);
           } catch (Exception e) {
               e.printStackTrace();
           }finally {
               JdbcUtils.close(connection);
           }
           return null;
       }
   
   }
   
   ~~~

   

5. 编写UserDao接口，此接口用于服务接口实现调用

   **注：改`package org.studyCSL.dao.impl;`为`package org.studyCSL.dao;`**

   其实就是定义服务层可以对数据库进行的操作

   ~~~java
   package org.studyCSL.dao;
   
   import org.studyCSL.pojo.User;
   
   public interface UserDao {
   
       /**
        *  根据用户名查询用户信息
        * @param username
        * @return 返回null则没有这个用户
        */
       public User queryUserByUsername(String username);
   
       /**
        *  保存用户信息至数据库
        * @param user
        * @return -1表示操作失败
        */
       public int saveUser(User user);
   
       /**
        *  根据用户名和密码查询用户信息
        * @param username
        * @return null 用户名或密码错误
        */
       public User queryUserByUsernameAndPassword(String username,String password);
   
   
   
   }
   
   ~~~

   

   **写UserDao接口的实现类（UserDaoImpl.java）**

   

   - 该类实现（implement）UserDao接口，继承（extends）自BaseDao（因为要对数据库进行操作，而数据库的操作全部在Base利用泛型实现了）

   - 其实就是封装sql语句，利用BaseDao里面的方法去实现服务层对数据库的操作

     

   ~~~java
   package org.studyCSL.dao.impl;
   
   import org.studyCSL.dao.UserDao;
   import org.studyCSL.pojo.User;
   
   public class UserDaoImpl extends BaseDao implements UserDao {
   
       // 继承可调用父类方法，实现接口里面的根据不同条件的查询
   
       @Override
       public User queryUserByUsername(String username) {
           String sql = "select `id`,`username`,`password`,`email` from t_user where username = ?";
           return queryForOne(User.class,sql,username);
       }
   
       @Override
       public User queryUserByUsernameAndPassword(String username, String password) {
           String sql = "select `id`,`username`,`password`,`email` from t_user where username = ? and password = ?";
           return queryForOne(User.class,sql,username,password);
       }
   
       @Override
       public int saveUser(User user) {
           String sql = "insert into t_user (`username`,`password`,`email`) VALUES (?,?,?)";
           return update(sql,user.getUsername(),user.getPassword(),user.getEmail());
       }
   
   
   }
   
   ~~~

   **测试：**

   **注意：创建Dao的实例的时候，创建的类都是Dao的impl实例**，Service层同理

   UserService userService = new UserServiceImpl();

   ~~~java
   package org.studyCSL.test;
   
   import org.junit.Test;
   import org.studyCSL.dao.UserDao;
   import org.studyCSL.dao.impl.UserDaoImpl;
   import org.studyCSL.pojo.User;
   
   import static org.junit.Assert.*;
   
   public class UserDaoTest {
   
       UserDao userDao = new UserDaoImpl();
   
       @Test
       public void queryUserByUsername() {
   
           System.out.println(userDao.queryUserByUsername("admin"));
       }
   
       @Test
       public void saveUser() {
           System.out.println(userDao.saveUser(new User(null,"csl8489","123456","wryyyyy14@163.com")));
       }
   
       @Test
       public void queryUserByUsernameAndPassword() {
           System.out.println(userDao.queryUserByUsernameAndPassword("admin","admin"));
       }
   }
   ~~~

   

6. 书写Service接口，定义web层可以调用的服务

   **传入的参数都是User的JavaBean**

   ~~~java
   package org.studyCSL.service;
   
   import org.studyCSL.pojo.User;
   
   public interface UserService {
   
       /**
        *  用户注册
        * @param user
        */
       public void registerUser(User user);
   
       /**
        *  登录
        * @param user
        * @return 返回null 登录失败，有值，登录称国
        */
       public User login(User user);
   
       /**
        *  检测用户名是否存在
        * @param username
        * @return true 表示用户名已存着， false表示用户名可用
        */
       public boolean existUsername(String username);
   
   }
   
   ~~~

   

   **服务实现类**

   ~~~java
   package org.studyCSL.service.impl;
   
   import org.studyCSL.dao.UserDao;
   import org.studyCSL.dao.impl.UserDaoImpl;
   import org.studyCSL.pojo.User;
   import org.studyCSL.service.UserService;
   
   public class UserServiceImpl implements UserService {
   
       private UserDao userDao = new UserDaoImpl();
   
       @Override
       public void registerUser(User user) {
   
           // 注册前先判断是否有相同用户名
           //if (userDao.queryUserByUsername(user.getUsername()) == null){
           userDao.saveUser(user);
   //        }else {
   //            System.out.println("用户名或密码已存在，请重新注册");
   //        }
   
       }
   
       @Override
       public User login(User user) {
           return userDao.queryUserByUsernameAndPassword(user.getUsername(),user.getPassword());
       }
   
       @Override
       public boolean existUsername(String username) {
   
           if (userDao.queryUserByUsername(username) == null){
               // 没查到表示可用
               return false;
           }
   
           return true;
       }
   }
   
   ~~~

   

   **测试：**

   

   ~~~java
   package org.studyCSL.test;
   
   import org.junit.Test;
   import org.studyCSL.pojo.User;
   import org.studyCSL.service.UserService;
   import org.studyCSL.service.impl.UserServiceImpl;
   
   import static org.junit.Assert.*;
   
   public class UserServiceTest {
   
       UserService userService = new UserServiceImpl();
   
       @Test
       public void registerUser() {
           userService.registerUser(new User(null,"csl77","cslcch123","wwwcslcc@163.com"));
           userService.registerUser(new User(null,"abc778","cslcch123","wwwcslcch@163.com"));
       }
   
       @Test
       public void login() {
           System.out.println(userService.login(new User(null,"csl777","123488856",null)));;
       }
   
       @Test
       public void existUsername() {
           if (!userService.existUsername("csl779")){
               System.out.println("用户名可用");
           }else {
               System.out.println("用户名不可用");
           }
       }
   }
   ~~~

   

7. web层调用













导入的包需要注意：

1. 
2. 



创建接口





调节控件的路径：

1. 利用base标签限制相对路径
2. 全部href、src都写成绝对路径