## SSM



 ![img](https://gitee.com//wangongzhuo/picgo_image/raw/master///1589898827992.png) 



#### 一、工厂方法解耦





#### 二、Spring的IOC容器

![1611577198408](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611577198408.png)

~~~java
public class Client {

    /**
     * 获取spring的Ioc核心容器，并根据id获取对象
     *
     * ApplicationContext的三个常用实现类：
     *      ClassPathXmlApplicationContext：它可以加载类路径下的配置文件，要求配置文件必须在类路径下。不在的话，加载不了。(更常用)
     *      FileSystemXmlApplicationContext：它可以加载磁盘任意路径下的配置文件(必须有访问权限）
     *
     *      AnnotationConfigApplicationContext：它是用于读取注解创建容器的，是明天的内容。
     *
     * 核心容器的两个接口引发出的问题：
     *  ApplicationContext:     单例对象适用              采用此接口
     *      它在构建核心容器时，创建对象采取的策略是采用立即加载的方式。也就是说，只要一读取完配置文件马上就创建配置文件中配置的对象。spring自行判断多例和单例
     *
     *  BeanFactory:            多例对象使用
     *      它在构建核心容器时，创建对象采取的策略是采用延迟加载的方式。也就是说，什么时候根据id获取对象了，什么时候才真正的创建对象。
     * @param args
     */
    public static void main(String[] args) {
        //1.获取核心容器对象
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
//        ApplicationContext ac = new FileSystemXmlApplicationContext("C:\\Users\\zhy\\Desktop\\bean.xml");
        //2.根据id获取Bean对象
        IAccountService as  = (IAccountService)ac.getBean("accountService");
        IAccountDao adao = ac.getBean("accountDao",IAccountDao.class);

        System.out.println(as);
        System.out.println(adao);
        as.saveAccount();


    }
}
~~~



![1611577757630](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611577757630.png)



##### 2.1 spring对bean的管理



**(resource/bean.xml)**



###### 创建bean的方式

1. 使用默认构造函数创建（无参构造器）

   方式：使用bean标签，配置id和class属性；如果类中没有默认构造，则无法创建

   ~~~xml
   <bean id="accountService" class="org.csl.service.impl.AccountServiceImpl"/>
   ~~~

   

   

2. 使用普通工厂中的方法创建对象（使用某个类中的方法创建对象，并存入spring容器）

   方式：

   ~~~xml
   <bean id="instanceFactory" class="org.csl.factory.InstanceFactory"/>
   <bean id="accountService" factory-bean="instanceFactory" factory-method="getAccountService"/>
   ~~~

   说明：

   **factory-bean指定类，factory-method指定方法**

   

3. 使用工厂中的静态方法创建对象（使用某个类中的静态方法创建对象），并存入spring容器

   方式：

   ~~~xml
   <bean id="accountService" class="org.csl.factory.StaticFactory" factory-method="getAccountService"/>
   ~~~

###### bean的作用范围



    <!--
    	bean的作用范围调整：默认是单例bean
        scope属性指定bean的作用范围
            取值：
                singleton：单例
                prototype：多例
                request：web的请求范围
                session：web的会话范围
                global-session：全局会话范围，非集群时就是session
    
    -->






###### bean对象的生命周期

~~~xml
    <!--bean的生命周期
        单例：
            产生：容器创建时创建
            销毁：容器销毁时销毁
        多例：
            产生：使用对象时创建
            销毁：当对象长时间不用，且没有其他对象引用时，由垃圾回收机制回收
    -->

    <bean id="accountService" class="org.csl.service.impl.AccountServiceImpl" scope="singleton"
          init-method="init" destroy-method="destroy"/>
~~~





###### 调用bean

~~~java
public class Client {

    /**
     *  获取spring的IOC核心容器，并根据id获取对象
     * @param args
     */
    public static void main(String[] args) {
        ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        //2.根据id获取Bean对象
        IAccountService as  = (IAccountService)ac.getBean("accountService");
        as.saveAccount();

        //手动关闭容器,执行销毁方法
        ac.close();
    }
}
~~~



##### 2.2 注入bean

bean.xml



###### 2.2.1 构造函数注入

描述：利用bean标签，将数据注入到标签指定的有参构造器内



使用方法：

~~~xml
    <!--构造函数注入-->
    <!--
        标签：constructor-arg
        使用位置：bean标签内部
        标签属性：
            type：指定要注入的数据类型，该数据类型也是构造函数中某些参数的数据类型
            index：用于指定要注入的数据给构造函数中指定索引位置的参数赋值，索引位置从0开始
            name：用于指定给构造函数中指定名称中的参数赋值
            ========================用于指定给构造函数里的参数赋值========================
            value：用于提供基本类型和String类型的数据
            ref：引用关联的bean对象，指定其他的bean对象（在IOC核心容器中出现过的bean对象 ）

    优点：获取bean对象时，注入数据是必须的操作，否则对象无法创建（没有无参构造器的情况下），在构造完成之后，立即可以使用。
    缺点：必须注入所有的参数，即使有些参数用不上
    -->

    <bean id="accountService" class="org.csl.service.impl.AccountServiceImpl">
        <constructor-arg name="name" value="test"></constructor-arg>
        <constructor-arg name="age" value="18"></constructor-arg>
        <constructor-arg name="birthday" ref="now"></constructor-arg>
    </bean>

    <!--配置日期对象-->
    <bean id="now" class="java.util.Date"/>
~~~



对应的构造器

![1611718741705](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611718741705.png)





###### 2.2.2 set注入

描述：利用对应类中属性的set方法来反射注入数据，获取set方法的方式是利用set和属性名开头大写拼接搜索注入方法



使用方法：

~~~xml
    <!--set方法注入-->
    <!--
        property标签
        使用位置：bean标签内部
        属性:
        name：指定注入是所用的set方法名称

    优点：创建对象时没有明确的限制，可以直接使用默认构造器
    缺点：如果某个成员变量必须有值，则set方法无法保证一定注入（人为）
    -->
    <bean id="accountService2" class="org.csl.service.impl.AccountServiceImpl2">
        <property name="name" value="test"/>
        <property name="age" value="18"/>
        <property name="birthday" ref="now"/>
    </bean>


~~~



对应的类：

![1611718927650](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611718927650.png)





###### 2.2.3 集合注入

描述：对于一些集合属性如List、Array、Map、Properties，可以利用特定的标签注入



注意：这些属性同样要生成对应的set方法

使用方法：

~~~xml

    <!--复杂类型注入/集合注入-->
    <!--
        用于给list结构注入的标签：
            list array set
        用于给map结构注入的标签：
            map props
        结构相同标签可以互换，即array里的array标签可以放到set里注入
    -->
    <bean id="accountService3" class="org.csl.service.impl.AccountServiceImpl3">
        <property name="myStrs">
            <array>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
                <value>DDD</value>
            </array>
        </property>

        <property name="myList">
            <list>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
                <value>DDD</value>
            </list>
        </property>

        <property name="mySet">
            <set>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
                <value>DDD</value>
            </set>
        </property>

        <property name="myMap">
            <map>
                <entry key="a" value="aa"/>
                <entry key="b">
                    <value>bb</value>
                </entry>
            </map>
        </property>

        <property name="myProps">
            <props>
                <prop key="c">ccc</prop>
                <prop key="d">ddd</prop>
            </props>
        </property>
    </bean>
~~~



对应的类：

![1611719103873](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611719103873.png)



#### 三、注解IOC





#### 开启注解扫描

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

        <!--把对象交给spring来创建-->
<!--        <bean id="accountService" class="org.csl.service.impl.AccountServiceImpl"/>-->
<!--        <bean id="accountDao" class="org.csl.dao.impl.AccountDaoImpl"/>-->

        <!--告知spring创建容器时扫描注解，配置所需要的标签不在bean.xml内-->
        <context:component-scan base-package="org.csl"/>
</beans>
~~~





##### 3.1 创建对象



用法说明：

~~~java
 * 用于创建对象
 *      与<bean></bean>功能一样
 *      Component:
 *          作用：把当前类对象存入spring容器中
 *          属性：
 *              value：指定bean的id，默认值：当前类名小写
 *
 *       =====功能与上注解一样，目的区分MVC三层结构=====
 *       Controller：一般用在表现层
 *       Service：一般用在业务层
 *       Repository：一般用在持久层
~~~

案例：

![1611975773690](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611975773690.png)

![1611975804765](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611975804765.png)





##### 3.2 注入数据









用法说明：

~~~java
 * 用于注入数据
 *      与<bean></bean>内<property></property>一样
 *      Autowried:
 *          作用：自动按照类型注入，只要容器中有唯一的一个bean对象与要注入的变量类型匹配，则注入成功
 *          ，如果没有匹配的，则报错。
 *          使用位置：变量、方法都行
 *          细节：注解注入的时候set方法不是必须的
 *
 *      Qualifier:
 *          作用：按照类注入的基础上再按照名称注入，给类成员注入的时候不能单独使用(与其他搭配使用如Autowired)，但是方法参数可以。
 *
 *      Resource:
 *          作用：直接按照bean的id注入，可以独立使用
 *          属性：name用于指定bean的id
 *
 *      ============以上只能注入bean类型数据，不能注入基本类型===========
 *      集合类型只能通过xml注入
 *
 *      Value
 *          作用：用于注入基本类型和String类型的数据
 *          属性：
 *              value：用于指定数据的值，可以使用SpringEL表达式
 *              SpEL：${表达式}
~~~



案例：

![1611975861393](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611975861393.png)



![1611842945927](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611842945927.png)



**注意：如果有多个同类型的bean匹配，遵循：先比较类型，再比较变量名（与bean的key比较，即创建bean时设置的value）的原则。**



##### 3.3 改变作用域



用法说明：

~~~java
 * 用于改变作用域
 *      与bean的scope属性一样
 *
 *      Scope
 *          作用：用于指定bean的作用范围
 *          属性：
 *              value：取值：singleton、prototype
 *          使用：在类的上方
~~~



![1611975954959](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611975954959.png)

##### 3.4 与周期相关



用法：

~~~java
 * 生命周期相关
 *      与bean的init-method、destroy-method一样
 *      PreDestroy
 *          作用：指定销毁方法
 *      PostConstruct
 *          作用：指定初始方法
~~~



案例：

![1611975976946](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611975976946.png)





#### 四、完整部署spring



##### 4.1 非注解版



###### 4.2.1 导入坐标



**pom.xml**

~~~xml
    <packaging>jar</packaging>



    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>commons-dbutils</groupId>
            <artifactId>commons-dbutils</artifactId>
            <version>1.4</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.21</version>
        </dependency>

        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
~~~



###### 4.1.2 创捷接口、实体类、接口实现类



![1611976354283](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611976354283.png)



###### 4.1.3 创建bean.xml利用spring管理bean 



~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--配置用户服务bean-->
    <bean id="accountService" class="org.csl.service.Impl.AccountServiceImpl">
        <!--注入dao-->
        <property name="accountDao" ref="accountDao"/>
    </bean>

    <!--dao的bean-->
    <bean id="accountDao" class="org.csl.dao.impl.AccountDaoImpl">
        <!--注入QueryRunner-->
        <property name="runner" ref="runner"/>
    </bean>

    <!--配置QueryRunner的bean-->
    <bean id="runner"  class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <!--注入数据源-->
        <constructor-arg name="ds" ref="dataSource"/>
    </bean>

    <!--配置数据源对象 使用c3p0连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--注入连接数据库的信息-->
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/eesy_spring?serverTimezone=GMT%2B8&amp;useUnicode=true&amp;characterEncoding=utf8&amp;autoReconnect=true&amp;useSSL=false"/>
        <property name="user" value="root"/>
        <property name="password" value="mysql123"/>
    </bean>
</beans>
~~~



说明：spring的c3p0连接池需要用到与数据库匹配的数据库驱动jar包（8.0.21），同时需要在构造注入bean的时候添加

**jdbc的url对应的注入变量值为：jdbc:mysql://localhost:3306/eesy_spring?serverTimezone=GMT%2B8&amp;useUnicode=true&amp;characterEncoding=utf8&amp;autoReconnect=true&amp;useSSL=false**



###### 4.1.4 测试



~~~java

    @Test
    public void testFindAll(){
        // 获取容器
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        // 获取accountService业务对象
        IAccountService as = ac.getBean("accountService",IAccountService.class);
        // 执行方法
        List<Account> accounts = as.findAllAccount();
        for(Account account: accounts){
            System.out.println(account);
        }
~~~



![1611976649007](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611976649007.png)





##### 4.2 注解版



目录结构：

![1611982845576](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611982845576.png)



spring中的classpath

![1611983196800](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1611983196800.png)







###### 4.2.1 创建配置类



注解说明：

~~~java
/**
 * 该类是一个配置类，它的作用和bean.xml是一样的
 * spring中的新注解
 * Configuration
 *     作用：指定当前类是一个配置类
 *     细节：当配置类作为AnnotationConfigApplicationContext对象创建的参数时，该注解可以不写。
 * ComponentScan
 *      作用：用于通过注解指定spring在创建容器时要扫描的包
 *      属性：
 *          value：它和basePackages的作用是一样的，都是用于指定创建容器时要扫描的包。
 *                 我们使用此注解就等同于在xml中配置了:
 *                      <context:component-scan base-package="com.itheima"></context:component-scan>
 *  Bean
 *      作用：用于把当前方法的返回值作为bean对象存入spring的ioc容器中
 *      属性:
 *          name:用于指定bean的id。当不写时，默认值是当前方法的名称
 *      细节：
 *          当我们使用注解配置方法时，如果方法有参数，spring框架会去容器中查找有没有可用的bean对象。
 *          查找的方式和Autowired注解的作用是一样的
 *  Import
 *      作用：用于导入其他的配置类
 *      属性：
 *          value：用于指定其他配置类的字节码。
 *                  当我们使用Import的注解之后，有Import注解的类就父配置类，而导入的都是子配置类
 *  PropertySource
 *      作用：用于指定properties文件的位置
 *      属性：
 *          value：指定文件的名称和路径。
 *                  关键字：classpath，表示类路径下
 */
~~~



**创建主配置类**

~~~java
@ComponentScan("org.csl")
@Import(JdbcConfig.class)
@PropertySource("classpath:jdbcConfig.properties")
public class SpringConfiguration {

}
~~~



**导入的配置类JdbcConfig.java**



~~~java
public class JdbcConfig {

    @Value("${jdbc.driver}")
    private String driver;

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    /**
     * 用于创建一个QueryRunner对象
     * @param dataSource
     * @return
     */
    @Bean(name="runner")
    @Scope("prototype")
    public QueryRunner createQueryRunner(@Qualifier("ds2") DataSource dataSource){
        return new QueryRunner(dataSource);
    }

    /**
     * 创建数据源对象
     * @return
     */
    @Bean(name="ds2")
    public DataSource createDataSource(){
        try {
            ComboPooledDataSource ds = new ComboPooledDataSource();
            ds.setDriverClass(driver);
            ds.setJdbcUrl(url);
            ds.setUser(username);
            ds.setPassword(password);
            return ds;
        }catch (Exception e){
            throw new RuntimeException(e);
        }
    }

//    @Bean(name="ds1")
//    public DataSource createDataSource1(){
//        try {
//            ComboPooledDataSource ds = new ComboPooledDataSource();
//            ds.setDriverClass(driver);
//            ds.setJdbcUrl("jdbc:mysql://localhost:3306/eesy_spring");
//            ds.setUser(username);
//            ds.setPassword(password);
//            return ds;
//        }catch (Exception e){
//            throw new RuntimeException(e);
//        }
//    }
}
~~~



**说明：Qualifier注解可以使用到参数上，指定数据源来自于哪个bean**（案例中有ds1和ds2）



###### 4.2.2 创建配置文件



**jdbcConfig.properties**

~~~properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/eesy_spring?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8&autoReconnect=true&useSSL=false
jdbc.username=root
jdbc.password=mysql123
~~~







###### 4.2.3 整合spring和junit4



整合说明：

~~~java
/**
 * 使用Junit单元测试：测试我们的配置
 * Spring整合junit的配置
 *      1、导入spring整合junit的jar(坐标)
 *      2、使用Junit提供的一个注解把原有的main方法替换了，替换成spring提供的
 *             @Runwith
 *      3、告知spring的运行器，spring和ioc创建是基于xml还是注解的，并且说明位置
 *          @ContextConfiguration
 *                  locations：指定xml文件的位置，加上classpath关键字，表示在类路径下
 *                  classes：指定注解类所在地位置
 *
 *   当我们使用spring 5.x版本的时候，要求junit的jar必须是4.12及以上
 */
~~~





导入的坐标：

~~~xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
~~~





测试类：

~~~java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfiguration.class)
public class AccountServiceTest {

    @Autowired
    private IAccountService as = null;


    @Test
    public void testFindAll() {
        //3.执行方法
        List<Account> accounts = as.findAllAccount();
        for(Account account : accounts){
            System.out.println(account);
        }
    }

    @Test
    public void testFindOne() {
        //3.执行方法
        Account account = as.findAccountById(1);
        System.out.println(account);
    }

    @Test
    public void testSave() {
        Account account = new Account();
        account.setName("test anno");
        account.setMoney(12345f);
        //3.执行方法
        as.saveAccount(account);

    }

    @Test
    public void testUpdate() {
        //3.执行方法
        Account account = as.findAccountById(4);
        account.setMoney(23456);
        as.updateAccount(account);
    }

    @Test
    public void testDelete() {
        //3.执行方法
        as.deleteAccount(4);
    }
~~~





#### 五、AOP



##### 5.1 代理



特点：字节码随用随创建、加载

作用：不修改源码的基础上对方法增强



###### 5.1.1 基于接口的动态代理（JDK）



- 使用方法：Proxy.newProxyInstance()方法，实现增强代码。

- newProxyInstance参数：

  - ClassLoader:类加载器：

    用于加载代理对象字节码，和被代理对象使用相同的类加载器，代理谁就写谁的类加载.getClassLoader()

    

  - Class[]:字节码数组让代理对象和被代理对象有相同方法

    

  - InvocationHandler:用于提供增强的代码：就是在执行接口方法前都要经过的代码块如何代理，一般写一个该接口的实现类，通常写匿名内部类，不是必须要匿名内部类。

    谁用谁写

- 使用要求：方法没有接口的时候是不能实现的。

  

- 案例：

~~~java
public static void main(String[] args) {
        final Producer producer = new Producer();

        IProducer proxyProducer = (IProducer) Proxy.newProxyInstance(producer.getClass().getClassLoader(),
                producer.getClass().getInterfaces(),
                new InvocationHandler() {
                    /**
                     * 执行被代理对象的任何接口方法都会经过该方法
                     * @param proxy  代理对象的引用
                     * @param method 当前执行的方法
                     * @param args   当前执行方法所需的参数
                     * @return       和代理对象方法有相同的返回值
                     * @throws Throwable
                     */
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        // 提供增强代码
                        Object returnValue = null;
                        // 1.获取方法执行参数
                        Float money = (Float) args[0];
                        // 2.判断当前方法是不是销售
                        if ("saleProducer".equals(method.getName())){
                            returnValue = method.invoke(producer,money * 0.8f);
                        }
                        return returnValue;
                    }
                });

        proxyProducer.saleProducer(10000);
    }
~~~









###### 5.1.2 基于子类的动态代理（cglib）



~~~java

        /**
         * 动态代理
         * 特点：字节码随用随创建、加载
         * 作用：不修改源码的基础上对方法增强
         *      基于接口的动态代理--->JDK
         *      基于子类的动态代理--->cglib
         *
         * 如何使用代理对象
         *      使用Enhancer类中的create的方法
         * 创建代理对象的要求：
         *      被代理类不能是最终类
         * create：
         *      Class:字节码
         *          用于指定代理对象的字节码
         *
         *      Callback:用于提供增强的代码：就是在执行接口方法前都要经过的代码块
         *          如何代理，一般写一个该接口的实现类，通常写匿名内部类，不是必须要匿名内部类
         *          一般写该接口的子接口实现类MethodInterception
         *
         */
~~~



案例：

~~~java
    public static void main(String[] args) {
        final Producer producer = new Producer();

        Producer cglibProducer = (Producer) Enhancer.create(producer.getClass(), new MethodInterceptor() {
            /**
             *  执行被代理对象的任何方法都会经过改方法
             * @param proxy
             * @param method
             * @param args
             *      与基于接口的动态代理中invoke方法一样
             * @param methodProxy 当前执行方法的代理对象
             * @return
             * @throws Throwable
             */
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                // 提供增强代码
                Object returnValue = null;
                // 1.获取方法执行参数
                Float money = (Float) args[0];
                // 2.判断当前方法是不是销售
                if ("saleProducer".equals(method.getName())){
                    returnValue = method.invoke(producer,money * 0.8f);
                }
                return returnValue;
            }
        });

        cglibProducer.saleProducer(10000);
    }

