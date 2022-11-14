# Spring框架概述

+ Spring是轻量级的开源的JavaEE框架
+ Spring 可以解决企业应用开发的复杂性
+ Spring 有两个核心部分：**IOC** 和 **Aop**
  + IOC：控制反转，把`创建对象过程交给 Spring 进行管理，不需要用new的过程创建对象`
  + Aop：`面向切面`，不修改源代码进行功能增强

## Spring 特点

+ 方便解耦，简化开发
+ Aop 编程支持，即不改变源代码也能扩展功能
+ 方便程序测试
+ 方便和其他框架进行整合
+ 方便进行事务操作
+ 降低 API 开发难度

## Spring5的集成IDEA

1. 下载Spring5：[repo.spring.io](https://repo.spring.io/ui/native/release/org/springframework/spring/)

2. 进入地址选择5.2.6稳定版本![image-20211001184701318](.\Spring5Notes_images\image-20211001184701318.png)

3. 下载`spring-5.2.6.RELEASE-dist.zip`并解压

4. 打开IDEA，创建一个普通的Java工程，如下图，最后Finish创建即可![image-20211001214704125](.\Spring5Notes_images\image-20211001214704125.png)![image-20211001214740177](.\Spring5Notes_images\image-20211001214740177.png)![image-20211001214828629](.\Spring5Notes_images\image-20211001214828629.png)

5. 在该工程下新建lib目录并导入Spring5相关jar包，先导入四个基本要用到的包，和其依赖的日志包

   + spring-`beans`-5.2.6.RELEASE.jar
   + spring-`context`-5.2.6.RELEASE.jar
   + spring-`core`-5.2.6.RELEASE.jar
   + spring-`expression`-5.2.6.RELEASE.jar
   + `commons-logging`-1.1.1.jar

   ![image-20211001233606927](.\Spring5Notes_images\image-20211001233606927.png)

6. 在Project Structure中点击+号，Add JARS，将jar包添加进模块Modules，点击OK，APPLY即可![image-20211001233654276](.\Spring5Notes_images\image-20211001233654276.png)![image-20211001233804602](.\Spring5Notes_images\image-20211001233804602.png)![image-20211001233911165](.\Spring5Notes_images\image-20211001233911165.png)

7. 创建一个普通的类，在这个类中创建普通的方法

   ```java
   package com.martha.spring5;
   
   public class User {
       void add(){
           System.out.println("add...");
       }
   }
   ```

8. 在**src目录下创建Spring配置文件**，在配置文件中配置创建的对象

   + 注意：Spring配置文件使用xml格式

   + xml文件的名称可自定义![image-20211001234623895](.\Spring5Notes_images\image-20211001234623895.png)

   + 文件内容如下

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
       <!--配置User对象的创建-->
       <bean id="user" class="com.martha.spring5.User"></bean>
     </beans>
     ```

9. 配置完毕后，可进行代码测试编写

   ```java
   public class TestSpring {
       @Test
       public void test() {
           // 1.首先加载Spring配置文件
           ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
           // 2.获取配置创建的对象
           User user = context.getBean("user", User.class);
           System.out.println(user);
           user.add();
       }
   }
   ```

# IOC容器

## IOC底层原理

### 什么是IOC

+ IOC就是控制反转，把对象的创建和对象之间的调用过程，交给Spring进行管理
+ 使用IOC的目的：为了降低耦合度
+ 以上的入门案例就是IOC实现

### IOC底层原理

+ **xml解析**
+ **工厂模式**
+ **反射**

> ​	**原始方式**实现在UserService的execute方法内部调用UserDao的add方法，需要先在execute方法内部创建UserDao对象，随后使用userDao对象调用add方法实现execute方法内部调用add方法
>
> ​	但是这种原始的方式，耦合度太高了，UserService与UserDao之间的关联过于紧密，当UserDao的路径变化了，UserService也要跟着变，当UserDao的add方法变了，UserService也要跟着变，不利于程序的扩展。，虽然能实现功能，但是就是耦合度太高了，我们追求高内聚、低耦合

![image-20211002000945213](.\Spring5Notes_images\image-20211002000945213.png)

> ​	**工厂模式**：通过工厂的设计模式完成了一个解耦操作，通过工厂来创建对象，降低了UserService与UserDao之间的耦合度，但也只是进一步降低了耦合度而已
>
> ​	但是，虽然降低了UserService与UserDao之间的耦合度，但是与工厂之间也还是存在耦合度，要明确的是，实际开发中，类与类之间或对象与对象之间，不可能完全没有耦合度，只能够降低到最低限度，我们最终的目的就是将耦合度降低到最低限度

![image-20211002001522597](.\Spring5Notes_images\image-20211002001522597.png)

> **IOC过程：**虽然也是存在耦合，但是更进一步降低了耦合度，如UserDao所处的包变了，那么只需要改动xml文件UserDao所对应的全类名即可，主要用到了xml文件、工厂模式、反射更进一步降低了耦合度

![image-20211002002206267](.\Spring5Notes_images\image-20211002002206267.png)

## IOC接口

+ IOC思想基于IOC容器完成，IOC容器就是**对象工厂**

+ Spring提供了IOC容器实现的两种方式：有如下两个接口，二者功能相似，但还是有区别的

  1. `BeanFactory`

     + IOC容器最基本实现方式，是Spring本身内置的自己用的接口，所以开发中不去使用

     + 加载配置文件的时候，不会创建对象，而是在getBean获取对象或使用对象时才去创建对象

       ```java
       public class TestSpring {
           @Test
           public void test() {
               // 1.首先加载Spring配置文件，此时仅仅只加载配置文件，不会创建对象
               BeanFactory context = new ClassPathXmlApplicationContext("bean.xml");
               // 2.此时才会进行对象的创建
               User user = context.getBean("user", User.class);
               System.out.println(user);
               user.add();
           }
       }
       ```

  2. `ApplicationContext`**开发推荐**

     + 是BeanFactory接口的子接口，提供了更多更强大的功能，一般由开发人员进行使用

     + 加载配置文件的时候，就会将配置文件中配置的对象全部进行创建`虽然使用BeanFactory会节省资源，等用到时在进行创建对象，但是我们一般将这种耗时耗资源的过程都在服务器启动时就进行完成，而不是用到了再去耗时耗资源`

       ```java
       public class TestSpring {
           @Test
           public void test() {
               // 1.首先加载Spring配置文件，此时就已经加载了配置文件中配置过的全部的对象了
               BeanFactory context = new ClassPathXmlApplicationContext("bean.xml");
               // 2.获取对象
               User user = context.getBean("user", User.class);
               System.out.println(user);
               user.add();
           }
       }
       ```

  3. ApplicationContext接口的两个主要的实现类：

     1. `FileSystemXmlApplicationContext`：那么就是指定xml配置文件在磁盘上的全路径
     2. `ClassPathXmlApplicationContext`：对应的是src类路径下的xml配置文件

     ![image-20211002003920125](.\Spring5Notes_images\image-20211002003920125.png)

## IOC操作Bean管理（基于XML）

### 什么是Bean管理

> Bean管理指的是两个操作
>
> + Spring创建对象：即在bean.xml中bean配置的类，即称为创建对象
> + Spring注入属性：

### Bean管理操作有两种方式

1. 基于xml配置文件的方式实现Bean管理操作
2. 基于注解方式实现Bean管理操作

#### 基于XML方式创建对象

1. 在spring配置文件中使用bean标签，在标签中添加对应属性，就可以实现对象的创建

   ```xml
   <!--
   	id属性：给该对象起的别名，是获取该对象的唯一标识，主要用于getBean方法的第一个参数，如getBean("user", User.class);
   	class属性：表示创建对象所在类的全路径
   -->
   <bean id="user" class="com.martha.spring5.User"></bean>
   ```

2. 创建对象的时候，默认执行的也是类中**无参数的构造方法**完成对象的创建，所以一定要在类中提供无参构造器

#### 基于XML方式注入一般属性

> DI：是IOC中的一种具体实现，表示依赖注入，就是注入属性，需要在创建对象的基础上完成

```java
// 第一种注入方式，通过对象.setter方法注入设置类的属性
Book book = new Book();
book.setBook_name("abc"); // 原始方式设置类的属性
```

```java
// 第二种注入方式：有参构造器注入
public Book(String book_name) {
    this.book_name = book_name;
}
```

> 以上两种注入方式都是原始注入方式，通过类的setter和有参构造器进行属性的赋值操作，以下演示这两种操作的Spring的依赖注入

##### 通过set方式注入属性

1. 首先，肯定要有一个类，其后定义类的属性与对应的setter方法（老标准JavaBean了）

   ```java
   public class Book {
       private String name;
       private String author;
   
       public void setName(String name) {
           this.name = name;
       }
   
       public void setAuthor(String author) {
           this.author = author;
       }
   }
   ```

2. 在Spring配置文件**bean标签配置对象的创建**，并在bean标签**内部**使用**property标签配置属性的注入**

   ```xml
   <!--使用set方法注入对象属性值-->
   <bean id="book" class="com.martha.spring5.Book">
       <!--
   		使用property标签完成属性注入
   		property标签的name属性对应类中属性名称
   		property标签的value属性表示：向属性中注入的值
       -->
       <property name="name" value="Martha"/>
       <property name="author" value="Martha-X"/>
   </bean>
   ```

   > ​	走到这一步，就可以理解，依赖注入就是要在对象创建的基础上完成对属性的赋值操作，也就是要先创建对象，才能进行注入操作

##### 有参构造器注入属性

1. 首先，肯定要有一个类，其后定义**类的属性**与对应的**有参构造方法**（老标准JavaBean了）

   ```java
   public class Book {
       private String name;
       private String author;
       public Book(String name, String author) {
           this.name = name;
           this.author = author;
       }
   }
   ```

2. 在Spring配置文件中配置

   ```xml
   <!--constructor-arg方式一：通过属性名直接对应注入
   	使用constructor-arg标签完成属性注入
   	constructor-arg标签name属性表示：有参构造器中形参名
   	constructor-arg标签的value属性表示：向属性注入的值
   -->
   <constructor-arg name="name" value="MarthaX"></constructor-arg>
   <constructor-arg name="author" value="MarthaXR"></constructor-arg>
   ```

   ```xml
   <!--constructor-arg方式二：通过索引顺序对应注入【但不常用，因为可读性差】
   	constructor-arg标签index属性表示：有参构造器的参数索引下标，可以将有参构造器中的参数理解为一个数组，0代表有参构造器括号中的第一个参数，以此类推...
   	constructor-arg标签的value属性表示：向属性注入的值
   -->
   <constructor-arg index="0" value="姓名"></constructor-arg>
   <constructor-arg index="1" value="作者"></constructor-arg>
   ```

   > 需要注意的是，通过有参构造器注入参数的方式只会通过有参构造器创建对象，

##### p名称空间注入

> 使用p名称空间注入，可以简化基于xml配置的方式，该方式是对之前set注入的方式做了进一步的简化

1. 在xml配置文件中添加p名称空间

   ```xml
   xmlns:p="http://www.springframework.org/schema/p"
   ```

   ![image-20211012214637868](.\Spring5Notes_images\image-20211012214637868.png)

2. 进行属性注入，在bean标签里面进行操作

   ```xml
   <!--使用p名称空间形式的set属性注入-->
   <bean id="book" class="com.martha.spring5.Book" p:name="MarthaXX" p:author="MarthaXR"></bean>
   ```

#### 基于XML方式注入其它类型属性

##### 注入属性-字面量

> 字面量就是给属性设置的固定值，给字面量赋值可以赋值以下类型的值
>
> + null值
> + 包含特殊符号的属性值

###### 给属性设置空值

```xml
<!--设置空值的方式就是使用一个null标签代表该属性的值-->
<bean id="book" class="com.martha.spring5.Book">
    <property name="name">
        <null></null>
    </property>
    <property name="author" value="Martha"></property>
</bean>
```

###### 给属性设置包含特殊符号的值

```xml
<!--设置包含特殊符号的值的方式就是使用一个<![CDATA[]>标签,将包含特殊符号的内容值写入[]中即可完成赋值-->
<bean id="book" class="com.martha.spring5.Book">
    <property name="name">
        <value><![CDATA[<<南京南京>>]]></value>
    </property>
    <property name="author" value="Martha"></property>
</bean>
```

##### 注入属性-外部bean

1. 创建两个类，如UserDaoImpl与UserService类，在UserService类中调用UserDao接口的实现方法，也就是UserDaoImpl类

   ```java
   public class UserService {
       // 1.创建UserDao类型属性，并生成set方法
       private UserDao userDao;
   
       // 2.生成userDao的setter方法
       public void setUserDao(UserDao userDao) {
           this.userDao = userDao;
       }
   
       public void add() {
           System.out.println("service add...");
           userDao.update();//执行userDao的update()方法
       }
   }
   ```

   ```java
   public class UserDaoImpl implements UserDao {
       @Override
       public void update() {
           System.out.println("UserDaoImpl update()..");
       }
   }
   ```

2. 通过Spring配置文件注入外部bean

   ```xml
   <!--创建service和dao对象创建，并在userService中完成UserDao属性的赋值-->
   <bean id="userService" class="com.martha.service.UserService">
       <!--注入UserService中的UserDao属性
   		property标签的name属性代表的是属性名
   		property标签的ref属性：指明要注入的bean的唯一标识id，即可完成对外部bean的注入操作
       -->
       <property name="userDao" ref="userDaoImpl"></property>
   </bean>
   <bean id="userDaoImpl" class="com.martha.dao.impl.UserDaoImpl"></bean>
   ```

##### 注入属性-内部bean和级联赋值

###### 注入属性-内部bean

1. 创建员工类和部门类，员工类中有部门的成员变量，用于表示该员工隶属于哪一个部门，可通过外部bean注入的方式给该引用对象赋值，也可以通过内部bean注入的方式给该引用对象赋值

   ```java
   // 部门类
   public class Dept {
       private String name;
   
       public void setName(String name) {
           this.name = name;
       }
   
       public String getName() {
           return name;
       }
   }
   ```

   ```java
   // 员工类
   public class Emp {
       private String name;
       private String gender;
       private Dept dept;  // 员工属于某一个部门，使用对象形式表示
   
       public void setName(String name) {
           this.name = name;
       }
   
       public void setGender(String gender) {
           this.gender = gender;
       }
   
       public void setDept(Dept dept) {
           this.dept = dept;
       }
   
       public void show() {
           System.out.println(name + " " + gender + "在" + dept.getName() + "工作");
       }
   }
   ```

2. 通过spring配置文件配置对象信息，内部bean注入的方式就是在property标签中嵌套一个bean标签的配置

   ```xml
   <!--配置对象的创建-->
   <bean id="emp" class="com.martha.bean.Emp">
       <!--为员工的两个普通的属性赋值-->
       <property name="name" value="Martha"></property>
       <property name="gender" value="男"></property>
       <!--设置对象类型的属性Dept，可以使用引入外部bean的方式，但这里演示使用内部bean的方式-->
       <property name="dept">
           <!--在一个bean中，嵌套另外一个bean，就叫做内部bean方式-->
           <bean id="dept" class="com.martha.bean.Dept">
               <property name="name" value="IT部"></property>
           </bean>
       </property>
   </bean>
   ```

###### 注入属性-级联赋值

> 还是以上员工类和部门类

1. **级联赋值的第一种写法**

   ```xml
   <!--配置对象的创建-->
   <bean id="emp" class="com.martha.bean.Emp">
       <!--为员工的两个普通的属性赋值-->
       <property name="name" value="Martha"></property>
       <property name="gender" value="男"></property>
       <!--通过引用外部bean的方式，完成级联赋值，emp对象创建的同时，属性被初始化，引用了dept对象，dept对象的属性也相继完成了初始化，这就是级联赋值-->
       <property name="dept" ref="dept"></property>
   </bean>
   <!--dept对象的创建及属性的赋值-->
   <bean id="dept" class="com.martha.bean.Dept">
       <property name="name" value="IT Section"></property>
   </bean>
   ```

2. **级联赋值的第二种写法**

   ```xml
   <!--配置对象的创建-->
   <bean id="emp" class="com.martha.bean.Emp">
       <!--为员工的两个普通的属性赋值-->
       <property name="name" value="Martha"></property>
       <property name="gender" value="男"></property>
       <!--引用外部bean -> dept为其后的代码提供前提-->
       <property name="dept" ref="dept"></property>
       <!--有一个前提，由于要获取dept的对象才能调用dept的name属性，所以要在emp类中生成dept对象的getter方法，否则无法使用-->
       <property name="dept.name" value="技术部"></property>
   </bean>
   <!--dept对象的创建配置-->
   <bean id="dept" class="com.martha.bean.Dept"></bean>
   ```

##### 注入属性-集合属性的注入

###### 注入数组类型属性

1. 定义一个学生类，学生要学很多课程【定义数组、list、map、set】类型属性并生成对应的set方法，通过spring配置文件的方式注入多个课程信息

   ```java
   //学生类
   public class Student {
       private String[] coursesArr; // 学生要学的课程数组类型
   
       private List<String> coursesList; // 学生要学的课程集合类型
   
       private Map<String, String> courseMap; // 学生要学的课程的Map类型
       
       private Set<String> coursesSet; // 学生要学的课程的无序集合类型
   
       public void setCourses(String[] coursesArr) {
           this.coursesArr = coursesArr;
       }
   
       public void setCourseMap(Map<String, String> courseMap) {
           this.courseMap = courseMap;
       }
   
       public void setCoursesList(List<String> coursesList) {
           this.coursesList = coursesList;
       }
       
       public void setCoursesSet(Set<String> coursesSet) {
           this.coursesSet = coursesSet;
       }
   }
   ```

2. 通过spring的配置文件配置Student对象的创建和数组等集合内容的注入

   ```xml
   <!--配置Student类对象的创建-->
   <bean id="student" class="com.martha.collectiontype.Student">
       <!--数组类型的注入：使用array标签代表要注入的是数组类型值，多个value标签代表多个值-->
       <property name="coursesArr">
           <array>
               <value>计算机操作系统</value>
               <value>数据库系统概论</value>
               <value>高级语言程序设计</value>
           </array>
       </property>
   </bean>
   ```

###### 注入List集合类型属性

> 还是Student类

```xml
<!--配置Student类对象的创建-->
<bean id="student" class="com.martha.collectiontype.Student">
    <!--List集合类型的注入：使用list标签代表要注入的是List集合类型值，多个value标签代表多个值-->
    <property name="coursesList">
        <list>
            <value>软件工程</value>
            <value>数据结构与算法分析</value>
            <value>集合论与图论</value>
        </list>
    </property>
</bean>
```

###### 注入Map集合类型属性

```xml
<!--配置Student类对象的创建-->
<bean id="student" class="com.martha.collectiontype.Student">
    <!--Map类型的注入：使用map标签代表要注入的是Map类型值，多个entry标签代表多个值，key和value是在entry标签上设置-->
    <property name="courseMap">
        <map>
            <entry key="高级语言程序设计" value="Java"></entry>
            <entry key="集合论与图论" value="离散数学"></entry>
            <entry key="数据库系统概论" value="MySQL"></entry>
        </map>
    </property>
</bean>
```

###### 注入Set集合类型属性

```xml
<!--配置Student类对象的创建-->
<bean id="student" class="com.martha.collectiontype.Student">
    <!--Set类型的注入：使用set标签代表要注入的是Set类型值，多个value标签代表多个值-->
    <property name="coursesSet">
        <set>
            <value>HTML</value>
            <value>JavaScript</value>
            <value>Vue</value>
        </set>
    </property>
</bean>
```

##### 在集合中设置对象引用类型值

> 注入List集合类型，List的值是对象引用类型

1. 定义一个学生类和课程类，学生有一个课程集合属性表示多门课程在学的学生

   ```java
   //学生类
   public class Student {
       private List<Course> courseList; // 课程对象集合
       
       public void setCourseList(List<Course> courseList) {
           this.courseList = courseList;
       }
   }
   ```

2. 在spring配置文件中配置对象引用的注入

   ```xml
   <!--配置Student类对象的创建-->
   <bean id="student" class="com.martha.collectiontype.Student"><property name="courseList">
       <!--使用ref标签与其bean属性指明要注入的对象唯一标识，即可完成注入-->
       <property name="courseList">
           <list>
               <ref bean="course1"></ref>
               <ref bean="course2"></ref>
               <ref bean="course3"></ref>
           </list>
       </property>
   </bean>
   <!--配置课程类对象的创建，用于Student类课程集合的注入-->
   <bean id="course1" class="com.martha.collectiontype.Course">
   	<property name="name" value="计算机科学"></property>
   </bean>
   <bean id="course2" class="com.martha.collectiontype.Course">
   	<property name="name" value="离散数学"></property>
   </bean>
   <bean id="course3" class="com.martha.collectiontype.Course">
   	<property name="name" value="Java"></property>
   </bean>
   ```

##### 提取集合属性的注入部分

1. 在spring配置文件中引入名称空间util

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          <!--1.添加如下这一句-->
          xmlns:util="http://www.springframework.org/schema/util"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
   		<!--2.复制上一段并将beans替换为util即可-->
                              http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
   </beans>
   ```

2. 使用util标签完成List集合注入提取以及使用

   ```xml
   <!--提取list集合类型属性注入-->
   <util:list id="public_courseLists">
       <value>HTML</value>
       <value>JS</value>
       <value>REACT</value>
   </util:list>
   
   <!--使用所提取的公共的List集合类型属性-->
   <bean id="student2" class="com.martha.collectiontype.Student">
       <!--向Student类中的课程集合注入公共的集合值，ref代表引用公共集合的唯一标识-->
       <property name="coursesList" ref="public_courseLists"></property>
   </bean>
   ```

### Bean管理之FactoryBean（工厂Bean）

> Spring有两种类型的bean，一种普通Bean，另外一种就是工厂Bean（FactoryBean）

#### 普通Bean的特点

> 在配置文件中定义的bean的类型就是返回类型`理解如下`

```xml
<!--全类名最后一定是类名，所以getBean()返回的类型也一定是该类-->
<bean id="student" class="com.martha.collectiontype.Student"></bean>
```

```java
class Test(){
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    Student stu = context.getBean("student",Student.class); // 返回值类型一定是Student
}
```

#### 工厂Bean（了解含义即可）

> 工厂Bean的特点就是：在配置文件中定义的bean的类型可以与所返回的类型不一致`通过如下步骤理解`

1. **定义一个类**，让这个类作为工厂Bean，且需要**实现FactoryBean接口**

   ```java
   public class MyFactoryBean implements FactoryBean{...}
   ```

2. 实现接口中的方法，**在实现的方法中定义返回的类型**

   ```java
   public class MyFactoryBean implements FactoryBean<Course> {
   
       /**
        * @return 定义返回的Bean的实例对象
        * @throws Exception
        */
       @Override
       public Course getObject() throws Exception {
           Course course = new Course();
           course.setName("Java");
           return course;
       }
   
       /**
        * @return 返回Bean的类型
        */
       @Override
       public Class<?> getObjectType() {
           return null;
       }
   
       /**
        * @return 是否是一个单例的对象
        */
       @Override
       public boolean isSingleton() {
           return FactoryBean.super.isSingleton();
       }
   }
   
   ```

### Bean的作用域

> 在Spring配置文件中，设置创建Bean实例是单实例还是多实例，单例还是多例就是Bean的作用域
>
> 单实例：getBean()返回的都是同一个对象
>
> 多实例：getBean()返回的都是新建的对象

+ 在Spring中，默认情况下，Bean是单实例对象

  ```java
  public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("bean4.xml");
      Student stu1 = context.getBean("student", Student.class);
      Student stu2 = context.getBean("student",Student.class);
      // 默认返回的都是同一个对象
      System.out.println(stu1); //com.martha.collectiontype.Student@27a5f880
      System.out.println(stu2); //com.martha.collectiontype.Student@27a5f880
      System.out.println(stu1 == stu2); // true
  }
  ```

+ 可在Spring配置文件bean标签属性`scope`中设置该单实例还是多实例，scope常用两个取值如下：

  + `singleton`：默认值，表示单实例对象
  + `prototype`：表示多实例对象
  + request：创建对象时候会自动放入request域对象中（不常用，了解即可）
  + session：创建对象时候会自动放入session域对象中（不常用，了解即可）

  ```xml
  <bean id="student" class="com.martha.collectiontype.Student" scope="prototype"></bean>
  ```
  
  ```java
  public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("bean4.xml");
      Student stu1 = context.getBean("student", Student.class);
      Student stu2 = context.getBean("student",Student.class);
      System.out.println(stu1); // com.martha.collectiontype.Student@1d29cf23
      System.out.println(stu2); // com.martha.collectiontype.Student@5f282abb
      System.out.println(stu1 == stu2); // false
  }
  ```
  
+ `singleton`与`prototype`的区别

  + 设置bean标签scope属性值为singleton时（或者不设置即默认singleton），在加载Spring配置文件的时候（`ApplicationContext context = new ClassPathXmlApplicationContext("bean4.xml");`）就会创建单实例对象
  + 设置bean标签scope属性值为prototype时，并不是在加载Spring配置文件时创建多实例对象，而是在调用getBean()方法的时候创建多实例的对象

### Bean的生命周期

> 生命周期指的是一个从对象创建到销毁的过程

#### bean的生命周期5个基本步骤

1. 在Spring.xml配置文件中配置bean标签，默认**通过空参构造器创建bean实例**
2. **调用setter方法**为bean中的属性**设置属性值**以及注入对其它bean的引用
3. 调用bean的初始化方法（需要进行配置）
4. bean实例就获取到了，bean对象就可以使用了
5. 当容器关闭的时候会调用bean销毁的方法（需要进行配置销毁的方法）

#### bean生命周期的演示

1. 定义一个Orders类

   ```java
   public class Orders {
       private String name;
   
       public Orders() {
           System.out.println("1.（创建对象） Orders类的无参构造器...");
       }
   
       public void setName(String name) {
           this.name = name;
           System.out.println("2.（设置属性值） name属性值被注入...");
       }
   
       //配置创建执行的初始化的方法，该初始化方法需要在bean标签中配置方可生效
       public void initMethod() {
           System.out.println("3.（执行初始化方法）");
       }
   
       //配置销毁时执行的方法，该销毁方法需要在bean标签中配置方可生效
       public void destroyMethod() {
           System.out.println("5.（执行销毁方法）");
       }
   }
   ```

2. 配置Spring配置文件

   ```xml
   <!--
   	init-method代表所要执行的初始化方法名，即生命周期第三步的配置
   	destroy-method代表所要执行的销毁方法名，即生命周期第五步的配置
   -->
   <bean id="orders" class="com.martha.bean.Orders" init-method="initMethod" destroy-method="destroyMethod">
       <property name="name" value="Martha"></property>
   </bean>
   ```

3. 测试运行

   ```java
   ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean5.xml");
   Orders orders = context.getBean("orders", Orders.class); // 生命周期第四步：获取实例对象
   System.out.println("4.（获取实例对象）" +  orders);
   //通过context.close()方法手动使得bean实例销毁，ApplicationContext中没有close方法，只有实现类ClassPathXmlApplicationContext具有
   context.close(); //容器关闭
   ```

#### 添加后置处理器步骤的bean生命周期7个步骤的最终演示

**步骤如下:**

1. 在Spring.xml配置文件中配置bean标签，默认**通过空参构造器创建bean实例**
2. **调用setter方法**为bean中的属性**设置属性值**以及注入对其它bean的引用
3. `将bean实例传递至bean后置处理器的postProcessBeforeInitialization方法`
4. 调用bean的初始化方法（需要进行配置）
5. `将bean实例传递至bean后置处理器的postProcessAfterInitialization方法`
6. bean实例就获取到了，bean对象就可以使用了
7. 当容器关闭的时候会调用bean销毁的方法（需要进行配置销毁的方法）

**演示步骤如下：**

1. 定义一个类，实现接口BeanPostProcessor，**创建后置处理器**，在其中有两个方法即实现第三步和第五步的执行（将BeanPostProcessor中的两个方法复制进自定义类中，将权限修饰符改为public，将注解改为Override）

   ```java
   public class MyBeanPost implements BeanPostProcessor {
       @Override
       public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
           System.out.println("在生命周期初始化方法之前执行的方法");
           return bean;
       }
   
       @Override
       public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
           System.out.println("在生命周期初始化方法之后执行的方法");
           return bean;
       }
   }
   ```

2. 在Spring配置文件中配置自定义后置处理器

   ```xml
   <!--配置自定义的后置处理器，Spring会将该自定义的类（实现了BeanPostProcessor的类）作为后置处理器来执行，会为当前配置文件中配置的所有bean实例都添加后置处理器执行-->
   <bean id="myBeanPost" class="com.martha.bean.MyBeanPost"></bean>
   ```

   ![image-20211016192623385](.\Spring5Notes_images\image-20211016192623385.png)

### XML自动装配

#### 什么自动装配

> 自动装配指的是根据指定装配规则（属性名或者属性类型），Spring自动将匹配的属性值进行注入，简化了手动操作
>
> + 根据属性名称自动装配
> + 根据属性类型自动装配
>
> ```xml
> <bean id="book" class="com.martha.spring5.Book">
>     <!--之前是通过手动指定属性名，手动装配其值-->
>     <property name="name" value="Martha"/>
> </bean>
> ```

#### 演示自动装配的过程

1. 创建Emp和Dept类，定义如下：

```java
public class Emp {
    private Dept dept;

    public void setDept(Dept dept) {
        this.dept = dept;
    }

    @Override
    public String toString() {
        return "Emp{" +
                "dept=" + dept +
                '}';
    }

    public void test() {
        System.out.println(dept);
    }
}

public class Dept {
    private String name;

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Dept{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

```xml
<!--
    实现自动装配
    bean标签属性autowire用于指定通过何种方式自动装配
        byName：根据属性名称进行自动注入，注入值bean的id唯一标识（如dept）和类（Emp）的属性名称（dept）要一样
                byName不会出现byType所要担心的问题，因为bean的id标识是唯一的（需要保证所有bean.id唯一），且类的成员变量也不能定义相同的
        byType：根据属性类型进行自动注入，类（Emp）的属性dept的类型为Dept，Spring会找到类型为Dept类型的bean进行注入
                byType具有的问题就是如果配置文件中有多个相同类型的bean（需要保证所有的class唯一），那么Spring就无法确定要注入哪一个
-->
<bean id="emp" class="com.martha.autowire.Emp"></bean>
<bean id="dept" class="com.martha.autowire.Dept" autowire="byName"></bean>
或
<bean id="dept" class="com.martha.autowire.Dept" autowire="byType"></bean>
```

### 引入外部属性文件

> 如在spring配置文件中引入其它配置文件中配置的内容，如数据库配置文件

1. 直接在Spring配置文件中配置Druid数据库连接池的信息

   1. 配置Druid连接池

   2. 引入Druid连接池依赖jar包，置于lib目录下

      ```xml
      <!--直接配置Druid数据库连接池-->
      <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
          <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
          <property name="url" value="jdbc:mysql///test"></property>
          <property name="username" value="root"></property>
          <property name="password" value="123456"></property>
      </bean>
      
      ```

2. 引入外部属性文件配置数据库连接池

   1. 在src目录下创建properties配置文件，内容如下

      ```properties
      # 推荐使用自定义前缀，如prop，避免多个键重复冲突
      prop.driverClass=com.mysql.jdbc.Driver
      prop.url=jdbc:mysql://localhost:3306/test
      prop.userName=root
      prop.password=123456
      ```

   2. 在Spring配置文件中引入properties文件中的对应的键值对

      1. 首先要引入context名称空间

      ```xml
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                 http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
      </beans>
      ```

      2. 在Spring配置文件使用标签引入外部属性文件

         ```xml
         <!--引入外部properties文件-->
         <context:property-placeholder location="classpath:jdbc.properties"/>
         
         <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
             <property name="driverClassName" value="${prop.driverClass}"></property>
             <property name="url" value="${prop.url}"></property>
             <property name="username" value="${prop.userName}"></property>
             <property name="password" value="${prop.password}"></property>
         </bean>
         ```
      
   3. 导入mysql-connector-java-5.1.40.jar驱动，并获取连接
   
      ```java
      @Test
      public void testDruid() throws SQLException {
          ApplicationContext context = new ClassPathXmlApplicationContext("bean_context.xml");
          DruidDataSource dataSource = context.getBean("dataSource", DruidDataSource.class);
          DruidPooledConnection connection = dataSource.getConnection();
          System.out.println(connection); // 获取连接成功
      }
      ```

## IOC操作Bean管理（基于注解）

### 什么是注解

> 注解是代码特殊标记；格式：@注解名称（属性名称=属性值，属性名称=属性值..）

+ 注解可以作用在类上面，方法上面，属性上面
+ 使用注解的目的：简化XML配置

### Spring针对Bean管理中创建对象提供注解

+ @Component
+ @Service
+ @Controller
+ @Repository

> 上面四个注解的功能是相同的，都可以用于创建bean实例，但每个注解都应用在不同层面中，使得开发人员更加清晰，当前组件所扮演的角色

### 基于注解方式实现对象的创建

1. 引入`spring-aop-5.2.6.RELEASE.jar`依赖![image-20211017203733090](.\Spring5Notes_images\image-20211017203733090.png)

2. **开启组件扫描**，告诉Spring在哪个类加了注解要去扫描哪一个类，看哪个有注解

   1. 引入context名称空间

      ```xml
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
      
      </beans>
      ```

   2. 通过context:component-scan标签开启组件扫描，并指定扫描位置

      ```xml
      <!--
      	如果要扫描多个包：
      		多个包之间使用逗号隔开【com.martha.dao,com.martha.service..】
      		或者指定多个包共同的父目录(包)【如下】
      -->
      <context:component-scan base-package="com.martha"></context:component-scan>
      ```

   3. 创建类，在类上面添加创建对象注解

      ```java
      // value可以省略不写，不写的话默认就是类名且其首字母小写UserService---userService
      @Component(value = "userService") // 相当于<bean id="userService" class="com.martha.service"></bean>
      public class UserService {
          public void add() {
              System.out.println("service add..");
          }
      }
      ```

### 组件扫描配置的常见分析

```xml
<!--
        1.use-default-filters：表示不再使用Spring默认的扫描方式，而使用自定义的扫描方式
        2.context:include-filter标签表示如何扫描，以下的含义就是不再使用Spring默认的扫描方式，只扫描com.martha包下所有类型（type）为注解的（annotation）
        且注解类型为Controller的类【只扫描com.martha包下@Controller注解的类，其它内容都不扫描】
-->
<context:component-scan base-package="com.martha" use-default-filters="false">
    <context:include-filter type="annotation"
                  expression="org.springframework.stereotype.Controller">			</context:include-filter>
</context:component-scan>
```

```xml
<!--
   context:exclude-filter：设置不扫描哪些内容
   context:exclude-filter标签表示不扫描哪些内容，以下的含义就是使用Spring默认的扫描方式，扫描com.martha包下所有的但不包括类型（type）为注解的（annotation）
   且注解类型为Controller的类【不扫描@Controller注解的类，com.martha包下其它内容都进行扫描】
-->
<context:component-scan base-package="com.martha">
    <context:exclude-filter type="annotation"
                         expression="org.springframework.stereotype.Controller">	</context:exclude-filter>
</context:component-scan>
```

### 基于注解实现属性的注入

> 注解方式注入属性最常用的前三个注解如下：

1. `@Autowired`：**根据属性类型**进行**自动**注入或**装配**
2. `@Qualifier`：**根据属性名称**进行注入或**装配**
3. `@Resource`：可以**根据属性类型**，也可以根据**属性名称**进行注入或**装配**
4. `@Value`：注入普通类型属性

#### Autowired注解演示

> 创建UserDao接口与其实现类，并在UserService类中调用UserDao接口的方法，使用Autowired注解自动装配UserDao属性，代码如下

```java
public interface UserDao {
    void add();
}

@Repository
public class UserDaoImpl implements UserDao{

    @Override
    public void add() {
        System.out.println("UserDao add()..");
    }
}
```

```java
@Service
public class UserService {
    /*
        定义dao类型属性，且不需要添加set方法
        Autowired根据类型进行注入
     */
    @Autowired
    private UserDao userDao;

    public void add() {
        System.out.println("service add..");
        userDao.add();
    }
}
```

> 缺点：一个接口可以有多个实现类，这时候根据类型注入就会存在问题，可以使用Autowired结合Qualifier注解明确指定根据哪一个名称自动注入，请往后走↓

#### Qualifier注解演示

> 创建UserDao接口与其实现类，并在UserService类中调用UserDao接口的方法，使用Autowired注解结合Qualifier注解自动装配UserDao属性，代码如下：

```java
public interface UserDao {
    void add();
}

@Repository(value="userDaoImpl1") // 因要根据名称进行自动注入，所以在此指定该实现类的名称
public class UserDaoImpl implements UserDao{

    @Override
    public void add() {
        System.out.println("UserDao add()..");
    }
}
```

```java
@Service
public class UserService {
    /*
        定义dao类型属性，且不需要添加set方法
        Autowired根据类型进行注入
     */
    @Autowired
    @Qualifier("userDaoImpl1") // 指明要注入的类型的属性名称
    private UserDao userDao;

    public void add() {
        System.out.println("service add..");
        userDao.add();
    }
}
```

#### Resource注解演示

+ Resource根据类型自动注入的演示

  ```java
  @Resource
  private UserDao userDao;
  ```

+ Resource根据名称进行自动注入的演示

  ```java
  @Resource(name="userDaoImpl1")
  private UserDao userDao;
  ```

> Resource注解是javax即Java扩展包下的注解，而不是Spring下的注解，Spring官方更推荐使用Autowired和Qualifier注解，但是功能可以做到

#### Value注解的演示

```java
@Value(value="Martha")
private String name;
```

### 完全注解开发

> 了解即可，后期用SpringBoot，自然就用到了

1. 定义一个类，替代SpringXML配置文件，如下：

   ```java
   @Configuration  // 作为配置类：替代XML配置文件
   @ComponentScan(basePackages = {"com.martha"})
   public class SpringConfig {
   }
   ```

2. 编写测试类，加载配置类文件进行测试，这时候就要用到`AnnotationConfigApplicationContext`对象了

   ```java
   @Test
   public void testConfigurationClass(){
       ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class); // 之前是加载配置文件，如今使用到了配置类，那么就要转向去加载配置类才能获取到信息
       UserService userService = context.getBean("userService", UserService.class);
       userService.add();
   }
   ```

# AOP

## 什么是AOP

> AOP指的是面向切面编程，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高了程序的可重用性，同时提高了开发的效率
>
> AOP通俗地来讲，就是**不修改源代码**，通过一种嵌入的方式，将一种外部定义好的功能，嵌入其中执行，**即在不修改源代码的前提下在主干功能中添加新功能**，降低了耦合度，啥时候不要就可以不要掉，无需修改源代码，如下图：使用登录的例子说明AOP

![image-20211019195418866](.\Spring5Notes_images\image-20211019195418866.png)

## AOP底层原理

1. AOP底层使用到了动态代理，有以下两种情况的动态代理
   1. 有接口的情况：使用JDK动态代理
   2. 无接口的情况：使用CGLIB动态代理

### 动态代理之有接口的情况

> 创建接口实现类代理对象，增强类的方法

![image-20211019200723755](.\Spring5Notes_images\image-20211019200723755.png)

### 动态代理无接口的情况

> 创建子类的代理对象，增强类的方法

![image-20211019200753462](.\Spring5Notes_images\image-20211019200753462.png)

## AOP底层原理之JDK动态代理实现

1. 使用JDK动态代理，使用Proxy类里面的方法创建代理对象，步骤如下：

   1. 创建接口，定义方法

      ```java
      public interface UserDao {
          public int add(int a,int b);
          public String update(String id);
      } 
      ```

   2. 创建接口实现类，实现方法

      ```java
      public class UserDaoImpl implements UserDao {
      
          @Override
          public int add(int a, int b) {
              System.out.println("add方法执行了");
              return a + b;
          }
      
          @Override
          public String update(String id) {
              System.out.println("update方法执行了");
              return id;
          }
      }
      ```

   3. 使用`Proxy.newProxyInstance(..)`创建接口的实现类代理对象

      ```java
      public class JDKProxy {
          public static void main(String[] args) {
              // 创建接口实现类代理对象
              Class[] interfaces = {UserDao.class};
              UserDao userDao = new UserDaoImpl();
              // 返回代理对象【接口 = 实现类的代理对象】
              UserDao userDaoProxy = (UserDao) Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces, new UserDaoProxy(userDao));
              int add = userDaoProxy.add(1, 2);
              System.out.println(add);
          }
      }
      
      class UserDaoProxy implements InvocationHandler {
          //谁需要创建代理对象，则将其传递过来，可通过有参构造器传递,如UserDaoImpl需要创建代理对象，那么就将obj赋值为UserDaoImpl类型
          private Object obj;
      
          public UserDaoProxy(Object obj) {
              this.obj = obj;
          }
      
          // invoke方法在UserDaoProxy对象创建时便会执行，该方法就是增强的逻辑
          // proxy就是代理对象，method表示当前的执行的方法，arg就是参数
          @Override
          public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
              //在方法之前做一个处理
              System.out.println("我是在原有方法" + method.getName() + "之前执行的功能，该方法参数为" + Arrays.toString(args));
              //被增强的方法自己正常执行，通过参数method.invoke方法即可执行被代理类对象中原有的方法【第一个参数表示被代理类对象，第二个参数则为方法所需的参数】
              Object result = method.invoke(obj, args); //被代理类对象的原有方法执行后可能有返回值
      
              //在方法之后做一个处理
              System.out.println("我是在原有方法" + method.getName() + "之后执行的功能，该方法参数为" + Arrays.toString(args));
              return result;
          }
      }
      ```

## AOP操作术语

> 有以下四个常见基本术语：

1. 连接点![image-20211019205414134](.\Spring5Notes_images\image-20211019205414134.png)
2. 切入点![image-20211019205511799](.\Spring5Notes_images\image-20211019205511799.png)
3. 通知（增强），通知有以下五种类型![image-20211019205549778](.\Spring5Notes_images\image-20211019205549778.png)
   1. 前置通知`@Before`：在原始方法**执行之前执行**
   2. 后置通知（返回通知）`@AfterReturning`：在原始方法**执行之后执行**,**出现异常**就**不执行**了
   3. 环绕通知`@Around`：在原始方法**前后都执行**
   4. 异常通知`@AfterThrowing`：在原始方法执行中**出现异常**则**执行**
   5. 最终通知`@After`：在方法**执行之后执行**，无论原始方法有没有出现异常**都会执行**`可联想以下finally`
4. 切面：只是一个过程![image-20211019210158779](.\Spring5Notes_images\image-20211019210158779.png)

## AOP的准备工作

> Spring框架一般基于AspectJ实现AOP操作

### 什么是AspectJ

> ​	AspectJ不是Spring组成部分，是一个独立的AOP框架，可脱离Spring单独使用，一般将AspectJ和Spring框架一起使用，进行AOP操作

1. 在项目工程中引入AOP的相关依赖

   + `spring-aspects-5.2.6.RELEASE.jar`
   + `com.springsource.net.sf.cglib-2.2.0.jar`
   + `com.springsource.org.aopalliance-1.0.0.jar`
   + `com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar`

2. 切入点表达式：切入点表达式的作用就是可以知道**哪个类里面的哪个方法进行增强**

   + 语法结构：`execution([权限修饰符][返回值类型][类全路径][方法名称]([参数列表]))`

   + 切入点表达式举例：

     1. **某个类的某个方法**对`com.martha`包下的`Person`类中的eat(String food)方法进行增强【扩展功能】

        execution(* com.martha.Person.eat(..))【*代表权限修饰符可以任意，方法参数列表使用两个点代替】

     2. **某个类的所有方法**对`com.martha`包下`Person`类的所有的方法都进行增强

        execution(* com.martha.Person.*(..))【权限修饰符可以任意，参数列表使用两个点代替】

     3. **所有类的所有方法**对`com.martha`包下所有类的所有方法都进行增强

        execution(* com.martha.\*.*(..))【权限修饰符可以任意，参数列表使用两个点代替】

## 基于AspectJ实现AOP操作的两种方式

### 基于AspectJ配置文件的方式实现AOP（了解）

> 在开发中，一般都是使用注解的形式进行AOP的配置，因为方便

1. 创建两个类，增强类和被增强类，创建方法

   ```java
   // 被增强类
   public class Book {
       public void primitiveMethod() {
           System.out.println("原始方法...");
       }
   }
   // 增强类
   public class BookProxy {
       public void before(){
           System.out.println("before前置通知方法被执行...");
       }
   }
   ```

2. 在Spring配置文件中创建两个类对象

   ```xml
   <!--在Spring中创建增强类和被增强类的对象，这一步骤相当于在两个类上标注了@Component注解-->
   <bean id="book" class="com.martha.aspectj.Book"></bean>
   <bean id="bookProxy" class="com.martha.aspectj.BookProxy"></bean>
   ```

3. 在Spring配置文件中配置切入点

   ```xml
   <!--配置Aop增强-->
   <aop:config>
       <!--
         切入点:
           id：自定义切入点的名字
           expression：表示切入点表达式
       -->
       <aop:pointcut id="pointCut" expression="execution(* com.martha.aspectj.Book.primitiveMethod(..))"></aop:pointcut>
   
       <!--
         配置切面：相当于在增强类上方标注@Aspect注解
           ref:指的是增强类，此处引用bean的id即可
       -->
       <aop:aspect ref="bookProxy">
           <!--
             增强作用在具体方法上：相当于在增强方法上标注@Before前置通知注解及其切入点表达式
               method：指的是增强类中的增强方法的名称
               pointcut-ref：通过配置好的切入点表达式的id值，引用切入点表达式
           -->
           <aop:before method="before" pointcut-ref="pointCut"></aop:before>
       </aop:aspect>
   </aop:config>
   ```

### 基于AspectJ注解的方式实现AOP（推荐）

1. 创建User类，在类中定义add方法

   ```java
   //被增强类
   public class User {
       public void add() {
           System.out.println("add");
       }
   }
   ```

2. 创建增强类UserProxy，增强类用于编写所要扩展的功能逻辑，在增强类中创建不同的方法，代表不同的通知类型，即代表增强逻辑的如何执行

   ```java
   //增强类
   public class UserProxy {
       //前置通知方法
       public void before() {
           System.out.println("before()前置通知方法只在原始方法执行前执行...");
       }
   }
   ```

3. 进行通知类型的配置

   1. 在Spring配置文件中，开启注解扫描（需要开启context名称空间）

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                                 http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                                 http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
          <!--开启注解扫描-->
          <context:component-scan base-package="com.martha.aspectj"></context:component-scan>
      </beans>
      ```

   2. 使用@Component注解创建User和UserProxy对象

      ```java
      //被增强类
      @Component
      public class User {
          public void add() {
              System.out.println("add");
          }
      }
      
      //增强类
      @Component
      public class UserProxy {
          //前置通知方法
          public void before() {
              System.out.println("before()前置通知方法只在原始方法执行前执行...");
          }
      }
      ```

   3. 在增强类上面添加注解@Aspect，标识UserProxy为一个增强类，在该类中编写增强逻辑

      ```java
      //增强类
      @Component
      @Aspect
      public class UserProxy {
          //定义将要用于前置通知的方法
          public void before() {
              System.out.println("before()前置通知方法只在原始方法执行前执行...");
          }
      }
      ```

   4. 在Spring配置文件中开启AspectJ生成代理对象（需要开启aop名称空间）

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xmlns:aop="http://www.springframework.org/schema/aop"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
              http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
              http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
          <!--开启注解扫描-->
          <context:component-scan base-package="com.martha.aspectj"></context:component-scan>
          <!--开启AspectJ生成代理对象，以下配置的意思就是会去类中检查有没有使用@AspectJ注解过的类，有就将其注解的对象生成为代理类对象-->
          <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
      </beans>
      ```

   5. 配置不同类型的通知：在增强类中，在作为通知方法上面添加通知类型的注解，并且使用切入点表达式配置

      ```java
      //增强类
      @Component
      @Aspect
      public class UserProxy {
          //前置通知方法：不限修饰符，为指定包下User类的add方法添加前置通知
          @Before(value = "execution(* com.martha.aspectj.User.add(..))")
          public void before() {
              System.out.println("before()前置通知方法只在原始方法执行前执行...");
          }
      }
      ```

   6. 测试配置后AOP的执行

      ```java
      @Test
      public void testAop() {
          ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
          User user = context.getBean("user", User.class);
          user.add();//运行结果如下
          /*
                  before()前置通知方法只在原始方法执行前执行...
                  add
           */
      }
      ```

   7. 剩余通知的演示

      ```java
      //后置通知方法，与@After功能类似，主要区别就是会在方法返回值之后执行，遇到异常不执行
      @AfterReturning(value = "execution(* com.martha.aspectj.User.add(..))")
      public void afterReturning() {
          System.out.println("afterReturning()后置通知方法执行...");
      }
      
      //异常通知方法
      @AfterThrowing(value = "execution(* com.martha.aspectj.User.add(..))")
      public void afterThrowing() {
          System.out.println("AfterThrowing()异常通知方法执行...");
      }
      
      //环绕通知方法
      @Around(value = "execution(* com.martha.aspectj.User.add(..))")
      public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {//通过ProceedingJoinPoint类可以调用并执行被增强方法
          System.out.println("around()环绕通知方法执行...");
          proceedingJoinPoint.proceed(); // 执行被增强方法【即原始方法】，环绕通知比较特殊，必须要手动调用被增强方法
          System.out.println("around()环绕通知方法执行...");
      }
      
      //最终通知方法【有无异常最终都会执行】
      @After(value = "execution(* com.martha.aspectj.User.add(..))")
      public void after() {
          System.out.println("after()最终通知方法无论有无异常都会执行...");
      }
      ```

      ```java
      around()环绕通知方法执行...
      before()前置通知方法只在原始方法执行前执行...
      add
      around()环绕通知方法执行...
      after()后置通知方法无论有无异常都会执行...
      afterReturning()后置通知方法执行...
      ```

## 相同execution切入点配置的抽取

> 如以上配置的五种通知类型，它们的切入点表达式都相同，那么可以考虑做一个抽取实现共用

1. 首先先抽取相同的切入点表达式，使用一个PointCut的注解，将重复的切入点表达式定义在内，标注在一个自定义的方法上边

   ```java
   @PointCut(value = "execution(* com.martha.aspectj.User.add(..))")
   public void pointCut(){}
   ```

2. 使用被`@PointCut`注解标注的方法，来**间接地**使用到公共的切入点表达式，如下做了个替换

   ```java
   //后置通知方法，与@After功能类似，主要区别就是会在方法返回值之后执行，遇到异常不执行
   @AfterReturning(value = "pointCut()")
   public void afterReturning() {
       System.out.println("afterReturning()后置通知方法执行...");
   }
   
   //异常通知方法
   @AfterThrowing(value = "pointCut()")
   public void afterThrowing() {
       System.out.println("AfterThrowing()异常通知方法执行...");
   }
   
   //环绕通知方法
   @Around(value = "pointCut()")
   public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {//通过ProceedingJoinPoint类可以调用并执行被增强方法
       System.out.println("around()环绕通知方法执行...");
       proceedingJoinPoint.proceed(); // 执行被增强方法【即原始方法】，环绕通知比较特殊，必须要手动调用被增强方法
       System.out.println("around()环绕通知方法执行...");
   }
   
   //最终通知方法【有无异常最终都会执行】
   @After(value = "pointCut()")
   public void after() {
       System.out.println("after()最终通知方法无论有无异常都会执行...");
   }
   ```

## 设置增强类的优先级

> 若同时存在有**多个增强类对**同一个被增强类中的**同一个方法进行增强**，那么就需要设置增强类的优先级

1. 创建被增强类--User类，在类中定义add方法【即被增强方法】

   ```java
   //被增强类
   public class User {
       public void add() {
           System.out.println("add");
       }
   }
   ```

2. 创建两个增强类UserProxy，PersonProxy，两个增强类的都对User类中的add()方法进行前置通知类型的增强，这时候谁先增强谁后增强就需要有一个优先级别

   ```java
   //增强类
   @Component
   @AspectJ
   public class UserProxy {
       //前置通知方法
       @Before(value = "execution(* com.martha.aspectj.User.add(..))")
       public void before() {
           System.out.println("before()前置通知方法只在原始方法执行前执行...");
       }
   }
   //增强类
   @Component
   @AspectJ
   public class PersonProxy {
       //前置通知方法
       @Before(value = "execution(* com.martha.aspectj.User.add(..))")
       public void before() {
           System.out.println("before()前置通知方法只在原始方法执行前执行...");
       }
   }
   ```

3. 在增强类上添加`@Order(数字值)`注解，数字值越小优先级越高，以下演示让PersonProxy的前置通知先执行

   ```java
   //增强类
   @Component
   @AspectJ
   @Order(3) //优先级不如PersonProxy
   public class UserProxy {
       //前置通知方法
       @Before(value = "execution(* com.martha.aspectj.User.add(..))")
       public void before() {
           System.out.println("before()前置通知方法只在原始方法执行前执行...");
       }
   }
   //增强类
   @Component
   @AspectJ
   @Order(1)
   public class PersonProxy {
       //前置通知方法
       @Before(value = "execution(* com.martha.aspectj.User.add(..))")
       public void before() {
           System.out.println("before()前置通知方法只在原始方法执行前执行...");
       }
   }
   ```

## AOP集成完全注解开发（了解）

> 创建配置类，则不需要创建Spring.xml配置文件

```java
@Configuration //标注该类是一个配置类
@ComponentScan(basePackages={"com.martha"}) // 配置注解扫描
@EnableAspectJAutoProxy(proxyTargetClass=true)// 配置开启AspectJ自动生成代理对象
// 相当于配置文件中的 <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
public class ConfigAop{..}
```

# JdbcTemplate

## 什么是JdbcTemplate

> Spring框架对JDBC进行了封装，使用JdbcTemplate（Jdbc模板）可以很方便地实现对数据库的操作

## JdbcTemplate的引入和使用

1. 导入相关jar包或依赖
   + `mysql-connector-java-5.1.7-bin.jar`**MySQL驱动依赖**
   + `druid-1.1.10.jar`**数据库连接池依赖**
   + `spring-jdbc-5.2.6.RELEASE.jar`**Jdbc封装依赖**
   + `spring-tx-5.2.6.RELEASE.jar`**针对事务的操作依赖**
   + `spring-orm-5.2.6.RELEASE.jar`**整合其它框架如MyBatis共同操作数据库时需要orm依赖**![image-20211031022017904](.\Spring5Notes_images\image-20211031022017904.png)

2. 配置Druid数据库连接池

   1. 在src目录下创建以下内容的配置文件

      ```properties
      # 推荐使用自定义前缀，如prop，避免多个键重复冲突
      prop.driverClassName=com.mysql.jdbc.Driver
      prop.url=jdbc:mysql://localhost:3306/test
      prop.username=root
      prop.password=123456
      ```

   2. 在Spring配置文件中引入外部配置文件

      ```xml
      <!--使用context名称空间引入外部属性文件-->
      <context:property-placeholder location="classpath:druid.properties"/>
      <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
          <property name="driverClassName" value="${prop.driverClassName}"></property>
          <property name="username" value="${prop.username}"></property>
          <property name="password" value="${prop.password}"></property>
          <property name="url" value="${prop.url}"></property>
      </bean>
      ```

3. 在Spring配置文件中配置JdbcTemplate对象，并通过该对象的setDataSource()方法注入DataSource数据源

   ```xml
   <!--配置JdbcTemplate对象-->
   <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
       <property name="dataSource" ref="druidDataSource"></property>
   </bean>
   ```

4. 创建service类，创建dao类，实现具体数据库操作

   1. 在BookDaoImpl类中注入JdbcTemplate

      ```java
      @Repository
      public class BookDaoImpl implements BookDao {
          // 注入JdbcTemplate，通过Jdbc模板可以实现对数据库的操作
          @Autowired
          private JdbcTemplate jdbcTemplate;
      }
      ```

   2. 在BookService类中注入BookDao

      ```java
      @Service
      public class BookService {
          // 注入BookDao
          @Autowired
          private BookDao bookDao;
      }
      ```

## JdbcTemplate操作数据库之添加操作

1. 创建数据库表所对应的JavaBean

   ```java
   @Component
   public class Tag {
       private int id;
       private String name;
       private byte isBlock;
       private String description;
   
       public Tag() {
       }
   
       public Tag(int id, String name, byte isBlock, String description) {
           this.id = id;
           this.name = name;
           this.isBlock = isBlock;
           this.description = description;
       }
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public byte getIsBlock() {
           return isBlock;
       }
   
       public void setIsBlock(byte isBlock) {
           this.isBlock = isBlock;
       }
   
       public String getDescription() {
           return description;
       }
   
       public void setDescription(String description) {
           this.description = description;
       }
   
       @Override
       public String toString() {
           return "Tag{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   ", isBlock=" + isBlock +
                   ", description='" + description + '\'' +
                   '}';
       }
   }
   ```

2. 编写service和dao，在dao进行数据库添加操作，调用jdbcTemplate.`update(String sql,Object ... args)`方法可实现增删改操作

   + 第一个参数为sql语句
   + 第二个参数为可变形参，可设置sql中的语句值

   ```java
   // dao层
   public interface TagDao {
       /**
        * @param tag
        * @Description 添加方法
        */
       void addTag(Tag tag);
   }
   @Repository
   public class TagDaoImpl implements TagDao {
       @Autowired
       private JdbcTemplate jdbcTemplate;
   	
       // 在dao层进行数据库添加操作
       @Override
       public void addTag(Tag tag) {
           // 创建SQL语句
           String sql = "INSERT INTO tag(`name`,isBlock,description) VALUES(?,?,?)";
           // 调用update方法进行插入操作
           int updateCount = jdbcTemplate.update(sql, tag.getName(), tag.getIsBlock(), tag.getDescription());
           System.out.println(updateCount); // 返回值指的是成功添加了几条记录
       }
   }
   ```

   ```java
   // service层
   @Service
   public class TagService {
       @Autowired
       private TagDao tagDao;
   
       public void addTag(Tag tag) {
           tagDao.addTag(tag);
       }
   }
   ```

4. 模拟测试插入操作

   ```java
   @Test
   public void testTagInsert() {
       ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
       TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
       String description = "a标签是超链接标签，用于从一张页面链接到另一张页面";
       Tag tag = new Tag(0, "<a>", 2, description); // 创建对象传入参数值
       tagService.addTag(tag); // 进行添加操作
   }
   ```

![image-20211031201602463](.\Spring5Notes_images\image-20211031201602463.png)

## JdbcTemplate操作数据库之修改、删除操作

1. 在TagService层中添加updateTag、deleteTag方法

```java
public void updateTag(Tag tag) {
    tagDao.updateTag(tag);
}

