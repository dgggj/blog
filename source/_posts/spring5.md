---
title: Spring5
tags: java
cover: 'https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/images/beijing.jpg'
abbrlink: 22726
date: 2022-04-03 15:43:56
categories: spring
---
# Spring——IOC（控制反转）

## 一、IOC容器

### 1、什么是IOC（控制反转）

- a）把对象创建和对象之间的调用过程，交给Spring进行管理

- b）使用IOC目的：为了降低耦合度

### 2、IOC底层原理

-  a）xml解析     b)工厂模式         C)反射

![ys](https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/spring/origin.png)



​                             ![gc](https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/spring/factory.png)

![ioc](https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/spring/IOC.png)

- IOC（接口）

1、IOC思想基于IOC容器完成，IOC容器底层就是对象工厂

### 3、Spring提供的IOC容器实现的两种方式（两个接口）

- a）BeanFactory接口：IOC容器基本实现是Spring内部接口的使用接口，不提供给开发人员进行使用（加载配置文件时候不会创建对象，在获取对象时才会创建对象。）

- b）ApplicationContext接口：BeanFactory接口的子接口，提供更多更强大的功能，提供给开发人员使用（加载配置文件时候就会把在配置文件对象进行创建）推荐使用！

```java
//加载spring配置文件
ApplicationContext context=new ClassPathXmlApplicationText("bean1.xml");
```

### 4、ApplicationContext接口的实现类（具体根据API文档查看☺）

在idea中选中ApplictionContext上按control+h查看

![api](/Users/xiaoluyouqu/Desktop/截屏2022-01-23 下午5.00.12.png)





## 二、IOC容器-Bean管理

### 1、IOC操作Bean管理

-  a）Bean管理就是两个操作：（1）Spring创建对象；（2）Spring注入属性

### 2、Bean管理操作有两种方式

- 1）基于XML配置文件创建对象
- 2）基于注解方式实现。

#### 2.1基于XML配置文件创建对象

**基于XML配置文件创建对象**

```xml
<!--1 配置User对象创建-->
<bean id="user" class="com.atguigu.spring5.User"></bean>
```

(1)在spring配置文件中，使用bean标签，标签里添加对应的属性，就可以实现对象创建

(2)bean标签中常用属性介绍：

- id属性   唯一标识
- class属性:类全路径

(3)创建对象时，默认执行无参数构造方法

**基于xml方式注入属性**

(1)DI:依赖注入，就是注入属性

- 1.使用set方法注入

```java
//（1）传统方式： 创建类，定义属性和对应的set方法
public class Book {
        //创建属性
        private String bname;

        //创建属性对应的set方法
        public void setBname(String bname) {
            this.bname = bname;
        }
  public static void main(String[] args){
    Book book=new Book();
    book.seBname("你好");
  }
}

```

```xml
<!--（2）spring方式： set方法注入属性-->
<bean id="book" class="com.atguigu.spring5.Book">
    <!--使用property完成属性注入
        name：类里面属性名称
        value：向属性注入的值
    -->
    <property name="bname" value="Hello"></property>
    <property name="bauthor" value="World"></property>
</bean>


```

```java
//(2)spring方式测试。在一个类中
 		@Test
    public void testAdd(){
        ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
        Book book = context.getBean("book", Book.class);
        System.out.println(book);
    }
//输出
Book{bname='Hello', bauthor='World'}

```



- 2使用有参构造注入

```java
public class Book {
        //创建属性
        private String oname;
  			private String address;

        public Book(String oname,String address){
          this.oname=oname;
          this.address=address;
        }
  //（1）传统有参注入
  public static void main(String[] args){
    Book book=new Book("你好");
  }
}
```

```xml
<!--（2）spring方式：有参数构造注入属性-->
<bean id="orders" class="com.atguigu.spring5.Orders">
    <constructor-arg name="oname" value="Hello"></constructor-arg>
    <constructor-arg name="address" value="China！"></constructor-arg>
</bean>
```

- 3、p名称空间注入（了解即可）

```xml
<!--1、添加p名称空间在配置文件头部-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"		<!--在这里添加一行p-->

<!--2、在bean标签进行属性注入（算是set方式注入的简化操作）-->
    <bean id="book" class="com.atguigu.spring5.Book" p:bname="very" p:bauthor="good">
    </bean>
```

- 4注入空值和特殊符号

