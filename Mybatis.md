## Mybatis



### 1.环境搭建



#### 1.1 步骤

1. 创建maven工程

   1. 写groupid（pom.xml包名）、artifactid（对应项目名称）

2. 在pom.xml中写依赖（导入坐标）

   1. 先写dependencies作为放各个dependency的标签

   2. 写一个dependency，然后如果环境内没有，则右下角会提示，点击`import changes`就会自动下载

   3. 示例

      ~~~xml
          <dependencies>
              <dependency>
                  <groupId>org.mybatis</groupId>
                  <artifactId>mybatis</artifactId>
                  <version>3.4.5</version>
              </dependency>
              <dependency>
                  <groupId>mysql</groupId>
                  <artifactId>mysql-connector-java</artifactId>
                  <version>5.1.6</version>
              </dependency>
              <dependency>
                  <groupId>log4j</groupId>
                  <artifactId>log4j</artifactId>
                  <version>1.2.12</version>
              </dependency>
              <dependency>
                  <groupId>junit</groupId>
                  <artifactId>junit</artifactId>
                  <version>4.10</version>
              </dependency>
          </dependencies>
      ~~~

3. 创建实体类和dao接口

   1. 在src/main/java下创建：org.csl.dao.IUserDao
   2. 在src/main/java下创建：org.csl.domain.User

4. 创建Mybatis的主配置文件

   1. 在src/resources/下创建：SqlMapConfig.xml

      ~~~xml
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE configuration
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-config.dtd">
      <!--mybatis的主配置文件-->
      <configuration>
          <!--配置环境-->
          <environments default="mysql">
              <!--配置mysql的环境-->
              <environment id="mysql">
                  <!--配置事务的类型-->
                  <transactionManager type="JDBC"></transactionManager>
                  <!--配置数据连接池-->
                  <dataSource type="POOLED">
                      <!--配置连接数据库的基本信息-->
                      <property name="driver" value="com.mysql.jdbc.Driver"/>
                      <property name="url" value="jdbc:mysql://localhost:3306/eesy"/>
                      <property name="username" value="root"/>
                      <property name="password" value="mysql123"/>
                  </dataSource>
              </environment>
          </environments>
      
          <!--指定映射配置文件的位置，即每个dao独立的配置文件-->
          <mappers>
              <mapper resource="com.csl.dao.IUserDao.xml"></mapper>
          </mappers>
      </configuration>
      ~~~

      

5. 创建映射配置文件

   1. 在src/resources/下创建：org.csl.dao.IUserDao.xml

      ~~~xml
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      
      <mapper namespace="com.csl.dao.IUserDao">
          <!--配置查询所有-->
          <select id="findAll">
              select  * from user
          </select>
      </mapper>
      ~~~

      

6. 注意事项

   - 在Mybatis中把持久层的操作接口名称和映射文件叫做Mapper，所以IUserDao和IUserMapper的意思是一样的

   - 在创建目录和包的时候默认一级结构

     转换成三级结构：项目管理器的右上角齿轮-取消紧凑中间包的勾选

   - **Mybatis的映射配置文件位置必须和dao接口的包结构相同**

   - **映射配置文件的mapper标签的namespace属性取值必须是dao接口的全限定类名**(org.csl.dao.IUserDao)

   - **映射配置文件的操作配置（select），id属性值必须是dao接口的方法名**
   
   - sql语句不用写分号



#### 1.2 出现的问题

1. 报错：Cause: java.sql.SQLException: Unknown initial character set index '255' received from server.

   原因： 驱动与数据库字符集不匹配 

   解决办法：在url中添加字符集说明：java"jdbc:mysql://localhost:3306/eesy?characterEncoding=utf8" 

2. 注意读入的Resources包应该是是import org.apache.ibatis.io.Resources;



### 2.入门案例

1. 读取配置文件
2. 创建SqlSessionFactory工厂
3. 创建SqlSession
4. 创建Dao接口的代理对象
5. 执行Dao中的方法
6. 释放资源



样例代码：

~~~java
public class MybatisTest {

