# Spring5

## 课程内容介绍

1、Spring概述

2、IOC容器

3、AOP

4、JDBCTemplate

5、事物管理

6、Spring5新特性

## 一    Spring概述

1. Spring是轻量级的开源JavaEE开发框架
2. 目的：解决企业应用开发的复杂性
3. Spring有两个核心部分：==IOC和AOP==
   - ***IOC：控制反转，把创建对象的过程交给Spring进行管理***
   - ***AOP：面向切面，不修改源代码进行功能增强***
4. Spring特点：
   - 方便解耦，简化开发
   - AOP编程的支持
   - 声明式事物的支持
   - 方便程序测试
   - 方便和其他框架进行整合
   - 降低JavaEE API的使用难度
   - Spring源码是学习典范
5. Spring入门案例

xml配置文件：

```xml
<!--    配置Base01对象01创建-->
    <bean id="user" class="test.User"></bean>
```

Spring通过配置文件创建对象：

```java
package test;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test01 {
    @Test
    public void test() {
        //加载Spring配置文件
        ApplicationContext context =new ClassPathXmlApplicationContext("bean1.xml");
        //获取配置创建的对象
        User user = context.getBean("user", User.class);
        System.out.println(user);
        user.add();
    }
}
```

## 二    IOC(控制反转)

### （1） IOC底层原理

- 什么是IOC？

IOC是控制反转，把对象创建和对象之间的调用过程，交给Spring统一进行管理

目的：降低耦合度

- IOC底层用到哪些？

xml解析、工厂模式、反射

### （2）IOC接口（BeanFactory）

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂

2. Spring提供IOC容器实现两种方式（接口）：
   
   - BeanFactory:IOC容器基本实现，是Spring内部的使用接口，不提供开发人员使用
     
     - 加载配置文件时不会创建对象，在获取对象时才去创建对象
   
   - ApplicationContext：Beanfactory的子接口，提供更强大的功能，一般由开发人员使用
     
     - 加载配置文件时就会创建对象

### （3）IOC操作Bean管理（基于xml）

- 什么是Bean管理？
  
  1. Spring创建对象
     
     ==创建对象默认使用无参构造方法==
  
  2. Spring注入属性（DI，也叫依赖注入）

- Bean管理操作有两种方式
  
  1. 基于xml配置文件方式实现
  2. 基于注解方式实现

#### 1、set方法注入属性

```java
 public class Book {
     private String bookname;
     private double price;

     public void setBookname(String bookname) {
         this.bookname = bookname;
     }

     public void setPrice(double price) {
         this.price = price;
     }

     public void printBook () {
         System.out.println("书名:" + bookname + "\n书的价格:" + price);
     }
 }
```

     ```xml
         <bean id="book" class="study.Book">
              <property name="bookname" value="一千零一夜"></property>
              <property name="price" value="99.99"></property>
         </bean>
    
     ```

![结果](C:\Users\Zenith\AppData\Roaming\Typora\typora-user-images\image-20220317114238214.png)

#### 2、有参构造方法注入属性

```java
public Book(String bookname, double price) {
    this.bookname = bookname;
    this.price = price;
}
```

```xml
    <bean id="book" class="study.Book">
         <constructor-arg name="bookname" value="三国演义"></constructor-arg>
         <constructor-arg name="price" value="199.99"></constructor-arg>
    </bean>
```

![image-20220317170543316](C:\Users\Zenith\AppData\Roaming\Typora\typora-user-images\image-20220317170543316.png)

#### 3、基于p名称空间注入

```xml
<!--添加p名称空间在配置文件中：-->
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:p="http://www.springframework.org/schema/p"

    <bean id="book" class="study.Book" p:bookname="西游记" p:price="199.99">
    </bean>
</beans>
```

#### 4、xml注入其他类型属性

##### （1） 字面量

```xml
    <!--注入null值-->
     <property name="adress">
         <null/>
     </property>

    <!--属性值包括特殊符号-->
    <!--符号转义|把带特殊符号的内容写入CDATA-->
     <property name="address">
         <value>
             <![CDATA[<<南京>>]]>
         </value>
     </property>
```

##### （2）注入外部bean

```xml
    <!--注入外部bean-->
    <bean id="userService" class="UserService">
         <property name="userDao" ref="userDao"></property>
    </bean>
    <bean id="userDao" class="UserDaoImp"></bean>
```

##### （3）注入内部bean和级联赋值