public void removeTag(int id) {
    tagDao.removeTag(id);
}
```

2. 在TagDaoImpl类中实现修改与删除方法

   ```java
   @Override
   public void updateTag(Tag tag) {
       String sql = "UPDATE tag SET `name` = ?,isBlock = ?,description = ? WHERE id = ?";
       int updateCount = jdbcTemplate.update(sql, tag.getName(), tag.getIsBlock(), tag.getDescription(), tag.getId());
       System.out.println("成功修改 " + updateCount + " 条记录");
   }
   
   @Override
   public void removeTag(int id) {
       String sql = "DELETE FROM tag WHERE id = ?";
       int removeCount = jdbcTemplate.update(sql,id);
       System.out.println("成功删除" + removeCount + " 条记录");
   }
   ```

3. 测试即可

   ```java
   @Test
   public void testTagUpdate() {
       ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
       TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
       String description = "a标签是超链接标签，用于从一张页面链接到另一张页面";
       Tag tag = new Tag(1, "<a></a>", 2, description); // 创建对象传入参数值
       tagService.updateTag(tag); // 进行修改操作
   }
   @Test
   public void testTagRemove() {
       ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
       TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
       tagService.removeTag(2); // 进行删除操作
   }
   ```

## JdbcTemplate操作数据库之查询操作

### 查询返回某个值

> 如查询表中有多少条记录数，返回的是某个值
>
> 使用jdbcTemplate.queryForObject(String sql,Class<T> requiredType);方法查询单个值
>
> + 第一个参数为sql语句
> + 第二个参数表示返回值类型的class对象，如查询的返回值为int，那么就是int.class
> + 

```java
/**
 * @Description 获取tag表中所有记录数
 * @Param []
 * @Return int
 * @Author 
 * @Date  
 */