    public static void main(String[] args) throws Exception{
        // 1.读取配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
        // 2.创建SqlSessionFactory工厂-工厂模式
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        // 3.使用工厂生产SqlSession对象
        SqlSession session = factory.openSession();
        // 4.使用SqlSession创建Dao接口的代理对象
        IUserDao userDao = session.getMapper(IUserDao.class);
        // 5.使用代理对象执行方法
        List<User> users = userDao.findAll();
        // 6。释放资源
        for (User user : users){
            System.out.println(user);
        }
        session.close();
        in.close();


    }
}

~~~



##### 2.1 样例分析

![1609900987940](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1609900987940.png)

注意：在映射配置文件中设置resultType属性指定要封装的实体类，属性值为实体类的全限定类名



##### 2.2 基于注解的mybatis

1. 在dao接口方法上使用@Select注解，并且指定Sql语句
2. 在SqlMapConfig.xml中的mapper配置中使用class属性指定dao接口的全限定类名



样例：

~~~java
/**
 *  用户的持久层接口
 */
public interface IUserDao {

    /**
     *  查询所有操作
     * @return
     */
    @Select("select * from user")
    List<User> findAll();
}
~~~

~~~xml
    <mappers>
        <mapper class="org.csl.dao.IUserDao"/>
    </mappers>
~~~



缺点：对于复杂的sql查询没有优势。



### 3.  解析过程分析



![1609906724346](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1609906724346.png)

![1609906666580](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1609906666580.png)



**理解：完成查询的主干还是JDBC的流程，通过读取解析配置文件来获取JDBC需要的信息，信息包括两个部分。**

**主干：获取Connection对象-获取预处理对象-执行查询获得结果集-遍历封装结果集对象-返回封装好的结果（可以是List、也可以是Object）**



### 4.UserMapper新增方法（扩展CRUD）



1. 在接口类里新增方法（以增加为例）src/main/java/org/csl/dao/**IUserDao.java**

   ~~~java
       /**.
        * 保存用户
        * @param user
        */
       void saveUser(User user);
   ~~~

   

2. 修改映射配置文件Resources/org/csl/dao/**IUserDao.xml**

   ~~~xml
       <!--保存用户-->
       <insert id="saveUser" parameterType="org.csl.domain.User">
       --         配置插入操作后，获取插入数据的id
       <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
           select last_insert_id()
       </selectKey>
           insert into user(username,birthday,sex,address)values(#{username},#{birthday},#{sex},#{address});
       </insert>
   ~~~

   

   说明：

   1. <selectKey></selectKey>可以保证在插入之后返回插入用户的id，在Test中调用`user.getId`即可获取

   2. parameterType：传入Sql语句中的参数类型

   3. resultType：结果集中每个元素的数据类型

   4. keyProperty：对应的实体类属性名称

   5. keyColumn：对应表的列名

   6. order：什么时候执行这个操作，这里AFTER，表示在插入之后进行执行

   7. 参数的占位：#{}

      ![1610004312837](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610004312837.png)

      **在#{}下，使用模糊查询，百分号和输入作为实参数输入Sql，即：userDao.findByName("%王%")**

      

      #{}为参数设置占位符，${}为字符串替换，在编译时候会转换成整个sql语句，而#{}则会变成`?`占位符。

      ![1615881516858](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615881516858.png)
   
   8. 
   
   
   
   ~~~java
       /**
        * 测试保存数据
        */
       @Test
       public void testSave() {
           User user = new User(null,"mybatis saveuser2",new Date(),"男","北京市顺义区");
           System.out.println(user);
           // 5.执行保存
           userDao.saveUser(user);
           System.out.println(user);
       }
   
   ~~~




### 5.封装输入参数



情景：用于查询的实际参数不仅仅只有一个对象（一种类型），而有其他类型的参数，可以通过封装输入参数成queryVo对象来实现在映射配置文件中的参数的使用。

适用：多表联查，多对象查询



#### 5.1 使用方法

##### 5.1.1 建立queryVo对象

~~~java
public class QueryVo {

    private User user;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }
}
~~~



##### 5.1.2 在IUserDao.xml中使用

~~~xml
    <!--根据queryVo的条件查询用户-->
    <select id="findUserByVo" parameterType="org.csl.domain.QueryVo" resultType="org.csl.domain.User">
        select * from user where username like #{user.username}
    </select>