```xml
<bean id="book" class="com.atguigu.spring5.Book">
    <!--（1）null值-->
    <property name="address">
        <null/><!--属性里边添加一个null标签-->
    </property>
    
    <!--（2）特殊符号赋值-->
     <!--属性值包含特殊符号
       a 把<>进行转义 &lt; &gt;
       b 把带特殊符号内容写到CDATA
      -->
        <property name="address">
            <value><![CDATA[<<南京>>]]></value>
        </property>
</bean>

```





**注入属性-外部bean**

 a）创建两个类service和dao类

```java
public interface UserDao {
     void update();
}

```

```java
public class UserDaoImpl implements UserDao {
    @Override
    public void update() {
        System.out.println("update...");
    }
}
```

```java
//原始方式：创建UserDao对象
public class UserService {
    public static void main(String[] args) {
        UserDao userDao = new UserDaoImpl();
        userDao.update();
    }
}
```

```java
//spring方式创建
public class UserService {
    //创建UserDao类型属性，生成set方法
  private UserDao userDao;
   public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void add() {
        System.out.println("service add...............");
        userDao.update();//调用dao方法
    }
}
```

b)在spring配置文件中设置

```xml
<!--1 service和dao对象创建-->
<bean id="userService" class="com.atguigu.spring5.service.UserService">
    <!--注入userDao对象
        name属性：类里面属性名称
        ref属性：创建userDao对象bean标签id值
    -->
    <property name="userDao" ref="userDaoImpl"></property>
</bean>
<bean id="userDaoImpl" class="com.atguigu.spring5.dao.UserDaoImpl"></bean>
```

**基于XML方式注入内部bean和级联赋值**

a）注入属性-内部bean

>  1）一对多关系：部门和员工
>  一个部门有多个员工，一个员工属于一个部门（部门是一，员工是多）
>  2）在实体类之间表示一对多关系，员工表示所属部门，使用对象类型属性进行表示

```java
//部门类
public class Dept {
    private String dname;
    public void setDname(String dname) {
        this.dname = dname;
    }
}

//员工类
public class Emp {
    private String ename;
    private String gender;
    //员工属于某一个部门，使用对象形式表示
    private Dept dept;
    
    public void setDept(Dept dept) {
        this.dept = dept;
    }
    public void setEname(String ename) {
        this.ename = ename;
    }
    public void setGender(String gender) {
        this.gender = gender;
    }
}
```

（3）在spring配置文件中配置