int getTotalCount(); // dao层

// impl类
@Override
public int getTotalCount() {
    String sql = "SELECT COUNT(*) FROM tag";
    jdbcTemplate.queryForObject();
    return 0;
}
```

```java
// Service层
public int getTotalCount(){
    return tagDao.getTotalCount();
}
```

> 查询结果测试

```java
@Test
public void testGetTotalCount() {
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
    int totalCount = tagService.getTotalCount(); // 查询总数操作
    System.out.println(totalCount); // 1条记录
}
```

### 查询返回元组即对象

> 查询数据库中的指定id的一条记录，并封装为对象输出
>
> 使用jdbcTemplate.queryForObject(String sql,RowMappper<T> rowMapper,Object ... args);方法查询一条记录并包装为对象返回
>
> 1. sql：指的是所要执行的sql语句
> 2. rowMapper：是一个接口，可返回不同类型的数据，使用这个接口中的实现类可以完成数据的封装
>    + 实现类一：new BeanPropertyRowMapper<>()：泛型中填入返回的对象名即类名，括号中填入返回值对象类型，会将查出的数据自动地封装为填入的对象，如`new BeanPropertyRowMapper<Person>(Person.class)`
> 3. args：sql语句 ？占位符依序对应的参数值，没有则可以不写【因为是可变形参】

```java
/**
 * @Description 查询对应id的一条数据
 * @Param [id]
 * @Return com.martha.entity.Tag
 * @Author 
 * @Date  
 */