~~~







###### 5.1.3 动态代理结合实例



**步骤**：

1. 构建利用动态代理的Service工厂(factory.beanFactory.java)

   **将原来ServiceImpl中的事务管理器转移到这个类中，用于复用事务管理器，每一个Service操作都会经过这一个类进行事务管理。**

   

   **并且将service的set方法定为final**

   

   ~~~java
   /**
    *  用于创建Service代理对象的工厂
    */
   public class beanFactory {
   
       private IAccountService accountService;
   
       private  TransactionManager txManager;
   
       public void setTxManager(TransactionManager txManager) {
           this.txManager = txManager;
       }
   
       public final void setAccountService(IAccountService accountService) {
           this.accountService = accountService;
       }
   
       /**
        *  获取Service的代理对象
        */
       public IAccountService getAccountService(){
           return (IAccountService) Proxy.newProxyInstance(accountService.getClass().getClassLoader(),
                   accountService.getClass().getInterfaces(), new InvocationHandler() {
   
                       /**
                        *  添加事务的支持
                        * @param proxy
                        * @param method
                        * @param args
                        * @return
                        * @throws Throwable
                        */
                       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                           Object rtValue = null;
                           try {
                               // 1.开启事务
                               txManager.beginTransaction();
                               // 2.执行Service中的操作
                               rtValue = method.invoke(accountService, args);
                               // 3.提交事务
                               txManager.commit();
                               // 4.返回结果
                               return rtValue;
                           } catch (Exception e) {
                               // 5.回滚
                               txManager.rollback();
                               throw new RuntimeException(e);
                           } finally {
                               // 6.释放连接
                               txManager.release();
                           }
   
                       }
                   });
       }
   }
   
   ~~~