~~~

可以使用`.`运算获取对应的封装类型参数



##### 5.1.3 测试

~~~java
    /**
     *  测试根据用户名查找数据利用QueryVo的OGNL表达式
     *  主要思想就是把一种情况：传入的参数可能不仅是一种类型，比如可能有User和其他类型的参数（多对象查询、多表查询）
     *  把传入的参数封装成一个queryVo的对象，在xml文件中可以使用 vo的属性名.参数名 ，获取输入的参数，使得传入的参数更灵活
     */
    @Test
    public void testFindUserByVo(){
        QueryVo vo = new QueryVo();
        User user = new User();
        user.setUsername("%王%");
        vo.setUser(user);
        List<User> users = userDao.findUserByVo(vo);
        for (User u : users){
            System.out.println(u);
        }
    }
~~~





### 6.properties标签和typeAlias标签



#### 6.1 properties

##### 6.1.1 主要作用

将datasource中property标签的value值从properties标签里的内容获取

##### 6.1.2 使用方法

- 在datasource的property中：value="${driver}"

  ~~~xml
              <!--配置连接池-->
              <dataSource type="POOLED">
                  <!--如果引用外部properties，那么在datasource标签内的property标签的value属性的${}必须与外部properties文件的变量名一致-->
                  <property name="driver" value="${jdbc.driver}"/>
                  <property name="url" value="${jdbc.url}"/>
                  <property name="username" value="${jdbc.username}"/>
                  <property name="password" value="${jdbc.password}"/>
              </dataSource>
  ~~~

  **注意：如果用resource或url属性引用外部properties，那么在datasource标签内的property标签的value属性的${}必须与外部properties文件的变量名一致**

- 在properties中：

  ~~~xml
      <properties>
  <!--        内部配置-->
          <property name="driver" value="com.mysql.jdbc.Driver"/>
          <property name="url" value="jdbc:mysql://localhost:3306/eesy?characterEncoding=utf8"/>
          <property name="username" value="root"/>
          <property name="password" value="mysql123"/>
      </properties>
  ~~~



**引用外部properties:**

在properties指定resource属性或url属性

- resource属性默认在工程的Resources下的文件
- url可以指定磁盘中的文件

         url属性：resource属性指定的是在该工程中的Resources目录下的文件，而url可以指定在磁盘中的绝对路径，但是这个路径必须是英文。
            依照的url写法：协议 主机 端口号 URI
            例如磁盘中：file:///C:/Users/，///表示省略了主机号和端口号，表示默认localhost:8000或localhost:8000
~~~xml
    <properties resource="jdbcConfig.properties"></properties>
~~~



**当数据库出现时区错误**



修改配置文件中的jdbc.url=

 jdbc:mysql://localhost:3306/你的数据库名?serverTimezone=UTC&useUnicode=true&characterEncoding=utf8&useSSL=true 





#### 6.2 typeAilas



##### 6.2.1 主要作用

建立全限定类名/接口/包名与自己设定的名字的映射，建立映射后不区分大小写



##### 6.2.2 使用方法

type属性：为实体类/接口/包的全限定名

alias属性：为前面type的别名



类：

~~~xml
<typeAlias type="org.csl.domain.User" alias="user"></typeAlias>
~~~



package标签：用于要配置别名的包，指定之后，该包下的实体类都会注册别名，并且类名就是别名，不区分大小写

包：

~~~xml
<typeAlias>
    <package name="org.csl.domain">
    </package>
</typeAlias>
~~~



接口：

在mappers标签下

指定dao接口所在的包，当指定之后就不需要写mapper以及resource/class

**默认在Resources下的org.csl.dao**

~~~xml
<package name="org.csl.dao"></package>
~~~





### 7.连接池与事务控制





#### 7.1连接池

![1610102378220](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610102378220.png)



POOLED与UNPOOLED对比

![1610101368252](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610101368252.png)



##### 7.1.1UNPOOLED

每次都创建



##### 7.1.2POOLED原理分析



获取连接时分为空闲池和活动池

空闲池idleConnection数据结构为一个ArrayList。