```xml
<!--内部bean-->
    <bean id="emp" class="com.atguigu.spring5.bean.Emp">
        <!--设置两个普通属性-->
        <property name="ename" value="Andy"></property>
        <property name="gender" value="女"></property>
        <!--设置对象类型属性-->
        <property name="dept">
            <bean id="dept" class="com.atguigu.spring5.bean.Dept"><!--内部bean赋值-->
                <property name="dname" value="宣传部门"></property>
            </bean>
        </property>
    </bean>

```

 (4）注入属性-级联赋值

```xml
<!--方式一：级联赋值-->
    <bean id="emp" class="com.atguigu.spring5.bean.Emp">
        <!--设置两个普通属性-->
        <property name="ename" value="Andy"></property>
        <property name="gender" value="女"></property>
        <!--级联赋值-->
        <property name="dept" ref="dept"></property>
    </bean>
    <bean id="dept" class="com.atguigu.spring5.bean.Dept">
        <property name="dname" value="公关部门"></property>
    </bean>

```

```java
 //方式二：生成dept的get方法（get方法必须有！！）
    public Dept getDept() {
        return dept;
    }
```

```xml
 <!--级联赋值-->
    <bean id="emp" class="com.atguigu.spring5.bean.Emp">
        <!--设置两个普通属性-->
        <property name="ename" value="jams"></property>
        <property name="gender" value="男"></property>
        <!--级联赋值-->
        <property name="dept" ref="dept"></property>
        <property name="dept.dname" value="技术部门"></property>
    </bean>
    <bean id="dept" class="com.atguigu.spring5.bean.Dept">
    </bean>
```

 



**IOC 操作 Bean 管理——xml 注入集合属性**

>  1、注入数组类型属性 2、注入 List 集合类型属性 3、注入 Map 集合类型属性

```java
//（1）创建类，定义数组、list、map、set 类型属性，生成对应 set 方法
public class Stu {
    //1 数组类型属性
    private String[] courses;
    //2 list集合类型属性
    private List<String> list;
    //3 map集合类型属性
    private Map<String,String> maps;
    //4 set集合类型属性
    private Set<String> sets;
    
    public void setSets(Set<String> sets) {
        this.sets = sets;
    }
    public void setCourses(String[] courses) {
        this.courses = courses;
    }
    public void setList(List<String> list) {
        this.list = list;
    }
    public void setMaps(Map<String, String> maps) {
        this.maps = maps;
    }
```

```xml
<!--（2）在 spring 配置文件进行配置-->
    <bean id="stu" class="com.atguigu.spring5.collectiontype.Stu">
        <!--数组类型属性注入-->
        <property name="courses">
            <array>
                <value>java课程</value>
                <value>数据库课程</value>
            </array>
        </property>
        <!--list类型属性注入-->
        <property name="list">
            <list>
                <value>张三</value>
                <value>小三</value>
            </list>
        </property>
        <!--map类型属性注入-->
        <property name="maps">
            <map>
                <entry key="JAVA" value="java"></entry>
                <entry key="PHP" value="php"></entry>
            </map>
        </property>
        <!--set类型属性注入-->
        <property name="sets">
            <set>
                <value>MySQL</value>
                <value>Redis</value>
            </set>
        </property>
</bean>


```

 **2、在集合里面设置对象类型值**

```java
  //学生所学多门课程
    private List<Course> courseList;//创建集合
    public void setCourseList(List<Course> courseList) {
        this.courseList = courseList;
    }
```

```xml
    <!--创建多个course对象-->
    <bean id="course1" class="com.atguigu.spring5.collectiontype.Course">
        <property name="cname" value="Spring5框架"></property>
    </bean>
    <bean id="course2" class="com.atguigu.spring5.collectiontype.Course">
        <property name="cname" value="MyBatis框架"></property>
    </bean>
    
   	<!--注入list集合类型，值是对象-->
		<bean id="stu" class="com.atguigu.spring5.collectiontype.Stu">
       <property name="courseList">
           <list>
               <ref bean="course1"></ref>
               <ref bean="course2"></ref>
           </list>
       </property>
		</bean>

```

```xml
<!--第一步：在 spring 配置文件中引入名称空间 util-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util" <!--添加util名称空间-->
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">  <!--添加util名称空间-->
    
<!--第二步：使用 util 标签完成 list 集合注入提取-->
<!--把集合注入部分提取出来-->
 <!--1 提取list集合类型属性注入-->
    <util:list id="bookList">
        <value>易筋经</value>
        <value>九阴真经</value>
        <value>九阳神功</value>
    </util:list>

 <!--2 提取list集合类型属性注入使用-->
    <bean id="book" class="com.atguigu.spring5.collectiontype.Book" scope="prototype">
        <property name="list" ref="bookList"></property>
    </bean>


```





**IOC 操作 Bean 管理（FactoryBean）**

> 1、Spring 有两种类型 bean，一种普通 bean，另外一种工厂 bean（FactoryBean）

> 2、普通 bean：在配置文件中定义 bean 类型就是返回类型

> 3、工厂 bean：在配置文件定义 bean 类型可以和返回类型不一样 
>
> 第一步 创建类，让这个类作为工厂 bean，实现接口 FactoryBean 
>
> 第二步 实现接口里面的方法，在实现的方法中定义返回的 bean 类型

```java
public class MyBean implements FactoryBean<Course> {

    //定义返回bean
    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setCname("abc");
        return course;
    }
}
```

```xml
//xml中配置
<bean id="myBean" class="com.atguigu.spring5.factorybean.MyBean">
</bean>
```

**IOC 操作 Bean 管理（bean 作用域）**

在 Spring 里面，默认情况下，bean 是单实例对象，下面进行作用域设置：验证是否为单实例对象，可以创建两个对象，比较它们的地址是否相同。

```java
	   	@Test
			public void test(){ 				
				ApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml");
        Emp emp = context.getBean("emp", Emp.class);
        Emp emp1=context.getBean("emp",Emp.class);
        System.out.println(emp.hashCode());
        System.out.println(emp1.hashCode());
    }
```



> > （1）在 spring 配置文件 bean 标签里面有属性（scope）用于设置单实例还是多实例
> >
> > （2）scope 属性值 第一个值 默认值，singleton，表示是单实例对象 第二个值 prototype，表示是多实例对象

```xml
<bean id="book" class="com.atguigu.spring5.collectiontype.Book" scope="prototype"><!--设置为多实例-->
        <property name="list" ref="bookList"></property>
</bean>

```

（3）singleton 和 prototype 区别

 a）singleton 单实例，prototype 多实例

 b）设置 scope 值是 singleton 时候，加载 spring 配置文件时候就会创建单实例对象 ；设置 scope 值是 prototype 时候，不是在加载 spring 配置文件时候创建对象，在调用 getBean 方法时候创建多实例对象

**IOC 操作 Bean 管理（bean 生命周期）**

    1、生命周期 ：从对象创建到对象销毁的过程
    
    2、bean 生命周期
    
     （1）通过构造器创建 bean 实例（无参数构造）
    
     （2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）
    
     （3）调用 bean 的初始化的方法（需要进行配置初始化的方法）
    
     （4）bean 可以使用了（对象获取到了）
    
     （5）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）
    
    3、演示 bean 生命周期 ：

```java
public class Orders {
         //无参数构造
    public Orders() {
         System.out.println("第一步 执行无参数构造创建 bean 实例");
     }
         private String oname;
    public void setOname(String oname) {
         this.oname = oname;
         System.out.println("第二步 调用 set 方法设置属性值");
     }
         //创建执行的初始化的方法
    public void initMethod() {
         System.out.println("第三步 执行初始化的方法");
     }
    @Test
    public void test(){
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml");
        Emp emp = context.getBean("emp", Emp.class);
         System.out.println("第四步 获取创建bean的实例对象");
        System.out.println(emp.hashCode());
      context.close();
        
     }
        
         //创建执行的销毁的方法
    public void destroyMethod() {
         System.out.println("第五步 执行销毁的方法");
     }
}
```



```xml
<!--配置文件的bean参数配置-->
<bean id="orders" class="com.atguigu.spring5.bean.Orders" init-method="initMethod" destroy-method="destroyMethod">	<!--配置初始化方法和销毁方法-->
    <property name="oname" value="手机"></property><!--这里就是通过set方式（注入属性）赋值-->
</bean>

<!--配置后置处理器-->
<bean id="myBeanPost" class="com.atguigu.spring5.bean.MyBeanPost"></bean>

```

```java
public class MyBeanPost implements BeanPostProcessor {//创建后置处理器实现类
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之前执行的方法");
        return bean;
    }
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之后执行的方法");
        return bean;
    }
}
```





> 4、bean 的后置处理器，bean 生命周期有七步 （正常生命周期为五步，而配置后置处理器后为七步）
>
> （1）通过构造器创建 bean 实例（无参数构造）
>
> （2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）
>
> （3）把 bean 实例传递 bean 后置处理器的方法 postProcessBeforeInitialization
>
> （4）调用 bean 的初始化的方法（需要进行配置初始化的方法）
>
> （5）把 bean 实例传递 bean 后置处理器的方法 postProcessAfterInitialization
>
> （6）bean 可以使用了（对象获取到了）
>
> （7）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）

**IOC操作Bean管理（xml自动装配）**

- **什么是自动装配**

（1）根据指定装配规则（属性名称或者属性类型），spring自动将匹配的属性注入

- **演示自动装配过程**

（1）根据属性名称自动注入

> bean标签属性autoawire，配置自动装配
>
> autowire属性常用的两个值：
>
> ​											byName根据属性名称注入，注入值bean的id值和类属性名称一样
>
> ​											byType：根据属性类型注入



```xml
 <bean id="emp" class="org.autowried.Emp" autowire="byName">
<!--        <property name="dept" ref="dept"></property>-->
 </bean>
 <bean id="dept" class="org.autowried.Dept"></bean>
```

\

**IOC 操作 Bean 管理(外部属性文件)**

- **直接配置数据库信息**

1）配置Druid（德鲁伊）连接池 （2）引入Druid（德鲁伊）连接池依赖 jar 包



```xml
<!--    直接配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/mybaties"></property>
        <property name="username" value="root"></property>
        <property name="password" value="hu824407"></property>
		</bean>
```

- **引入外部属性文件配置数据库连接池**

> （1）创建外部属性文件，properties 格式文件，写数据库信息（**jdbc.properties**）



```properties
prop.driverClass=com.mysql.jdbc.Driver
prop.url=jdbc:mysql://localhost:3306/mybaties
prop.userName=root
prop.password=hu824407
```



```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"><!--引入context名称空间-->
    
        <!--引入外部属性文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClass}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.userName}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>
    
</beans>

```





#### 2.2基于注解方式

**什么是注解**

（1）注解是代码特殊标记。格式；@注解名称（属性名称=属性值，属性名称=属性值）

（2）注解：作用在方法、属性、类上面

（3）目的：简化xml配置





**Spring针对Bean管理中创建对象提供注解**

（1）@Component

（2）@Service

（3）@Controller

（4）@Repository

*上面四个注解功能是一样的，都可以用来创建bean实例





**基于注解对对象创建**

- 第一步，引入依赖
- 第二步，开启组件扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                        http://www.springframework.org/schema/context  http://www.springframework.org/schema/context/spring-context.xsd">
<!--    开启组件扫描-->
    <context:component-scan base-package="com.che"></context:component-scan>

</beans>
```

- 第三步，创建类，在类上面添加创建对象注解

```java
//注解里面属性值可以忽略不写
//默认值是类名称把首字母小写
@Component(value = "userService")//等价<bean id="userService class="com....">
public class UserService {
    public void add(){
        System.out.println("add...");
    }
}
```

*开启组件扫描的细节特点

```xml
<!--    示例1 use-default-filters="false"表示现在不使用默认的filter，自己配置filter
        context:include-filter:设置扫描哪些内容-->
    <context:component-scan base-package="com.che" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    
<!--    示例2
        context:exclude-filter 设置不去扫描哪些内容-->
    <context:component-scan base-package="com.che">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

```



**基于注解方式实现属性注入**

(1)  @AutoWired   根据属性类型进行自动装配

第一步：把service和dao创建，在service和dao类上添加创建对象注解

第二部：service注入dao属性，在service类添加dao类型属性

```java
@Service(value = "userService")//等价<bean id="userService class="com....">
public class UserService {
    //定义dao类型属性，不需要添加set方法
    //添加注入属性注解
    @Autowired
    private UserDao userDao;

    public void add(){
        System.out.println("add...");
        userDao.add();
    }
}
```



(2)  @Qualifier		根据属性名称进行注入

和上面的@Autowired一起使用(因为UserDao这个借口可能不止UserDaoImpl这一个实现类。所以需要用这个指定)

```java
 //定义dao类型属性，不需要添加set方法
    //添加注入属性注解
    @Autowired
    @Qualifier(value = "userDaoImpl")
    private UserDao userDao;

    public void add(){
        System.out.println("add...");
        userDao.add();
    }
```



(3)  @Resource		可以根据类型注入，也可以根据名称注入

- 根据属性

```java
    @Resource
    private UserDao userDao;
```

- 根据名称

```java
   @Resource(name = "userDaoImpl")
    private UserDao userDao;
```



(4)  @Value	注入普通类型属性



```java
@Service(value = "userService")//等价<bean id="userService class="com....">
public class UserService {
    @Value(value = "che")
    private String name;
    //定义dao类型属性，不需要添加set方法
    //添加注入属性注解
    @Resource(name = "userDaoImpl")
    private UserDao userDao;

    public void add(){
        System.out.println("add..."+name);
        userDao.add();
    }
}
```







 **完全注解开发**

(1)创建配置类,代替配置文件

```java
package com.che.config;

@Configuration  //作为配置类代替bean.xml等配置文件
@ComponentScan(basePackages = {"com.che"})
public class SpringConfig {
}
```

(2)编写测试类

```java
    @Test
    public void test2(){
        //加载配置类
        ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
        UserService userService = context.getBean("userService", UserService.class);
        System.out.println(userService);
        userService.add();
    }
```









# Spring-AOP

## 一、AOP(概念)

### 1什么是AOP

(1)面前切面编程（方便），利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高开发效率

(2)不通过修改源代码方式，在主干功能里添加新功能



### 2AOP底层原理



#### 2.1AOP底层使用动态代理



（1）有两种情况动态代理

第一种  有接口情况，使用JDK动态代理

- 创建接口实现类代理对象，增强类的方法

![sd](https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/spring/JDK.jpg)

第二种 没有接口情况，使用CGLIB动态代理

- 创建子类的代理对象，增强类的方法

![sd](https://blog-chy-lua.oss-cn-shanghai.aliyuncs.com/spring/CGLIB.png)

#### 2.2AOP(JDK动态代理)

1.使用JDK动态代理，使用Proxy类里方法创建代理对象

java.lang.reflect.Proxy

- 调用newProxyInstance方法

方法有三个参数：

 第一参数，类加载器

 第二参数，增强方法所在的类，这个类实现的接口，*支持多个接口*

 第三参数，实现这个接口 InvocationHandler，创建代理对象，写增强的部分



2编写JDK动态代理代码

(1)创建接口，定义方法

```java
package com.che.dao;

public interface UserDao {
    public int add(int a,int b);
    public String update(String id);
}

```

(2)创建接口实现类，实现方法

```java
package com.che.dao;

public class UserDaoImpl implements UserDao{
    @Override
    public int add(int a, int b) {
        return a+b;
    }

    @Override
    public String update(String id) {
        return id;
    }
}
```

(3)使用Proxy类创建接口代理对象

(3)使用Proxy类创建接口代理对象

```java
public class JDKProxy {
    public static void main(String[] args) {
        //创建接口实现类代理对象
        Class[]interfaces={UserDao.class};
        UserDaoImpl userDao=new UserDaoImpl();
        UserDao dao =(UserDao) Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces, new UserDaoProxy(userDao));
        int add = dao.add(1, 2);
        System.out.println("result:"+add);

    }
}
class UserDaoProxy implements InvocationHandler{
    //创建谁的代理对象，把谁传过来
    private Object obj;
    public UserDaoProxy(Object obj){
        this.obj=obj;
    }
    //增强逻辑
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //方法之前执行
        System.out.println("方法执行之前。。。"+method.getName()+" :传递的参数。。。"+ Arrays.toString(args));