2. 在bean.xml中配置注入

   ~~~xml
       <!--配置代理的service-->
       <bean id="proxyAccountService" factory-bean="beanFactory" factory-method="getAccountService"/>
   
       <!--配置beanFactory-->
       <bean id="beanFactory" class="org.csl.factory.beanFactory">
           <property name="accountService" ref="accountService"/>
           <property name="txManager" ref="txManager"/>
       </bean>
   ~~~

   

3. 测试

   

   使用自动注入和Qualifier指定生成Service的bean，因为在spring容器中有两个ServiceImpl对象，需要指定id让spring识别这次注入的类型。

   

   ~~~java
   @RunWith(SpringJUnit4ClassRunner.class)
   @ContextConfiguration(locations = "classpath:bean.xml")
   public class AccountServiceTest {
   
       @Autowired
       @Qualifier(value = "proxyAccountService")
       private IAccountService as;
       
       @Test
       public void testTransfer(){
           as.transfer("ddd","bbb", 50f);
       }
   
   }
   ~~~

   



---



##### 5.2 基本术语



###### 5.2.1 连接点与切入点



![1612360119661](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1612360119661.png)



连接点：接口方法

切入点：被增强的接口方法



###### 5.2.2 通知



通知：拦截连接点后需要进行的操作

前置通知、后置通知、异常通知、最终通知、环绕通知

![1612360286865](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1612360286865.png)



###### 5.2.3 切面



由切入点和通知结合，以及其构成的一些固定的时序关系形成切面。

关系指如例中的先开启再操作再提交事务，在处理service中的某些操作的时候出现异常的时候执行异常通知这种关系。







---





##### 5.3 spring基于XML的AOP配置



以事务控制为例 day03_eesy_01accountAOP



###### 5.3.1 配置步骤



1. 导入切入点表达式依赖

   ~~~xml
       <dependencies>
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-context</artifactId>
               <version>5.0.2.RELEASE</version>
           </dependency>
   
           <dependency>
               <!--用于解析切入点表达式execution()-->
               <groupId>org.aspectj</groupId>
               <artifactId>aspectjweaver</artifactId>
               <version>1.9.6</version>
           </dependency>
       </dependencies>
   ~~~

2. 创建通知类、需要增强的业务实现类。

   ![1612412263874](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1612412263874.png)

   

3. 创建bean.xml用于配置bean

   配置步骤：

   - 1.通知交给bean管理（配置通知类的bean）

   - aop:config标签进行配置

   - aop:aspect标签表明开始切面配置

     ~~~java
                 属性：
                 id：给切面提供唯一藐视
                 ref：指定通知类bean的id
             4.在aop:aspect内用标签配置通知类型
                 例：让print()在切入点方法前执行===>前置通知
                 aop:before 前置通知
                     属性：
                     method：指定logger类（通知类）哪个方法为前置通知
                     pointcut：指定切入点表达式，表达式指对业务层中哪些方法增强（起作用）
     切入点表达式：
     关键字：execution（表达式）
     表达式：
     访问修饰符、返回类型、包名..包名.方法名(参数列表)
     写法：
     	1. 单配方法：public void org.csl.service.impl.AccountServiceImpl.saveAccount()
     	2. 全通配写法：
      		* *..*.*(..)
     	3.部分配置：
         3.1返回值使用通配符
     		* org.csl.service.impl.AccountServiceImpl.saveAccount()
                     
         3.2访问修饰符可以省略	
     		void org.csl.service.impl.AccountServiceImpl.saveAccount()
                     
         3.3包名可使用通配符，有几级包就写几级*.*		
         	* *.*.*.*.AccountServiceImpl.saveAccount()
         	*..* 可表示当前包及其子包，不用分多级去写
         	* *..AccountServiceImpl.saveAccount()
                     
         3.4类名和方法名可使用通配符
         	* *..*.*()
         3.5方法参数可使用通配符
     		可以直接写参数类型       int
     		引用类型（对象）写包名.类名的方式   java.lang.String
     		任意类型    *   有参数的方法才能执行
     		不限制参数   ..
     		* *..*.*(..)
     
         4.开发中的写法：业务层实现类下的所有方法
     	    * org.csl.service.impl.*.*(..)
     ~~~

     

   

   

   

   

   1. 配置业务实现类的bean

      ~~~xml
          <!--配置service的bean-->
          <bean id="accountService" class="org.csl.service.impl.AccountServiceImpl"/>
      
      
      ~~~

   2. 配置AOP

      ~~~xml
          <!--配置公共代码（通知类）logger的bean-->
          <bean id="logger" class="org.csl.utils.Logger"/>
      
          <!--配置aop-->
          <aop:config>
              <!--配置切面-->
              <aop:aspect id="logAdvice" ref="logger">
                  <!--配置前置通知，pointcut配置切入点和通知方法的关联-->
      <!--            <aop:before method="print" pointcut="execution(public void org.csl.service.impl.AccountServiceImpl.saveAccount())"/>-->
                  <aop:before method="print" pointcut="execution(* *..*.*(..))"/>
              </aop:aspect>
          </aop:config>
      ~~~

   3. 测试案例

      ~~~java
          public static void main(String[] args) {
              // 1.获取容器
              ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
      
              // 2.获取对象
              IAccountService as = (IAccountService) ac.getBean("accountService");
      
              // 3.执行方法
              as.saveAccount();
              as.updateAccount(1);
              as.deleteAccount();
          }
      ~~~





###### 5.3.2 配置四种通知类型



~~~xml
            <!--配置前置通知，pointcut配置切入点和通知方法的关联-->
            <aop:before method="bfPrint" pointcut-ref="pt1"/>
            <!--配置后置通知-->
            <aop:after method="afPrint" pointcut-ref="pt1"/>
            <!--配置异常通知-->
            <aop:after-throwing method="errorPrint" pointcut-ref="pt1"/>
            <!--配置最终通知，无论如何运行都会执行-->
            <aop:after-returning method="finPrint" pointcut-ref="pt1"/>
~~~



**通用化切入点表达式**

就是将切入点表达式内容做成公用的，将pointcut标签写在切面配置标签aop:aspect之前。

~~~xml
            <!--配置切入点表达式，id指定表达式唯一标识，expression指定表达式内容-->
            <!--写在标签内部只能内部使用，它还可以写在aop:aspect标签外(所有切面可用)-->
            <aop:pointcut id="pt1" expression="execution(* org.csl.service.impl.*.*(..))"/>
~~~



~~~xml
    <aop:config>
        <aop:pointcut id="pt1" expression="execution(* org.csl.service.impl.*.*(..))"/>
        <!--配置切面-->
        <aop:aspect id="logAdvice" ref="logger">
            <!--配置前置通知，pointcut配置切入点和通知方法的关联-->
            <aop:before method="bfPrint" pointcut-ref="pt1"/>
            <!--配置后置通知-->
            <aop:after method="afPrint" pointcut-ref="pt1"/>
            <!--配置异常通知-->
            <aop:after-throwing method="errorPrint" pointcut-ref="pt1"/>
            <!--配置最终通知，无论如何运行都会执行-->
            <aop:after-returning method="finPrint" pointcut-ref="pt1"/>

            <!--配置切入点表达式，id指定表达式唯一标识，expression指定表达式内容-->
            <!--写在标签内部只能内部使用，它还可以写在aop:aspect标签外(所有切面可用)-->
<!--            <aop:pointcut id="pt1" expression="execution(* org.csl.service.impl.*.*(..))"/>-->
        </aop:aspect>
    </aop:config>
~~~





**环绕通知**

![1612435487090](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1612435487090.png)



相当于自己配一个动态代理（方法增强、切面），然后在bean.xml中配置什么时候执行这个代理方法。

~~~xml
            <!--配置环绕通知-->
            <aop:around method="aroundPrint" pointcut-ref="pt1"/>
~~~



~~~java
    /**
     * 环绕通知
     */
    public Object aroundPrint(ProceedingJoinPoint pjp){
        Object rtValue = null;
        try {
            // 得到方法执行所需的参数
            Object[] args = pjp.getArgs();

            // 前置通知
            System.out.println("Run with ardPrint()");

            // 明确调用业务层方法
            rtValue = pjp.proceed(args);

            // 后置通知
            System.out.println("Run with ardPrint()");

            return rtValue;
        } catch (Throwable throwable) {
            // 异常通知
            System.out.println("Run with ardPrint()");
            throw new RuntimeException(throwable);
        } finally {
            /*最终通知*/
            System.out.println("Run with ardPrint()");
        }
        
    }
~~~



---



##### 5.4 spring基于注解的AOP配置



在bean.xml中**开启注解扫描和注解AOP**支持

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--开启注解扫描-->
    <context:component-scan base-package="org.csl"/>
    <!--开启spring开启注解AOP支持-->
    <aop:aspectj-autoproxy/>