Tag getTagForObject(int id); //dao接口层

// daoImpl实现类层
@Override
public Tag getTagForObject(int id) {
    String sql = "SELECT * FROM tag WHERE id = ?";
    Tag tag = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Tag>(Tag.class), id);
    return tag;
}
```

```java
//Service层
public Tag getTagForObject(int id){
    return tagDao.getTagForObject(id);
}
```

```java
@Test
public void testGetTagForObject() {
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
    Tag tag = tagService.getTagForObject(1); // 查询对应id记录
    System.out.println(tag);
}
```

### 查询返回元组即对象集合

> 调用jdbcTemplate.query(String sql,RowMappper<T> rowMapper,Object ... args);方法查询返回对象集合
>
> 1. sql：指的是所要执行的sql语句
> 2. rowMapper：是一个接口，可返回不同类型的数据，使用这个接口中的实现类可以完成数据的封装
>    + 实现类一：new BeanPropertyRowMapper<>()：泛型中填入返回的对象名即类名，括号中填入返回值对象类型，会将查出的数据自动地封装为填入的对象，如`new BeanPropertyRowMapper<Person>(Person.class)`
> 3. args：sql语句 ？占位符依序对应的参数值，没有则可以不写【因为是可变形参】

```java
/**
 * @Description 查询表中所有记录
 * @Param []
 * @Return java.util.List<com.martha.entity.Tag>
 * @Author 
 * @Date  
 */