        //被增强的方法执行
        Object res = method.invoke(obj, args);

        //方法执行之后
        System.out.println("方法执行后。。。"+obj);


        return res;
    }
}

```

```
结果：
方法执行之前。。。add :传递的参数。。。[1, 2]
方法执行后。。。com.che.spring5.UserDaoImpl@79fc0f2f
result:3
```



### 3AOP术语



- 1连接点

- ```java
  class User{
    add()                  1连接点：类里哪些方法可以被增强，这些方法称为连接点
    update()                2切入点：实际被真正增强的方法，称为切入点。。这四个方法都可以被增强。但实际增强的叫切入点     
    select()                   
    delete()
  }
  ```

- 2切入点

- 3通知（增强）

- ```
  (1)实际增强的部分称为通知
  通知（增强）有多种类型
  - 1前置通知
  - 2后置通知
  - 3环绕通知
  - 4异常通知
  - 5最终通知
  ```

- 4切面：是动作

- （1）把通知（增强）应用到切入点的过程

### 4AOP操作（准备）



1. Spring框架一般都是基于AspectJ实现AOP操作

   （1）什么是AspectJ？

   ​         AspectJ不是Spring组成部分，独立的AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作

2. 基于AspectJ实现AOP操作

​		（1）基于xml配置文件实现

​		 （2）基于注解方式实现（常用）

3. 在项目工程中引入AOP相关依赖





4切入点表达式

- 切入点表达式作用：知道对哪个类里面哪个方法进行增强
- 语法结构

excution(【权限修饰符】【返回类型】【类全路径】【方法名称】（【参数列表】）)

举例一：对com.atqia.dao.BookDao类里面的add方法进行增强

execution（* com.atqia.dao.BookDao.add(..)）

举例二对com.atqia.dao.BookDao类里面的所有方法进行增强

execution（* com.atqia.dao.BookDao.*(..)）

举例三对com.atqia.dao包里所有类里面的所有方法进行增强

execution（* com.atqia.dao.\*.*(..)）





### 5AOP操作（AspectJ注解）

- 1. 创建类，在类里面定义方法（被增强类）

     ```java
     package com.che.spring5.aopanno;
     
     public class User {
         public void add(){
             System.out.println("add....");
         }
     }
     ```

  2. 创建增强类（编写增强逻辑）

     ```java
     package com.che.spring5.aopanno;
     
     public class UserProxy {
        //前置通知
         public void before(){
             System.out.println("before.....");
         }
     }
     ```

  3. 进行通知到配置

     （1）在spring配置文件中，开启注解扫描

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:context="http://www.springframework.org/schema/context"
            xmlns:aop="http://www.springframework.org/schema/aop"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                             http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                             http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
     <!--    开启注解扫描-->
         <context:component-scan base-package="com.che.spring5.aopanno"></context:component-scan>
     
     </beans>
     ```

     (2)使用注解创建User和UserProxy对象

     ```java
     package com.che.spring5.aopanno;
     
     import org.springframework.stereotype.Component;
     
     @Component
     public class UserProxy {
         //前置通知
         public void before(){
             System.out.println("before.....");
         }
     }
     ```

     （3）在增强类上面添加注解@Aspect

       ```java
     package com.che.spring5.aopanno;
     
     import org.aspectj.lang.annotation.Aspect;
     import org.springframework.stereotype.Component;
     //增强的类
     @Component
     @Aspect  //生成代理对象
     public class UserProxy {
         //前置通知
         public void before(){
             System.out.println("before.....");
         }
     }
       ```

     (4)在spring配置文件中开启生成代理对象

     ```xml
     <!--    开启Aspect生成代理对象-->
         <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
     ```

  4. 配置不同类型的通知

     （1）在增强类里面，在作为通知方法上面添加通知类型注解，使用切入点表达式配置

     ```java
     @Component
     @Aspect
     public class UserProxy {
         //前置通知
         //Before注解表示作为前置通知
         @Before(value = "execution(* com.che.spring5.aopanno.User.add(..))")
         public void before(){
             System.out.println("before.....");
         }
       
       @After(value = "execution(* com.che.spring5.aopanno.User.add(..))")
         public void after(){//有么有异常都输出.
             System.out.println("after......");
         }
     
         @AfterReturning(value = "execution(* com.che.spring5.aopanno.User.add(..))")
         public void afterReturning(){//有异常不输出.结果后输出
             System.out.println("afterreturning.....");
         }
     
         @AfterThrowing(value = "execution(* com.che.spring5.aopanno.User.add(..))")
         public void afterThrowing(){
             System.out.println("afterthrowing.....");
         }
         @Around(value = "execution(* com.che.spring5.aopanno.User.add(..))")
         public void around(ProceedingJoinPoint proceedingJoinPoint)throws Throwable{
             System.out.println("环绕之前。。。。。");
             proceedingJoinPoint.proceed();
             System.out.println("环绕之后。。。。。。");
         }
     }
     ```

     测试

     ```java
         @Test
          public void testAop(){
             ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
             User user = context.getBean("user", User.class);
             user.add();
         }
     
     
     输出：
       环绕之前。。。。。
     before.....
     add....
     afterreturning.....
     after......
     环绕之后。。。。。。
     ```

     手动给个异常

     ```java
     @Component
     public class User {
         public void add(){
             int a=10/0;
             System.out.println("add....");
         }
     }
     ```

     在测试得出

     ```
     环绕之前。。。。。
     before.....
     afterthrowing.....
     after......
     
     java.lang.ArithmeticException: / by zero
     ```

     5. 公共切入点的抽取。