</beans>
~~~



为通知类配置注解：

~~~java
@Component("logger")
//表示当前类是一个切面类
@Aspect

public class Logger {

    /**
     * 切入点表达式的注解形式
     */
    @Pointcut("execution(* org.csl.service.impl.*.*(..))")
    public void pt1(){}

    /**
     *  前置通知
     */
    @Before("pt1()")
    public void bfPrint(){
        System.out.println("Run with bfPrint()");
    }

    /**
     *  后置通知
     */
    @After("pt1()")
    public void afPrint(){
        System.out.println("Run with afPrint()");
    }

    /**
     *  异常通知
     */
    @AfterThrowing("pt1()")
    public void errorPrint(){
        System.out.println("Run with errorPrint()");
    }

    /**
     *  最终通知
     */
    @AfterReturning("pt1()")
    public void finPrint(){
        System.out.println("Run with finPrint()");
    }

    /**
     * 环绕通知
     */
//    @Around("pt1()")
    public Object aroundPrint(ProceedingJoinPoint pjp){
        Object rtValue = null;
        try {
            // 得到方法执行所需的参数
            Object[] args = pjp.getArgs();

            // 前置通知
            System.out.println("Run with ardPrint()");

            // 明确调用业务层方法
            rtValue = pjp.proceed(args);

            // 后置通知
            System.out.println("Run with ardPrint()");

            return rtValue;
        } catch (Throwable throwable) {
            // 异常通知
            System.out.println("Run with ardPrint()");
            throw new RuntimeException(throwable);
        } finally {
            /*最终通知*/
            System.out.println("Run with ardPrint()");
        }

    }
}

~~~



配置业务类注解，注入业务bean

~~~java
@Service("accountService")
public class AccountServiceImpl implements IAccountService {

~~~





---



#### 6.JdbcTemplate



##### 6.1 概述

 JDBC已经能够满足大部分用户最基本的需求，但是在使用JDBC时，必须自己来管理数据库资源如：获取PreparedStatement，设置SQL语句参数，关闭连接等步骤。

 JdbcTemplate是Spring对JDBC的封装，目的是使JDBC更加易于使用。JdbcTemplate是Spring的一部分。JdbcTemplate处理了资源的建立和释放。他帮助我们避免一些常见的错误，比如忘了总要关闭连接。他运行核心的JDBC工作流，如Statement的建立和执行，而我们只需要提供SQL语句和提取结果。



##### 6.2 使用方法



###### 6.2.1 导入依赖



~~~xml
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.20.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.12.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.2.12.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>

    </dependencies>
~~~



###### 6.2.2 三个基本方法



execute、query、update

![1613749648269](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1613749648269.png)





###### 6.2.3 JdbcTemplate执行CRUD



代码案例：

~~~java
/**
 * JdbcTemplate的CRUD
 */
public class jdbcTemplateDemo3 {

    public static void main(String[] args) {

        // 获取容器
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        // 获取对象
        JdbcTemplate jt = ac.getBean("jdbcTemplate",JdbcTemplate.class);
        // 2.执行操作
        jt.execute("insert into account(name,money) values ('ggg',2000)");


        // 保存
        jt.update("insert into account(name,money) values (?,?)","eee",3333);
        // 更新
        jt.update("update account set name=?,money=? where id=?","eed","3334",8);
        // 删除
        jt.update("delete from account where id=?","9");
        // 查询所有
        List<Account> accounts = jt.query("select * from account where money > ?",new AccountRomMapper(),3000f);
        List<Account> accounts = jt.query("select * from account where money > ?",new BeanPropertyRowMapper<Account>(Account.class),3000f);
        for (Account account : accounts){
            System.out.println(account);
        }
        // 查询一个
        List<Account> accounts = jt.query("select * from account where id = ?",new BeanPropertyRowMapper<Account>(Account.class),7);
        System.out.println(accounts.isEmpty()?"empty":accounts.get(0));
        // 查询返回一行一列(聚合函数)
        Long count = jt.queryForObject("select count(*) from account where money > ?", Long.class, 2000f);
        System.out.println(count);
    }
}
~~~





###### 6.2.4 Jdbc在持久层dao下的使用



**说明：**

父类JdbcDaoSupport中会注入数据源创建JdbcTemplate对象，子类只需要继承JdbcDaoSupport并且使用super.getJdbcTemplate()获取JdbcTemplate的bean，在bean中则只需要向dao注入数据源即可获取到JdbcTemplate。



代码案例：

~~~java
public class AccountDaoImpl extends JdbcDaoSupport implements IAccountDao {


    public Account findAccountById(Integer accountId) {
        List<Account> accounts =  super.getJdbcTemplate().query("select * from account where id=?",new BeanPropertyRowMapper<Account>(Account.class),accountId);
        return accounts.isEmpty()?null:accounts.get(0);
    }

    public Account findAccountByName(String accountName) {
        List<Account> accounts =  super.getJdbcTemplate().query("select * from account where name=?",new BeanPropertyRowMapper<Account>(Account.class),accountName);
        if (accounts.isEmpty()){
            return null;
        }
        if (accounts.size() > 1){
            throw new RuntimeException("结果不唯一");
        }
        return accounts.get(0);
    }

    public void updateAccount(Account account) {
        super.getJdbcTemplate().update("update account set name=?,money=? where id=?",account.getName(),account.getMoney(),account.getId());
    }
}
~~~



###### 6.2.5 bean的注入



~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">


    <!--配置dao-->
    <bean id="accountDao" class="org.csl.dao.impl.AccountDaoImpl">
        <!--        <property name="jdbcTemplate" ref="jdbcTemplate"/>-->
        <!--继承父类JdbcDaoSupport的set方法进行注入-->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置jdbcTemplate-->
    <!--    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">-->
    <!--        <property name="dataSource" ref="dataSource"/>-->
    <!--    </bean>-->

    <!--配置数据源dataSource-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/eesy_spring"/>
        <property name="username" value="root"/>
        <property name="password" value="mysql123"/>
    </bean>

</beans>
~~~







测试案例

~~~java
package org.csl.jdbctemplate;

import org.csl.dao.IAccountDao;
import org.csl.domain.Account;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import java.sql.ResultSet;
import java.sql.SQLException;

/**
 * JdbcTemplate的CRUD
 */
public class jdbcTemplateDemo4 {

    public static void main(String[] args) {

        // 获取容器
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        // 获取对象
        IAccountDao accountDao = ac.getBean("accountDao",IAccountDao.class);

        Account account = accountDao.findAccountById(5);
        System.out.println(account);

        account.setMoney(4000f);
        accountDao.updateAccount(account);
    }
}


~~~





----







#### 7.Spring的事务控制



注入依赖：



~~~xml
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.2.12.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.8.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.6</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

    </dependencies>
~~~







##### 7.1 基于XML的声明式事务控制       



###### 7.1.1 使用步骤

    1.配置事务管理器
    2.配置事务通知
        导入事务约束，tx名称空间和约束，同时也需要aop的
        <tx:advice>标签配置事务通知：
            标签属性：
                id：通知标识符
                transaction-manager：提供事务管理器id
    3.配置aop通用切入点表达式
    4.建立切入点表达式与事务通知的关系
    5.配置事务属性			
- isolation：用于指定事务的隔离级别。默认值是DEFAULT，表示使用数据库的默认隔离级别。
- propagation：用于指定事务的传播行为。默认值是REQUIRED，表示一定会有事务，增删改的选择。查询方法可以选择SUPPORTS。
- read-only：用于指定事务是否只读。只有查询方法才能设置为true。默认值是false，表示读写。
- timeout：用于指定事务的超时时间，默认值是-1，表示永不超时。如果指定了数值，以秒为单位。
- rollback-for：用于指定一个异常，当产生该异常时，事务回滚，产生其他异常时，事务不回滚。没有默认值。表示任何异常都回滚。



###### 7.1.2 代码案例（bean.xml）

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--配置dao-->
    <bean id="accountDao" class="org.csl.dao.impl.AccountDaoImpl">
        <!--继承父类JdbcDaoSupport的set方法进行注入-->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置service-->
    <bean id="accountService" class="org.csl.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"/>
    </bean>

    <!--配置数据源dataSource-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/eesy_spring"/>
        <property name="username" value="root"/>
        <property name="password" value="mysql123"/>
    </bean>

    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="*" propagation="REQUIRED" read-only="false"/>
            <tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!--配置aop-->
    <aop:config>
        <!--配置切入点表达式-->
        <aop:pointcut id="pt1" expression="execution(* org.csl.service.impl.*.*(..))"/>
        <!--建立切入点表达式与事务通知的关系-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pt1"/>
    </aop:config>

</beans>
~~~



说明：

1. **配置事务通知的`tx:method`标签可以通过`*`来通配需要配置通知的类中的方法名称。如find*则表示以find开头的方法。**





##### 7.2 基于注解的声明式事务控制



###### 7.2.1 使用步骤

```
1.配置事务管理器
2.开启spring对注解事务支持（开启注解扫描、tx注解扫描）
3.在需要事务支持的地方使用@Transactional注解
```



###### 7.2.2 代码案例（bean.xml）



~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--配置spring创建时需要扫描的包-->
    <context:component-scan base-package="org.csl"/>

    <!--配置jdbcTemplate-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>


    <!--配置数据源dataSource-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/eesy_spring"/>
        <property name="username" value="root"/>
        <property name="password" value="mysql123"/>
    </bean>


    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <tx:annotation-driven transaction-manager="transactionManager"/>

</beans>
~~~



###### 7.2.3 配置注解



1. 在ServiceImpl和DaoImpl中分别使用Service注解和Repository注解，对应的成员变量则使用Autowired自动注入。

   ![1614050284139](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1614050284139.png)

   

   

2. 在需要使用事务控制的AccountServiceImpl类中使用Transactional注解

   ![1614050436772](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1614050436772.png)





###### 7.2.4 纯注解方式





##### 7.3 编程式事务控制









----





#### 8. SpringMVC





##### 8.1 入门程序



###### 8.1.1 执行流程

![1614223315262](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1614223315262.png)



详细步骤

![1614224385319](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1614224385319.png)



###### 8.1.2 入门程序步骤



1. 导入坐标

   ~~~xml
     <properties>
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
       <spring.version>5.2.12.RELEASE</spring.version>
     </properties>
   
     <dependencies>
       <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>4.12</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-web</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-webmvc</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>javax.servlet</groupId>
         <artifactId>servlet-api</artifactId>
         <version>2.5</version>
         <scope>provided</scope>
       </dependency>
   