如果空闲池还有连接则先拿空闲池，空闲池没有就去看活动池连接有没达到最大数量，没有就创建连接放入活动池；有通过队列方式(Oldest)将最先进入到活动池的连接返回给新的连接使用。

![1610101961419](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610101961419.png)



##### 7.1.3 JNDI数据源







#### 7.2 事务控制



##### 7.2.1事务原理



##### 7.2.2 自动提交



在opensession方法中传入true

![1610103014131](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610103014131.png)







### 8.动态Sql语句



与JSTL标签库相似

配置查询结果列名（数据库字段）和实体类属性名的对应关系

~~~xml
    <!-- 配置 查询结果的列名和实体类的属性名的对应关系 -->
    <resultMap id="userMap" type="uSeR">
        <!-- 主键字段的对应 -->
        <id property="userId" column="id"></id>
        <!--非主键字段的对应-->
        <result property="userName" column="username"></result>
        <result property="userAddress" column="address"></result>
        <result property="userSex" column="sex"></result>
        <result property="userBirthday" column="birthday"></result>
    </resultMap>
~~~

在配置中可以配置属性resultMap，相当于实体类User

~~~xml
    <select id="findUserByCondition" resultMap="userMap" parameterType="user">
~~~



##### 8.1 if标签



用于在sql语句中判断

用法：

~~~xml
            <if test="username != null">
                and username=#{username}
            </if>
            <if test="sex != null">
                and sex=#{sex}
            </if>
~~~



##### 8.2 where标签

用于代替where语句

用法：

~~~xml
        select * from user
        <where>
            <if test="username != null">
                and username=#{username}
            </if>
            <if test="sex != null">
                and sex=#{sex}
            </if>
        </where>
~~~





##### 8.3 foreach标签



用于集合查询、指定查询

语句：select * from user where id in(41,42,46)

查询id为41，42，46的用户信息



用法：

~~~xml
    <select id="findUserInIds" resultType="org.csl.domain.User">
        select * from user
        <where>
            <if test="ids != null and ids.size()>0">
                <foreach collection="ids" open="and id in (" close=")" item="id" separator=",">
                    #{id}
                </foreach>
            </if>
        </where>
    </select>
~~~



说明：

后跟语句：以open开头，close结束，中间用separator隔开，元素为item，元素对应的集合为collection，拼接成上述语句

标签内容：#{id}与item="id"一致



##### 8.4 sql标签







### *9.多表联查

以用户和账户为例



#### 9.1 一对一

建立两表联查的结果对应的实体类AccountUser（继承自Account）

将sql两表联查的数据存入AccountUser

对应的IAccountDao.xml

~~~xml
    <select id="findAllAccount" resultType="org.csl.domain.AccountUser">
        select a.*,u.username,u.address from account a,user u where u.id = a.uid;
    </select>
~~~





#### 9.2 一对多和多对一

一个用户可以有多个账户



**两张表User和Account**

步骤：

1. 在User实体类中建立账户列表List<Account>，并生成对应的set、get方法，从而建立一对多的结构

2. 在映射配置文件中建立实体类和表列名映射

   ~~~xml
       <!--建立查询结果和表的映射-->
       <resultMap id="userAccount" type="org.csl.domain.User">
           <id property="id" column="id"></id>
           <result property="username" column="username"></result>
           <result property="birthday" column="birthday"></result>
           <result property="sex" column="sex"></result>
           <result property="address" column="address"></result>
           <!--配置User对象中accounts集合映射-->
           <collection property="accounts" ofType="org.csl.domain.Account">
               <id column="aid" property="id"></id>
               <result column="uid" property="uid"></result>
               <result column="money" property="money"></result>
           </collection>
       </resultMap>
   
   ~~~

   ~~~xml
       <!--查询所有用户-->
       <select id="findAll" resultMap="userAccount">
           select u.*,a.id as aid,a.uid,a.money  from user u left outer join account a on u.id = a.uid
       </select>
   ~~~

   说明：

   - property属性表示的是实体类的类名，column为实体类对应表的列名，如果在查询中另外取了名字，则column就为对应的名字，**如a.id as aid,对应集合的collection中的id标签的column就应该为aid**

   - collection用于配置集合，将查询到一个实体类对应的集合（一个用户对应的多个账户）放到集合中，集合的`property`则为实体类中对应的List变量名

   - 对于type和ofType对应的都是返回元素的全限定类名，如果在SqlMapConfig或Dao.xml中配置了对应的typeAilas则可以使用typeAlias规范的写法

     