List<Tag> getTagForList();  //dao接口层

// daoImpl实现类层
@Override
public List<Tag> getTagForList() {
    String sql = "SELECT * FROM tag";
    List<Tag> tagList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Tag>(Tag.class));
    return tagList;
}
```

```java
// service层
public List<Tag> getTagForList() {
    return tagDao.getTagForList();
}
```

```java
@Test
public void testGetTagForList() {
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
    List<Tag> tagList = tagService.getTagForList(); // 查询所有tag记录
    System.out.println(tagList);
}
```

## JdbcTemplate操作数据库之批量操作

> 批量操作指的是，操作表中的多条记录
>
> 使用batchUpdate(String sql,List<Object[]> batchArgs)方法实现批量操作
>
> 1. sql：指的是所要执行的sql语句
> 2. batchArgs：List集合，添加多条记录数据

### 批量添加

```java
/**
 * @Description
 * @Param [tags]
 * @Return void
 * @Author
 * @Date
 */
void tagBatchInsert(List<Object[]> tags); // dao层

// daoImpl层
@Override
public void tagBatchInsert(List<Object[]> tags) {
    String sql = "INSERT INTO tag(`name`,isBlock,description) VALUES(?,?,?)";
    int[] batchUpdate = jdbcTemplate.batchUpdate(sql, tags);
    System.out.println(Arrays.toString(batchUpdate)); // 输出影响行数
}
```

```java
// service层
public void tagBatchInsert(List<Object[]> tags) {
    tagDao.tagBatchInsert(tags);
}
```

```java
@Test
public void testTagBatchInsert() {
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
    List<Object[]> list = new ArrayList<>();
    Object[] o1 = {"<hr/>", 2, "分割线"};
    Object[] o2 = {"<hr/>", 1, "分割线"};
    Object[] o3 = {"<hr/>", 3, "分割线"};
    list.add(o1);
    list.add(o2);
    list.add(o3);
    tagService.tagBatchInsert(list); // 调用批量添加方法
}
```

### 批量修改

> 批量修改其实就是批量添加修改以下sql语句即可和参数，其它代码都一样

```java
/**
 * @Description 批量修改
 * @Param [tags]
 * @Return void
 * @Author
 * @Date
 */
void tagBatchUpdate(List<Object[]> tags); // dao层

// daoImpl层
@Override
public void tagBatchUpdate(List<Object[]> tags) {
    String sql = "UPDATE tag SET `name` = ?,isBlock = ?,description = ? WHERE id = ?";
    int[] batchUpdates = jdbcTemplate.batchUpdate(sql, tags);
    System.out.println(Arrays.toString(batchUpdates));
}
```

```java
// service层
public void tagBatchUpdate(List<Object[]> tags) {
    tagDao.tagBatchUpdate(tags);
}
```

```java
@Test
public void testTagBatchUpdate() {
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
    List<Object[]> list = new ArrayList<>();
    Object[] o1 = {"<hr/>", 2, "分割线3", 2};
    Object[] o2 = {"<hr/>", 1, "分割线2", 3};
    Object[] o3 = {"<hr/>", 3, "分割线1", 4};
    list.add(o1);
    list.add(o2);
    list.add(o3);
    tagService.tagBatchUpdate(list); // 调用批量修改方法
}
```

### 批量删除

```java
/**
 * @Description 批量删除
 * @Param [tags]
 * @Return void
 * @Author
 * @Date
 */
void tagBatchRemove(List<Object[]> tags); // dao层