       <dependency>
         <groupId>javax.servlet.jsp</groupId>
         <artifactId>jsp-api</artifactId>
         <version>2.0</version>
         <scope>provided</scope>
       </dependency>
   
     </dependencies>
   ~~~

   

   ##### 8.1.3 解决工程创建过慢

   注：

   **创建maven工程时添加键值对：archetypeCatalog-internal或者修改maven的镜像源（解决创建过慢的问题）**

   

2. 在**web.xml中**配置前端控制器

   

   **利用初始化参数加载springmvc.xml，使web.xml与springmvc配置文件建立联系**

   

   ~~~xml
     <!--配置前端控制器-->
     <servlet>
       <servlet-name>dispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--提供初始化参数，加载spring配置文件springmvc.xml-->
       <init-param>
         <param-name>contextConfigLocation</param-name>
         <param-value>classpath:springmvc.xml</param-value>
       </init-param>
       <!--配置DispatcherServlet在启动服务器时就创建该对象，默认值为接收到请求才创建-->
       <load-on-startup>1</load-on-startup>
     </servlet>
     
     <servlet-mapping>
       <servlet-name>dispatcherServlet</servlet-name>
       <url-pattern>/</url-pattern>
     </servlet-mapping>
   ~~~

   

3. 创建控制类和前端页面

   控制器类

   ~~~java
   /**
    * 控制器类
    * @author maxcs
    */
   @Controller
   public class HelloController {
   
       /**
        *  请求路径
        * @return
        */
       @RequestMapping(path = "/hello")
       public String sayHello(){
           System.out.println("Hello springMVC");
           return "success";
       }
   }
   
   ~~~

   

   前端页面

   ~~~jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   	<body>
   		<h3>入门程序</h3>
   		<a href="hello">入门程序</a>
   	</body>
   </html>
   ~~~

   

   **注：**

   1. **a标签的跳转不用加`/`**

   2. **RequestMapping注解将控制器方法投入IOC容器，并且指定访问路径**

   3. **Controller标签将控制器类投入IOC容器**

      

4. 在Resources里创建SpringMVC配置文件

   

   **开启注解扫描和springMVC注解支持，配置视图解析器，用于 解析并跳转 控制器方法中返回的页面字符串**

   

   ~~~xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--开启注解扫描-->
       <context:component-scan base-package="org.csl"/>
   
       <!--配置视图解析器，用于 解析并跳转 控制器方法中返回的页面字符串-->
       <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <!--配置解析目录-->
           <property name="prefix" value="/WEB-INF/pages/"/>
           <!--配置解析目录文件后缀-->
           <property name="suffix" value=".jsp"/>
       </bean>
   
       <!--开启SpringMVC注解支持-->
       <mvc:annotation-driven/>
   </beans>
   ~~~





##### 8.2 RequestMapping注解



###### 8.2.1 概述



用于指定资源的访问路径



###### 8.2.2 用法



使用path属性指定映射路径



1. 在方法上

   如果方法所在的类没有固定的一级路径，在前端就可以用方法上的@RequestMapping访问

   

   案例：

   ~~~java
       /**
        *  请求路径
        * @return
        */
       @RequestMapping(path = "/hello")
       public String sayHello(){
           System.out.println("Hello springMVC");
           return "success";
       }
   ~~~

   

   访问：

   ![1614249104278](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1614249104278.png)

   

2. 在类上

   在类上书写，则代表是这一个类的方法下的所有父目录，访问时候要写上访问路径。

   

   案例：

   ~~~java
   /**
    * 控制器类
    * @author maxcs
    */
   @Controller
   @RequestMapping(path = "/user")
   public class HelloController {
   
       /**
        *  请求路径
        * @return
        */
       @RequestMapping(path = "/hello")
       public String sayHello(){
           System.out.println("Hello springMVC");
           return "success";
       }
   
       /**
        *  测试RequestMapping注解
        * @return
        */
       @RequestMapping(path = "/testRequestMapping")
       public String testRequestMapping(){
           System.out.println("测试RequestMapping注解");
           return "success";
       }
   }
   ~~~

   

   访问：





###### 8.2.3 属性



1. path[]、value[]：指定映射路径
2. method：指定请求方式是get还是post等等
3. params：指定请求的时候需要传入的参数
4. headers：指定限制请求头的条件



案例：

~~~java
    @RequestMapping(path = "/testRequestMapping",method = {RequestMethod.GET},params = {"username"},headers = {"Accept"})
~~~







##### 8.3 请求参数的绑定





###### 8.3.1 请求参数绑定实体类



1. 创建控制器配置映射

   ~~~java
   package org.csl.controller;
   
   import org.csl.domain.Account;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   /**
    * @author maxcs
    */
   @Controller
   @RequestMapping(path = "/param")
   public class ParamController {
   
       /**
        * 请求参数绑定入门
        * @param username
        * @param password
        * @return
        */
       @RequestMapping("/testParam")
       public String testParam(String username, String password){
           System.out.println("用户名：" + username);
           System.out.println("密  码：" + password);
           return "success";
       }
   
       /**
        * 把请求参数封装到JavaBean的类中
        * @return
        */
       @RequestMapping("/saveAccount")
       public String saveAccount(Account account){
           System.out.println(account);
           return "success";
       }
   }
   
   ~~~

2. 配置前端参数

   2.1**绑定不含引用类型**

   ~~~jsp
       <form action="param/saveAccount" method="post">
           姓名：<input type="text" name="username" /><br/>
           密码：<input type="text" name="password" /><br/>
           金额：<input type="text" name="money" /><br/>
           <input type="submit" value="提交"/><br/>
       </form>
   ~~~

   对应的实体类

   ~~~java
   public class Account implements Serializable {
   
       private String username;
       private String password;
       private Double money;
   ~~~

   

   

   2.2**绑定到引用类型**

   ~~~jsp
       <form action="param/saveAccount" method="post">
           姓名：<input type="text" name="username" /><br/>
           密码：<input type="text" name="password" /><br/>
           金额：<input type="text" name="money" /><br/>
           用户姓名：<input type="text" name="user.uname"><br/>
           用户年龄：<input type="text" name="user.age"><br/>
           <input type="submit" value="提交"/><br/>
       </form>
   ~~~

   对应实体类

   ~~~java
   public class Account implements Serializable {
   
       private String username;
       private String password;
       private Double money;
       private User user;
   ~~~

   

   

   2.3**绑定集合类型**

   ~~~jsp
       <%--把数据封装到Account中，类中存在userlist和usermap集合--%>
       <form action="param/saveAccount" method="post">
           姓名：<input type="text" name="username" /><br/>
           密码：<input type="text" name="password" /><br/>
           金额：<input type="text" name="money" /><br/>
   
           用户1姓名：<input type="text" name="userList[0].uname"><br/>
           用户1年龄：<input type="text" name="userList[0].age"><br/>
   
           用户2姓名：<input type="text" name="userMap['first'].uname"><br/>
           用户2年龄：<input type="text" name="userMap['first'].age"><br/>
           <input type="submit" value="提交"/><br/>
       </form>
   ~~~

   对应实体类

   ~~~java
   public class Account implements Serializable {
   
       private String username;
       private String password;
       private Double money;
   
       private List<User> userList;
       private Map<String,User> userMap;
   ~~~

   

   注：input标签的name**与实体类的属性名称一致**

   

   

3. 解决中文乱码

方法：在web.xml配置过滤器



配置方式：

~~~xml
  <!--配置中文乱码过滤器-->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

~~~





##### 8.4 自定义类型转换器



###### 8.4.1 概述

解决由前端页面传入的数据不能通过spring的默认转换器进行类型转化的情况。spring默认由字符串转换成参数类型。



**使用已经定义的接口转换**

使用到的接口：Converter，该接口定义了很多的类型转换器。



###### 8.4.2 使用步骤



1. 

   ~~~java
   package org.csl.utils;
   
   import org.springframework.core.convert.converter.Converter;
   
   import java.text.DateFormat;
   import java.text.SimpleDateFormat;
   import java.util.Date;
   
   /**
    * @author maxcs
    */
   public class StringToDateConverter implements Converter<String, Date> {
   
       /**
        *  s 为传入字符串的值
        * @param s
        * @return
        */
       @Override
       public Date convert(String s) {
           if (s == null){
               throw new RuntimeException("传入数据为空");
           }
           // 定义转换格式
           DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
           try {
               // 将字符串转换
               return df.parse(s);
           } catch (Exception e) {
               throw new RuntimeException("转换错误");
           }
   
       }
   
   
   }
   ~~~

   

2. 在springmvc.xml中配置类型转换器

   

   ~~~xml
       <!--配置自定义类型转换器-->
       <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
           <property name="converters">
               <set>
                   <!--将自定义的类放入转换器set集合-->
                   <bean class="org.csl.utils.StringToDateConverter"/>
               </set>
           </property>
       </bean>
   
   ~~~

   

   开启类型转换器

   ~~~xml
       <!--开启SpringMVC注解支持-->
       <mvc:annotation-driven conversion-service="conversionService"/>
   ~~~

   

3. 前端