```xml
     <!--内部bean-->
     <bean id="emp" class="study.IOC.lesson3.Employee">
        <property name="empname" value="张三"></property>
        <property name="gender" value="男"></property>
        <property name="dept">
            <bean id="dept" class="study.IOC.lesson3.Department">
                <property name="depname" value="安保"></property>
            </bean>
        </property>
     </bean>


     <!--级联赋值 方法一-->     
     <bean id="emp" class="study.IOC.lesson3.Employee">
         <property name="empname" value="张三"></property>
         <property name="gender" value="男"></property>
         <property name="dept" ref="dept">
         </property>
     </bean>
     <bean id="dept" class="study.IOC.lesson3.Department">
         <property name="depname" value="财务部"></property>
     </bean>

     <!--级联赋值 方法二-->     
     <bean id="emp" class="study.IOC.lesson3.Employee">
         <property name="empname" value="李四"></property>
         <property name="gender" value="女"></property>
         <property name="dept" ref="dept"></property>
         <!--需要设置dept的get方法-->
         <property name="dept.depname" value="营销部"></property>
     </bean>
     <bean id="dept" class="study.IOC.lesson3.Department"></bean>
```

##### （4）注入集合类型

```xml
    <bean id="students" class="study.IOC.lesson4.Students">
          <!--数组类-->
          <property name="grades">
              <array>
                  <value>三</value>
                  <value>四</value>
                  <value>五</value>
              </array>
          </property>

            <!--List类-->
          <property name="genders">
              <list>
                  <value>男</value>
                  <value>女</value>
                  <value>男</value>
              </list>
          </property>

          <!--Map类-->
          <property name="scores">
              <map>
                   <entry key="王小明" value="98.5"></entry>
                   <entry key="黄小飞" value="96.5"></entry>
                   <entry key="李大强" value="95.0"></entry>
              </map>
          </property>

        <!--集合的值为对象类型-->
          <property name="courses">
              <list>
                  <ref bean="course1"></ref>
                  <ref bean="course2"></ref>
                  <ref bean="course3"></ref>
              </list>
          </property>
    </bean>

    <bean id="course1" class="study.IOC.lesson4.Course">
          <property name="cname" value="Spring5"></property>
    </bean>
    <bean id="course2" class="study.IOC.lesson4.Course">
          <property name="cname" value="SpringMVC"></property>
    </bean>
    <bean id="course3" class="study.IOC.lesson4.Course">
          <property name="cname" value="MyBatis"></property>
    </bean>
```

##### （5）属性注入提取

```xml
 <!--属性注入抽取-->
 <beans
     xmlns:util="http://www.springframework.org/schema/util"
     xsi:schemaLocation="http://www.springframework.org/schema/util                  http://www.springframework.org/schema/util/spring-util.xsd">

     <util:list id="genders">
         <value>男</value>
         <value>女</value>
         <value>男</value>
     </util:list>
     <bean id="students2" class="study.IOC.lesson4.Students">
         <property name="genders" ref="genders"></property>
     </bean>
 </beans>
```

##### （6）FactoryBean

==工厂bean的返回类型可以和定义时不一致==

```java
//需要实现FactoryBean接口
public class MyBean implements FactoryBean {

    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        return course;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return FactoryBean.super.isSingleton();
    }
}
```

##### （7）bean的作用域和生命周期

==在Spring中，可以设置创建bean实例是单实例还是多实例==

```xml
<!--创建多实例Bean--> 
<bean id="myBean" class="study.IOC.lesson5.MyBean" scope="prototype"></bean>

<!--创建单实例Bean--> 
<bean id="myBean" class="study.IOC.lesson5.MyBean" scope="singleton"></bean>
```

scope值为singleton时，在加载配置文件时就会创建实例

scope值为prototype时，在调用getBean方法时才会创建实例

bean的生命周期：

1. 通过构造器创建bean实例
2. 为bean的属性设置值和对其他bean的引用（调用set方法）
3. 调用bean的初始化方法（需要进行配置）
4. bean的使用
5. 当容器关闭时，调用bean的销毁方法（需要进行配置）

配置后置处理器需要实现一个叫BeanPostProcessor的接口，并重写

```java
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("初始化方法之前");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("初始化方法之后");
        return bean;
    }
```

```xml
    <bean id="book" class="study.IOC.lesson5.Book"
          init-method="initMethod" destroy-method="desMethod">
        <property name="bname" value="罗杰疑案"></property>
    </bean>
    <!--配置后置处理器-->
    <bean id="myBeanPost" class="study.IOC.lesson5.MyBeanPost"></bean>
```