// daoImpl层
@Override
public void tagBatchRemove(List<Object[]> tags) {
    String sql = "DELETE FROM tag WHERE id = ?";
    int[] batchUpdates = jdbcTemplate.batchUpdate(sql, tags);
    System.out.println(Arrays.toString(batchUpdates));
}
```

```java
// service层
public void tagBatchRemove(List<Object[]> tags) {
    tagDao.tagBatchRemove(tags);
}
```

```java
@Test
public void testTagBatchRemove() {
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    TagService tagService = context.getBean("tagService", TagService.class); // 获取service对象
    List<Object[]> list = new ArrayList<>();
    Object[] o1 = {4};
    Object[] o2 = {5};
    Object[] o3 = {6};
    Object[] o4 = {7};
    Object[] o5 = {8};
    list.add(o1);
    list.add(o2);
    list.add(o3);
    list.add(o4);
    list.add(o5);
    tagService.tagBatchRemove(list); // 调用批量修改方法
}
```

# 事务管理

> 事务：事务是数据库操作的最基本单元，是逻辑上的一组操作，要么都成功，如果有一个失败那么所有操作都将失败，这就是事务
>
> 典型事务场景：银行转账的例子

## 事务的四大特性（ACID）

1. `原子性`：指的是要么都成功，要么都失败，有一个失败整套流程都将失败
2. `一致性`：操作之前与操作之后的总量是不变的，如A转账给B 100元，A和B的钱款加起来总量是不变的
3. `隔离性`：多事务操作时，彼此之间不产生影响，如两个线程同时操作同一条记录，那么两个线程之间不会产生影响的
4. `持久性`：事务提交之后，表中的数据要真正地发生变化，即物理存储发生变化，即持久性

## 搭建事务操作环境

> 通过银行转账的例子，搭建一个事务操作环境，示例图如下：

![image-20211101193246417](.\Spring5Notes_images\image-20211101193246417.png)

1. 创建数据库表，如下

![image-20211101193722142](.\Spring5Notes_images\image-20211101193722142.png)

2. 创建service，搭建dao，完成对象的创建和注入关系
   1. service注入dao，在dao层注入JdbcTemplate，在JdbcTemplate中注入DataSource

      ```java
      public interface UserDao {
      
      }
      @Repository
      public class UserDaoImpl implements UserDao{
          
          @Autowired
          private JdbcTemplate jdbcTemplate;
      }
      ```

      ```java
      @Service
      public class UserService {
      
          @Autowired
          private UserDao userDao;
      
      }
      ```

   2. 在dao层创建两个方法，加钱和减钱的方法，在service创建转账的方法，转账的方法包含加钱和减钱的操作

      ```java
      public interface UserDao {
          /**
           * @Description 加钱的方法
           * @Param []
           * @Return void
           * @Author Martha-X
           * @Date 2021/11/1 20:04
           */
          void moneyAddition();
      
          /**
           * @Description 减钱的方法
           * @Param []
           * @Return void
           * @Author Martha-X
           * @Date 2021/11/1 20:00
           */
          void moneyDeduction();
      }
      
      @Repository
      public class UserDaoImpl implements UserDao{
      
          @Autowired
          private JdbcTemplate jdbcTemplate;
          /*
              演示操作：Martha转账50给Lucy
           */
          @Override
          public void moneyAddition() {
              String sql = "UPDATE t_account SET money = money + ? WHERE `name` = ?";
              jdbcTemplate.update(sql,50,"Lucy");
          }
          @Override
          public void moneyDeduction() {
              String sql = "UPDATE t_account SET money = money - ? WHERE `name` = ?";
              jdbcTemplate.update(sql,50,"Martha");
          }
      }
      ```

      ```java
      @Service
      public class UserService {
      
          @Autowired
          private UserDao userDao;
      
          /**
           * @Description 转账的方法
           * @Param
           * @Return
           * @Author Martha-X
           * @Date 2021/11/1 20:34
           */
          public void creditTransfer() {
              // Lucy‘s money + 100
              userDao.moneyAddition();
              // Martha's money - 50
              userDao.moneyDeduction();
          }
      }
      ```

      ```java
      public class ProcedureTest {
          private static ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
      
          @Test
          public void testCreditTransfer(){
              UserService service = context.getBean("userService",UserService.class);
              service.creditTransfer();
          }
      }
      ```

      > 基本的逻辑就已经搭建好了，但还未考虑逻辑的健壮性，即事务的四个属性都还未考虑到

      > 异常演示，若Martha在转账期间突然断网了，下面模拟异常：

      ```java
      @Service
      public class UserService {
      
          @Autowired
          private UserDao userDao;
      
          /**
           * @Description 转账的方法
           * @Param
           * @Return
           * @Author Martha-X
           * @Date 2021/11/1 20:34
           */
          public void creditTransfer() {
              // Martha's money - 50
              userDao.moneyDeduction();
              
              int i = 10/0; //异常模拟意外情况
              
              // Lucy‘s money + 50
              userDao.moneyAddition();
          }
      }
      ```

      > 这时候，Martha的钱少了，但是Lucy的钱并没有增加，出现了不满足事务一致性的情况，那么就需要使用**事务**解决这个问题，如下

      ```java
      @Service
      public class UserService {
      
          @Autowired
          private UserDao userDao;
      
          /**
           * @Description 转账的方法
           * @Param
           * @Return
           * @Author Martha-X
           * @Date 2021/11/1 20:34
           */
          public void creditTransfer() {
              // ①将加钱减钱操作使用try-catch进行包裹
              try{
                  // ②开启事务操作
                  
                  // 业务操作流程代码
                  // Martha's money - 50
                  userDao.moneyDeduction();
      
                  int i = 10/0; //异常模拟意外情况
      
                  // Lucy‘s money + 50
                  userDao.moneyAddition();
                  
                  // ③提交事务操作【能走到这一步说明一切顺利，那么就可以放心提交事务了】
              }catch(Exception e){
      			// ④回滚事务操作【若出现异常，那么就要满足事务的原子性，在此即都失败】
              }
              
          }
      }
      ```

      > 这种解决方式称为编程式事务，要手动编写事务代码，后期我们直接用一个@Transactional注解就可以将这几个事务步骤全部替代完成，如下：

      ```java
      @Service
      public class UserService {
      
          @Autowired
          private UserDao userDao;
      
          /**
           * @Description 转账的方法
           * @Param
           * @Return
           * @Author Martha-X
           * @Date 2021/11/1 20:34
           */
          @Transactional // 在指定方法上边添加事务操作
          public void creditTransfer() {
              // Martha's money - 50
              userDao.moneyDeduction();
      
              int i = 10/0; //异常模拟意外情况
      
              // Lucy‘s money + 50
              userDao.moneyAddition();
          }
      }
      ```

      

## Spring集成事务管理的介绍

1. 我们在JavaEE架构中，一般将事务操作集成在Service层，即加到逻辑业务层更加准确
2. 在Spring中集成事务操作有两种方式
   1. 编程式事务管理（不方便）：就是在代码体中开启事务、提交事务等都需要编代码，如以上Service的代码四个步骤  ↑
   2. 声明式事务管理（开发中常用）
      1. 基于注解方式（开发中常用）
      2. 基于XML配置文件的方式

3. **在Spring中进行声明式事务管理，底层使用的是AOP原理**
4. Spring事务管理API，有些接口和实现类
   + 提供了一个接口`PlatformTransactionManager`，代表事务管理器，这个接口针对不同的框架提供了不同的实现类，我们使用JdbcTemplate或日后整合MyBatis都要使用`DataSourceTransactionManager`实现类![image-20211101211341919](.\Spring5Notes_images\image-20211101211341919.png)

## 声明式事务管理

### 基于注解方式

> 省去了编程式事务所要编写的代码，使用一个@Transactional注解即可替代并完成事务操作的四个步骤

1. 在Spring配置文件中配置事务管理器

   ```xml
   <!--创建并配置事务管理器-->
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <!--事务管理器和JdbcTemplate对象类似地都要传入数据源，此处为Druid数据源-->
       <property name="dataSource" ref="druidDataSource"></property>
   </bean>
   ```

2. 在Spring配置文件中开启事务注解

   1. 开启 tx 名称空间

      ```xml
      xmlns:tx="http://www.springframework.org/schema/tx"
      
      http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
      ```

   2. 开启事务注解驱动，并指定事务管理器

      ```xml
      <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
      ```

   3. 在**Service类上面**或**Service类中方法上边**添加事务注解

      + 若将注解添加到了Service类上边，代表该Service类中的所有方法都被添加上了事务操作

        ```java
        @Service
        @Transactional // 在类上边添加事务注解
        public class UserService {
        
            @Autowired
            private UserDao userDao;
        
            /**
             * @Description 转账的方法
             * @Param
             * @Return
             * @Author Martha-X
             * @Date 2021/11/1 20:34
             */
            public void creditTransfer() {
                // Martha's money - 50
                userDao.moneyDeduction();
        
                int i = 10/0; //异常模拟意外情况
        
                // Lucy‘s money + 50
                userDao.moneyAddition();
            }
        }
        ```

      + 若将注解添加到了Service类中的某个方法上边，那么就只是这个方法具有事务操作

        ```java
        @Service
        public class UserService {
        
            @Autowired
            private UserDao userDao;
        
            /**
             * @Description 转账的方法
             * @Param
             * @Return
             * @Author Martha-X
             * @Date 2021/11/1 20:34
             */
            @Transactional // 在指定方法上边添加事务操作
            public void creditTransfer() {
                // Martha's money - 50
                userDao.moneyDeduction();
        
                int i = 10/0; //异常模拟意外情况
        
                // Lucy‘s money + 50
                userDao.moneyAddition();
            }
        }
        ```

### 基于XML配置文件的方式

> 实际开发中，使用的是注解的方式声明事务，可了解一下配置文件的过程

1. 配置事务管理器

   ```xml
   <!--创建并配置事务管理器-->
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <!--事务管理器和JdbcTemplate对象类似地都要传入数据源，此处为Druid数据源-->
       <property name="dataSource" ref="druidDataSource"></property>
   </bean>
   ```

2. 配置通知

   ```xml
   <!--配置通知，即配置事务，可看作是增强方法，即目前只是在构造一个@Transactional（..）注解-->
   <tx:advice id="txAdvice">
       <!--配置事务的一些参数-->
       <tx:attributes>
           <!--可指定一种规则地 在方法上面添加事务，即定义一种规则，在符合规则的方法上添加事务，如以下，也可以指定事务的隔离级别-->
           <!--利用方法名规则进行匹配方法，并为此方法添加上事务-->
           <tx:method name="creditTransfer" propagation="REQUIRED" timeout="5" read-only="false" rollback-for="NullPointerException.class"></tx:method>
           <!--利用通配符匹配方法名，匹配以credit开头的方法名的方法，并为这些方法添加上事务-->
           <tx:method name="credit*"></tx:method>
           <!--当然可以配置多个方法-->
       </tx:attributes>
   </tx:advice>
   ```

3. 配置切入点和切面

   ```xml
   <!--配置切入点与切面，即通过AOP实现，此刻就是在设置将@Transactional注解应用在哪里-->
   <aop:config>
       <!--配置切入点-->
       <aop:pointcut id="pointCut" expression="execution(* com.martha.service.UserService.*(..))"></aop:pointcut>
       <!--配置切面-->
       <aop:advisor advice-ref="txAdvice" pointcut-ref="pointCut"></aop:advisor>
   </aop:config>
   ```

## 声明式事务管理参数的配置

> 在Service类上面添加@Transactional事务注解，在这个注解中，可以配置针对事务相关的一些参数，如以下六种参数

![image-20211101220142016](.\Spring5Notes_images\image-20211101220142016.png)

1. 在Service类上面添加注解@Transactional，并在这个注解中可以配置事务的相关参数
   + propagation：事务传播行为
   + isolation：事务隔离级别
   + timeout：超时时间
   + readOnly：是否只读
   + rollbackFor：回滚
   + noRollbackFor：不回滚

### propagation【事务传播行为】

1. 事务传播行为：指的是多个事务方法之间进行调用，这个过程中事务是如何进行管理的

   > 事务方法：对数据库表数据进行变化的操作，即增删改操作，查询操作不叫做事务方法，事务的传播行为要求至少得存在有两个事务方法，具体如下图：存在如下情况，那么事务的行为将如何传播，即这些事务该怎么进行处理
   >
   > + 一个**有事务的方法**内部**调用**另一个**没有事务的方法**
   > + **没有事务的方法**内部**调用**另一个**有事务的方法**
   > + 或者两个方法都有事务，**事务方法调用事务方法**

   ![image-20211103221944502](.\Spring5Notes_images\image-20211103221944502.png)

   **Spring框架事务传播行为有7种**

   > 前两个传播行为经常使用，后面的做个了解

   ![image-20211103222340898](.\Spring5Notes_images\image-20211103222340898.png)

![image-20211103222734753](.\Spring5Notes_images\image-20211103222734753.png)

**配置了@Transactional注解，即使不配置传播行为，默认的传播行为就是REQUIRED，即如下代码**

```java
@Transactional
public class Test{...}
// 这两个类的事务参数--即事务的传播行为一致，当然可以自定义其它值
@Transactional(propagation = Propagation.REQUIRED)
public class Test{...}
```

#### REQUIRED

> REQUIRED：如果当前已经存在事务，那么加入该事务，**即共用一个Connection**，可以理解为使用同一个数据库连接**Connenction**，如果不存在事务，创建一个事务，**即创建一个新的Connection**，这是默认的传播属性值
>
> 以下为代码演示与剖析：

```java
@Transactional
public void service(){
    serviceA();
    serviceB();
}
@Transactional
public void serviceA(){..}
@Transactional
public void serviceB(){..}
```

> serviceA方法 和 serviceB方法 都声明了事务，默认情况下，propagation=PROPAGATION_REQUIRED，整个service调用过程中，只存在一个共享的事务，**即只有一个Connection**，**所有方法都会共用这个Connection**，当有任何异常发生的时候，所有操作进行回滚

#### REQUIRES_NEW

> REQUIRES_NEW：如果当前存在事务，先把当前事务相关内容封装到一个实体，然后重新创建一个新事务，**即一个新的Connection**，接受这个实体为参数，用于事务的恢复，更直白的说法就是暂停当前事务(当前无事务则不需要)，创建一个新事务。 针对这种情况，两个事务没有依赖关系，可以实现新事务回滚了，但外部事务继续执行
>
> 以下为代码演示与剖析：

```java
@Transactional
public void service(){
    serviceB();
    try{
        serviceA();
    }catch(Exception e){
        ..
    }
}

public void serviceB(){
    do sql;
}

@Transactional(propagation = Propagation.REQUIRED_NEW)
public void serviceA(){
	do sql 1;
    1 / 0;
    do sql 2;
}
```

> 当调用service接口时，由于serviceA使用的是REQUIRES_NEW，它会创建一个新的事务，**即serviceA将使用一个新的Connection**，但由于serviceA抛出了运行时异常，导致**serviceA整个被回滚了**，而在service方法中，**捕获了异常**，所以**serviceB是正常提交的**。 注意，service中的try … catch 代码是必须的，否则service也会抛出异常，导致serviceB也被回滚，即serviceA与serviceB使用不同的Connection对象，serviceA如果出异常回滚，只要捕获住了异常，那么就不会影响serviceB的正常执行

### isolation【事务隔离级别】

> 隔离级别是为了解决事务中隔离性而出现的概念，而事务的隔离性指的是在并发操作中产生的问题，即**隔离级别是为了解决并发操作中产生的问题**
>
> 事务的隔离性特性要求多事务并发操作彼此之间不会产生影响，若不考虑隔离性，将会产生很多问题，列举以下问题：
>
> + 存在有三个“读”的问题
>   + 脏读
>   + 不可重复读
>   + 幻读（虚读）

#### 脏读

> 指的是一个未提交的事务读取到了另一个未提交事务的数据
>
> **脏读是一个致命问题，程序中肯定不能发生**

![image-20211103231618257](.\Spring5Notes_images\image-20211103231618257.png)

> 东方不败与岳不群并发地对同一条记录执行操作，两个人都开起了不同的事务，脏读的意思就是，岳不群修改了5000为60000，但是还未提交，东方不败读取了到60000数据，但是岳不群出现了意外，回滚了，那么就不存在60000这个数据，东方不败还是在60000这个数据上做操作，这就是脏读，一个未提交的事务读取到了另一个未提交事务的数据

#### 不可重复读

> 指的是一个未提交的事务读取到了另一个已提交事务修改过的数据
>
> 不可重复读是一种现象，这种现象允许产生，只是在这种现象中，如果要做一些统计操作，数据肯定不准确，但是本身不能算问题，只能算是一种现象而已

![image-20211103232613375](.\Spring5Notes_images\image-20211103232613375.png)

> 东方不败与岳不群并发地对同一条记录执行操作，两个人都开起了不同的事务，不可重复读的意思就是，东方不败和岳不群同时读取了数据，都得到了5000，其次，岳不群修改了5000为900，并立即提交了，表中数据被修改，随后，若东方不败再次读取数据，就会读到与上一次不同的数据，即读到了岳不群事务提交修改后的数据，**可联想抖音点赞统计系统**

#### 幻读

> 指的是一个未提交的事务读取到另一个提交事务添加的数据

> 以上就是三个问题，需要使用事务的隔离级别来解决

![image-20211103234922786](.\Spring5Notes_images\image-20211103234922786.png)

1. READ UNCOMMITTED：读未提交，三个问题依然存在，无法解决这三个问题
2. READ COMMITTED：读已提交，解决了脏读的问题，但不可重复读和幻读依然存在
3. REPEATABLE READ：可重复读，解决了脏读和不可重复读，但幻读的问题依然存在**MySQL默认的事务隔离级别**
4. SERIALIZABLE：串行化，完美解决了三个问题

> 设置事务的隔离级别

```java
@Transactional(propagation = Propagation.REQUIRED,
               isolation =Isolation.REAPEATABLE_READ // 设置隔离级别为MySQL默认的读已提交
              )
public class Test(){..}
```

### timeout【超时时间】

> 事务需要在一定时间内进行提交，如果超时未提交，则进行事务的回滚，因为一个事务不可以让他一直不提交也不回滚，这样会影响一整个程序的运行，设置超时时间，就给程序的执行添加了一定的保障
>
> 一个事务默认的超时时间为 -1，即默认没有超时时间（不超时），若设置了事务的超时时间则默认以秒为单位进行时间计算，以下为设置事务的超时时间代码

```java
@Transactional(propagation = Propagation.REQUIRED,
               isolation = Isolation.REAPEATABLE_READ,
               timeout = 5 // 设置事务的超时时间为5s
              )
public class Test(){..}
```

### readOnly【是否只读】

> 一般来说，“读”代表着查询操作，“写”代表增删改操作
>
> readOnly的默认值为false，表示该事务既可以进行查询操作，也可以进行增删改操作
>
> 若设置readOnly的值为true，则表示该事务能且**只能作查询操作**，一般来说是不设置只读的，但也可以根据实际需要设置，以下为设置事务的是否只读代码

```java
@Transactional(propagation = Propagation.REQUIRED,
               isolation = Isolation.REAPEATABLE_READ,
               timeout = 5,
               readOnly = false // 表示该事务可执行的操作有【增删改查】
              )
public class Test(){..}
```

### rollbackFor【回滚】

> 可针对指定的出现异常的类型，设置事务的回滚，即出现了指定的异常我就回滚，其他异常就不回滚
>
> 赋值为异常的类型，以下为回滚的代码示例

```java
@Transactional(propagation = Propagation.REQUIRED,
               isolation = Isolation.REAPEATABLE_READ,
               timeout = 5,
               readOnly = false, // 表示该事务可执行的操作有【增删改查】
               rollbackFor = NullPointerException.class // 当出现空指针异常我就回滚
              )
public class Test(){..}
```

### noRollbackFor【不回滚】

> 可针对指定的出现异常的类型，设置事务的不回滚，即出现了指定的异常我不回滚，其他异常就回滚
>
> 赋值为异常的类型，以下为不回滚的代码示例

```java
@Transactional(propagation = Propagation.REQUIRED,
               isolation = Isolation.REAPEATABLE_READ,
               timeout = 5,
               readOnly = false, // 表示该事务可执行的操作有【增删改查】
               noRollbackFor = NullPointerException.class // 当出现空指针异常没关系，出现不属于空指针的异常我就回滚
              )