   ![1614484288992](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1614484288992.png)





获取原生API方法

~~~java
    /**
     * 原生的API
     * @return
     */
    @RequestMapping("/testServlet")
    public String testServlet(HttpServletRequest request, HttpServletResponse response){
        System.out.println("执行了...");
        System.out.println(request);

        HttpSession session = request.getSession();
        System.out.println(session);

        ServletContext servletContext = session.getServletContext();
        System.out.println(servletContext);

        System.out.println(response);
        return "success";
    }
~~~









##### 8.5 SpringMVC常用注解



###### 8.5.1 RequestParam



作用：把请求中的实参传给控制器中的形参

属性：

- value：请求参数的名称
- required：指定请求参数是否提供该参数（是不是一定要有），默认为true。



代码示例

~~~java
    /**
     * RequestParam注解
     * @return
     */
    @RequestMapping("/testRequestParam")
    public String testRequestParam(@RequestParam(name = "username") String username){
        System.out.println("okok");
        System.out.println(username);
        return "success";
    }
~~~

~~~jsp
    <a href="anno/testRequestParam?username=csl">RequestParam</a><br/>
~~~





###### 8.5.2 RequestBody



作用：获取请求体内容，获取的是key=value的Map型数据，**get请求不适用**

属性：required 是否必须有请求体



代码示例：

~~~java
    /**
     * RequestBody注解
     * @return
     */
    @RequestMapping("/testRequestBody")
    public String testRequestBody(@RequestBody String body){
        System.out.println("okok");
        System.out.println(body);
        return "success";
    }
~~~

~~~jsp
    <form action="anno/testRequestBody" method="post">
        用户1姓名：<input type="text" name="username"><br/>
        用户1年龄：<input type="text" name="age"><br/>
        <input type="submit" value="提交"/><br/>
    </form>
~~~





username=csl777&age=11



###### 8.5.3 PathVariable



作用：绑定url中的占位符，如/delete/{id}，{id}为占位符

属性：

- value：url中占位符名称
- required：是否必须提供占位符



代码示例：

~~~java
    /**
     * PathVariable注解
     * @return
     */
    @RequestMapping("/testPathVariable/{sid}")
    public String testPathVariable(@PathVariable(name = "sid") String id){
        System.out.println(id);
        return "success";
    }
~~~

~~~jsp
    <a href="anno/testPathVariable/10">PathVariable</a><br/>
~~~





###### 8.5.4 RequestHeader



作用：获取请求头里的信息



代码示例：

~~~java
    /**
     * RequestHeader注解
     * @return
     */
    @RequestMapping("/testRequestHeader")
    public String testRequestHeader(@RequestHeader(value = "Accept") String header){
        System.out.println(header);
        return "success";
    }
~~~





###### 8.5.5 CookieValue



作用：获取浏览器的Cookie到控制器参数

value：cookie名称

required：是否要有Cookie



代码示例：

~~~java
    /**
     * CookieValue注解
     * @return
     */
    @RequestMapping("/testCookieValue")
    public String testCookieValue(@CookieValue(value = "JSESSIONID") String cookie){
        System.out.println(cookie);
        return "success";
    }
~~~



###### 8.5.6 ModelAttribute



作用：用在方法上表示当前方法会在控制器方法执行前，先执行；用在参数上表示获取指定的数据给参数赋值。



属性：

- value：用于获取数据的key（可以是实体类的属性，map的key）

  

**应用场景：当表单提供的不是完整的实体类数据，保证没有提交数据的字段使用数据库对象原来的数据。**





###### 8.5.7 SessionAttribute



作用：用于多次执行控制器方法间的参数共享，**作用在类上。**

属性：

- value：用于指定存入的属性名称
- type：指定存入的数据类型



代码示例：



~~~java
    /**
     * SessionAttribute注解
     * @return
     */
    @RequestMapping("/testSessionAttribute")
    public String testSessionAttribute(Model model){
        System.out.println("testSessionAttribute");
        // 底层会存储到request对象中
        model.addAttribute("msg","message1");
        return "success";
    }

    /**
     * SessionAttribute注解，从Session获取
     * @return
     */
    @RequestMapping("/getSessionAttribute")
    public String getSessionAttribute(ModelMap model){
        // 底层会存储到request对象中
        System.out.println((String) model.get("msg"));
        // model.getAttribute()
        return "success";
    }

~~~



在success页面获取

开启EL表达式：isELIgnored="false"

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h3>Success</h3>

    ${ requestScope.msg}

    ${ sessionScope.msg}
</body>
</html>
~~~



清除：

![1614507354514](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1614507354514.png)





---



##### 8.6 响应返回值类型



###### 8.6.1 String类型

（将页面转发到特定的页面success.jsp，与之前的model的存储Request一样）

![1615291631791](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615291631791.png)





###### 8.6.2 void类型



默认情况：默认跳转到请求路径同名的文件下

/pages/user/testVoid.jsp

可以使用原生Servlet的重定向和请求转发来实现跳转

~~~java
    /**
     * 返回值为void类型
     * @param request
     * @param response
     */
    @RequestMapping("/testVoid")
    public void testVoid(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("Running testVoid()...");
        // 利用请求转发来跳转，这种方法不走视图解析器，路径要写全
//        request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request,response);

        // 重定向，不能重定向到WEB-INF下的文件
        response.sendRedirect(request.getContextPath() + "/index.jsp");


//        设置解决中文乱码
//        response.setCharacterEncoding("UTF-8");
//        response.setContentType("text/html;charset=UTF-8");
        // 直接往浏览器输入
//        response.getWriter().print("你好");

        return;
    }
~~~



说明：

1.  redirect的路径一定不能在WEB-INF路径下，因为redirect是相当于用户直接访问了路径，而用户不能访问WEB-INF目录下的文件，只有程序内部转发的时候才能转发到WEB-INF下的JSP。 

   

2.  需要注意，**重定向和请求转发二者都不和视图解析器一同使用，可以在项目中利用请求转发跳转到任意页面**。 

   

3. 利用原生HttpServelet的重定向需要拼接项目路径，但是对于spring框架中封装的重定向，则可以直接使用资源路径，不用拼接项目路径









###### 8.6.3 返回值是ModelAndView类型



**setViewName可以使页面跳转。**

返回String的方法底层也会使用到ModelAndView方法

~~~java
    /**
     * 返回ModelAndView对象,会调用视图解析器设置的路径
     * @return
     */
    @RequestMapping("/testModelAndView")
    public ModelAndView testModelAndView(){
        // 创建ModelAndView对象
        ModelAndView mv = new ModelAndView();
        System.out.println("Running testModelAndView()...");
        // 模拟从数据库中查询出User对象
        User user = new User("csl","csl777",21);

        // user存储到mv对象,也会把user存入request对象中
        mv.addObject("user",user);
        // 跳转到指定页面
        mv.setViewName("success");

        // 将请求转发到特定的页面
        return mv;
    }
~~~



**说明：user存储到ModelAndView对象,也会把user存入request对象中**



##### 8.7 forward和redirect的使用

spring封装了原生Servlet中的两种跳转：请求转发和重定向

可以直接使用return来跳转



~~~java
    /**
     *  使用关键字的方式进行转发或者重定向，不适用视图解析器
     * @return
     */
    @RequestMapping("/testForwardOrRedirect")
    public String testForwardOrRedirect(){
        System.out.println("Running testForwardOrRedirect()...");
        // 请求转发
        return "forward:/WEB-INF/pages/success.jsp";
        // 请求重定向
        return "redirect:/index.jsp";

        // 将请求转发到特定的页面

    }
~~~



注意：这里使用的重定向跳转路径是相对于当前路径而言的，如果要准确需要写http://localhost:8080/xxxx/index.jsp





##### 8.8 ResponseBody响应json数据和静态资源



###### 8.8.1 配置不过滤静态资源的请求



![1615300665667](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615300665667.png)



在springmvc.xml中，关闭对静态资源的拦截

~~~xml

    <!--开启前端控制器关闭对静态资源的拦截-->
    <mvc:resources mapping="/js/**" location="/js/"/>
~~~

![1615386554977](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615386554977.png)

###### 8.8.2 利用ajax完成请求和响应过程



1. 利用原生ajax发送异步请求

   ~~~javascript
       // 页面加载，绑定单击事件
       $(function(){
           $("#btn").click(function(){
               // alert("hello btn");
               // 发送ajax请求
               $.ajax({
                   // json格式书写，设置属性
                   url:"user/testAjax",
                   contentType:"application/json;charset=UTF-8",
                   data:'{"username":"csl777","password":"123456","age":"18"}',
                   dataType:"json",
                   type:"POST",
                   success:function (data) {
                       //data服务器段响应的json数据
                       alert(data);
                       alert(data.username);
                       alert(data.password);
                       alert(data.age);
                   }
               });
           });
       });
   ~~~

   

2. 将页面发送的ajax请求数据注入到javabean中

   导入坐标（**ajax请求体内的数据属性名和实体类属性名对应**）

   ~~~xml
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-databind</artifactId>
         <version>2.9.0</version>
       </dependency>
   
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-core</artifactId>
         <version>2.9.0</version>
       </dependency>
   
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-annotations</artifactId>
         <version>2.9.0</version>
       </dependency>
   ~~~

   

3. 在控制器内利用**@RequestBody注解**接收ajax数据，利用**@ResponseBody返回**ajax数据

   ~~~java
      /**
        * 模拟异步请求响应
        */
       @RequestMapping("/testAjax")
       public @ResponseBody User testAjax(@RequestBody User user){
           System.out.println("Running testAjax()...");
           System.out.println(user);
           // 响应
           user.setUsername("new Name");
           user.setPassword("new Password");
           user.setAge(20);
           System.out.println(user);
           return user;
       }
   ~~~

   **RequestBody可以将user数据自动封装成json数据，然后发送到前端页面，前端页面可以使用data来调用**

   

   

   
---





##### 8.9 springMVC的文件上传



###### 8.9.1 文件上传的前提



![1615385774708](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615385774708.png)



![1615385850615](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615385850615.png)



###### 8.9.2 使用步骤



**生成唯一id的方法：**

`String uuid = UUID.randomUUID().toString().replace("-","");`

`fileName = uuid + "_" + fileName;`



导入坐标

~~~xml
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.1</version>
    </dependency>

    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.4</version>
    </dependency>
~~~



前端

~~~jsp
    <form action="user/fileUpload" method="post" enctype="multipart/form-data">
        选择文件：<input type="file" name="upload"/><br/>
        <input type="submit" value="上传">
    </form>
~~~



控制器

~~~java
    /**
     *  文件上传
     * @return
     */
    @RequestMapping("fileUpload")
    public String fileUpload(HttpServletRequest request) throws Exception{
        System.out.println("Running fileUpload()...");

        // 使用fileupload组件
        // 指定上传位置(获取绝对路径)
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        File file = new File(path);
        // 判断路径是否存在，不存在则创建
        if (!file.exists()){
            file.mkdirs();
        }
        // 解析request对象，获取上传文件
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload upload = new ServletFileUpload(factory);
        // 解析request
        List<FileItem> fileItems = upload.parseRequest(request);
        // 遍历
        for (FileItem item :fileItems){
            // 判断当前item对象是否是 上传文件项 还是一个 普通表单项
            if (item.isFormField()){
                // 说明是普通表单项

            }else {
                // 获取上传文件名称
                String fileName = item.getName();
                // 生成唯一的文件id
                String uuid = UUID.randomUUID().toString().replace("-","");
                fileName = uuid + "_" + fileName;
                // 完成文件上传
                item.write(new File(path,fileName));
                // 删除临时文件
                item.delete();
            }
        }


        return "success";
    }
~~~



###### 8.9.3 文件上传原理分析

![1615550434833](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615550434833.png)



配置步骤



配置文件解析器

~~~xml