##### （8）自动装配

autowire配置自动装配，常用属性值：byName,byType

```xml
    <bean id="dog" class="study.IOC.lesson6.Dog" autowire="byType">
    </bean>
    <bean id="breed1" class="study.IOC.lesson6.Breed">
        <property name="color" value="white"></property>
    </bean>
```

##### （9） 导入外部文件

```xml
<!--需要先引入context名称空间-->
<context:property-placeholder location="classpath:pro_File/dog.properties"/>
```

### （4）IOC操作Bean管理（基于注解）

Bean管理创建对象提供的注解主要有：

1. @Component
2. @Service
3. @Controller
4. @Repository

#### 1、使用注解创建对象的步骤

第一步，引入依赖

![image-20220612133214990](C:\Users\Zenith\AppData\Roaming\Typora\typora-user-images\image-20220612133214990.png)

第二步，开启组件扫描

```xml
<!--需要引入context名称空间-->
<!--开启组件扫描-->
<context:component-scan base-package="study.IOC.lesson7"></context:component-scan>
```

第三步，创建类，在类上面添加创建对象的注解  

```java
@Component(value = "haoChiMao")
public class Cat {
    public void scream() {
        System.out.println("喵喵喵~~~");
    }
}
```

#### 2、组件扫描细节

```xml
<!--组件扫描配置-->
    <context:component-scan base-package="study.IOC.lesson7" use-default- filters="false">
        <!--        只会扫描带Component注解的类-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Component"/>
    </context:component-scan>

    <context:component-scan base-package="study.IOC.lesson7">
        <!--        不会扫描带Component注解的类-->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Component"/>
    </context:component-scan>
```

#### 3、使用注解方式实现属性注入

1. @Autowired：根据属性类型自动装配

2. @Qualifier：根据属性名称进行注入【需要和@Autowired一起使用】

3. @Resource：可以根据属性类型，也可以根据名称注入【javax包中】

4. @Value：注入普通类型属性
   
   ```java
   @Component
   public class Novel {
     @Autowired
     public Category category;
   }
   ```

#### 4、完全注解开发

1. 创建配置类

```java
@Configuration
@ComponentScan(basePackages = {"study.IOC.lesson8"})
public class l8_config { 
}
```

2. 创建注解配置应用context

```java
ApplicationContext context =
            new AnnotationConfigApplicationContext(l8_config.class);
```

## 三    AOP(面向切面)

### （1）什么是AOP？

AOP即Aspect Oriented Programming，面向切面编程  ，通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术，是OOP的延续。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各个部分之间的耦合度降低，提高程序的可重用性，同时提高开发的效率。

AOP底层原理：使用==动态代理==，有两种情况动态代理——有接口和无接口。

有接口，使用==JDK动态代理==：创建接口实现类代理对象，增加类的方法

无接口，使用==CGLIB动态代理==：创建当前类子类的代理对象

### （2）JDK动态代理AOP

1. 使用Proxy类里面的方法创建代理对象

```java
newProxyInstance(ClassLoader loader, 类<?>[] interfaces, InvocationHandler h)
```

第一个参数，类加载器

第二个参数，增强方法所在的类，这个类实现的接口（支持多个接口）

第三个参数，实现InvocationHandler, 创建代理对象，写增强的方法

2. 示例：

```java
//创建接口
public interface UserDao {
    public int add(int a, int b)；
    public String update(String id);
}
//实现接口
public class UserDaoImpl implements UserDao {
    @override
    public int add(int a, int b) {
        return a + b;
    }

     @override
    public int update(String id) {
        return id;
    }
}
```

```java
public class JDKProxy {
    public static void main(String[] args) {
        Class[] interfaces = {UserDao.class};
        UserDao userDao = new UserDaoImpl();
        UserDao dao = (UserDao)Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces, new UserDaoProxy(userDao));
        int result = dao.add(2, 3);
        System.out.println(result);
    }
}

class UserDaoProxy implements InvocationHandler {
    //需要创建的代理对象，就把谁传过来
    private Object obj;

    public UserDaoProxy(Object obj) {
        this.obj = obj;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //方法之前执行
        System.out.println("方法之前······" + method.getName() + "传递的参数" + Arrays.toString(args));

        //被增强的方法执行
        Object res = method.invoke(obj, args);

        //方法之后
        System.out.println("方法之后······" + obj);
        return res;
    }
}
```