public class Test(){..}
```

## 事务操作之完全注解开发

1. 创建配置类，使用配置类替代Spring配置文件

   ```java
   @Configuration // 标注该类是一个取代了Spring配置文件的Spring的配置类
   @ComponentScan(basePackages = "com.martha") // 配置组件扫描，在com.martha包下扫描
   @EnableTransactionManagement // 开启事务，即事务管理器生效，开启了事务注解
   // 相当于配置文件中的 <tx:annotation-driven></tx:annotation-driven>
   public class SpringConfig(){
   	// 配置文件中的其它配置需要在配置类中完成，如下
       
       /*
       	相当于 <bean id="" class=""></bean>
       		即创建了一个Bean示例，即配置对象
           由于使用配置文件返回的对象类型就是配置的类型，如getBean();
          	那么就使用一个方法返回该对象类型的Bean实例，类似地，配置文件中bean标签配置的property就在该方法中使用对象来设置
       */
       @Bean 
       public DruidDataSource getDruidDataSource(){
           DruidDataSource dataSource = new DruidDataSource();
           dataSource.setDriverClassName("com.mysql.jdbc.Driver");
           dataSource.setUrl("jdbc:mysql://localhost:3306/test");
           dataSource.setUsername("root");
           dataSource.setPassword("123456");
           return dataSource;
       }
   }
   // @Bean所标注的方法就相当于下面即配置文件中的标签配置，其含义如下
   /*
   	①在SpringIOC容器中创建DruidDataSource对象，在其对象中，附带其设置的参数，在IOC容器中就存在了这么个DruidDataSource对象
   */
   ```

   ```xml
   <!--配置Druid数据库连接池数据源-->
   <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
       <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
       <property name="username" value="root"></property>
       <property name="password" value="123456"></property>
       <property name="url" value="jdbc:mysql://localhost:3306/test"></property>
   </bean>
   ```

   > 解释完上一个，解释下一个注入外部bean的问题：

   ```java
   @Configuration // 标注该类是一个取代了Spring配置文件的Spring的配置类
   @ComponentScan(basePackages = "com.martha") // 配置组件扫描，在com.martha包下扫描
   @EnableTransactionManagement // 开启事务，即事务管理器生效，开启了事务注解
   public class SpringConfig(){
   	// 配置文件中的其它配置需要在配置类中完成，如下
       @Bean 
       public DruidDataSource getDruidDataSource(){
           DruidDataSource dataSource = new DruidDataSource();
           dataSource.setDriverClassName("com.mysql.jdbc.Driver");
           dataSource.setUrl("jdbc:mysql://localhost:3306/test");
           dataSource.setUsername("root");
           dataSource.setPassword("123456");
           return dataSource;
       }
       
       // 外部注入bean示例方法一
       @Bean
       public JdbcTemplate getJdbcTemplate(){
           JdbcTemplate jdbcTemplate = new JdbcTemplate();
           // jdbcTemplate需要注入外部的dataSource对象
           
           // 第一种方式（不推荐），主动调用配置类中对应对象的方法名来获取dataSource对象
           jdbcTemplate.set(getDruidDataSource()); // 这么做的方式就是dataSource会被创建两次，因为第一个dataSource对象已经存在于SpringIOC容器中了，这边再调用了以下，就又new了一个dataSource对象，所以不推荐使用
       }
       
       /* 外部注入bean示例方法二：为了演示第二种方式，重新定义一个获取JdbcTemplate的方法，开发中直接用这种方法即可*/
       @Bean
       public JdbcTemplate getJdbcTemplate(DataSource dataSource){
           JdbcTemplate jdbcTemplate = new JdbcTemplate();
           // jdbcTemplate需要注入外部的dataSource对象
           
           /*首先为本方法定义 所要注入类型对应的  形式参数，此处要注入的是DruidDataSource，那么就定义一个DataSource类型的，相当于它父类级别的类型即可，因为可能也会使用到其它数据库连接池，如果形参定义成了DruidDataSource，那么一旦更换了数据库连接池，如c3p0，就没办法根据类型进行注入了，所以定义为可兼容类型，即上转型对象最好*/
   		jdbcTemplate.set(dataSource); // SpringIOC容器会根据类型自动注入，相当于@Autowired
           /*当set的时候，它会到IOC容器中去寻找与之匹配的类型，并进行set注入
       }
       
   }
   ```

   > 解释完注入外部bean，完整的代码如下：

### 事务操作完全注解后的最终代码与测试

```java
@Configuration // 标注该类是一个取代了Spring配置文件的Spring的配置类
@ComponentScan(basePackages = "com.martha") // 配置组件扫描，在com.martha包下扫描
@EnableTransactionManagement // 开启事务，即事务管理器生效，开启了事务注解
public class SpringConfig(){
    // 配置文件中的其它配置需要在配置类中完成，如下

    // 配置DruidDataSource对象
    @Bean 
    public DruidDataSource getDruidDataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/test");
        dataSource.setUsername("root");
        dataSource.setPassword("123456");
        return dataSource;
    }

    // 配置JdbcTemplate对象
    @Bean
    public JdbcTemplate getJdbcTemplate(DataSource dataSource){
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        // jdbcTemplate需要注入外部的dataSource对象
        jdbcTemplate.set(dataSource);
        return jdbcTemplate;
    }

    // 配置事务管理器对象
    @Bean
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource){
        DataSourceTransactionManager transactionManager = 
            new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
}
```

```java
@Test
public void test(){
    ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class); // 通过加载配置类的方式初始化IOC容器
    UserService service = context.getBean("UserService",UserService.class);
    userService.creditTransfer(); // 转账操作
}
```

# Spring5新特性

+ 整个Spring5框架的代码都是基于Java8的，即至少要求JDK版本在1.8及以上，运行时可兼容JDK9，同时删除了不常用的代码库、类和方法

+ Spring5框架自带了通用的日志封装，使得操作更加方便，通过日志能够更好地观察程序的运行过程并且能够更快速地排查出运行所出现的问题，当然也可以自己整合其它的日志，如log4j2等等

  > Spring5已经移除了Log4jConfigListener，也就是Spring5不支持Log4j了，只有之前版本如Spring4支持，官方建议使用`Log4j2`也就是Log4j的第二版本，接下来的知识点就是**Spring5整合Log4j2日志**

## Spring5整合Log4j2日志

1. 引入相关jar包

   + `log4j-api-2.11.2.jar`
   + `log4j-core-2.11.2.jar`
   + `log4j-slf4j-impl-2.11.2.jar`
   + `slf4j-api-1.7.30.jar`

2. 在src目录下创建一个**固定名称**`log4j2.xml`的配置文件，内容模板如下：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
   <!--Configuration后面的status用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，可以看到log4j2内部各种详细输出-->
   <configuration status="DEBUG">
       <!--先定义所有的appender-->
       <appenders>
           <!--输出日志信息到控制台-->
           <console name="Console" target="SYSTEM_OUT">
               <!--控制日志输出的格式-->
               <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
           </console>
       </appenders>
       <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
       <!--root：用于指定项目的根日志，如果没有单独指定Logger，则会使用root作为默认的日志输出-->
       <loggers>
           <root level="info">
               <appender-ref ref="Console"/>
           </root>
       </loggers>
   </configuration>
   ```

4. 配置完后就可以使用了，自定义一个类，在自己的类中测试输出自定义的不同级别的日志信息

   ```java
   public class User{
       // 包为org.slf4j.Logger，都是slf4j，别导错了
       private static final Logger logger = LoggerFactory.getLogger(User.class);
       
       public static void main(String[] args){
           logger.info("hello log4j2"); // 提示信息级别的日志信息输出
           logger.warn("hello log4j2"); // 警告信息级别的日志信息输出
       } 
   }
   ```

## @Nullable注解与函数式注册对象

> Spring5框架核心容器支持@Nullable注解

### @Nullable注解的说明

1. 这个注解可以应用在方法上面`表示方法的返回值可以为空`、属性上面`表示属性值可以为空`、参数上面`表示参数值可以为空`【**如果没加这个注解，若参数为空，可能在下面调用时会出现空指针异常，加了的话，即使为空也不会报空指针异常**】

   ```java
   // 注解用在方法上边，表示方法的返回值可以为空
   @Nullable
   public String getName(){..}
   
   // 注解用在方法参数左边，表示方法的参数值可以为空
   public void setName(@Nullable String name){..}
   
   public class Hello{
       @Nullable  // 注解用在属性上边，表示该属性值可以为空
       String name;
   }
   ```

### 函数式注册对象

> Spring5核心容器支持函数式风格GenericApplicationContext的做法，即支持Java8  Lambada表达式相关的操作，**使用Lambada表达式将一个手动new出来的对象交由IOC容器管理**，但是一般我们不这样使用，了解以下新特性，以下为代码示例：

```java
public class User{..}

public static void main(String[] args){
    User user = new User(); // 手动创建的对象，SpringIOC容器是不知道这个对象存在的，故不能在Spring容器中作操作，所以我们手动创建的对象，需要在IOC容器中进行注册，注册之后，它才会被交给Spring进行管理，若没有注册，那么Spring是无法管理该对象的，注册步骤如下：
}
```

> 函数式风格创建（注册）对象，交由Spring进行管理

```java
public void testRegister(){
    // 1.创建GenericApplicationContext对象，该对象被Spring5所支持
    GenericApplicationContext context = new GenericApplicationContext();
    // 2.调用context对象的方法注册
    context.refresh(); // 表示将对象中的内容清空，即将进行注册操作
    // 3.开始以下注册过程
    	// 第一个参数要写上要注册的对象类型
    	// 第二个参数使用Lambada表达式new出来该对象，即通过Lambada表达式在SpringIOC容器中实现对该对象的注册，这样该对象就归IOC容器管理了
    context.registerBean(User.class,()->new User());
    // 4.注册完之后，就可以在IOC容器中获取到该对象
    User user = (User)context.getBean("com.martha.User"); // 通过全类名获取
    // 或者在注册的时候指明其标识，上面没有指明标识，默认不是类名的首字母小写，而是只能通过类的全类名来获取，以下在注册对象时指明了对象的唯一标识，在获取时可以通过该标识获取该对象
    context.registerBean("user",User.class,()->new User());
    User user = (User)context.getBean("user"); // 通过标识名获取
}
```

## Spring5整合JUnit5单元测试框架

> Spring5支持整合JUnit5单元测试框架，但是也支持JUnit4

### Spring5整合JUnit4

1. 引入Spring相关的针对测试的依赖`spring-test-5.2.6.RELEASE.jar`

2. 自定义测试类，使用注解方式完成对指定代码的测试

3. 此处因为要测试整合的JUnit4，所以要引入JUnit4外部依赖![image-20211104204016308](.\Spring5Notes_images\image-20211104204016308.png)

```java
@RunWith(SpringJUnit4ClassRunner.class) //该注解用于指定目前所整合的JUnit单元测试框架的版本
@ContextConfiguration("classpath:bean.xml") // 该注解用于指定Spring配置文件，它将自动加载指定的Spring配置文件与以下代码等价
/*
ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
*/
public class Spring5WithJUnit4 {
	// 因为测试类上面的注解已经帮我们加载了Spring配置文件，所以想要获取Service层，那么可以直接注入即可
    @Autowired
    private UserService userService; // 相当于
    /*
    UserService service = context.getBean("userService",UserService.class);
    */
    
    
    // 直接声明测试方法并直接测试即可
    @Test
    public void testUserService(){
        userService.creditTransfer();
    }
}
```

### Spring5整合JUnit5

1. 首先要引入JUnit5的外部依赖![image-20211104204156754](.\Spring5Notes_images\image-20211104204156754.png)

2. 创建测试类，使用注解完成测试的过程

   ```java
   @ExtendWith(SpringExtension.class) // 注解引用
   @ContextConfiguration("classpath:bean.xml")// 该注解用于指定Spring配置文件，它将自动加载指定的Spring配置文件
   public class Spring5WithJUnit5 {
       // 自动注入Service层
       @Autowired
       private UserService userService;
       
   	// 直接声明测试方法并直接测试即可
       @Test
       public void testUserService(){
           userService.creditTransfer();
       }
   }
   ```

### Spring5整合JUnit5测试框架的优化

> 以上我们在测试类上面需要添加两个注解，这时候提出了一个Spring的复合注解，使用这个复合注解可以替代上面的两个注解完整JUnit5的整合

```java
@SpringJUnitConfig(locations = "classpath:bean.xml") // 只需要指定Spring配置文件即可自动完成注解的配置与配置文件的加载
public class Spring5WithJUnit5 {
    // 自动注入Service层
    @Autowired
    private UserService userService;
    
	// 直接声明测试方法并直接测试即可
    @Test
    public void testUserService(){
        userService.creditTransfer();
    }
}
```

## SpringWebFlux（Spring5新功能）

> SpringWebFlux是Spring5框架中出现的一个新的模块，是用于我们进行Web开发的一个模块，如下图

![image-20211104211345898](.\Spring5Notes_images\image-20211104211345898.png)

### SpringWebFlux介绍

+ SpringWebFlux是Spring5添加的一个新的模块，是用于Web开发的，功能与SpringMVC类似，但二者底层有很大区别，SpringWebFlux是因当前一种比较流行的一种方式，叫做响应式编程而出现的框架
+ 使用传统的Web框架，比如SpringMVC，这些基于Servlet容器基础之上进行运行的，而SpringWebFlux是一种异步非阻塞的框架，而异步非阻塞的框架在Servlet3.1版本之后才支持，但SpringWebFlux框架并不是基于Servlet容器去运行的，它的核心是基于Reactor的相关API实现的，Reactor是Reactive中具体的一种框架，Reactive又叫做响应式编程
+ SpringWebFlux相对于传统的框架，既可以支持Servlet3.1容器，即它可以在Servlet或者Tomcat容器中进行运行，也支持其它容器，如Netty等等，同时相比于传统框架，SpringWebFlux支持的容器更加丰富且功能更加强大

#### 异步非阻塞的理解

> 什么是异步非阻塞？这就要拆开来理解了
>
> + 有异步就有同步
> + 有非阻塞就有阻塞
>
> 不管是异步还是同步，阻塞还是非阻塞，它们的区别就是**针对的对象不一样**
>
> **同步和异步**：同步和异步是**针对调用者来说的**，如我是调用者，我发送了一个请求
>
> + 在同步状态下，我需要等到对方响应后，我才去做其他事情（**调用发完请求等待相应再做其他事情，这种情况就称为同步**）
> + 在异步状态下，我不管对方有没有回应，我都去做其它事情（**调用者发完请求就去做其他事情，这种情况就称为异步**）
>
> **阻塞和非阻塞**：阻塞和非阻塞是**针对被调用者来说的**，如A是调用者，B是被调用者，假设A调用了B
>
> + 在阻塞情况下，被调用者B收到A的调用请求之后，B**直到做完了请求任务之后才给出反馈**，这种情况就称为阻塞（阻塞需要等待，如A要B执行插入数据的任务，B执行完任务才告诉A我做完了，A等待了B）
> + 在非阻塞情况下，被调用者B收到A的调用请求之后，B**马上给出反馈，然后再去执行请求任务**，这种情况就称为非阻塞（非阻塞不需要等待，如A要B执行插入数据的任务，B还没开始执行就先告诉A我做完了，然后B再去执行插入任务，A没有等待B执行完任务）

> 等动车的例子就能很好地说明以上四种专业知识，如乘客（调用者），动车（被调用者）
>
> + 乘客等待动车到来，不做任何自己的事，就干等着，即**同步**
> + 乘客在等动车的同时在做自己的事，比如在听歌、聊天、写代码等等，即**异步**
> + 假如动车站没有广播，那么乘客就只能干等着动车到来（等反馈），即**阻塞**
> + 动车站的广播还没等动车到站就先告诉乘客，动车即将到站（先反馈），即**非阻塞**

#### SpringWebFlux与SpringMVC

> 我们做Web开发，在Spring中，有SpringMVC就更加方便了，但为什么还出现了一个SpringWebFlux呢，它到底有什么特点？有什么好处？与SpringMVC相比，二者有什么区别？

##### SpringWebFlux的特点

+ `异步非阻塞`：在有限的资源下，提高系统的吞吐量和伸缩性，即**在有限的资源下能够处理更多的请求**，是以Reactor为基础实现响应式编程
+ `函数式编程`：由于Spring5框架是基于Java8的，所以SpringWebFlux使用Java8函数式编程的方式实现路由请求

##### SpringWebFlux与SpringMVC的对比

![image-20211104220822407](.\Spring5Notes_images\image-20211104220822407.png)

+ 以上两个框架都可以使用注解方式进行操作，且都可以运行在Tomcat容器中
+ SpringMVC采用命令式编程方式，即一行一行代码进行执行，好处就是便于我们程序的理解和调试，这么写更加清晰；但是SpringWebFlux采用异步响应式编程的方式

##### 如何选用SpringWebFlux和SpringMVC

> 之前肯定都用的SpringMVC，但SpringWebFlux与SpringMVC很多东西都相似，例如想要将SpringMVC切换为SpringWebFlux，里面很多注解方式都一样，切换成本会比较小，在实际中，一般项目，使用SpringMVC可以实现，但如果说项目中用到了远程服务调用，就可以尝试采用SpringWebFlux实现了，因为符合了SpringWebFlux的特点，“在有限的资源中处理更多的请求，提高系统吞吐量和伸缩性”，所以SpringWebFlux一般应用在什么地方，诸如微服务操作中有一个词叫做网关，**网关需要处理很多的请求**，而微服务网关就可以使用异步非阻塞的方式做到，而SpringWebFlux就非常适合做微服务网关
>
> 但是更多的项目，还是基于SpringMVC来实现，而SpringWebFlux颠覆了之前很多的思想，即通过响应式方式实现操作

### 响应式编程

#### 什么是响应式编程

> 响应式编程是一种面向**数据流**和变化传播的编程范式，这意味着可以在编程语言中很方便地表达静态或动态的数据流，而相关的计算模型会自动将**变化的值**通过数据流进行**传播**
>
> 可根据电子表格的例子来解释上面这段话：

![image-20211104233740970](.\Spring5Notes_images\image-20211104233740970.png)

> B1中的值，C1中的值，这种值可以看作数据或数据流，而现在B1的值由 1 变成了 5，就发生了变化，当它一发生变化，这个值就传播到了D1，D1重新将两个值做了相加操作（可联想易语言中的当编辑框内容被改变事件，根据其它单元格值的变化而变化）

#### Java8及之前版本中响应式编程的例子

> 响应式编程本质上使用到了一种设计模式，即观察者模式，而在Java8中，有一个典型的例子，Java8中**提供了观察者模式的两个类**，使用这两个类，能实现观察者模式，即响应式编程
>
> + `Observe`
> + `Observable`
>
> 观察者模式：观察周边与你关心的数据的变化，当一变化，发生通知，最终完成响应，以下为Java8典型的响应式编程的例子

```java
//首先自定义一个类继承Observable类
public class ObserverDemo extends Observable{
    public static void main(String[] args){
        ObserverDemo observerDemo = new ObserverDemo();//创建对象
        //添加观察者
        observerDemo.addObserver((o,arg)->{
            System.out.println("发生变化");
        });
        observerDemo.addObserver((o,arg)->{
            System.out.println("发生变化");
        });
        observerDemo.setChanged();// 数据变化
        observerDemo.notifyObservers();// 通知
    }
}
```

#### Java9及之后版本中响应式编程的例子

> Java8的那两个类被取代了，Java9中的Flow类才是真正意义上的响应式编程类，Reactor就是对该类进行了封装详见https://www.bilibili.com/video/BV1Vf4y127N5?p=54

#### Reactor实现响应式编程

> 在响应式编程操作中，基本上都要满足Reactive规范，而Reactor就是满足了这种规范，SpringWebFlux的核心就是Reactor，所以基于Reactor讲解以下该如何使用

+ 在Reactor中有两个核心类，这两个类都实现了Publisher接口，提供了丰富的操作符，能实现函数式编程的做法

  + Mono：实现的是发布者，返回0（即空的）或1个元素，发送了一个元素之后，发送了一个完成信号![image-20211105003127140](.\Spring5Notes_images\image-20211105003127140.png)
  + Flux：实现的是发布者，返回N个元素，如下图，发送了三个元素值，发送一个完成信号![image-20211105003007236](.\Spring5Notes_images\image-20211105003007236.png)

+ Flux和Mono都是数据流的发布者，使用Flux和Mono都可以发出三种数据信号

  + 元素值

  + 错误信号

  + 完成信号

    > 错误信号和完成信号都代表终止信号，终止信号用于告诉订阅者数据流结束了；
    >
    > 错误信号会终止数据流，且同时会将错误的信息传递给订阅者

+ 三种信号的特点
  + 错误信号与完成信号都是终止信号，二者不能共存；
    + 如果没有发出任何元素值，而是直接发出一个完成信号或者一个错误信号，那就表示它是一个空的数据流
    + 如果没有发出错误信号，也没有完成信号，那么数据流就是无限的，可以一直执行下去

**代码演示Flux与Mono**

> 演示Flux能发送多个元素，Mono就发送一个元素或0个元素

1. 引入依赖

   ```xml
   <dependency>
   	<groupId>io.projectreactor</groupId>
       <artifactId>reactor-core</artifactId>
       <version>3.1.5.REALEASE</version>
   </dependency>
   ```

2. 创建一个类实现即可

   ```java
   public class TestReactor{
       public static void main(String[] args){
           // just方法可以直接声明数据元素
           Flux.just(1,2,3,4); //多个元素
           Mono.just(1); // 一个元素
           
           // 其他的方法
           Integer[] arr = {1,2,3,4};
           Flux.fromArray(arr); //通过数组创建一个数据流
           
           List<Integer> list = Arrays.asList(arr);
           Flux.fromIterable(list); // 通过集合的方式创建一个数据流
           
           Stream<Integer> stream = list.stream();
           Flux.fromSteam(stream); // 通过流的方式创建数据流
       }
   }
   ```

+ 调用just或者其它的方法只是声明了数据流，但是数据流并没有被发出，只有进行了订阅之后，才会触发数据流，不订阅是什么都不会发生的

  ```java
  Flux.just(1,2,3,4).subscribe(System.out::print); //多个元素
  Mono.just(1).subscribe(System.out::print); // 一个元素
  ```

+ 关于Reactor操作符

  + 操作符就是施加在数据流之上的一道操作流程
  + `map`：将元素映射为新元素，将每一个数据值经过map操作符的操作后变成一个个新的元素（**改头换面**，如map将每一个元素做一个平方的操作，那么就得到了每一个元素对应的平方后的新的值）![image-20211105005302503](.\Spring5Notes_images\image-20211105005302503.png)
  + `flatMap`：将每个元素映射（转换）为流，将这个流合并成一个大的数据流，随后进行返回或输出出，**没有先后顺序，主要是将元素转换为流形式**![image-20211105005535223](.\Spring5Notes_images\image-20211105005535223.png)

### WebFlux执行流程与核心API

> SpringWebFlux是基于Reactor实现的，且默认容器为Netty，Netty是高性能的、基于异步事件驱动的NIO框架（异步非阻塞）

**阻塞（BIO）的方式**![image-20211105010248097](.\Spring5Notes_images\image-20211105010248097.png)

**非阻塞（NIO）的方式**![image-20211105010425399](.\Spring5Notes_images\image-20211105010425399.png)

> SpringWebFlux的执行过程与SpringMVC是很相似的，只是组件有些不一样，过程大部分一样

+ SpringWebFlux有一个核心控制器DispatchHandler，DispatchHandler在Spring中实现了WebHandler接口![image-20211105010805249](.\Spring5Notes_images\image-20211105010805249.png)![image-20211105010839348](.\Spring5Notes_images\image-20211105010839348.png)![image-20211105011037173](.\Spring5Notes_images\image-20211105011037173.png)
+ SpringWebFlux有一个核心控制器DispatchHandler，主要负责请求的处理，除了DispatchHandler之外还有一些其他的相关组件，如
  + HanddlerMapping（请求查询到处理的方法）
  + HandlerAdapter（真正负责请求处理）
  + HandlerResultHandler（响应结果的处理）
+ 若想使用SpringWebFlux实现函数式编程，有两个针对函数式编程的重要接口
  + RouterFunction（路由处理，即将请求转发给对应Handler）
  + HandlerFunction（处理请求并响应函数，即执行具体方法）

### Spring5应用WebFlux

#### 基于注解编程模型实现

> 使用注解编程模型方式和SpringMVC的使用很相似，只需要将一些相关依赖配置到项目中，SpringBoot会自动配置相关运行容器，默认情况下使用Netty服务器

1. 创建SpringBoot工程，在pom.xml中将SpringBoot的版本改为2.2.1（SpringBoot2.0版本开始，默认使用的就是Spring5）![image-20211105012101621](.\Spring5Notes_images\image-20211105012101621.png)

2. 引入依赖，只需要修改pom.xml文件中artifactId为spring-boot-starter为spring-boot-starter-webflux![image-20211105012243826](.\Spring5Notes_images\image-20211105012243826.png)

3. 在application.properties配置文件中自定义启动的端口号![image-20211105012348604](.\Spring5Notes_images\image-20211105012348604.png)

4. 创建包和相关类，在entity包下创建User类，在service包下定义UserService![image-20211105012513787](.\Spring5Notes_images\image-20211105012513787.png)![image-20211105012829356](.\Spring5Notes_images\image-20211105012829356.png)![image-20211105012917963](.\Spring5Notes_images\image-20211105012917963.png)![image-20211105013343981](.\Spring5Notes_images\image-20211105013343981.png)

5. 创建Controller层

   ```java
   @RestController
   public class UserController {
       //注入 service
       @Autowired
       private UserService userService;
       //id 查询
       @GetMapping("/user/{id}")
       public Mono<User> geetUserId(@PathVariable int id) {
           return userService.getUserById(id);
       }
       //查询所有
       @GetMapping("/user")
       public Flux<User> getUsers() {
           return userService.getAllUser();
       }
       //添加
       @PostMapping("/saveuser")
       public Mono<Void> saveUser(@RequestBody User user) {
           Mono<User> userMono = Mono.just(user);
           return userService.saveUserInfo(userMono);
       } 
   }
   ```

> 说明：
>
> SpringMVC 方式实现，同步阻塞的方式，基于 SpringMVC+Servlet+Tomcat
>
> SpringWebflux 方式实现，异步非阻塞 方式，基于 SpringWebflux+Reactor+Netty

#### 基于函数式编程模型实现

1. 在使用函数式编程模型操作SpringWebflux ，**需要我们自己初始化服务器**

2. 基于函数式编程模型时候，有两个核心接口：

   + RouterFunction（实现路由功能，请求转发给对应的 handler）

   + HandlerFunction（处理请求生成响应的函数）：核心任务**定义两个函数式接口的实现**并且**启动需要的服务器**
   + SpringWebflux 请求和响应不再是 ServletRequest 和 ServletResponse ，而是`ServerRequest` 和 `ServerResponse`

1. 把注解编程模型工程复制一份 ，保留 entity 和 service 内容

2. 创建 Handler（具体实现方法）

   ```java
   public class UserHandler {
       private final UserService userService;
       public UserHandler(UserService userService) {
           this.userService = userService;
       }
       //根据 id 查询
       public Mono<ServerResponse> getUserById(ServerRequest request) {
           //获取 id 值
           int userId = Integer.valueOf(request.pathVariable("id"));
           //空值处理
           Mono<ServerResponse> notFound = ServerResponse.notFound().build();
           //调用 service 方法得到数据
           Mono<User> userMono = this.userService.getUserById(userId);
           //把 userMono 进行转换成流返回
           //使用 Reactor 操作符 flatMap
           return userMono.flatMap(person -> ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(fromObject(person))).switchIfEmpty(notFound);
       }
       //查询所有
       public Mono<ServerResponse> getAllUsers() {
           //调用 service 得到结果
           Flux<User> users = this.userService.getAllUser();
           return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(users,User.cl
   ass);
       }
       //添加
       public Mono<ServerResponse> saveUser(ServerRequest request) {
           //得到 user 对象
           Mono<User> userMono = request.bodyToMono(User.class);
           return  ServerResponse.ok().build(this.userService.saveUserInfo(userMono));
       } 
   }
   ```

3. 初始化服务器，编写 Router，定义一个类，在类中定义方法

   ```java
   //1 创建 Router 路由
   public RouterFunction<ServerResponse> routingFunction() {
       //创建 hanler 对象
       UserService userService = new UserServiceImpl();
       UserHandler handler = new UserHandler(userService);
       //设置路由
       return RouterFunctions.route(GET("/users/{id}").and(accept(APPLICATION_JSON)),handler::getUserById).andRoute(GET("/users").and(accept(APPLICATION_JSON)),handler::getAllUsers);
   }
   
   //2 创建服务器完成适配
   public void createReactorServer() {
       //路由和 handler 适配
       RouterFunction<ServerResponse> route = routingFunction();
       HttpHandler httpHandler = toHttpHandler(route);
       ReactorHttpHandlerAdapter adapter = new 
           ReactorHttpHandlerAdapter(httpHandler);
       //创建服务器
       HttpServer httpServer = HttpServer.create();
       httpServer.handle(adapter).bindNow();
   }
   
   
   public static void main(String[] args) throws Exception{
       Server server = new Server();
       server.createReactorServer();
       System.out.println("enter to exit");
       System.in.read();
   }
   ```

   ```java
   public class Client {
       public static void main(String[] args) {
           //调用服务器地址
           WebClient webClient = WebClient.create("http://127.0.0.1:5794");
           //根据 id 查询
           String id = "1";
           User userresult = webClient.get().uri("/users/{id}", id)
               .accept(MediaType.APPLICATION_JSON).retrieve().bodyToMono(User
                                                                         .class)
               .block();
           System.out.println(userresult.getName());
           //查询所有
           Flux<User> results = webClient.get().uri("/users")
               .accept(MediaType.APPLICATION_JSON).retrieve().bodyToFlux(User
                                                                         .class);
           results.map(stu -> stu.getName())
               .buffer().doOnNext(System.out::println).blockFirst();
       } 
   }
   ```

   