    <!--配置文件解析器对象，解析从前端页面上传的文件-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!--规定最大的文件大小，value的单位为字节-->
        <property name="maxUploadSize" value="10485760"/>
    </bean>
~~~



控制器逻辑

~~~java
    @RequestMapping("fileUpload")
    public String fileUpload(HttpServletRequest request, MultipartFile upload) throws Exception{
        System.out.println("Running fileUpload()...");

        // 使用fileupload组件
        // 指定上传位置(获取绝对路径)
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        File file = new File(path);
        // 判断路径是否存在，不存在则创建
        if (!file.exists()){
            file.mkdirs();
        }
        // 获取上传文件名称
        String fileName = upload.getOriginalFilename();
        // 生成唯一的文件id
        String uuid = UUID.randomUUID().toString().replace("-","");
        fileName = uuid + "_" + fileName;
        // 完成文件上传
        upload.transferTo(new File(path,fileName));

        return "success";
    }
~~~



注意点：

1. 控制器内的MultipartFile参数属性名需要和前端的请求标签的name属性名一致
2. springmvc.xml中配置文件解析器的bean id需要与spring框架解析器类名一致且小驼峰规则命名
3. 



###### 8.9.4 跨服务器文件上传



![1615552941021](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615552941021.png)



使用步骤



导入坐标（同时在接收用户请求的应用服务器和用于处理图片的图片服务器中导入）

~~~xml
    <dependency>
      <groupId>com.sun.jersey</groupId>
      <artifactId>jersey-core</artifactId>
      <version>1.19.4</version>
    </dependency>

    <dependency>
      <groupId>com.sun.jersey</groupId>
      <artifactId>jersey-client</artifactId>
      <version>1.19.4</version>
    </dependency>
~~~



控制器逻辑

~~~java
    /**
     *  跨服务器文件上传
     * @return
     */
    @RequestMapping("fileUpload3")
    public String fileUpload3(MultipartFile upload) throws Exception{
        System.out.println("Running fileUpload3()...");

        // 定义上传服务器路径
        String path = "http://localhost:9090/fileuploadServer/uploads/";
        // 获取上传文件名称
        String fileName = upload.getOriginalFilename();
        // 生成唯一的文件id
        String uuid = UUID.randomUUID().toString().replace("-","");
        fileName = uuid + "_" + fileName;
        // 完成文件上传，跨服务器上传

        // 创建客户端对象
        Client client = Client.create();
        // 和图片服务器建立连接
        WebResource webResource = client.resource(path + fileName);
        // 上传服务器和文件
        webResource.put(upload.getBytes());

        return "success";
    }
~~~



**注意：**

遇到的坑：

出现 Request processing failed; nested exception is com.sun.jersey.api.client.UniformInterfaceException: PUT http://localhost:9090/fileuploadServer/uploads/e7f22535abae44f8877586753d022f31_1.png returned a response status of 405 Method Not Allowed 错误

原因： tomcat中 **readonly** 这个参数默认为true，即禁止delete和put操作 

解决：需要在Tomcat目录下的conf文件夹下的web.xml中修改readonly，再重启tomcat 

![1615557928553](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615557928553.png)

~~~xml
<init-param>
        <param-name>readonly</param-name>
        <param-value>false</param-value>
</init-param>
~~~







---



##### 8.10 springMVC异常处理



![1615623431925](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615623431925.png)



###### 8.10.1 springmvc异常处理实现



位于包exception

1. 构建异常信息类

   自定义异常信息类继承自父类Exception，使用到该自定义类的方法需要抛出该异常类对象。

   ~~~java
   public class SysException extends Exception{
   
       private String message;
   
       public SysException(String message) {
           this.message = message;
       }
   
       @Override
       public String getMessage() {
           return message;
       }
   
       public void setMessage(String message) {
           this.message = message;
       }
   }
   ~~~

   

2. 构建捕获异常处理器类

   ~~~java
   public class SysExceptionResolver implements HandlerExceptionResolver {
   
       /**
        *
        * @param httpServletRequest
        * @param httpServletResponse
        * @param o
        * @param e 抛出的异常类对象SysException
        * @return
        */
       @Override
       public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
   
           SysException ex = null;
           if (e instanceof SysException){
               ex = (SysException) e;
           }else {
               ex = new SysException("系统正在维护");
           }
   
           // 利用ModelAndView跳转
           ModelAndView mv = new ModelAndView();
           mv.addObject("errorMessage",ex.getMessage());
           mv.setViewName("error");
   
           return mv;
       }
   }
   ~~~

   

3. 控制器中的调用

   控制器方法需要抛出自定义SysException

   ~~~java
       @RequestMapping("/testException")
       public String testException() throws SysException{
           System.out.println("Runing testException()...");
           try {
               int a = 10/0;
           } catch (Exception e) {
               e.printStackTrace();
               // 抛出自定义异常信息
               throw new SysException("出现错误");
           }
   
   
           return "success";
       }
   
   ~~~

   利用ModelAndView跳转到指定的error页面





##### 8.11 springMVC拦截器



###### 8.11.1 概要

功能上类似于jee里的filter拦截器，只会拦截控制器方法，不会拦截类似于css、js、html等页面或静态资源。



三大过滤器方法

preHandle、postHandle、afterCompletion



###### 8.11.2 执行流程

![1615709395825](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615709395825.png)



拦截器1preHandle->拦截器2preHandle->

控制器方法->

拦截器2postHandle->拦截器1postHandle->

页面跳转->

拦截器2afterCompletion->拦截器1afterCompletion



###### 8.11.3 使用步骤



1. 构建过滤器函数，实现`HandlerInterceptor`接口，实现三个接口方法

   ~~~java
   
   /**
    *  自定义拦截器
    * @author maxcs
    */
   public class MyInterceptor1 implements HandlerInterceptor {
   
       /**
        * 在controller方法执行前执行
        * @param request
        * @param response
        * @param handler
        * @return true 表示放行，执行下一个拦截器，如果没有则执行controller中的方法
        * @throws Exception
        *  应用场景，可以判断用户的登录状态，登录了就不拦截，没有登录就直接跳转到登录页面
        */
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           System.out.println("Running in MyInterceptor1.MyInterceptor1()...");
           //request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
           return true;
       }
   
       /**
        *  后处理的方法,当controller方法执行后，跳转页面之前需要执行的方法
        * @param request
        * @param response
        * @param handler
        * @param modelAndView
        * @throws Exception
        */
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           System.out.println("Running in MyInterceptor1.postHandle()...");
           //request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
       }
   
   
       /**
        * 在跳转完成之后执行的方法，前两个方法执行完之后还可以跳转页面
        * 这个方法是意见完成跳转之后执行，执行完成有不能再跳转页面
        * @param request
        * @param response
        * @param handler
        * @param ex
        * @throws Exception
        * 应用场景：释放资源-->用于关闭一些跳转页面之后的写入流
        */
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           System.out.println("Running in MyInterceptor1.afterCompletion()...");
       }
   }
   
   ~~~

   

2. 在resources目录下springmvc.xml中配置过滤器

   代码中配置了两个拦截器

   ~~~xml
       <!--配置mvc拦截器-->
       <mvc:interceptors>
           <!--拦截器1-->
           <!--配置具体拦截器-->
           <mvc:interceptor>
               <!--要拦截的具体的方法-->
               <mvc:mapping path="/user/*"/>
               <!--不需要拦截的方法-->
   <!--            <mvc:exclude-mapping path=""/>-->
               <!--配置拦截器对象-->
               <bean class="org.csl.interceptor.MyInterceptor1"/>
           </mvc:interceptor>
   
           <!--拦截器2-->
           <mvc:interceptor>
               <!--要拦截的具体的方法-->
               <mvc:mapping path="/**"/>
               <bean class="org.csl.interceptor.MyInterceptor2"/>
           </mvc:interceptor>
       </mvc:interceptors>
   
   ~~~

   

3. 结果验证

   ![1615643104220](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1615643104220.png)





##### *9.SSM整合

   

***整合方向：首先建立spring->整合spring与springmvc->整合spring与mybatis**



###### 9.1 整合步骤（不使用xml文件建立Mapper与接口映射的方式）