3. 写select语句（左外连接，求并集）

   ~~~xml
       <!--查询所有用户-->
       <select id="findAll" resultMap="userAccount">
           select u.*,a.id as aid,a.uid,a.money  from user u left outer join account a on u.id = a.uid
       </select>
   ~~~

   



#### 9.3 多对多

![1610186587649](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610186587649.png)



三张表：User表、user_role表，Role表

user_role表存储User表和Role表的id映射，即每一个User的id，对应的Role的id



##### 9.3.1 从Role到user_role-User到User

1. 在Role中建立Role与User的关系：List<User> users，并生成set，get方法;

2. 在IRoleDao.xml建立返回类型映射配置resultMap

   ~~~xml
       <!--建立查询结果和表的映射-->
       <!--定义role表的ResultMap-->
       <!--property 为属性名，column为表列名-->
       <resultMap id="roleMap" type="org.csl.domain.Role">
           <id property="roleId" column="rid"></id>
           <result property="roleName" column="role_name"></result>
           <result property="roleDesc" column="role_desc"></result>
           <collection property="users" ofType="org.csl.domain.User">
               <id column="id" property="id"></id>
               <result column="username" property="username"></result>
               <result column="birthday" property="birthday"></result>
               <result column="sex" property="sex"></result>
               <result column="address" property="address"></result>
           </collection>
       </resultMap>
   ~~~

   

3. 通过左外连接写select语句

   ~~~xml
       <select id="findAll" resultMap="roleMap">
           SELECT u.*,r.`ID` AS rid,r.`ROLE_NAME`,r.`ROLE_DESC` FROM role r
           LEFT OUTER JOIN user_role ur ON r.`ID`=ur.`RID`
           LEFT OUTER JOIN USER u ON  ur.`UID`=u.`id`
       </select>
   ~~~

   

   对应的输出结果（将Role和Role对象中的User对象集合都输出）：

   ![1610187118490](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610187118490.png)

   ![1610187056258](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610187056258.png)



##### 9.3.2 从User到role_user到Role

其实就是上面的例子反过来，修改一sql和resultMap即可，不做多说明。



1. 建立关系，在User中设置List<Role>  roles，并且生成set，get方法

2. 在IUserDao.xml中建立属性和表的映射，用于接收查询结果

   ~~~xml
       <!--建立查询结果和表的映射-->
       <resultMap id="userMap" type="org.csl.domain.User">
           <id property="id" column="id"></id>
           <result property="username" column="username"></result>
           <result property="birthday" column="birthday"></result>
           <result property="sex" column="sex"></result>
           <result property="address" column="address"></result>
           <collection property="roles" ofType="org.csl.domain.Role">
               <id column="rid" property="roleName"></id>
               <result column="role_name" property="roleName"></result>
               <result column="role_desc" property="roleDesc"></result>
           </collection>
       </resultMap>
   ~~~

3. 通过左外连接写Sql语句

   ~~~xml
       <!--查询所有用户-->
       <select id="findAll" resultMap="userMap">
                   SELECT u.*,r.`ID` AS rid,r.`ROLE_NAME`,r.`ROLE_DESC` FROM user u
                    LEFT OUTER JOIN user_role ur ON ur.`UID`=u.`id`
                    LEFT OUTER JOIN role r ON  r.`ID`=ur.`RID`
       </select>
   ~~~

   

   对应的输出结果：

   ![1610187468580](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610187468580.png)

   ![1610187491513](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610187491513.png)











### 10.mybatis延迟加载



##### 10.1 概述

在需要使用的时候调用对方的配置文件来查询，不用则不查询。



比如当查出的对象还需要get一个属性的时候，可以再查。



##### 10.2一对一延迟加载

步骤：

1. 在全局配置文件中开启延迟加载

   ![1610289243554](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610289243554.png)

   ~~~xml
       <settings>
           <!--开启全局延迟加载开关-->
           <setting name="lazyLoadingEnabled" value="true"/>
           <setting name="aggressiveLazyLoading" value="false"/>
       </settings>
   ~~~