```java
 //相同切入点的抽取
    @Pointcut(value = "execution(* com.che.spring5.aopanno.User.add(..))")
    public void pointDemo(){
        
    }
    //前置通知
    //Before注解表示作为前置通知
    @Before(value = "pointDemo()")
    public void before(){
        System.out.println("before.....");
    }
```



6有多个增强类对同一个方法进行增强，设置增强类的优先级

（1）在增强类上添加@Order（数值）,数值越小优先级越高



```java
@Component
@Aspect
@Order(1)
public class ProxyUser {
    @Before(value = "execution(* com.che.spring5.aopanno.User.add(..))")
    public void before(){
        System.out.println("proxy的before增强方法");
    }
}
```



输出

```
proxy的before增强方法
环绕之前。。。。。
before.....
add....
afterreturning.....
after......
环绕之后。。。。。。

```



7. 完全使用注解开发

   （1）创建配置类不需要创建xml文件

   ```java
   package com.che.spring5.Aopconf;
   
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.annotation.EnableAspectJAutoProxy;
   
   @Configuration
   @ComponentScan("com.che")
   @EnableAspectJAutoProxy(proxyTargetClass = true)
   public class AopXml {
   }
   ```

   测试

   ```java
   public class TestAop {
   
   
       @Test
        public void testAop(){
           ApplicationContext context = new AnnotationConfigApplicationContext(AopXml.class);
           User user = context.getBean("user", User.class);
           user.add();
       }
   }
   ```

   

   



### 6AOP操作（AspectJ配置文件）(了解)