1. 导入坐标

   ~~~xml
     <properties>
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
       <spring.version>5.2.12.RELEASE</spring.version>
       <slf4j.version>1.6.6</slf4j.version>
       <log4j.version>1.2.12</log4j.version>
       <mysql.version>8.0.21</mysql.version>
       <mybatis.version>3.4.5</mybatis.version>
     </properties>
   
     <dependencies>
   
       <!-- spring -->
       <dependency>
         <groupId>org.aspectj</groupId>
         <artifactId>aspectjweaver</artifactId>
         <version>1.6.8</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-aop</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-web</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-webmvc</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-test</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-tx</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-jdbc</artifactId>
         <version>${spring.version}</version>
       </dependency>
   
       <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>4.11</version>
         <scope>compile</scope>
       </dependency>
   
       <dependency>
         <groupId>mysql</groupId>
         <artifactId>mysql-connector-java</artifactId>
         <version>${mysql.version}</version>
       </dependency>
   
       <dependency>
         <groupId>javax.servlet</groupId>
         <artifactId>servlet-api</artifactId>
         <version>2.5</version>
         <scope>provided</scope>
       </dependency>
   
       <dependency>
         <groupId>javax.servlet.jsp</groupId>
         <artifactId>jsp-api</artifactId>
         <version>2.0</version>
         <scope>provided</scope>
       </dependency>
   
       <dependency>
         <groupId>jstl</groupId>
         <artifactId>jstl</artifactId>
         <version>1.2</version>
       </dependency>
   
       <!-- log start -->
       <dependency>
         <groupId>log4j</groupId>
         <artifactId>log4j</artifactId>
         <version>${log4j.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.slf4j</groupId>
         <artifactId>slf4j-api</artifactId>
         <version>${slf4j.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.slf4j</groupId>
         <artifactId>slf4j-log4j12</artifactId>
         <version>${slf4j.version}</version>
       </dependency>
       <!-- log end -->
       <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis</artifactId>
         <version>${mybatis.version}</version>
       </dependency>
   
       <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis-spring</artifactId>
         <version>1.3.0</version>
       </dependency>
   
       <dependency>
         <groupId>c3p0</groupId>
         <artifactId>c3p0</artifactId>
         <version>0.9.1.2</version>
         <type>jar</type>
         <scope>compile</scope>
       </dependency>
   
     </dependencies>
   ~~~

   

   导入`log4j.properties`

   ![1616162207190](C:\Users\maxcs\AppData\Roaming\Typora\typora-user-images\1616162207190.png)

   

2. 整合spring与springmvc

   **（1）开启注解扫描，只扫描除控制器之外的注解，controller留给springmvc配置文件扫描**

   （2）构建实体类domain，Service、Dao接口、controller类

   

   

   配置文件`applicationContext.xml`

   ~~~xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
   	http://www.springframework.org/schema/beans/spring-beans.xsd
   	http://www.springframework.org/schema/context
   	http://www.springframework.org/schema/context/spring-context.xsd
   	http://www.springframework.org/schema/aop
   	http://www.springframework.org/schema/aop/spring-aop.xsd
   	http://www.springframework.org/schema/tx
   	http://www.springframework.org/schema/tx/spring-tx.xsd">
   
       <!--开启注解扫描-->
       <context:component-scan base-package="org.csl">
           <!--配置哪些注解不扫描-->
           <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>
   
       <!--spring整合mybatis-->
       <!--配置连接池-->
       <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
           <property name="driverClass" value="com.mysql.jdbc.Driver"/>
           <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ssm?serverTimezone=GMT%2B8&amp;
           useUnicode=true&amp;characterEncoding=utf8&amp;autoReconnect=true&amp;useSSL=false"/>
           <property name="user" value="root"/>
           <property name="password" value="mysql123"/>
       </bean>
   
       <!--配置SqlSessionFactory-->
       <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
           <property name="dataSource" ref="dataSource"/>
       </bean>
   
       <!--配置接口所在的包-->
       <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
           <property name="basePackage" value="org.csl.dao"/>
       </bean>
   
       <!--配置spring框架声明式事务管理-->
       <!--配置事务管理器对象-->
       <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <property name="dataSource" ref="dataSource"/>
       </bean>
   
       <!--配置事务通知-->
       <tx:advice id="txAdvice" transaction-manager="transactionManager">
           <tx:attributes>
               <tx:method name="find *" read-only="true"/>
           </tx:attributes>
       </tx:advice>
   
       <!--配置AOP增强-->
       <aop:config>
           <!--配置切入点表达式，建立切入点表达式与事务管理器的联系-->
           <aop:advisor advice-ref="txAdvice" pointcut="execution(* org.csl.service.impl.*ServiceImpl.*(..))"/>
       </aop:config>
   </beans>
   
   
   
   ~~~

   

   springMVC配置文件：springmvc.xml

   **（1）配置视图解析器、静态资源过滤、开启springmvc注解支持、在web.xml中配置前端控制器、更改配置文件路径、配置中文乱码过滤器，spring监听器**

   （2）往对应的Controller、Service层写注解

   （3）构建测试方法和测试页面

   

   ~~~xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:context="http://www.springframework.org/schema/context"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--开启注解扫描，只扫面Controller-->
       <context:component-scan base-package="org.csl">
           <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>
   
       <!--配置视图解析器对象-->
       <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <!--配置解析目录-->
           <property name="prefix" value="/WEB-INF/pages/"/>
           <!--配置解析目录文件后缀-->
           <property name="suffix" value=".jsp"/>
       </bean>
       <!--配置过滤静态资源-->
       <mvc:resources mapping="/js/**" location="/js/"/>
   
       <!--开启springMVC注解支持-->
       <mvc:annotation-driven/>
   </beans>
   ~~~

   

   测试方法

   ~~~java
       @Test
       public void run1(){
           // 加载配置文件
           ApplicationContext ac = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
           // 获取对象
           AccountService as = (AccountService) ac.getBean("accountService");
           // 调用方法
           as.findAll();
       }
   ~~~

   

3. 整合spring与mybatis

   （1）配置mybatis的数据库连接池、SqlSessionFactory、**接口扫描映射（与用xml管理映射不一样的地方）**到applicationContext.xml中(spring-config.xml)

   （2）在对应的Dao接口(AccountDao上使用注解配置Sql语句)

   （3）在ServiceImpl设置Dao对象、在controller设置Service对象并自动注入@Autowied，用于进行真正的整合操作

   

   **在web.xml中将启动监听，监听spring配置文件applicationConfig.xml在tomcat启动的时候创建spring容器**

   ~~~xml
     <!--设置配置文件路径，将配置文件路径调度到Resources-->
     <context-param>
       <param-name>contextConfigLocation</param-name>
       <param-value>classpath:applicationContext.xml</param-value>
     </context-param>
   
   
     <!--解决中文乱码过滤器-->
     <filter>
       <filter-name>characterEncodingFilter</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <!--设置编码集-->
       <init-param>
         <param-name>encoding</param-name>
         <param-value>UTF-8</param-value>
       </init-param>
       <!--第二个是forceEncoding，这是一个boolean类型变量，意思是是否要设置响应编码-->
       <!--设置为true，就会把响应编码方式改成encoding，即UTF-8 ，这样顺手就把响应编码乱码解决了-->
       <init-param>
         <param-name>forceEncoding</param-name>
         <param-value>true</param-value>
       </init-param>
     </filter>
   
   
     <filter-mapping>
       <filter-name>characterEncodingFilter</filter-name>
       <url-pattern>/*</url-pattern>
     </filter-mapping>
   
     <!--配置Spring监听器，用于加载spring配置文件到springContext容器，
   默认只加载WEB-INF目录下的applicationContext.xml配置文件-->
   
     <listener>
       <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
     </listener>
   
   
     <!--配置前端控制器-->
     <servlet>
       <servlet-name>dispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--加载springmvc.xml配置文件-->
       <!--启动服务器就创建-->
       <init-param>
         <param-name>contextConfigLocation</param-name>
         <param-value>classpath:springmvc.xml</param-value>
       </init-param>
       <load-on-startup>1</load-on-startup>
     </servlet>
   
     <servlet-mapping>
       <servlet-name>dispatcherServlet</servlet-name>
       <url-pattern>/</url-pattern>
     </servlet-mapping>
   
   ~~~

   

   测试整合（在没配置上述spring配置文件时，还是使用mybatis创建）

   ~~~java
   public class TestMybatis {
   
       private InputStream in;
       private SqlSession sqlSession;
       private AccountDao accountDao;
       private SqlSessionFactory factory;
   
       @Before//用于在测试方法执行之前执行
       public void init() throws IOException {
           // 1.读取配置文件，生成字节输入流
           in = Resources.getResourceAsStream("SqlMapConfig.xml");
           // 2.获取SqlSessionFactory
           factory = new SqlSessionFactoryBuilder().build(in);
           // 3.获取SqlSession对象
           sqlSession = factory.openSession();
           // 4.获取dao代理对象
           accountDao = sqlSession.getMapper(AccountDao.class);
       }
   
       @After//用于在测试方法执行之后执行
       public void destroy() throws Exception{
           // 提交事务
           sqlSession.commit();
           // 6.释放资源
           sqlSession.close();
           in.close();
       }
   
   
       @Test
       public void run1() throws Exception{
           // 执行方法
           List<Account> list = accountDao.findAll();
           for (Account account : list){
               System.out.println(account);
           }
       }
   
       @Test
       public void run2() throws Exception{
           // 执行方法
           accountDao.saveAccount(new Account(null,"Jobs", 5000d));
       }
   
   }
   
   ~~~
   
   
   
4. 最终测试

   

   控制器处理

   ~~~java
   @RequestMapping("/account")
   @Controller
   public class AccountController {
   
       @Autowired
       private AccountService accountService;
   
       /**
        *  测试springMVC
        * @return
        */
       @RequestMapping("/testFindAll")
       public String testFindAll(Model model){
           System.out.println("表现层springMVC.findAll()...");
           // 调用service方法
           List<Account> list = accountService.findAll();
           model.addAttribute("list",list);
           return "list";
       }
   
       /**
        *  用于保存用户信息
        * @param account
        * @return
        */
       @RequestMapping("/save")
       public String save(Account account) throws Exception{
           accountService.saveAccount(account);
           return "redirect:/account/testFindAll";
       }
   
   }
   ~~~

   

   前端页面

   index.jsp（**处理中文乱码，设置pageEncoding属性为UTF-8**）

   ~~~jsp
   <%@ page contentType="text/html;charset=UTF-8" pageEncoding="UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
       <a href="account/testFindAll">测试springMVC</a>
       <form action="account/save" method="post">
           姓名：<input type="text" name="name"><br/>
           金额：<input type="text" name="money"><br/>
           提交：<input type="submit" value="保存"><br/>
       </form>
   </body>
   </html>
   ~~~

   list.jsp（引入JSTL标签库）

   ~~~jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
       <h3>查询所有</h3>
       ${list}
       <c:forEach items="${list}" var="account">
           ${account.id}
           ${account.name}
           ${account.money}
       </c:forEach>
   </body>
   </html>
   ~~~



###### 9.2 使用xml映射配置文件的方式整合ssm



在9.1的基础上进行更改



9.2.1 利用外部文件对数据源进行注入



修改配置文件applicationContext(spring-mybatis-config)

~~~xml
    <!--引入外部数据库连接池配置文件jdbcConfig.properties-->
    <context:property-placeholder location="classpath:jdbcConfig.properties"/>

~~~

~~~xml
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
~~~



在SqlSessionFactory下增加mapper接口映射属性

~~~xml
    <!--配置SqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--配置mapper接口映射，在xml文件中配置SQL语句-->
        <!--配置这个可以让下面的Scanner从接口找到对应的映射配置文件*.xml的位置-->
        <property name="mapperLocations" value="classpath:org/csl/dao/*.xml"/>
    </bean>

    <!--配置接口所在的包，这个必须要有-->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--ScannerConfigurer会自动扫描basePackage所在的包-->
        <property name="basePackage" value="org.csl.dao"/>
    </bean>
~~~

**注意：**

1.  MapperScannerConfigurer会自动扫描basePackage指定的包，找到映射接口类和映射XML文件，并进行注入 。
2. 如果不配置mapperLocations属性，则默认映射配置文件在与接口在同一个包下



建立接口映射配置文件AccountDao.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.csl.dao.AccountDao">

    <select id="findAll" resultType="org.csl.domain.Account">
        select * from account
    </select>

    <insert id="saveAccount" parameterType="org.csl.domain.Account">
        insert into account (name,money) values (#{name},#{money})
    </insert>

</mapper>
~~~



最后，注释掉对应接口中的注解。













##### 10、SVN、TortoiseSVN、AdminLTE介绍与使用方法





###### 10.1 SVN



概述：自由开源的版本控制系统























   

   

   

   

   

   

   

   