2. 在IAccountDao.xml映射配置文件中，设置关联对象，利用association标签指定关联对象、方法、参数

   ~~~xml
       <resultMap id="accountMap" type="org.csl.domain.Account">
           <id property="id" column="id"/>
           <result property="uid" column="uid"/>
           <result property="money" column="money"/>
           <!-- 一对一关系映射，配置封装user的内容
               select 属性指定的内容为查询用户的唯一标识
               column 属性指定用户根据id查询时，所需要的参数信息
           -->
           <association property="user" column="uid" javaType="org.csl.domain.User" select="org.csl.dao.IUserDao.findById">
   
           </association>
       </resultMap>
   ~~~

   **说明：**

   property标识Account中设置的User对象属性，用来存放查到的User

   column指定用于按需查询的参数

   javaType用于指定返回类型

   select用于指定按需查询的时候需要调用的配置文件中的操作(Sql)

   

3. 设置操作的返回类型

   IAccountDao.xml

   ~~~xml
       <!--根据用户id查询账户列表-->
       <select id="findAll" resultMap="accountMap">
           select * from account
       </select>
   ~~~

   IUserDao.xml

   ~~~xml
       <!--根据id查询用户-->
       <select id="findById"  parameterType="java.lang.Integer" resultType="org.csl.domain.User">
           select * from user where id=#{id}
       </select>
   ~~~

   

   

##### 10.3一对多延迟加载



1. 同上

2. 设置关联对象

   ~~~xml
       <!--建立查询结果和表的映射-->
       <resultMap id="userAccount" type="org.csl.domain.User">
           <id property="id" column="id"></id>
           <result property="username" column="username"></result>
           <result property="birthday" column="birthday"></result>
           <result property="sex" column="sex"></result>
           <result property="address" column="address"></result>
           
           <!--配置User对象中accounts集合映射-->
           <collection property="accounts" ofType="org.csl.domain.Account" select="org.csl.dao.IAccountDao.findAccountByUid" column="id"></collection>
       </resultMap>
   
   ~~~

3. 调整操作返回类型

   IUserDao.xml

   ~~~xml
       <select id="findAll" resultMap="userAccount">
           select * from user
       </select>
   ~~~

   IAccountDao.xml

   ~~~xml
       <select id="findAccountByUid" resultType="org.csl.domain.Account">
           select * from account where uid = #{uid}
       </select>
   ~~~

   说明：这里的`#{uid}`不一定需要是uid，id也可以，用于命名获取传过来的参数





### 11.mybatis缓存



#### 11.1 概念

![1610276178663](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610276178663.png)





#### 11.2 一级缓存



##### 11.2.1 概述

一级缓存是SqlSession范围的缓存，当调用SqlSession的修改、添加、删除，commot()，close()等方法就会清空一级缓存。



例如：连续两次查询，并且查询的相同的信息，第二次查询就不会再去数据库查询，而是在SqlSession的缓存区中查询。

##### 11.2.2方法





#### 11.3 二级缓存



##### 11.3.1概述

二级缓存是在SqlSessionFactory级的缓存，



![1610287432305](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610287432305.png)



![1610287863836](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610287863836.png)



##### 11.3.2 配置步骤

1. 在全局配置文件SqlMapConfig.xml中设置

   ~~~xml
       <settings>
           <setting name="cacheEnabled" value="true"/>
       </settings>
   ~~~

   注意setting标签的位置，应该在properties标签的后面，typeAliases的前面。

   原因是当mybatis内核文件解析SqlMapConfig.xml生成configuration对象时，是按照以下顺序进行将标签转化成参数构造configuration对象的

   **(properties?,settings?,typeAliases?,typeHandlers?,objectFactory?,objectWrapperFactory?,reflectorFactory?,plugins?,environments?,databaseIdProvider?,mappers?)"**

   

2. 在对应的dao映射文件中设置

   在mapper标签下：

   ~~~xml
       <cache/>
   ~~~

   ![1610288352255](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610288352255.png)

   

3. 配置操作支持二级缓存

   ~~~xml
       <select id="findById"  parameterType="java.lang.Integer" resultType="org.csl.domain.User" useCache="true">
   ~~~

4. 测试

   ~~~java
       @Test
       public void testFirstLevelCache(){
           SqlSession sqlSession = factory.openSession();
           IUserDao userDao = sqlSession.getMapper(IUserDao.class);
           User user = userDao.findById(41);
           System.out.println(user);
           sqlSession.close();// 关闭一级缓存
   
           SqlSession sqlSession2 = factory.openSession();
           IUserDao userDao2 = sqlSession2.getMapper(IUserDao.class);
           User user1 = userDao2.findById(41);
           System.out.println(user1);
           sqlSession2.close();
   
   
           System.out.println(user == user1);// true
   
       }
   ~~~

   ![1610288706096](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610288706096.png)

   说明：虽然显示的是两个对象，但是后面的那个对象是用于存放用户数据的对象，从日志可以看到，并没有发起第二次查询，而是从cache中拿到数据放到这个新建的对象中，二级缓存区中存储的都是数据。







### 12.注解开发



CRUD：@Select、@Insert、@Update、Delete



##### 12.1 注解配置属性和列表名对应（与.xml的resultMap功能相同）



##### 12.1.1 配置方法

添加注解：

~~~java
    /**
     *  查询所有账户，并且获取每个账户所属的用户信息
     * @return
     */
    @Select("select * from account ")
    @Results(id = "resultMap", value = {
            @Result(id = true, column = "id", property = "id"),
            @Result(column = "uid", property = "uid"),
            @Result(column = "money", property = "money"),
            @Result(property = "user", column = "uid", one = @One(select="org.csl.dao.IUserDao.findById",fetchType = FetchType.EAGER))
    })
    List<Account> findAll();
~~~

说明：

1. Results：id属性指定这个查询返回块的id，其他接口可以通过@ResultMap(value={"id"})使用这个映射，property属性，column表列名
2. 最后一个Result，利用uid作为参数，实现一对一查询，**利用注解One（select，fetchType），利用全限定类名.方法名的方法调用另外一个接口的操作**
3. fetchType是延时查询，EAGER是立即，LAZY是延时



#### 12.2 单表

只写CRUD注解然后测试。



##### 12.3 一对一（多对一）



在Account中添加User属性，然后在@Results中配置即可

```java
/**
 *  查询所有账户，并且获取每个账户所属的用户信息
 * @return
 */
@Select("select * from account ")
@Results(id = "resultMap", value = {
        @Result(id = true, column = "id", property = "id"),
        @Result(column = "uid", property = "uid"),
        @Result(column = "money", property = "money"),
        @Result(property = "user", column = "uid", one = @One(select="org.csl.dao.IUserDao.findById",fetchType = FetchType.EAGER))
})
List<Account> findAll();
```
~~~java
    /**
     *  根据id查询用户
     * @param id
     * @return
     */
    @Select("select * from user where id=#{id}")
    @ResultMap(value = {"userMap"})
    User findById(Integer id);
~~~





#### 12.4 一对多



在User中添加List<Account>作为一对多映射

~~~java
    /**
     *  查询所有用户，同时获取到用户下所有账户的信息
     * @return
     */
    @Select("select * from user")
    @Results(id = "userMap" ,value = {
            @Result(id=true,column = "id",property = "id"),
            @Result(column = "username",property = "username"),
            @Result(column = "birthday",property = "birthday"),
            @Result(column = "sex",property = "sex"),
            @Result(column = "address",property = "address"),
            @Result(property = "accounts", column = "id",many = @Many(select = "org.csl.dao.IAccountDao.findAccountByUid",fetchType = FetchType.LAZY))
    })
    List<User> findAll();
~~~

~~~java
    /**
     *  根据用户id查询账户信息
     * @param userId
     * @return
     */
    @Select("select * from account where uid = #{userId}")
    List<Account> findAccountByUid(Integer userId);
~~~



**利用注解Many（select，fetchType），利用全限定类名.方法名的方法调用另外一个接口的操作**







#### 12.5 注解开发启动二级缓存



在需要开启二级缓存的接口定义上方添加注解

~~~java
@CacheNamespace(blocking=true)
~~~

![1610366302794](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1610366302794.png)

