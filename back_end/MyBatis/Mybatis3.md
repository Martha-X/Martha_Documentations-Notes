# Mybatis

+ MyBatis是一款优秀的**持久层框架**
+ 它**支持定制化SQL**、存储过程以及高级映射
+ MyBatis避免了几乎所有的JDBC代码和手动设置参数以及获取结果集
+ MyBatis**可以使用简单的XML**或**注解来配置和映射原生类型**、接口和Java的POJO【普通老式Java对象】为数据库中的记录
+ MyBatis本是apache的一个开源项目iBatis，2010年这个项目由apache software foundation迁移到了google code，并且改名为MyBatis
+ 2013年11月份迁移到GitHub

## 获取Mybatis

+ Maven依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.5</version>
  </dependency>
  ```

+ GitHub：https://github.com/mybatis/mybatis-3/releases

+ 中文文档地址：https://mybatis.org/mybatis-3/zh/index.html

**持久化**

+ 数据持久化【将程序的数据在持久状态和瞬时状态转化的过程】
+ 内存：**断电即失**
+ 数据库(jdbc)、io(文件持久化)，两种方式进行数据持久化，但是IO特别浪费资源

**为什么需要持久化？**

+ 有些对象不能让他丢掉
+ 内存太贵了(外在原因)

**持久层**

Dao层、Service层、Controller层

+ 完成持久化工作的代码块
+ 层界限十分明显

**为什么需要Mybatis？**

+ 方便(帮助我们将数据存入数据库中)
+ 传统的JDBC代码太复杂，随后简化为框架，自动化操作
+ 不用Mybatis也可以，但学了用更容易上手
+ 优点
  + 简单易学
  + 灵活
  + 解除SQL与程序代码之间的耦合(SQL与代码的分离)
  + 提供XML标签，支持编写动态SQL

# 第一个Mybatis程序（HelloWorld）

1. 首先我们在数据库中创建一个employee表，并添加一些数据![image-20211112172536879](.\\MyBatisNotes_images\image-20211112172536879.png)

2. 创建一个普通的Maven项目并删除src目录使其成为父工程且**导入依赖**，在子工程中创建对应数据表的JavaBean对象Employee![image-20211112172616519](.\\MyBatisNotes_images\image-20211112172616519.png)

   ```xml
   <dependencies>
       <!-- Mybatis -->
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis</artifactId>
           <version>3.5.5</version>
       </dependency>
       <!--MySQL驱动-->
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>5.1.46</version>
       </dependency>
       <!-- 日志 -->
       <dependency>
           <groupId>log4j</groupId>
           <artifactId>log4j</artifactId>
           <version>1.2.17</version>
       </dependency>
       <!--Junit单元测试-->
       <dependency>
           <groupId>junit</groupId>
           <artifactId>junit</artifactId>
           <version>4.12</version>
           <scope>test</scope>
       </dependency>
   </dependencies>
   ```

3. 在Mybatis-demo01子工程中`main\resources\`目录下创建`mybatis-config.xml`Mybatis全局配置文件并配置如下内容：**该配置文件中有数据源信息和一些运行环境的信息**

   ```XML
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <!--核心配置文件-->
   <configuration>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <!--设置MySQL驱动-->
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <!--设置MySQL url-->
                   <property name="url" value="jdbc:mysql:///mybatis?userSSL=true&amp;userUnicode=true&amp;characterEncoding=UTF-8"/>
                   <!--设置M设置ySQL用户名-->
                   <property name="username" value="root"/>
                   <!--设置MySQL密码-->
                   <property name="password" value="1234"/>
               </dataSource>
           </environment>
       </environments>
       <mappers>
       	<mapper resource=""/>
       </mappers>
   </configuration>
   ```

   > Tips：我们之前创建Spring的配置文件可以直接创建，但是Mybatis的配置文件我们需要手动做一个模板，往后就可以直接创建Mybatis配置文件了，步骤如下：

   1. 打开IDEA设置，创建一个mybatis-config.xml模板，Mybatis配置文件模板内容如下：

      ```xml
      <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE configuration
              PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-config.dtd">
      <configuration>
      
      </configuration>
      ```

      ![image-20211112174138038](.\\MyBatisNotes_images\image-20211112174138038.png)

   2. 这时候就可以直接创建Mybatis配置文件了![image-20211112174252022](.\\MyBatisNotes_images\image-20211112174252022.png)

   > 那么该配置文件有啥用呢？就是Mybatis要根据这个配置文件创建一个SqlSessionFactory

4. 在`main\resources\`目录下创建Employee类的**SQL映射文件**`EmployeeMapper.xml`，SQL映射文件中配置了每一条SQL以及SQL的封装规则等

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <!--namespace名称空间-->
   <mapper namespace="com.martha.mybatis.bean.EmployeeMapper">
       <!--
           id:就是唯一标识
           resultType:返回值类型，我们查出Employee表中的数据，想要将其封装为一个Employee对象，那么就指定为Employee对象的全类名
           #{id}:这个写法的意思就是从传递过来的参数中取出id值，进行相关操作
       -->
       <select id="selectEmp" resultType="com.martha.mybatis.bean.Employee">
           select id,last_name lastName,email,gender from employee where id = #{id}
       </select>
   </mapper>
   ```

5. 配置完SQL映射文件后，需要将此SQL映射文件在Mybatis全局配置文件中注册

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <!--核心配置文件-->
   <configuration>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <!--设置MySQL驱动-->
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <!--设置MySQL url-->
                   <property name="url" value="jdbc:mysql:///mybatis?userSSL=true&amp;userUnicode=true&amp;characterEncoding=UTF-8"/>
                   <!--设置M设置ySQL用户名-->
                   <property name="username" value="root"/>
                   <!--设置MySQL密码-->
                   <property name="password" value="1234"/>
               </dataSource>
           </environment>
       </environments>
       <!--
   我们写好的sql映射文件(xxxMapper.xml)一定要注册到全局配置文件（mybatis-config.xml）中
           当这个映射文件在类路径下，那么直接写名称即可
           当这个映射文件在某个包下，如在com.martha包下，那么就com/martha/xxxMapper.xml即可
       -->
       <mappers>
       	<mapper resource="EmployeeMapper.xml"/>
       </mappers>
   </configuration>
   ```

6. 编写测试类，根据Mybatis配置文件（全局配置文件），创建一个`SqlSessionFactory`对象，得到它就能来创建一个SqlSession，使用**SqlSession对象就能够进行增删改查操作**，一个SqlSession就代表和数据库的一次会话，**用完需要关闭此资源**

   ```java
   @Test
   public void testSqlSessionFactory() {
       InputStream inputStream = null; // 输入流声明
       SqlSession sqlSession = null; // SqlSession对象声明
       try {
           String resource = "mybatis-config.xml";
           inputStream = Resources.getResourceAsStream(resource);
           // 根据全局配置文件的得到一个SqlSessionFactory
           SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
           // 获取一个SqlSession实例，sqlSession能直接执行已经映射的sql语句
           sqlSession = sqlSessionFactory.openSession();
           // 查询一条记录：第一个参数为sql的唯一标识（就是EmployeeMapper文件中的select标签的id值，来告诉Mybatis执行哪一条SQL语句，但是为了避免与其它mapper文件中标签的id值重名，我们最好在前面加上mapper文件的namespace），第二个参数为执行sql语句的要用的参数
           Employee employee = sqlSession.selectOne("com.martha.mybatis.bean.EmployeeMapper.selectEmp", "1");
           System.out.println(employee); // 输出员工对象
           //Employee{id=1, lastName='Martha', gender='1', email='19161X@qq.com'}
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           sqlSession.close(); // 关闭SqlSession
           if (inputStream != null) {
               try {
                   inputStream.close(); // 关闭流
               } catch (IOException e) {
                   e.printStackTrace();
               }
           }
       }
   }
   ```

> 到此，我们就搭建了第一个Mybatis式的HelloWord程序

# 接口式编程Mybatis

> 我们第一个HelloWord已经写完了，但是对于我们之前写的HelloWorld，有一些问题：
>
> + 每次我们要进行增删改查操作的时候，这个sql的唯一标识id很长![image-20211112192957546](.\\MyBatisNotes_images\image-20211112192957546.png)
> + 包括我们在增删改查中传入的参数，可传入的参数是一个Object param，所以什么参数都能传入
>
> 而接下来呢Mybatis就为我们提供了一个更高级的方式，我们可以使用一个接口，使用这个接口来描述我们给定的这个SQL语句的参数以及返回值等等信息，步骤如下：

1. 编写Dao/Mapper，即创建一个com.martha.mybatis.dao下创建EmployeeMapper，层内容如下：

   ```java
   public interface EmployeeMapper {
   	// 通过id查出数据并封装为一个Employee对象
       public Employee getEmployeeById(Integer id);
   }
   ```

2. 要是放在以前我们肯定会为接口创建一个实现类，但是这里不是，我们上面定义的接口，接口中的方法的功能是通过id查出数据并封装为一个Employee对象，而我们的SQL映射文件的功能也可以从Employee表中查出数据并封装为一个Employee对象，所以说我们Mybatis提供了一个功能，**接口可以与配置文件进行动态绑定**，即接口实现类由原来的DaoImpl转变为了一个Mapper文件

   > 那么怎么绑定呢？在之前我们SQL映射文件中的namespace的值是自定义的，现在不能随便写了
   >
   > + 我们需要**将SQL映射文件的namespace的值指定为接口的全类名**，Mybatis就会将他们绑定在一起
   > + 我们也要将SQL标签的唯一标识id，指定为接口中的方法名，那么我们的SQL标签也与接口中的方法进行了绑定

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <!--namespace：指定为接口的全类名-->
   <mapper namespace="com.martha.mybatis.dao.EmployeeMapper">
       <!--将唯一标识id指定为接口中的方法名，意思就是我这个select标签，是EmployeeMapper接口下getEmployeeById方法的实现，即id对应Dao/Mapper层方法名-->
       <select id="getEmployeeById" resultType="com.martha.mybatis.bean.Employee">
           select id,last_name lastName,email,gender from employee where id = #{id}
       </select>
   </mapper>
   ```

3. 获取SqlSessionFactory，进行操作

   ```java
   @Test
   public void testSqlSession() throws Exception {
       // 1.获取SqlSessionFactory对象
       SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
       // 2.通过工厂获取sqlSession对象
       SqlSession sqlSession = sqlSessionFactory.openSession();
       // 3.本来我们该通过sqlSession直接调用增删改查方法，但是我们使用了接口的方式，那么我们就使用以下方式：
       // 获取接口的实现类对象
       try {
           EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
           // 调用接口的方法
           Employee employee = mapper.getEmployeeById(2);
           System.out.println(employee);// Employee{id=2, lastName='Beth', gender='0', email='19161@qq.com'}
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           sqlSession.close(); // 关闭
       }
   }
   ```

   > 可以发现我们执行成功了，我们可以获取到一个接口的实现类，但是我没根本没写实现类，其实我们只要将接口与SQL映射文件绑定在一起，底层就会为我们的接口自动创建一个代理对象，通过代理对象去执行增删改查操作，所以getEmployeeById方法就是由代理对象来执行的
   >
   > 所以接口式编程是我们最常用的方式，因为我们接口规定的方法参数拥有更强的类型检查，我让你传Integer类型，你就不能传入Double，包括我们的方法有明确的返回值
   >
   > 包括我们的接口本身就是一种规范，这样对于我们接口的实现，我们可以使用Mybatis来做，我们也看到了，Mybatis为接口创造了一个代理对象，通过接口，将我们DAO层与DAO的实现分离了出来，方便我们后续开发和维护

**接口式编程的总结**

> 1. 接口式编程
>
>    + 在以往我们的接口都称为dao，实现类则称为daoImpl
>
>    + 接触Mybatis之后，我们的接口都称为mapper，我们并没有为这个接口写实现类，而是使其与配置文件xxxMapper.xml绑定在一起，这个SQL映射文件就相当于接口的实现
>
> 2. SqlSession对象：它代表和数据库的一次会话，对数据库的操作都是通过它来实现的，用完要关掉
>
>    + SqlSession和Connection对象一样，都是非线程安全的，每次使用我们都应该去获取新的对象，不要将其设置为共享的成员变量
>
> 3. 虽然Mapper接口没有实现类，但是Mybatis会为这个接口生成一个代理对象，我们传入的是一个接口类型，但是拿到的是一个代理对象，而代理对象产生的前提就是将接口和SQL映射文件进行绑定，绑定之后Mybatis才会为这个接口生成一个代理对象，该代理对象通过调用接口的方法相应地执行了SQL映射文件中对应的SQL语句
>
>    `EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);`
>
> 4. Mybatis有两个重要的配置文件：
>
>    + Mybatis的全局配置文件：包含数据库连接池信息以及事务管理器信息...等系统运行环境信息
>    + SQL映射文件：保存了每一条SQL语句的映射信息，Mybatis正是通过这个文件将SQL语句抽取出来，使得我们可以定制

**结论**：

+ namespace中的值要和Dao/mapper的接口全类名一致
+ id就是对应namespace中，即指定接口中的的方法名
+ resultType：Sql语句执行后的返回值

# Mybatis全局配置文件

> Mybatis有两个重要的配置文件即全局配置文件和SQL映射文件，MyBatis的配置文件包含了影响MyBatis行为甚深的设置(settings)和属性(properties)信息，所以这两个配置文件要怎么写是我们要着重研究的

## properties标签引入外部配置文件（了解）

> Mybatis可以使用properties标签来引入外部properties配置文件的内容，因为我们在全局配置文件的配置信息是写死的，我们希望能写成一个外部的配置文件，把数据源的信息保存起来

1. 将全局配置文件中的数据源信息配置为一个jdbc.properties文件，该配置文件位于`main\resources`目录下

   ```properties
   jdbc.driver=com.mysql.jdbc.Driver
   jdbc.url=jdbc:mysql:///mybatis
   jdbc.username=root
   jdbc.password=123456
   ```

2. 在全局配置文件中，引入外部jdbc.properties配置文件，如下：

   ```xml
   <configuration>
       <!--properties标签用于引入外部文件
   		有两个属性来指定外部文件的路径：
   			resource：用于引用类路径下的资源
   			url：用于引入网络路径或者磁盘路径下的资源
   	-->
       <properties resource="jdbc.properties"></properties>
       
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <!--引入外外部配置文件之后，就动态获取其中的信息-->
                   <property name="driver" value="${jdbc.driver}"/>
                   <property name="url" value="${jdbc.url}"/>
                   <property name="username" value="${jdbc.root}"/>
                   <property name="password" value="${jdbc.password}"/>
               </dataSource>
           </environment>
       </environments>
   </configuration>
   ```

> 我们最终Mybatis与Spring整合的时候，我们数据源信息的配置完全交给了Spring来做，所以properties标签我们也基本上不会去使用了，了解一下

## settings标签运行时行为设置

> settings是一个非常重要的标签，标签中包含了很多的设置项，这些设置项都能直接影响Mybatis的运行时行为，所以一定要了解每一个设置项的作用

```xml
<configuration>
    <!--settings标签包含了很多重要的设置项-->
    <settings>
        <!--每一个单独的setting用来设置每一个设置项
				每一个设置项都有一个name属性（设置项名）和value属性（设置项取值）
	如我们设置一个开启驼峰命名mapUnderscoreTocamelCase，默认值为false，我们将其设置为true
开启的效果就是如果数据库表中的字段名为max_age，而我们JavaBean中对应的属性名为maxAge，符合驼峰命名规则，并且mapUnderscoreTocamelCase=true，那么Mybatis会自动将我们的查询结果为maxAge赋值，所以就是这个设置的作用
		-->
    	<setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--引入外外部配置文件之后，就动态获取其中的信息-->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.root}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

+ cacheEnabled：该配置影响所有映射器中配置的缓存的全局开关【默认值true】
+ lazyLoadingEnabled：延迟加载的全局开关，当开启时所有的关联对象都会延迟加载，特定关联关系中可以通过设置fetchType属性来覆盖该项的开关状态【默认值false】
+ useColumnLabel：使用列标签代替列名，不同的驱动在这方面会有不同的表现，具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果【默认值true】
+ defaultStatementTimeout：设置超时时间，他决定驱动等待数据库响应的秒数
+ mapUnderscoreToCamelCase：是否开启自动驼峰命名规则(camel case)映射【默认值false】

## typeAliases别名处理器（了解）

> 别名处理器的作用就是将我们的Java类型起一个短一点简单一点的别名（**别名不区分大小写**），这样的话，以后我们全局配置文件中引用该Java类的全类名的地方，我们就可以直接指定它的别名啦，不用写全类名，这样的话能方便许多

```xml
<configuration>
    <!--可以为我们的Java全类名起一个别名-->
   <typeAliases>
       <!--为某一个Java类型起别名
			type属性就是指定要起别名的类型全类名
如下我们为Employee类的全类名起了一个别名，别名默认就是类名小写--employee
或我们不想要默认的，可以使用alias属性为其指定一个新的别名
		-->
    	<typeAlias type="com.martha.mybatis.bean.Employee"></typeAlias>
       
		<!--但是我们这样给一个类起别名就写一个typeAlias标签，但如果我们一个包下有20多个类呢？所以我们还可以使用package标签为某个包下（当前包与后代包）的所有类进行批量起别名，默认别名为类名小写-->
       <package name="com.martha.mybatis.bean"></package>
    </typeAliases>
    
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--引入外外部配置文件之后，就动态获取其中的信息-->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.root}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

> 指定了Employee的别名之后，我们就可以在SQL映射文件中，将返回值类型改为该别名，效果是一样的

```xml
<select id="getEmployeeById" resultType="employee">
        select id,last_name lastName,email,gender from employee where id = #{id}
</select>
```

> 当然使用批量起别名，万一后代包下与当前包的类名一样了，那么Mybatis就会报错，所以我们还可以在批量起别名的情况下通过@Alias注解，标注在类上方，为某个类指定新的别名

```java
@Alias("emp") // 指定别名为 emp
public class Employee{...}
```

> 要注意的是，Mybatis已经为Java内部的类型起好了别名，所以我们起别名的时候，尽量不要与它们冲突
>
> 但是呢，虽然起别名好，但是我们配置完别名，如果我们想要通过Ctrl进入对应的Java类，就进不去，然后我们还要通过别名一个一个找，相反就很麻烦了，**所以还是推荐使用全类名的方式**

## typeHandlers类型处理器（了解）

> 类型处理器的作用就是架起Java类型与数据库类型一一映射的一个桥梁，比如我们将一个Java对象保存进数据库时，就牵扯到将Java中String类型的变量保存成数据库中varchar类型，比如我们要查询数据库，数据库中有一个Integer类型数据，那我们又要转成Java对应的Integer类型
>
> 所以在做Java类型与数据库类型进行映射的时候，Mybatis用的就是typeHandlers

## plugins插件

> 插件是MyBatis提供的一个非常强大的机制，我们可以通过插件来修改MyBatis的一些核心行为；插件通过**动态代理机制**，可以介入四大对象的任何一个方法的执行
>
> + Executor
> + ParameterHandler
> + ResultSetHandler
> + StatementHandler

## environments运行环境

> 既然这个标签叫做environments，顾名思义Mybatis可以配置多种环境

```xml
<environments default="development">
    <!--每一个单独的environment标签代表一个具体的环境信息
    environment标签的id属性代表当前环境的唯一标识，可以通过environments标签的default属性动态地指定id属性的值，来进行当前环境的快速切换
-->
    <environment id="test">
    	<!--当然每一个单独的environment标签，必须有两个标签
		transactionManager标签：事务管理器（了解就行，我们后面都是使用Spring来进行事务控制的）
			type：配置的是事务管理器的类型，有两种取值：JDBC|MANAGED（了解）
				JDBC代表的就是使用JDBC的方式进行事务的提交回滚操作
				MANAGED代表的就是使用JavaEE服务器容器的方式进行事务的控制
		dataSource标签:数据源（了解）
			type：数据源类型，有三种取值UNPOOLED（不使用连接池）|POOLED（使用连接池）|JNDI（在EJB或应用服务器这类容器中查找指定的数据源）
-->
    </environment>
    
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql:///mybatis"/>
            <property name="username" value="root"/>
            <property name="password" value="123456"/>
        </dataSource>
    </environment>
</environments>
```

## databaseIdProvider多数据库支持

> 配置了这个标签，Mybatis会根据不同的数据库可以执行不同的SQL语句

```xml
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
  	
    <!--支持多数据库厂商
	type：作用就是得到数据库厂商标识（驱动），mybatis就能根据数据库厂商来执行不同的SQL语句
-->
    <databaseIdProvider type="DB_VENDOR">
        <!--property-name：数据库产商标识
			property-value：为标识起一个别名，方便SQL语句使用databaseId属性引用
-->
    	<property name="MySQL" value="mysql"></property>
        <property name="Oracle" value="oracle"></property>
    </databaseIdProvider>
</configuration>
```

```xml
<select id="getEmployeeById" resultType="employee" databaseId="mysql">
        select id,last_name lastName,email,gender from employee where id = #{id}
</select>
```

## Mappers标签SQL映射注册

> mappers标签用于将SQL映射文件注册在全局配置文件中

```xml
<!--注册一个SQL映射：第一种方式
	 有两个属性来指定SQL映射文件的位置：
			resource：用于引用类路径下的SQL映射文件，在开发中我们常使用一个包来保存SQL映射文件
			url：用于引入网络路径或者磁盘路径下的SQL映射文件
-->
<mappers>
    <mapper resource="com/martha/EmployeeMapper.xml"/>
</mappers>
```

> 第二种注册方式

```xml
<mappers>
<!--class顾名思义就是类名的意思，在这里我们要写接口的全类名，即注册接口
			这种方式要想实现有两种条件：
				1. SQL映射文件和接口放在同一包下
				2. SQL映射文件的名称和接口名称保持一致

-->
    <mapper class="com.martha.mybatis.dao.EmployeeMapper"></mapper>
</mappers>
```

> 第三种注册方式（常用）：批量注册，也是有以下两个要求，否则Mybatis找不到SQL映射文件
>
> 1. SQL映射文件和接口放在同一包下
>
> 2. SQL映射文件的名称和接口名称保持一致

```xml
<mappers>
    <package name="com.martha.mybatis.dao"></mapper>
</mappers>
```

**在Maven工程中，扫描包还是报错未绑定，那么可以在父工程pom.xml添加如下配置信息**

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```

## 总结

> 全局配置文件中的标签配置是有顺序的，一定要按照顺序配置，否则就会报错

![image-20211112222602585](.\\MyBatisNotes_images\image-20211112222602585.png)

# 映射文件CRUD操作

1. 我们之前接口就定义了一个查询方法，我们将剩余增加、删除、修改方法都添加上

   ```java
   public interface EmployeeMapper {
       public Employee getEmployeeById(Integer id);
   
       public void addEmployee(Employee employee);
   
       public void updateEmployee(Employee employee);
   
       public void deleteEmployee(Integer id);
   }
   ```

2. 在对应SQL映射文件中为这三个新方法添加定义（实现）

   ```xml
   <!--public void addEmployee(Employee employee);
           id为方法名
           parameterType为传入的参数类型：当然parameterType可以省略，因为我们查询的时候就没指定参数类型，如果要写就指定参数全类名或者别名
           查询语句中的values()对应的就是JavaBean中的属性名称，表示取出对象中的属性为数据库指定（对应）字段赋值
           注意，我们的SQL语句不以分号结尾
       -->
   <insert id="addEmployee" parameterType="com.martha.mybatis.bean.Employee">
       insert into employee(last_name, email, gender)
       values (#{lastName}, #{email}, #{gender})
   </insert>
   
   <!--public void updateEmployee(Employee employee);
           因为parameterType可以省略，所以我们就不加了
       -->
   <update id="updateEmployee">
       update employee
       set last_name=#{lastName},
       email=#{email},
       gender=#{gender}
       where id = #{id}
   </update>
   
   <!--public void deleteEmployeeById(Integer id);-->
   <delete id="deleteEmployeeById">
       delete
       from employee
       where id = #{id}
   </delete>
   ```

3. 接下来我们就可以在程序中进行增删改查的测试了

   ```java
   @Test
   public void testCRUD() {
       SqlSessionFactory sqlSessionFactory = null;
       SqlSession sqlSession = null;
       try {
           sqlSessionFactory = getSqlSessionFactory();
           // 空参数的openSession不会自动提交数据
           sqlSession = sqlSessionFactory.openSession();
           // 获取接口的代理对象
           EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
           // 测试添加方法
           //mapper.addEmployee(new Employee(0,"Jame","1","james@qq.com"));
           // 测试修改方法
           //mapper.updateEmployee(new Employee(3,"James","1","james@qq.com"));
           // 测试删除方法
           mapper.deleteEmployeeById(3);
           // 手动提交数据
           sqlSession.commit();
       } catch (Exception e) {
           sqlSession.rollback();
           e.printStackTrace();
       } finally {
           sqlSession.close();
       }
   }
   ```

   > 在增删改查操作中要注意：
   >
   > 1. 可以看到我们接口中定义的增删改方法返回值为void，如果我们希望返回值为boolean，其实也是可以的
   >
   >    Mybatis允许增删改查**直接定义**以下返回值类型
   >
   >    + Integer或int：我们的增删改是的结果影响多少行，将**影响结果**的**行数赋值**给该返回值类型
   >    + Long或long：我们的增删改是的结果影响多少行，将**影响结果**的**行数赋值**给该返回值类型
   >    + Boolean或boolean：我们的增删改是的结果影响多少行，如果返回值类型为Boolean/boolean，只要影响**超过一行数据，那么则返回true**，影响0行就返回false
   >
   > 2. 若我们通过SqlSessionFactory创建的是一个空参数的openSession()，那么**我们需要手动提交数据**，需要自动提交的话，就直接声明sqlSessionFactory.openSession(`true`);即可实现自动提交

## insert时获取自增主键的值

> 我们上面对CRUD做了一个简单的测试，现在我们需要讨论一下关于主键生成的问题，当我们添加了一条Employee数据，我们希望**将刚刚添加的这条数据的主键给我拿到**，在MySQL中我们的为当前主键设置了为自增，每次主键的值都是MySQL自增出来的，我们想要获取到这个值，如果是原生JDBC的方式可以通过调用ResultSet.getGeneratedKeys()获取自动生成的主键，那么Mybatis怎么做的呢？
>
> 其实Mybatis也是利用ResultSet.getGeneratedKeys()这个方法为我们获取到的，我们要获取自增主键的值，只需要在SQL映射文件的**插入标签中**进行如下配置即可，有一个属性叫做userGeneratedKeys，它默认是false的，我们将其改为true，告诉Mybatis使用自动生成的主键策略，配置好后，还要使用keyProperty属性告诉Mybatis将获取到的主键值交给谁，也就是Mybatis获取到这个主键值以后，将该主键值赋值给JavaBean对象中的哪个属性

1. 在插入语句中配置获取主键值策略，并将插入时获取到的主键值赋值给Employee类的id属性

   ```xml
   <insert id="addEmployee" parameterType="com.martha.mybatis.bean.Employee" useGeneratedKeys="true" keyProperty="id">
       insert into employee(last_name, email, gender)
       values (#{lastName}, #{email}, #{gender})
   </insert>
   ```

2. 测试插入操作，输出Employee的id属性，查看当前插入时自增的主键值

   ```java
   // 测试添加方法
   Employee employee = new Employee(0,"Jame","1","james@qq.com");
   mapper.addEmployee(employee);
   System.out.println(employee.getId()); // 获取主键值 9
   ```

## 映射文件参数处理

> 我们研究一下Mybatis是如何进行参数处理的，我们在mapper接口中定义的方法和方法参数，在SQL映射文件中只需要通过`#{}`就能获取对象中的属性值，或者方法形参对应的值，对于参数处理上其实还是有非常多的规则的，比如单个参数的处理、多个参数的处理、命名参数的处理，如下

### 单个参数的处理

```java
// 如单个参数——id
public Employee getEmployeeById(Integer id);
```

```xml
<!--在这里我们通过#{id}就能获取到方法形参的id值-->
<select id="getEmployeeById" resultType="com.martha.mybatis.bean.Employee">
    select id, last_name lastName, email, gender
    from employee
    where id = #{id}
</select>
```

> 如果是传入单个参数时，Mybatis不会做特殊处理，`#{}`中你甚至可以都不用写参数名，随便写也可以，因为只有一个参数，就是取出这个参数的值，所以对Mybatis来说就只有一个参数，你写啥都一样，如下也是可以获取到id值的

```xml
<!--在这里我们通过#{id}就能获取到方法形参的id值-->
<select id="getEmployeeById" resultType="com.martha.mybatis.bean.Employee">
    select id, last_name lastName, email, gender
    from employee
    where id = #{idabc}
</select>
```

### 多个参数的处理

```java
// 我们来传上两个参数
public Employee getEmpByIdAndLastName(Integer id,String lastName);
```

```xml
<!-- 在这里通过获取方法的两个形参拼接查询条件 -->
<select id="getEmpByIdAndLastName" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where id = #{id}
          and last_name = #{lastName}
</select>
```

> 运行时出现了异常![image-20211113125914783](.\\MyBatisNotes_images\image-20211113125914783.png)
>
> Mybatis会对我们的多个参数进行特殊的处理
>
> + 多个参数会被封装成一个Map【key：param1...paramN（或者参数索引0...N也可以），value：才是我们传入的参数值】
> + `#{}`此时需要从map中获取指定key的值，如
>
>   1. 通过param1，param2获取对应的参数值：#{param1}，#{param2}
>
>   2. 通过索引获取对应的参数值：#{0}，#{1}

```xml
<!-- 使用第一种方式 -->
<select id="getEmpByIdAndLastName" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where id = #{param1}
          and last_name = #{param2}
</select>
<!-- 使用第二种方式 -->
<select id="getEmpByIdAndLastName" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where id = #{0}
          and last_name = #{1}
</select>
```

> 但是如果参数过多的情况下，使用这两种方式显然可读性非常差，所以**推荐使用命名参数**

### 命名参数的处理

> 明确指定封装多个参数为Map的时候，Map的key是啥，不要使用param1...了，直接为其参数指定key，
>
> 使用@Param注解为方法参数来明确指定封装为Map时，它所对应的key，如下，使用@Param注解为其指定key后，在SQL映射文件中，就可以使用`#{指定key}`来取出对应的参数值啦

```java
// 我们来传上两个参数，并通过@Param注解明确指定他们在Map中的key
public Employee getEmpByIdAndLastName(@Param("id") Integer id,@Param("lastName")String lastName);
```

```xml
<!--使用指定的key来获取参数值-->
<select id="getEmpByIdAndLastName" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where id = #{id}
          and last_name = #{lastName}
</select>
```

> 我们发现使用id和lastName正好是我们Employee类的属性，且如果多个参数的话，使用@Param注解标识多个参数，也会很长，所以如果多个参数正好是我们业务逻辑的数据类型，我们就可以直接传入POJO类，那么通过`#{属性名}`就可以直接取出传入的POJO属性值，这个方式我们开始就试过了，这里就不演示了
>
> 如果多个参数**不是业务模型中的数据**，没有对应的POJO类，为了方便，我们**也可以传入Map**，因为它最终也会将多个参数封装为Map，这个时候`#{key}`就是取出Map中对应的值，演示如下：

```java
// 我们将方法参数定义为一个Map对象
public Employee getEmpByMap(Map<String,Object> map);
```

```java
Map<String,Object> map = new HashMap<>();
map.put("id",1);
map.put("lastName","Martha");
Employee employee = employeeMapper.getEmpByMap(map);
```

```xml
<!--使用Map中的的key来获取参数值，查出Employee对象-->
<select id="getEmpByMap" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where id = #{id}
          and last_name = #{lastName}
</select>
```

> 但是呢，如果这多个参数不是模型中的数据且又要经常使用，那么每次都要创建一个Map，也会很麻烦，所以在这里我们建议编写一个TO（Transfer Object）数据传输对象，就是将其封装为一个JavaBean

![image-20211113134020000](.\\MyBatisNotes_images\image-20211113134020000.png)

## #{}与${}的取值区别

> 我们之前使用的`#{}`，可以获取Map中的值，或者是POJO对象属性的值，那么对于Mybatis来说，还有一种取值方式就是`${}`，它的取值效果和`#{}`都是一样的，但是它们有啥区别呢？
>
> 那么我们下面就来演示一下二者的取值有啥不同，代码如下：

```xml
<!--id使用${}来获取；lastName使用#{}来获取-->
<select id="getEmpByMap" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where id = ${id}
          and last_name = #{lastName}
</select>
```

```java
Map<String,Object> map = new HashMap<>();
map.put("id",1);
map.put("lastName","Martha");
Employee employee = employeeMapper.getEmpByMap(map);
System.out.println(employee);
```

![image-20211113203954180](.\\MyBatisNotes_images\image-20211113203954180.png)

> `#{}`：是以**预编译的形式**，将参数设置到SQL语句中，就像原生JDBC使用PreparedStatement操作一样，**可以防止SQL注入**
>
> `${}`：取出的值直接就拼接在SQL语句中，没法防止SQL注入，会有安全隐患
>
> 所以大多数情况下，我们取出参数的值就应该使用`#{}`
>
> 那么在某些情况下，我们项目中可能会有分库分表操作，那么这样的话查询我们的表，比如按照年份分表拆分：select * from `2016_salary`，如2016的位置就是原生JDBC不支持占位符的地方我们就可以使用`${}`进行取值，那么我们就可以动态来拼接SQL语句了：
>
> + select * from `${year}_salary where xxx`
> + select * from employee order by `${name} ${order}`，order by 后不支持占位符，我们就需要使用拼串来实现
> + **所以总结就是能使用占位符的地方使用`#{}`，不能使用占位符的地方且有需求就使用`${}`**

```xml
<!--在这里我们可以动态获取表名，比如使用map.put("tableName","employee")，那么就可以在下面非占位符的位置使用拼串，即${}的方式进行动态取值-->
<select id="getEmpByMap" resultType="com.martha.mybatis.bean.Employee">
    select * from ${tableName} where id=#{id} and last_name=#{lastName}
</select>
```

## #{}取值时指定参数相关规则

> `#{}`可以在取值的时候，指定该值的Java类型，以及数据库对应的JDBC类型，能指定的类型如下：
>
> + javaType：Java类型
> + jdbcType：数据库JDBC类型
> + mode：存储过程
> + numericScale：保留几位小数
> + resultMap：规定所封装的结果类型
> + typeHandler：规定处理该数据的类型处理器
> + jdbcTypeName：与jdbcType相同
> + expression：表达式（未来准备支持的功能）
>
> 主要我们来了解一下jdbcType，jdbcType通常需要在某种特定的条件下被设置，在我们数据为null的时候，有些数据库可能不能识别Mybatis对null的默认处理，比如Oracle，如果你的数据为null的时候，Oracle就会报错，这个时候我们就必须告诉Mybatis，这个jdbcType是什么类型
>
> 因为Mybatis对所有的null值默认映射的都是原生JDBC的OTHER类型，这样的话Oracle不能正确处理，而MySQL能兼容这个类型，所以当使用的是Oracle环境的时候，需要在进行取值操作的时候，指明jdbcType为NULL类型，而不是默认的OTHER类型
>
> 当然我们也可以将全局配置文件中setting中的jdbcTypeForNULL=NULL即可
>
> 两种方式都行

```xml
<!--假设这是一个Oracle环境，指明jdbcType为NULL类型-->
<select id="getEmpByMap" resultType="com.martha.mybatis.bean.Employee">
    select * from employee where id=#{id} and last_name=#{lastName,jdbcType=NULL}
</select>
```

## select返回List

> 我们查询经常会返回一个List的集合，那么像这种情况要怎么处理呢？代码如下：

```java
//根据名字进行模糊查询，返回多个Employee对象
public List<Employee> getEmpForList(String lastName);
```

```xml
<!--public List<Employee> getEmpForList(String lastName);
        如果查询后返回的是一个List集合，resultType要指定为集合中元素的类型全类名，此处为Employee类型，那么resultType就是Employee对象的全类名
        我们Mybatis会将每一条数据封装为Employee对象，并最终装入List集合中
    -->
<select id="getEmpForList" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where last_name like #{lastName}
</select>
```

```java
EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
List<Employee> list = mapper.getEmpForList("%a%");
list.forEach(System.out::println);
```

> 所以说返回一个List集合，resultType类型指定为集合中的元素的类型即可，Mybatis会自动将每一条数据封装为元素类型对象，并最终装入List集合中

## select返回Map

> 我现在查询Employee对象想返回一个Map集合，key就是字段名，value就是字段对应的值

```java
// 返回一条记录的Map，key为列名，值为字段对应的值
public Map<String,Object> getEmpForMapById(Integer id);
```

```xml
<!--public Map<String,Object> getEmpForMapById(Integer id);
        因为我们希望将数据封装为一个Map，所以这时候，我们的resultType就应该指定为Map的别名map
        因为Mybatis已经为我们经常使用的Java类型规定好了对应的别名
    -->
<select id="getEmpForMapById" resultType="map">
    select *
    from employee
    where id = #{id}
</select>
```

```java
EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
Map<String, Object> map = mapper.getEmpForMapById(1);
System.out.println(map); // {gender=1, last_name=Martha, id=1, email=19161X@qq.com}
```

> 可见我我们对单条记录的封装已经生效，若我想实现多条记录封装为一个Map，那么单条记录的话，它的key可以是列名，那么多条记录的话，就需要这么来定义`Map<Integer,Employee>`，因为每一条查出来的数据我们都能封装成一个Employee对象，而这个Map的键就是这条记录的主键，**即键是记录的主键，值是封装后的JavaBean**

```java
// 根据名称进行模糊查询，查询出多条封装为Map的Employee对象，键为主键，值为Employee对象
public Map<Integer,Employee> getEmpForMapByName(String lastName);
```

```xml
<!--public Map<Integer,Employee> getEmpForMapByName(String name);
        在这里我们想要将很多条查出来的记录封装为Employee对象，所以说resultType写的还是Map集合中的元素的类型
        Mybatis知道Map中要装入Employee，但是Mybatis并不知道要使用主键来作为Map的key，所以我们需要给我们的mapper接口对应的方法上方加一个注解
        这个注解就叫@MapKey，意思是告诉Mybatis在封装这个Map的时候，Map的key使用Employee的哪一个属性进行封装
    -->
<select id="getEmpForMapByName" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where last_name like #{lastName}
</select>
```

```java
// 根据名称进行模糊查询，查询出多条封装为Map的Employee对象，键为主键，值为Employee对象
@MapKey("id") // 该注解意味着告诉Mybatis在封装Map的时候，使用元素类型（Employee）的哪一个属性作为Map的key
public Map<Integer,Employee> getEmpForMapByName(String lastName);
```

```java
EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
Map<Integer, Employee> map = mapper.getEmpForMapByName("%e%");
System.out.println(map);
/*
{2=Employee{id=2, lastName='Beth', gender='0', email='19161@qq.com'}, 4=Employee{id=4, lastName='Jame', gender='1', email='james@qq.com'}, 5=Employee{id=5, lastName='Jame', gender='1', email='james@qq.com'}}
*/
```

## resultMap

### select之resultMap自定义结果集映射规则

> resultMap与resultType是两个非常重要的属性，resultType是与我们自动封装有关的，我们查出数据之后，我们指定要封装为什么类型，那么Mybatis就会自动地将数据封装为指定类型，但是在我们查出数据的时候，若**列名与JavaBean的属性名不一样**，我们一开始是封装不成功的，
>
> + 那么我们的解决办法就是在SQL语句中，为指定字段添加别名，使得别名与我们的JavaBean属性名一致；
> + 或者当我们的列名与我们的JavaBean属性名符合我们的驼峰命名法，那我们就在全局配置文件中开启驼峰命名法
> + 当然还有最后一种办法，就是使用resultMap，叫做自定义结果集，我们可以自定义我们Employee对象的属性对应哪个字段
>
> 我们更多复杂的封装规则，我们都是依赖于resultMap，它是一个非常强大非常重要的一个东西

```java
public Employee getEmpById(Integer id);
```

```xml
<!--resultType和resultMap，它们只能二选一出现-->
<mapper namespace="com.martha.mybatis.dao.EmployeeMapperPlus">
    <!--自定义某个JavaBean的封装规则，比如哪个属性对应哪个列，在这里进行指定
        type：指定自定义规则的Java类型
    -->
    <resultMap id="resultMapForEmp" type="com.martha.mybatis.bean.Employee">
        <!--id标签用于指定主键列的封装规则，使用id标签来定义主键在底层会有优化，用下面的result标签也没问题，就是推荐使用id标签
            column指定表中列名，在查询时有有指定别名就为指定为别名
            property指定JavaBean中属性名
        -->
        <id column="id" property="id"></id>
        <!--result标签用于定义普通列的封装规则，如下我们指定了last_name的封装规则，就可以将全局配置文件中的驼峰命名设置取消了-->
        <result column="last_name" property="lastName"/>
        <result column="email" property="email"/>
        <result column="gender" property="gender"/>
    </resultMap>
    
    
    <!--public Employee getEmpById(Integer id);
		resultMap：指定外部封装规则的id，这样就引用到了封装规则
-->
    <select id="getEmpById" resultMap="resultMapForEmp">
        select *
        from employee
        where id = #{id}
    </select>
</mapper>
```

### resultMap实现关联查询（级联赋值方式）

> 先来看JavaBean的改动

```java
public class Employee {
    private Integer id;
    private String lastName;
    private String gender;
    private String email;
    private Department dept; // 一个Employee员工对应一个部门
}

public class Department{
    private Integer id;
    private String departmentName;
}
```

**对应部门数据库如下**

```mysql
CREATE TABLE department(
	id INT(11) PRIMARY KEY AUTO_INCREMENT,
	department_name VARCHAR(255)
)
# 为employee表添加一个列，部门id列
ALTER TABLE employee
  ADD COLUMN dept_id INT (11);
#为了保证数据完整性，我们为dept_id添加外键关联
ALTER TABLE employee
  ADD CONSTRAINT fk_emp_deptId FOREIGN KEY (dept_id) REFERENCES department (id)
```

![image-20211114132255082](.\\MyBatisNotes_images\image-20211114132255082.png)

![image-20211114132828359](.\\MyBatisNotes_images\image-20211114132828359.png)

> 根据上表我们发现一个员工都有一个与之对应的部门信息，我们的需求就是查询Employee之后封装Employee对象，但是如果一个Employee对应一个Department，即每一个员工都有其部门信息，这个部门的引用也在Employee对象中，这个时候我要封装部门对象，要如何操作？即我们查出Employee的同时查出该员工对应的部门信息

```java
public Employee getEmpWithDept(Integer id);
```

```mysql
SELECT * FROM employee emp,department dept
WHERE emp.`id`=dept.`id` AND emp.id = 1;  -- 试着关联查询一下
```

![image-20211114133534509](.\\MyBatisNotes_images\image-20211114133534509.png)

> 我们可以看到employee表中的id与department表中的id字段重名了，那么在我们resultMap自定义结果集映射时，就无法区分开来，哪个列对应哪个属性，所以我们需要在查询出结果集时，为相同字段起别名以区分开来，这样对我们结果集映射比较好办

```mysql
SELECT
  emp.*,
  dept.id department_id,
  dept.`department_name`
FROM
  employee emp,
  department dept
WHERE emp.`id` = dept.`id`
  AND emp.id = 1; -- 最终sql测试
```

![image-20211114134010900](.\\MyBatisNotes_images\image-20211114134010900.png)

> 我们为部门表的id指定一个别名，这样一来就区分开了，我们就可以根据查出的列进行结果集映射的操作

```xml
<!--联合查询，封装Department对象时，可以采用级联属性赋值的方式
        id：引用该结果集的唯一标识
        type：我们要来定义Employee对象的封装规则，那么我们就将type指定为Employee对象全类名
    -->
<resultMap id="resultMapForEmpWithDept" type="com.martha.mybatis.bean.Employee">
    <id column="id" property="id"/>
    <result column="last_name" property="lastName"></result>
    <result column="gender" property="gender"></result>
    <result column="email" property="email"></result>
    <!--对于属性引用类型的赋值，我们可以采用级联属性的赋值方式-->
    <result column="department_id" property="dept.id"></result>
    <result column="department_name" property="dept.departmentName"></result>
</resultMap>

<!--public Employee getEmpWithDept(Integer id);
        查询Employee对象同时查出其对应的部门信息，此处填写resultType返回一个Employee对象肯定不能自动封装部门信息，因为部门属性是一个对象，所以只能使用resultMap自定义结果集映射关系
    -->
<select id="getEmpWithDept" resultMap="resultMapForEmpWithDept">
    SELECT emp.*,
    dept.id department_id,
    dept.department_name
    FROM employee emp,
    department dept
    WHERE emp.id = dept.id
    AND emp.id = #{id}
</select>
```

```java
EmployeeMapperPlus mapper = sqlSession.getMapper(EmployeeMapperPlus.class);
Employee emp = mapper.getEmpWithDept(1);
System.out.println(emp);
```

![image-20211114141427689](.\\MyBatisNotes_images\image-20211114141427689.png)

### resultMap实现关联查询（association定义关联对象封装规则方式）

> 我们可以使用**级联属性赋值的方式**，将我们部门表中的数据查询出的数据封装到Employee对象的部门属性中，除此之外，还有一种方式，就是使用association定义关联对象封装规则方式，即嵌套结果集的方式

```xml
<!--联合查询，封装Department对象时，也可以采用association指定Department对象封装规则的方式实现联合查询
        id：引用该结果集的唯一标识
        type：我们要来定义Employee对象的封装规则，那么我们就将type指定为Employee对象全类名
    -->
<resultMap id="resultMapForEmpWithDept" type="com.martha.mybatis.bean.Employee">
    <id column="id" property="id"></id>
    <result column="last_name" property="lastName"></result>
    <result column="gender" property="gender"></result>
    <result column="email" property="email"></result>
    <!--association可以指定关联的JavaBean对象的封装规则，在association标签中指定关联对象的封装规则
            property：指的就是Employee对象中关联的Javabean对象Department对象的引用变量名（private Department dept;），即dept，也就是指定Employee对象中哪一个属性是关联的对象
            javaType：指定该关联对象的类型（不能省略）
        -->
    <association property="dept" javaType="com.martha.mybatis.bean.Department">
        <!--这时候这个id指的就是Department对象中的id了，但是column还是查询结果集中的字段名（或别名）-->
        <id column="department_id" property="id"></id>
        <result column="department_name" property="departmentName"></result>
    </association>
</resultMap>
```

### resultMap实现关联查询（association分步查询方式&延迟加载）

> 我们可以使用association对关联对象属性定义封装规则，也可以使用association进行分步查询
>
> 因为在实际开发中，既然有Department类，就肯定有其对应的DepartmentMapper接口来实现对Department表的增删改查操作，那么我们就可以在查询出Employee对象的同时，拿着employee表的的dept_id字段的值，再去执行DepartmentMapper接口中查询部门信息的方法，即分两个步骤进行：

```java
// EmployeeMapperPlus接口的方法：根据id查询员工及其对应部门，分步查询
public Employee getEmpWithDeptByStep(Integer id);

// DepartmentMapper接口的方法：根据id查询部门信息
public Department getDepartmentById(Integer id);
```

```xml
<!--DepartmentMapper.xmlSQL映射文件-->
<!--public Department getDepartment(Integer id);-->
<select id="getDepartmentById" resultType="com.martha.mybatis.bean.Department">
    select *
    from department
    where id = #{id}
</select>
```

```xml
<!--使用association进行分步查询
        1. 先按照员工id查出员工信息
        2. 再根据查出的员工信息表中的dept_id去部门表中查询部门信息
        3. 将查出的部门信息封装进Employee对象中
    -->
<resultMap id="resultMapForEmpWithDeptStep" type="com.martha.mybatis.bean.Employee">
    <id column="id" property="id"></id>
    <result column="last_name" property="lastName"></result>
    <result column="gender" property="gender"></result>
    <result column="email" property="email"></result>
    <!--使用association定义关联对象的封装规则
            我们之前是指定一个javaType然后指定封装规则，但现在我们希望的是dept属性对应的Department对象是按照dept_id的值查询出来的
            所以有一个属性select：select的意思就是，我dept属性的值是调用select所指定的方法，我们指定的是DepartmentMapper接口对应其SQL映射文件中的方法，所以在这里需要为select
            属性指定DepartmentMapper.xml映射文件的namespace+方法id，即可实现调用
            也就是说select是表示当前dept属性是调用select指定的方法查询出来的结果，关键我们在调用该方法的时候，也需要传入一个id值，
            那么这时候我们就可以将员工信息表中的dept_id传给这个方法就行了
            column：指定将员工表中哪一列的值传给select指定的方法

            整个流程就是：传入column指定的这列参数的值即dept_id的值，传入给select指定的方法，并将方法查询结果封装给dept属性
        -->
    <association property="dept" select="com.martha.mybatis.dao.DepartmentMapper.getDepartmentById"
                 column="dept_id"></association>
</resultMap>

<!--public Employee getEmpWithDeptByStep(Integer id);-->
<select id="getEmpWithDeptByStep" resultMap="resultMapForEmpWithDeptStep">
    select *
    from employee
    where id = #{id}
</select>
```

```java
EmployeeMapperPlus mapper = sqlSession.getMapper(EmployeeMapperPlus.class);
Employee emp = mapper.getEmpWithDeptByStep(2);
System.out.println(emp);
```

![image-20211114150440678](.\\MyBatisNotes_images\image-20211114150440678.png)

> 可以看见，执行了两个命令，即分步查询，实现对关联对象的赋值
>
> association分步查询的方式可以组合已有的方法来完成我们复杂的功能，第二个我们分步查询还有一个更厉害的地方，就是可以实现延迟加载，那么什么是延迟加载呢？
>
> 我们之前查询Employee对象，都是将部门信息同时查出来，而此时我们希望部门信息在我们使用的时候，再去查询，这样就非常节省数据库资源了，比如我们每次要Employee对象，但部门信息我们不一定每次都使用，要实现这个延迟加载的功能也非常简单，基于我们分步查询的基础上啥都不用改，只需要在我们分步查询的基础上加上两个配置，就能完成我们延迟加载的功能

```xml
<!--分步查询的代码都不要动-->
<resultMap id="resultMapForEmpWithDeptStep" type="com.martha.mybatis.bean.Employee">
    <id column="id" property="id"></id>
    <result column="last_name" property="lastName"></result>
    <result column="gender" property="gender"></result>
    <result column="email" property="email"></result>
    <association property="dept" select="com.martha.mybatis.dao.DepartmentMapper.getDepartmentById"
                 column="dept_id"></association>
</resultMap>

<!--public Employee getEmpWithDeptByStep(Integer id);-->
<select id="getEmpWithDeptByStep" resultMap="resultMapForEmpWithDeptStep">
    select *
    from employee
    where id = #{id}
</select>

<!--在全局配置文件中添加两个设置setting-->
<settings>
    <!--显式指定我们需要设置的配置的值，即使它们是默认的，但为了防止版本更替造成默认值改变，所以我们最好还是显式指定比较好-->
    <!--懒加载模式开启之后，你关联的值会在被使用的时候加载-->
	<setting name="lazyLoadingEnabled" value="true"></setting>
     <!--侵入延迟加载功能，属性将会被完全加载出来，在你需要任何一个属性的时候，全部属性都会被加载出来，如果设置为false，那么则按需求进行加载，那我们就将他设置为false-->
    <setting name="aggressiveLazyLoading" value="false"></setting>
</settings>
```

```java
EmployeeMapperPlus mapper = sqlSession.getMapper(EmployeeMapperPlus.class);
Employee emp = mapper.getEmpWithDeptByStep(2);
System.out.println(emp.getLastName()); // 我此时就只需要employee的名称，那么就与部门信息无关
```

![image-20211114152343363](.\\MyBatisNotes_images\image-20211114152343363.png)

```java
EmployeeMapperPlus mapper = sqlSession.getMapper(EmployeeMapperPlus.class);
Employee emp = mapper.getEmpWithDeptByStep(2);
System.out.println(emp); // 当我需要整个employee对象时，意味着包含关联属性dept，那么就与部门信息有关
```

![image-20211114152531861](.\\MyBatisNotes_images\image-20211114152531861.png)

### resultMap关联查询（collection定义关联集合封装规则--嵌套结果集方式）

> 关联的如果是一个对象的话，可以使用级联属性赋值或者association的方式嵌套结果集查询或者分步查询，一个员工对应一个部门，但是一个部门下可以有很多员工，代码如下：

```java
public class Department {
    private Integer id;
    private String departmentName;
    private List<Employee> empList;
}
```

> 之前我们都是关联一个对象，这次我们关联的是一个集合，我们希望查询部门时，将部门对应的的所有员工信息都查询出来

```java
public Department getDeptWithAllEmps(Integer id);
```

```mysql
SELECT
  dept.*,
  emp.id emp_id,
  emp.last_name lastName,
  emp.gender,
  emp.`email`,
  emp.`dept_id`
FROM
  department dept
  LEFT JOIN employee emp
    ON dept.`id` = emp.`dept_id`
WHERE dept.id = 1 # SQL测试，可见开发部对应着两名员工
```

![image-20211114154207611](.\\MyBatisNotes_images\image-20211114154207611.png)

```xml
<resultMap id="resultMapForDeptWithAppEmps" type="com.martha.mybatis.bean.Department">
    <id column="id" property="id"></id>
    <result column="department_name" property="departmentName"></result>
    <!--由于关联的属性是一个集合，所以不能使用级联属性，那么我们可以使用一个collection标签来定义集合类型关联属性的封装规则
            property：指定集合类型属性
            ofType：指定集合的元素类型，我们这里集合中每一个元素类型都是Employee对象，所以为Employee对象的全类名
        -->
    <collection property="empList" ofType="com.martha.mybatis.bean.Employee">
        <!--定义集合元素的封装规则-->
        <id column="emp_id" property="id"></id>
        <result column="last_mame" property="lastName"></result>
        <result column="email" property="email"></result>
        <result column="gender" property="gender"></result>
    </collection>
</resultMap>
<!--public Department getDeptWithAllEmps(Integer id);-->
<select id="getDeptWithAllEmps" resultMap="resultMapForDeptWithAppEmps">
    SELECT dept.*,
    emp.id emp_id,
    emp.last_name,
    emp.gender,
    emp.email,
    emp.dept_id
    FROM department dept
    LEFT JOIN employee emp
    ON dept.id = emp.dept_id
    WHERE dept.id = #{id}
</select>
```

```java
DepartmentMapper mapper = sqlSession.getMapper(DepartmentMapper.class);
// 调用接口的方法
Department dept = mapper.getDeptWithAllEmps(1);
System.out.println(dept);
```

![image-20211114155649631](.\\MyBatisNotes_images\image-20211114155649631.png)

### resultMap关联查询（collection定义关联集合封装规则--分步查询方式&延迟加载）

> 与association相似

```java
public Department getDeptWithAllEmpsByStep(Integer id);
// EmployeeMapper按照部门id查找所有员工
public List<Employee> getEmpsByDeptId(Integer id);
```

```xml
<!--EmployeeMapperSQL映射文件-->
<!--public Employee getEmpsByDeptId(Integer id);-->
<select id="getEmpsByDeptId" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where dept_id = #{id}
</select>
```

```xml
<resultMap id="resultMapForDeptWithAllEmpsByStep" type="com.martha.mybatis.bean.Department">
    <id column="id" property="id"></id>
    <result column="department_name" property="departmentName"></result>
    <!--分步查询-->
    <collection property="empList" select="com.martha.mybatis.dao.EmployeeMapperPlus.getEmpsByDeptId"
                ofType="com.martha.mybatis.bean.Employee" column="id"/>
</resultMap>

<!--public Department getDeptById(Integer id);-->
<select id="getDeptWithAllEmpsByStep" resultMap="resultMapForDeptWithAllEmpsByStep">
    select *
    from department
    where id = #{id}
</select>
```

```java
DepartmentMapper mapper = sqlSession.getMapper(DepartmentMapper.class);
// 调用接口的方法
Department dept = mapper.getDeptWithAllEmpsByStep(1);
System.out.println(dept.getDepartmentName());
```

![image-20211114161754392](.\\MyBatisNotes_images\image-20211114161754392.png)

> 扩展，在我们分步查询调用方法时，column就传递了一个值，那我们若希望传递多个列的值去到目标方法要咋办呢？
>
> ```xml
> <!--可见column属性就只传递了单个id字段-->
> <collection property="empList" select="com.martha.mybatis.dao.EmployeeMapperPlus.getEmpsByDeptId"
>                 ofType="com.martha.mybatis.bean.Employee" column="id"/>
> ```
>
> 我们可以将多列的值封装为一个Map进行传递给目标方法，如下，若目标方法具有多个参数，我们需要传入多个参数给目标方法去执行
>
> ```xml
> <!--目标方法-->
> <select id="getEmpByMap" resultType="com.martha.mybatis.bean.Employee">
>     select *
>     from employee
>     where id = #{dept_id}
>     and last_name = #{lastName}
> </select>
> 
> <!--那我们的column属性就需要这么来传参，{目标方法参数引用名=传入字段名}-->
> <collection property="xxx" select="xxx.getEmpByMap"
>                 ofType="xxx" column="{dept_id=id,lastName=departmentName}"/>
> <!--当然只是为了做一个例子，不必较真赋值的问题-->
> ```
>
> 所以可以看到我们要想传入多个参数给到目标方法只需要**将多个字段封装为Map**，Map的key是目标方法`#{}`中的值，value是我们要传入的字段名，结构就是column="`{key1=value1,key2=value2....}`"

> 还有一点，在分步查询的时候，association和collection标签中有一个属性叫做fetchType，值默认是lazy，表示使用延迟加载，我们虽然在全局配置文件中开启了延迟加载，但在分步查询时可以使用这个属性单独关闭掉该分步查询的延迟加载功能
>
> ```xml
> <!--fetchType：可以将当前分步查询功能禁用掉延迟加载
> 		lazy：延迟加载（默认值），全局配置文件开启了延迟加载，那么我默认就顺应全局配置的延迟加载		
> 		eager：立即加载，就算全局配置文件开启了延迟加载，但我也可以手动为当前这个分步查询关闭掉延迟加载（所以全局开启了延迟加载，我想让这个分步查询立即加载，不用到全局配置中修改，只需要在当前这里将fetchType的值修改为eager即可免疫全局的延迟加载影响）
> -->
> <collection fetchType="lazy"/>
> ```
>
> 

### resultMap之Discriminator鉴别器（了解）

> Mybatis可以根据鉴别器判断某列的值来改变我们的封装行为 ，以封装Employee为例子：
>
> 如果查出的是女生，就将部门信息查询出来，否则不查询
>
> 如果查出的是男生，将last_name这一字段的值赋值给email

```java
public Employee testDiscriminator(Integer id);
```

```xml
<resultMap id="testDiscriminator" type="com.martha.mybatis.bean.Employee">
    <id column="id" property="id"></id>
    <result column="last_name" property="lastName"></result>
    <result column="gender" property="gender"></result>
    <result column="email" property="email"></result>
    <!--根据gender属性鉴别，gender属性的Java类型为String类型，这里指定为String类型的别名-->
    <discriminator column="gender" javaType="string">
        <!--如果gender=0，即女生，那么封装规则依然是一个Employee，但是还要将部门信息一起查询出来-->
        <case value="0" resultType="com.martha.mybatis.bean.Employee">
            <association property="dept" column="dept_id"
                         select="com.martha.mybatis.dao.DepartmentMapper.getDepartmentById"></association>
        </case>
        <case value="1" resultType="com.martha.mybatis.bean.Employee">
            <id column="id" property="id"></id>
            <result column="last_name" property="lastName"></result>
            <result column="gender" property="gender"></result>
            <!--将last_name这一字段的值赋值给email-->
            <result column="last_name" property="email"></result>
        </case>
    </discriminator>
</resultMap>

<select id="testDiscriminator" resultMap="testDiscriminator">
    select *
    from employee
    where id = #{id}
</select>
```

```java
EmployeeMapperPlus mapper = sqlSession.getMapper(EmployeeMapperPlus.class);
// 调用接口的方法
Employee emp = mapper.testDiscriminator(2);
System.out.println(emp);
```

> 查询的是女生，结果如下：

![image-20211114164840051](.\\MyBatisNotes_images\image-20211114164840051.png)

> 查询的是男生，结果如下：

![image-20211114164914422](.\\MyBatisNotes_images\image-20211114164914422.png)

# 动态SQL

> 我们之前手写的SQL，一旦更换了条件，那么整句SQL就必须要重写，所以Mybatis为我们提供了动态SQL的拼写，使用以下标签即可完成动态SQL的实现：
>
> + where：用于封装查询条件
>
> + if：判断
> + choose(when,otherwise)：分支选择，相当于switch case
> + trim：结果串截取
> + set：封装修改条件
> + foreach：遍历集合

## if

> 查询Employee，要求，携带了哪个字段的查询条件就查出这个字段的值

```xml
<select id="getEmpsByConditionIF" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where
    /* test判断表达式（使用的是OGNL表达式）*/
    <if test="id!=null">
        id=#{id}
    </if>
    <if test="lastName!=null and lastName !=&quot;&quot;">
        and last_name=#{lastName}
    </if>
    <!--&quot;是引号的意思-->
    <if test="email!=null and email.trim()!=&quot;&quot;">
        and email=#{email}
    </if>
    /*OGNL会进行字符串与数值的自动转化判断*/
    <if test="gender==0 or gender==1">
        and gender=#{gender}
    </if>
</select>
```

```java
Employee emp = new Employee(1,"martha",null,null); // 就携带了两个参数
List<Employee > employeeList = mapper.getEmpsByConditionIF(emp);
System.out.println(employeeList);
```

![image-20211114200536715](.\\MyBatisNotes_images\image-20211114200536715.png)

> 但是有一个问题，就是如果我id没传参数，那么where直接就拼接到了and，那么SQL就会报错

![image-20211114200734049](.\\MyBatisNotes_images\image-20211114200734049.png)

> 解决方式就是使用where标签将所有的查询条件包括在内，将原本的where语句替换成where标签,where标签会将多出来的and或者or自动地去除掉

```xml
<select id="getEmpsByConditionIF" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    <where>
        /* test判断表达式（使用的是OGNL表达式）*/
        <if test="id!=null">
            id=#{id}
        </if>
        <if test="lastName!=null and lastName !=&quot;&quot;">
            and last_name=#{lastName}
        </if>
        <!--&quot;是引号的意思-->
        <if test="email!=null and email.trim()!=&quot;&quot;">
            and email=#{email}
        </if>
        /*OGNL会进行字符串与数值的自动转化判断*/
        <if test="gender==0 or gender==1">
            and gender=#{gender}
        </if>
    </where>
</select>
```

> 但是呢，where标签只会去除前面and和or，如果习惯将and写在后面，那么where还是不能够完美地处理掉的，如以下这种方式：还是会出问题，但是如果你遵守规则，将and写在前面，那么where还是能够完美解决问题的

```xml
<select id="getEmpsByConditionIF" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    <where>
        /* test判断表达式（使用的是OGNL表达式）*/
        <if test="id!=null">
            id=#{id} and
        </if>
        <if test="lastName!=null and lastName !=&quot;&quot;">
            last_name=#{lastName} and
        </if>
        <!--&quot;是引号的意思-->
        <if test="email!=null and email.trim()!=&quot;&quot;">
            email=#{email} and
        </if>
        /*OGNL会进行字符串与数值的自动转化判断*/
        <if test="gender==0 or gender==1">
            gender=#{gender}
        </if>
    </where>
</select>
```

![image-20211114201445138](.\\MyBatisNotes_images\image-20211114201445138.png)

## trim自定义字符串截取（了解）

> 因为where标签并不能完美有效地解决掉SQL的拼接问题（where标签只能解决前边多出and的问题，而不能解决后边多出and的问题），所以我们可以使用trim标签代替掉where标签,，trim标签体中是整个SQL拼成字符后后的结果
>
> trim标签有四个属性：
>
> + prefix：前缀，也就是给拼串后的结果加一个前缀
> + prefixOverrides：前缀覆盖，去掉结果串前面多余的指定的字符
> + suffix：后缀，也就是给拼串后的结果加一个后缀
> + suffixOverrides：后缀覆盖，去掉结果串后面多余的指定的字符，我们下面可能会多出一个and，所以就指定为and，**如果多出and，则将and去除**

```xml
<select id="getEmpsByConditionIF" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    <!--因为少了where，那么我们给结果串补上一个where前缀
		再由于可能会出现结果串结尾是一个and，所以这里使用后缀覆盖来去除指定and-->
    <trim prefix="where" suffixOverrides="and">
        <if test="id!=null">
            id=#{id} and
        </if>
        <if test="lastName!=null and lastName !=&quot;&quot;">
            last_name=#{lastName} and
        </if>
        <if test="email!=null and email.trim()!=&quot;&quot;">
            email=#{email} and
        </if>
        <if test="gender==0 or gender==1">
            gender=#{gender}
        </if>
    </trim>
</select>
```

## choose(when,otherwise)

> 我们希望实现，如果带了id，就是用id查询，如果带了lastName就使用lastName查询，不是像if判断那样都拼接起来，而是带了哪一个用哪一个查询，只会进入其中一个

```xml
<select id="getEmpsByConditionChoose" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    <where>
        <choose>
            <when test="id!=null">
                id = #{id}
            </when>
            <when test="lastName!=null">
                last_name = #{lastName}
            </when>
            <when test="email!=null">
                email = #{email}
            </when>
            <otherwise>
                gender = 0
            </otherwise>
        </choose>
    </where>
</select>
```

## set

> 你带了哪一列的值我就修改哪一列的值
>
> 以下这种方式在拼接时还是有`,`多出的风险

```xml
<update id="updateEmp">
    update employee
    set
    <if test="lastName != null">
        last_name = #{lastName},
    </if>
    <if test="email != null">
        email = #{email},
    </if>
    <if test="gender != null">
        gender = #{gender}
    </if>
    where id=#{id}
</update>
```

> 解决方式一（推荐），使用set标签包裹条件

```xml
<update id="updateEmp">
    update employee
    <set>
        <if test="lastName != null">
            last_name = #{lastName},
        </if>
        <if test="email != null">
            email = #{email},
        </if>
        <if test="gender != null">
            gender = #{gender}
        </if>
    </set>
    where id=#{id}
</update>
```

> 解决方式二：使用trim标签

```xml
<update id="updateEmp">
    update employee
    <!--前缀为set，去除后面可能多出的逗号-->
    <trim prefix="set" suffixOverrides=",">
        <if test="lastName != null">
            last_name = #{lastName},
        </if>
        <if test="email != null">
            email = #{email},
        </if>
        <if test="gender != null">
            gender = #{gender}
        </if>
    </trim>
    where id=#{id}
</update>
```

## foreach

> 可以用于遍历参数

```java
//查询员工id'在给定集合中的Employee信息
public List<Employee> getEmpsByConditionForeach(@Param("ids")List<Integer> ids);
```

```xml
<select id="getEmpsByConditionForeach" resultType="com.martha.mybatis.bean.Employee">
    select *
    from employee
    where id in
    <!--collection：指定当前要遍历的集合
                list类型的参数会特殊处理封装在Map中，Map的key就叫做list
               item：将当前遍历的元素赋值给指定的变量，通过#{}获取该变量的值
               separator：每个遍历出的元素之间使用的分隔符
               open：结果串拼接一个开始的字符
               close：结果串拼接一个结束的字符
            #{变量名}:就能取出变量的值也就是当前遍历出的元素
                index：遍历List集合的时候index就是索引，item就是当前值
                        遍历Map集合的时候index就是key，item就是value
        -->
    <foreach collection="ids" item="item_id" separator="," open="(" close=")">
        #{item_id}
    </foreach>
</select>
```

```java
Employee emp = new Employee(1,"martha",null,null);
List<Employee > employeeList = mapper.getEmpsByConditionForeach(Arrays.asList(1,2));
System.out.println(employeeList);
```

![image-20211114210053747](.\\MyBatisNotes_images\image-20211114210053747.png)

### foreach批量插入的两种方式

> foreach也经常用于批量保存

```java
public void addEmployees(@Param("emps") List<Employee> emps);
```

```xml
<insert id="addEmployees">
    insert into employee(last_name,email,gender)
    values
    <foreach collection="emps" item="item" separator=",">
        (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
    </foreach>
</insert>
```

```java
List<Employee> emps = new ArrayList<Employee>();
emps.add(new Employee(3,"abc","1","1316@qq.com",new Department(1,"")));
emps.add(new Employee(4,"aase","1","1365@qq.com",new Department(1,"")));
mapper.addEmployees(emps);
sqlSession.commit();
```

![image-20211114212952092](.\\MyBatisNotes_images\image-20211114212952092.png)

## 内置参数

> 不只是方法传入的参数可用于判断或者取值等等操作，Mybatis默认还有两个参数
>
> + _parameter：代表整个参数
>   + 如果是单个参数，那么\_parameter就是这个参数
>   + 如果是多个参数，那么参数会被封装为一个Map，_parameter就代表这个Map
> + _databaseId：如果配置了databaseIdProviderbia标签，那么\_databaseId就是代表当前数据库的别名

```xml
<!-- 两个内置参数：
   不只是方法传递过来的参数可以被用来判断，取值。。。
   mybatis默认还有两个内置参数：
   _parameter:代表整个参数
    单个参数：_parameter就是这个参数
    多个参数：参数会被封装为一个map；_parameter就是代表这个map

   _databaseId:如果配置了databaseIdProvider标签。
    _databaseId就是代表当前数据库的别名oracle
   -->

<!--public List<Employee> getEmpsTestInnerParameter(Employee employee);  -->
<select id="getEmpsTestInnerParameter" resultType="com.atguigu.mybatis.bean.Employee">
    <!-- bind：可以将OGNL表达式的值绑定到一个变量中，方便后来引用这个变量的值（但是还是不推荐使用，推荐模糊查询直接在传参数时就指定%%） -->
    <bind name="_lastName" value="'%'+lastName+'%'"/>
    <if test="_databaseId=='mysql'">
        select * from tbl_employee
        <if test="_parameter!=null">
            where last_name like #{lastName}
        </if>
    </if>
    <if test="_databaseId=='oracle'">
        select * from employees
        <if test="_parameter!=null">
            where last_name like #{_parameter.lastName}
        </if>
    </if>
</select>
```

## 抽取可重用的SQL片段

> 一个与增删改标签同级别的标签，sql标签，sql标签的作用就是用于抽取可重用的SQL片段，方便后续引用
>
> + 可将经常要查询的列名，或者插入时使用的列名抽取出来方便引用
> + 使用include标签来引用已经抽取的sql
> + include标签还可以自定义一些property，在sql标签内部就能使用其自定义的属性，取值的正确方式为`${}`，而不是`#{}`

```xml
<sql id="insertColumn">
    last_name,email,gender,dept_id
</sql>

<insert id="addEmployees">
    insert into employee(
   		 <!-- 引用外部定义好的sql-->
    	<include refid="insertColumn"></include>
    )
    values
    <foreach collection="emps" item="emp" separator=",">
        (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
    </foreach>
</insert>
```

# Mybatis缓存机制

> 缓存就是为了提高系统的运行速度，提升查询效率，一般我们需要经常查询的数据，且这些数据基本不变的情况下，我们就没必要再去给数据库发送SQL查询了，假设我们在开发一个用户量非常大的网站，这个菜单数据呢，对于每一个用户来说都一样，每一个用户访问都要去查询它，那么数据库服务器负担就会很严重，而我们如果将第一次查出的菜单数据，然后将其置入缓存中之后，我们接下来在要使用菜单数据，直接从缓存中取出，相比于从数据库中再去查询，再去封装结果要快得多，所以缓存机制也是我们持久化层框架非常重要的一个机制
>
> Mybatis中也有缓存机制，而且Mybatis默认为我们提供好了两级缓存，如下：
>
> + 一级缓存（又称为本地缓存）
> + 二级缓存（又称为全局缓存）

## 一级缓存

> 一级缓存的作用就是：与数据库同一次会话期间查询到的数据会放在本地缓存中，之后如果需要获取相同的数据，直接从本地缓存中获取，没必要再去查询数据库，代码示例如下：

```java
@Test
public void testCacheLevel_One(){
    SqlSessionFactory factory = getSqlSessionFactory();
    SqlSession session = factory.openSession();
    EmplpoyeeMapper mapper = session.getMapper(EmployeeMapper.class);
    Employee emp = mapper.getEmpById(1); // 第一次查询1号员工信息
    System.out.println(emp); // 输出1号员工信息
    Employee emp2 = mapper.getEmpById(1); // 第二次查询1号员工信息
    System.out.println(emp2); // 再次输出1号员工信息
    System.out.println(emp == emp2); // 比较两个对象是否相等
    session.close();
}
```

![image-20211115123501991](.\\MyBatisNotes_images\image-20211115123501991.png)

> 可以发现我们查询了两次1号员工信息，Employee对象信息输出了两次，但是SQL语句就只发送了一次，说明我们两次查询，第二次Mybatis并没有再次发送SQL去数据库中查询数据，而是因为它第二次要用的数据与第一次相同，Mybatis直接就从缓存中获取到了，并且我们还看到两个Employee对象居然还是相等的，说明第二次并没有创建新的Employee对象去封装结果，而是直接将第一次查询的数据拿出使用，这就是我们对一级缓存的体验

### 一级缓存失效的四种情况

> 一级缓存是一直开启的，一级缓存我们又称为SqlSession级别的缓存，既然是SqlSessiond的级别，也就是说一个SqlSession对象自己有一个一级缓存，新的SqlSession对象就是新的一级缓存了，两个一级缓存之间是不能共用的，所以说我们当前一级缓存中的数据能不能被使用到，这还是一个问题，我们就来讨论以下一级缓存的生效情况，如果我们没有使用到一级缓存的情况，效果就是Mybatis会重新发送SQL去数据库中查询数据，而不是去到缓存中获取，以下就是一级缓存失效的四种情况：
>
> + SqlSession对象不同
> + SqlSession对象相同，但是查询条件不同
> + SqlSession对象相同，但是两次查询之间进行了增删改操作
> + SqlSession对象相同，但我们手动清除了一级缓存
>
> 其实一级缓存就是我们SqlSession对象级别的的一个Map集合，我们将当前通过SqlSession对象查出的数据都会放入这个Map集合中，然后我下次查询如果还是当前会话，即还是同一个SqlSession对象，我先看当前Map集合中有没有，有了我就直接从Map中获取，没有我就发送SQL语句去数据库中查询，这样呢，就可以减轻我们数据库的负担

#### SqlSession对象不同

> 如果SqlSession对象不同，那么对应的一级缓存也不同，以下是代码示例：

```java
@Test
public void testCacheLevel_One(){
    SqlSessionFactory factory = getSqlSessionFactory();
    SqlSession session = factory.openSession();
    EmplpoyeeMapper mapper = session.getMapper(EmployeeMapper.class);
    Employee emp = mapper.getEmpById(1); // 第一次查询1号员工信息，使用的是session01
    System.out.println(emp); // 输出1号员工信息
    
    SqlSession session02 = factory.openSession(); // 新创建一个SqlSession对象
    EmplpoyeeMapper mapper = session.getMapper(EmployeeMapper.class);
    Employee emp2 = mapper.getEmpById(1); // 第二次查询1号员工信息，使用的是session02
    System.out.println(emp2); // 再次输出1号员工信息
    System.out.println(emp == emp2); // 比较两个对象是否相等
    session.close();
}
```

![image-20211115124543241](.\\MyBatisNotes_images\image-20211115124543241.png)

> 从日志和打印结果来看，Mybatis总共发送了两条SQL语句，并且封装的两个Employee对象也不相同，这就说明两个不同的SqlSession对象，意味着它们是两个不同的会话，第一次查出的Employee对象信息只会放在第一个SqlSession对象的一级缓存中，，第二次查出的Employee对象信息只会放在第二个SqlSession对象的一级缓存中，所以说我们第二次的查询并不会使用到第一个SqlSession对象的一级缓存，所以我们需要重新发送SQL去数据库中查询，所以也就查询了两次结果，封装为了两个不同的Employee对象，所以两个Employee对象的地址不相等

#### SqlSession对象相同，但是查询条件不同

```java
@Test
public void testCacheLevel_One(){
    SqlSessionFactory factory = getSqlSessionFactory();
    SqlSession session = factory.openSession();
    EmplpoyeeMapper mapper = session.getMapper(EmployeeMapper.class);
    // 使用同一个SqlSession对象进行查询操作
    Employee emp = mapper.getEmpById(1); // 第一次查询1号员工信息
    System.out.println(emp); // 输出1号员工信息
    Employee emp2 = mapper.getEmpById(3); // 第二次查询3号员工信息
    System.out.println(emp2); // 输出3号员工信息
    System.out.println(emp == emp2); // 比较两个对象是否相等
    session.close();
}
```

![image-20211115125313296](.\\MyBatisNotes_images\image-20211115125313296-16369519946951.png)

> 就算SqlSession对象相同，但是查询条件不同，很显然会发送两条SQL去到数据库中查询，**最主要的原因就是当前一级缓存中还没有这个数据**

#### SqlSession对象相同，但是两次查询之间进行了增删改操作

```java
@Test
public void testCacheLevel_One(){
    SqlSessionFactory factory = getSqlSessionFactory();
    SqlSession session = factory.openSession();
    EmplpoyeeMapper mapper = session.getMapper(EmployeeMapper.class);
    // 使用同一个SqlSession对象进行查询操作
    Employee emp = mapper.getEmpById(1); // 第一次查询1号员工信息
    System.out.println(emp); // 输出1号员工信息
    
    mapper.addEmp(new Employee(...)); // 我在两次查询之间进行了一次添加操作
    
    Employee emp2 = mapper.getEmpById(1); // 第二次还是查询1号员工信息
    System.out.println(emp2); // 输出1号员工信息
    System.out.println(emp == emp2); // 比较两个对象是否相等
    session.close();
}
```

![image-20211115125804552](.\\MyBatisNotes_images\image-20211115125804552.png)

> 这个情况我们也应该想得到，因为这个增删改操作可能就会对当前我们要查询的数据有影响，比如我将3号员工信息给删除了，我就不应该再从一级缓存中再取出该数据，因为数据库已经不存在了，所以这也意味着，如果两次查询之间存在增删改操作，那么Mybatis是不会从一级缓存中获取数据的，而是发送SQL去数据库中获取数据，这样比较真实

#### SqlSession对象相同，但我们手动清除了一级缓存

```java
@Test
public void testCacheLevel_One(){
    SqlSessionFactory factory = getSqlSessionFactory();
    SqlSession session = factory.openSession();
    EmplpoyeeMapper mapper = session.getMapper(EmployeeMapper.class);
    // 使用同一个SqlSession对象进行查询操作
    Employee emp = mapper.getEmpById(1); // 第一次查询1号员工信息
    System.out.println(emp); // 输出1号员工信息
    
    session.clearCache();// 我们在第二次查询相同数据之前，手动清除了一级缓存
    
    Employee emp2 = mapper.getEmpById(1); // 第二次还是查询1号员工信息
    System.out.println(emp2); // 输出1号员工信息
    System.out.println(emp == emp2); // 比较两个对象是否相等
    session.close();
}
```

![image-20211115130327622](.\\MyBatisNotes_images\image-20211115130327622.png)

> 这种情况当然不用想，还是需要重新发送SQL去查询，因为一级缓存没有数据了

## 二级缓存

> 我们上面了解了一级缓存，也称为SqlSession级别的缓存，也就是说每一次会话都对应着一个一级缓存，那这样的话**一级缓存的作用范围就是比较小**的，假设我要查询一个部门信息，先是使用第一个会话(SqlSession)进行查询，我查询除了第一个部门，那么我们就会将该部门信息，放入该会话所对应的一级缓存中，一旦该会话关闭掉，我第二个会话再次进行查询该部门信息，那么就必须重新发送SQL去到数据库中查询，而像我们的部门信息这样的数据一旦保存之后是很少修改的，我们希望的是，我们之前使用第一次会话查询过的部门信息，在第二次会话中依然可以不重新发送SQL而得到相应的数据呢？所以想要实现这样的效果，显然我们的一级缓存的作用范围就不适用了，接下来就需要使用到我们的二级缓存了
>
> 二级缓存是一个全局范围的一个缓存，是基于namespace级别的缓存，这个namespace就对应着我们的SQL映射文件中的namespace，每一个SQL映射文件都有一个自己的namespace，即一个名称空间对应一个二级缓存，我们可以理解为一个namespace对应着有它自己的一个Map集合，我们接下来就需要了解以下二级缓存的工作原理：
>
> 1. 我们通过一个会话，查询一条数据，那么这个数据就会被放入该会话对对应的一级缓存中
> 2. 如果该会话关闭，那么该会话的一级缓存中的数据会被保存到二级缓存中
> 3. 这样一来，即使你第一个会话关掉了，我们下一个会话查询数据还是可以参照二级缓存的内容了
> 4. 还需要说明的一点是：一个SqlSession对象可以getMapper()，它可以get到不同的mapper，即一个SqlSession对象可以实现对不同对象的增删改查操作，那么如果我们SqlSession对象既有通过EmployeeMapper查询的Employee信息，又有通过DepartmentMapper查询的Department信息，那么虽然二级缓存是全局的，但是这两个不同对象的信息会被放在不同的Map集合中，因为二级缓存是基于namespace的，EmployeeMapper对应一个namespace，DepartmentMapper对应一个namespace，所以Employee信息都会被放在EmployeeMapper对应的namespace对应的二级缓存中，Department信息都会被放在DepartmentMapper对应的namespace对应的二级缓存中，所以说不同namespace查出的数据会被放在自己对应的二级缓存（Map集合）中
>
> 下面就是二级缓存的使用步骤：

1. 在全局配置文件中开启二级缓存的设置，默认也是true，但是我们还是要遵循那个原则，防止版本更替造成的默认值改变，我们还是显式声明较好

   ```xml
   <settings>
   	<setting name="cacheEnabled" value="true"></setting>
   </settings>
   ```

2. 因为它是二级缓存，是namespace级别的，所以我们应该去每一个SQL映射文件中配置使用二级缓存

   ```xml
   <mapper namespace="xxx.xxxMapper">
       <!--通过声明cache标签就可以完成二级缓存的配置-->
   	<cache></cache>
   </mapper>
   ```

   > cache标签有一些属性，这些属性代表该二级缓存的一些默认的策略，如下：
   >
   > + eviction：表示缓存的回收策略
   >   + LRU（默认值）：回收最长时间不被使用到的数据信息
   >   + FIFO：先进先出，按对象信息进入数据的先后顺序进行移除
   >   + SOFT：软引用：移除基于垃圾回收器状态和软引用规则的对象
   >   + WEAK：弱引用：更积极地移除基于垃圾回收器状态和弱引用规则的对象
   > + flushInterval：缓存刷新间隔，即该二级缓存多长时间清空一次（默认是不清空），当然你可以手动设置一个毫秒值，来指定多长时间后进行清空缓存
   > + readOnly：缓存是否只读，
   >   + true：只读：Mybatis认为所有从缓存中获取数据的操作都是只读操作，不会修改数据，那么Mybatis就会为了加快获取速度，直接就会将数据在缓存中的引用交给用户，不安全但是速度快
   >   + false（默认）：非只读，Mybatis认为获取到的数据可能会被修改，这样Mybatis就不会直接交出引用，Mybatis就会使用序列化和反序列化的机制给你克隆一份新的数据给你，安全但是速度稍慢
   > + size：表示缓存中能存放多少个元素
   > + type：指定自定义缓存的全类名，但这里我们使用的Mybatis默认的二级缓存，当然也可以自定义缓存类，如果想要自定义缓存类，Mybatis也提供了Cache接口，只需要实现Mybatis提供的Cache接口，就可以通过type指定自定义的缓存类进行使用了

3. 二级缓存默认是非只读的，所以为了安全，Mybatis会使用序列化和反序列化技术来保存对象信息，所以我们的POJO对象需要实现序列化接口

   ```java
   public class Employee implements Serializable{
       private static final long serialVersionID=1L;
   }
   ```

   > POJO类实现了序列化接口后，才能进入二级缓存中保存，因为二级缓存默认就是通过序列化和反序列化来实现数据信息的保存的

4. 进行代码测试二级缓存的效果：

   ```java
   @Test
   public void testCacheLevel_two() {
       SqlSession sqlSession = null;
       SqlSession sqlSession2 = null;
       try {
           SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
           sqlSession = sqlSessionFactory.openSession();
           sqlSession2 = sqlSessionFactory.openSession();
           EmployeeMapper mapper1 = sqlSession.getMapper(EmployeeMapper.class);
           EmployeeMapper mapper2 = sqlSession2.getMapper(EmployeeMapper.class);
           List<Employee> emps = mapper1.getEmpsByConditionIF(new Employee());
           sqlSession.close(); // 只有关闭了会话，才会将数据从一级缓存中转移到二级缓存中
           List<Employee> emps2 = mapper2.getEmpsByConditionIF(new Employee());
           System.out.println(emps == emps2);
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           sqlSession2.close();
       }
   }
   ```

   ![image-20211115194632573](.\\MyBatisNotes_images\image-20211115194632573.png)

   > 可以看到两次不同的会话，还是只发送的一条SQL语句，第二个就是二级缓存命中率，意思就是在二级缓存中找到了相应数据
   >
   > 想要在二级缓存中获取到数据，最重要的就是要关闭会话，才能将数据转存至二级缓存中

### 与二级缓存有关的设置和属性

+ 全局配置文件设置中的 cacheEnabled 属性，默认就是true，开启缓存，如果我们设置为false就是关闭缓存，那么它关闭的是一级缓存还是二级缓存呢

  + 首先，二级缓存一定会被关闭
  + 但一级缓存不会被关闭

+ 在SQL映射文件中，每一个select标签，都有一个useCache属性，它的默认值为true，即默认使用缓存，那么我们将这个属性设置为false后，它关闭的是一级缓存还是二级缓存呢

  + 首先，二级缓存一定会被关闭
  + 但一级缓存不会被关闭

+ 在我们测试一级缓存的时候，我们发现，只要执行一次增删改操作，同一次会话的第二次相同结果的查询还是会发送SQL去到数据库中进行查询，究其原因呢是因为，SQL映射文件中的每一个增删改标签中，都有一个属性，叫做flushCache，默认值为true，就是清空缓存，意味着只要执行了增删改操作后，就会清空缓存

  + 首先，我们知道一级缓存肯定会被清空
  + 而且，二级缓存也会被清空

  > 其实查询标签也有flushCache属性，但是查询标签的该属性默认值为false，如果设置为true，也会清除一级和二级的缓存，所以如果设置为true，那么每次查询之后都会清空缓存

+ 我们之前测试过SqlSession对象的clearCache()方法，我们执行了该方法后

  + 一级缓存会被清空
  + 但是二级缓存不会被清空，可以这么理解，clearCache()方法是由当前SqlSession对象调用的，所以只会清空当前对话的一级缓存，而不会影响到二级缓存

> 所以我们只要注意一下，增删改查的属性一旦设置为true，都会清空一级和二级的缓存

+ 还有一个就是Mybatis3.2版本后推出的一个全局配置文件中的设置，localCacheScope属性，叫做本地缓存作用域，有两种取值：

  + SESSION（默认值）：即默认一级缓存是生效的，所有的数据默认都会保存在会话的一级缓存中
  + STATEMENT：可以禁用一级缓存
  
  ```xml
  <settings>
  	<setting name="localCacheScope" value="SESSION"></setting>
  </settings>
  ```
  
  > 但是呢这个属性，我们一般也没有去配置它，了解即可，只需要着重了解，每一次增删改之后，一级和二级缓存都会被清空

## 缓存原理图示

![image-20211115201945655](.\\MyBatisNotes_images\image-20211115201945655.png)

> 我们需要记住的就是，每一个会话访问数据信息的顺序就是
>
> 1. 先从二级缓存中查找
> 2. 二级缓存没有就从一级缓存中查找
> 3. 一级缓存没有，那么就发送SQL从数据库中查询

## 第三方缓存整合原理&ehcache适配包下载

![image-20211115202600168](.\\MyBatisNotes_images\image-20211115202600168.png)

> 我们可以看到，Mybatis随便一个Cache接口的实现类中实现的缓存结构都比较简陋，其实就是使用一个Map集合来保存数据信息，缓存对于Mybatis来说就是一个小Map，毕竟Mybatis只是一个做数据库交互的框架，它对缓存的实现不太专业，但虽然它不专业，但是它也知道，我提供一个Cache接口，将这个专业的缓存实现留给别人来实现，比如我们将缓存的数据现在流行于存放在Redis中，或者其它缓存中，这样一来呢，我们就能提供方便的扩展，因为我们的Mybatis提供了Cache接口了，只需要我们重写![image-20211115202945669](.\\MyBatisNotes_images\image-20211115202945669.png)
>
> 比如这两个方法，putObject的时候将数据保存在Redis中，getObject的时候我们从Redis中获取，这样的话我们就能实现自定义缓存，基于这样我们就来整合一个成熟的第三方框架`Ehcache`，`Ehcache`也是我们Hibernate中缓存的提供者，Hibernate也是使用它来实现的缓存机制的，Ehcache是一个非常专业的缓存，一个Java进程内的缓存框架，快速而且查询非常精干，使用起来也非常方便，我们就以它整合为例，那么怎么整合呢？
>
> 既然我们Mybatis提供了Cache接口，我们只需要自己来实现put给get的方法，把数据保存进Ehcache中，让Ehcache帮我们保存数据和查询数据，而至于什么时候保存这些缓存数据，这是Mybatis规定的，比如我查询出数据，在会话关闭的时候，我将数据保存进缓存中；这个时候，我们重写Cache接口，让数据保存进Redis或者Ehcache中，整合Ehcache步骤如下：

1. 引入`Ehcahce-core`Ehcache缓存核心依赖，与其依赖的日志

   ```xml
   <!-- https://mvnrepository.com/artifact/net.sf.ehcache/ehcache-core -->
   <dependency>
       <groupId>net.sf.ehcache</groupId>
       <artifactId>ehcache-core</artifactId>
       <version>2.6.8</version>
   </dependency>
   <!--依赖于这个日志依赖，因为不会自动导入，所以手动导入-->
   <dependency>
       <groupId>org.slf4j</groupId>
       <artifactId>slf4j-log4j12</artifactId>
       <version>1.6.2</version>
       <scope>test</scope>
   </dependency>
   ```

2. 接下来我们只需要自定义一个Cache接口的实现即可，然后保存数据的时候使用Ehcache来做，但这样的话我们还得写实现，挺麻烦的，Mybatis已经帮我们写好了这个实现，所以我们需要去到Mybatis的github地址中https://github.com/orgs/mybatis/repositories，我们需要哪一个就点进去哪一个，比如我们现在需要整合的是ehcache，那么我们就点击ehcache的项目整合，点进去就可以下载到Mybatis与ehcache整合相关的jar包，或者**使用Maven依赖的方式引入**![image-20211115204417109](.\\MyBatisNotes_images\image-20211115204417109.png)

   ![image-20211115210116080](.\\MyBatisNotes_images\image-20211115210116080.png)

   ![image-20211115210203137](.\\MyBatisNotes_images\image-20211115210203137.png)

3. 那我们就将Mybatis整合Ehcache的适配器依赖引入，即引入Mybatis对Ehcahce缓存的实现，不需要我们自己去实现Cache接口，然后重写方法，引入了下面这个依赖就ok了

   ```xml
   <!--Mybatis与Ehcache的整合适配器，即Mybatis帮我们完成了对Ehcache的实现-->
   <dependency>
       <groupId>org.mybatis.caches</groupId>
       <artifactId>mybatis-ehcache</artifactId>
       <version>1.2.1</version>
   </dependency>
   ```

4. 引入了依赖之后，我们只需要在SQL映射文件中的cache标签中，指定type属性为自定义缓存的全类名即可，而这个自定义缓存就是Mybatis帮我们整合写好了的Ehcache适配器的全类名

   ```xml
   <mapper namespace="xxx.xxxMapper">
       <!--通过声明cache标签的type属性就可以自定义缓存的实现-->
   	<cache type="org.mybatis.caches.ehcache.EhcacheCache"></cache>
   </mapper>
   ```

5. 指定完全类名之后，我们还需要在类路径下指定一个ehcache.xml文件，才能正常运行该适配器，文件内容如下：![image-20211115211706469](.\\MyBatisNotes_images\image-20211115211706469.png)

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:noNamespaceSchemaLocation="ehcache.xsd">
       <!-- 磁盘保存路径：一旦缓存中的数据超量后我们要保存在磁盘上的哪个路径中 -->
       <diskStore path="E:\Environment\Ehcache\storage" />
   
       <!--默认的缓存配置-->
       <defaultCache
                     maxElementsInMemory="10000"
                     maxElementsOnDisk="10000000"
                     eternal="false" 
                     overflowToDisk="true" 
                     timeToIdleSeconds="120"
                     timeToLiveSeconds="120" 
                     diskExpiryThreadIntervalSeconds="120"
                     memoryStoreEvictionPolicy="LRU">
       </defaultCache>
   </ehcache>
   
   <!-- 
   属性说明：
    diskStore：指定数据在磁盘中的存储位置。
    defaultCache：当借助CacheManager.add("demoCache")创建Cache时，EhCache便会采用<defalutCache/>指定的的管理策略
   
   以下属性是必须指定的：
    maxElementsInMemory - 指的是在内存中缓存的element元素的最大数目
    maxElementsOnDisk - 在磁盘上缓存的element的最大数目，若是0表示无穷大
    eternal - 设定缓存的elements是否永远不过期。如果为true，则缓存的数据始终有效，如果为false那么还要根据timeToIdleSeconds，timeToLiveSeconds判断
    overflowToDisk - 设定当内存缓存溢出的时候是否将过期的element元素保存到磁盘上
   
   以下属性是可选的：
    timeToIdleSeconds - 当缓存在EhCache中的数据前后两次访问的时间超过timeToIdleSeconds的属性取值时，这些数据便会删除，默认值是0,也就是可闲置时间无穷大
    timeToLiveSeconds - 缓存element的有效生命期，默认是0.,也就是element存活时间无穷大
    diskSpoolBufferSizeMB 这个参数设置DiskStore(磁盘缓存)的缓存区大小.默认是30MB.每个Cache都应该有自己的一个缓冲区.
    diskPersistent - 在VM重启的时候是否启用磁盘保存EhCache中的数据，默认是false。
    diskExpiryThreadIntervalSeconds - 磁盘缓存的清理线程运行间隔，默认是120秒。每个120s，相应的线程会进行一次EhCache中数据的清理工作
    memoryStoreEvictionPolicy - 当内存缓存达到最大，有新的element加入的时候， 移除缓存中element的策略。默认是LRU（最近最少使用），可选的有LFU（最不常使用）和FIFO（先进先出）
    -->
   ```

   > 当然ehcache.xml文件还有一个xsi:noNamespaceSchemaLocation="ehcache.xsd"，xsd文件在网盘中可以获取，默认放在了resources目录下![image-20211115213127407](.\\MyBatisNotes_images\image-20211115213127407.png)

```java
@Test
public void testCacheLevel_two() {
    SqlSession sqlSession = null;
    SqlSession sqlSession2 = null;
    try {
        SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
        sqlSession = sqlSessionFactory.openSession();
        sqlSession2 = sqlSessionFactory.openSession();
        EmployeeMapper mapper1 = sqlSession.getMapper(EmployeeMapper.class);
        EmployeeMapper mapper2 = sqlSession2.getMapper(EmployeeMapper.class);
        List<Employee> emps = mapper1.getEmpsByConditionIF(new Employee());
        sqlSession.close(); // 只有关闭了会话，才会将数据从一级缓存中转移到二级缓存中
        List<Employee> emps2 = mapper2.getEmpsByConditionIF(new Employee());
        System.out.println(emps == emps2);
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        sqlSession2.close();
    }
}
```

![image-20211115232409679](.\\MyBatisNotes_images\image-20211115232409679.png)![image-20211115232521849](.\\MyBatisNotes_images\image-20211115232521849.png)

> 这样就可以肯定Ehcache是在使用了
>
> 当前我们在EmployeeMapperSQL映射文件中，引入了自定义缓存，但是我又想在DepartmentMapperSQL映射文件中使用的话，每次都要导入`<cache type="org.mybatis.caches.ehcache.EhcacheCache"></cache>`就会很麻烦，这时候我们可以在DepartmentMapper映射文件中引用EmployeeMapper的缓存

```xml
<!--在DepartmentMapper文件下指定和哪个名称空间下的缓存一样，引用过来即可-->
<mapper>
    <cache-ref namespace="com.martha.mybatis.dao.EmployeeMapper"/>
</mapper>
```

> 第三方缓存整合的步骤：
>
> 1. 导入第三方缓存依赖/jar包
> 2. 导入Mybatis与第三方缓存整合的适配包，即缓存实现
> 3. 在mapper.xmlSQL映射文件中使用自定义缓存

![image-20211115233648745](.\\MyBatisNotes_images\image-20211115233648745.png)

# Mybatis整合Spring

> Spring是一个非常优秀的IOC框架，Mybatis与Spring整合的主要目的就是希望使用Spring容器来管理所有的组件，利用SpringIOC容器来解决组件之类的动态依赖注入，而且我们还希望使用Spring来控制事务，包括我们要用Spring来做非常多的功能
>
> 最终我们想要的做法就是，我们使用Mybatis写好了这些mapper接口，我们Spring的Service层，如果我们要调用Dao/Mapper层进行增删改查，我们最终的想法就是在Service层，只需要一个非常简单的@Autowired注解自动装配我们Dao/Mapper层的组件，这样的话我们之后使用起来就非常方便了，所以我们接下来需要来整合Spring，整合步骤与整合Ehcache步骤相似：

1. https://github.com/orgs/mybatis/repositories进去找到Spring，点击进去![image-20211115234539180](.\\MyBatisNotes_images\image-20211115234539180.png)![image-20211116000148628](.\\MyBatisNotes_images\image-20211116000148628.png)

2. 添加适配包依赖

   ```xml
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis-spring</artifactId>
       <version>2.0.0</version>
   </dependency>
   ```

# Mybatis逆向工程

> 有逆向就有正向，我们正向操作就是根据一个数据表创建出对应的JavaBean类，创建了JavaBean类，我们还得创建出对应的Mapper接口来操作这个数据表，Mapper接口有了，我们还要创建对应的SQL映射文件，什么都需要我们自己来创建，这就是我们正向的操作流程
>
> 逆向的操作流程就是Mybatis会根据这张数据表，逆向分析出这张数据表，根据这张数据表动态创建出对应的JavaBean类、Mapper接口、SQL映射文件，所以这就是所谓的代码生成器，帮助我们自动生成这些所需文件，这个逆向工程也是Mybatis支持的一个非常好的工程，我们可以去到Mybatis的官方地址，来获取这个逆向工程适配器

1. https://github.com/orgs/mybatis/repositories点击进去![image-20211116123728017](.\\MyBatisNotes_images\image-20211116123728017.png)

2. 导入相关MBG代码生成器依赖

   ```xml
   <!--Mybatis逆向工程MBG核心代码生成器-->
   <dependency>
       <groupId>org.mybatis.generator</groupId>
       <artifactId>mybatis-generator-core</artifactId>
       <version>1.4.0</version>
   </dependency>
   ```

3. 在resources目录下，创建一个generatorConfig.xml配置文件，内容如下：这个配置文件就是告诉我们的MBG逆向工程如何去生成这些文件，并且将这些文件放在哪里，将文件中重复的内容提取出来，配置为一个properties配置文件，然后引用这个配置文件`generator.properties`

   ```properties
   # MySQL info
   jdbc.driver=com.mysql.jdbc.Driver
   jdbc.url=jdbc:mysql://127.0.0.1:3306/csgo?characterEncoding=UTF-8&allowMultiQueries=true
   jdbc.username=root
   jdbc.password=123456
   # MBG genertor info
   generator.driverLocation=D:/Martha_DevelopEnvironment/MavenRepository/mysql/mysql-connector-java/8.0.30/mysql-connector-java-8.0.30.jar
   generator.targetBeanPackage=com.martha.csgo.yyyp.lease.bean
   generator.targetMapperPackage=com.martha.csgo.yyyp.lease.dao
   generator.targetXmlLocation=/resources/mapper
   generator.targetProject=./src/main/java
   generator.schema=csgo
   ```

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE generatorConfiguration
           PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
           "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
   <generatorConfiguration>
       <!--引入外部属性文件-->
       <properties resource="generator.properties"></properties>
       <!--指定数据库驱动JDBC驱动jar包的位置（逆向工程在运行时需要连接数据库，所以需要数据库驱动jar包）-->
       <classPathEntry
               location="${generator.driverLocation}"/>
   
       <!--
     targetRuntime="MyBatis3Simple":生成简单版的CRUD操作
     MyBatis3:豪华版
     一个数据库对应一个context
     -->
       <context id="default" targetRuntime="MyBatis3Simple">
           <!-- 生成的pojo，将implements Serializable接口，为了符合Mybatis中二级缓存使用的前提条件 -->
           <plugin type="org.mybatis.generator.plugins.SerializablePlugin"></plugin>
           <!--生成mapper.xml时覆盖原来生成过的Mapper.xml文件，而不是继续追加内容-->
           <plugin type="org.mybatis.generator.plugins.UnmergeableXmlMappersPlugin"/>
           <!--设置自动生成的文件是否自动添加注释-->
           <commentGenerator>
               <!--自动生成的文件是否取消自动添加注释：取消自动注释！-->
               <property name="suppressAllComments" value="true"></property>
               <!--是否生成注释代时间戳-->
               <!--<property name="suppressDate" value="true"></property>-->
           </commentGenerator>
           <!-- jdbcConnection：指定如何连接到目标数据库 -->
           <jdbcConnection driverClass="${jdbc.driver}"
                           connectionURL="${jdbc.url}"
                           userId="${jdbc.username}"
                           password="${jdbc.password}">
           </jdbcConnection>
   
           <!-- Java类型解析器，这边就让他使用默认的类型解析方式
               默认false，就是将JDBC DECIMAL 和 NUMERIC 类型解析为 Integer true，把JDBC DECIMAL
                   和 NUMERIC 类型解析为java.math.BigDecimal
           -->
           <javaTypeResolver>
               <property name="forceBigDecimals" value="false"/>
           </javaTypeResolver>
   
           <!-- javaModelGenerator：指定JavaBean的生成策略
    targetPackage="test.model"：生成JavaBean所在的目标包名
    targetProject="\MBGTestProject\src"：生成JavaBean所在的目标工程，我们指定为当前工程，即src\main\java\即可
    -->
           <javaModelGenerator targetPackage="${generator.targetBeanPackage}"
                               targetProject="${generator.targetProject}">
               <!--是否在当前路径下新加一层schema,eg：false路径com.oop.eksp.user.model， true:com.oop.eksp.user.model.[schemaName]-->
               <property name="enableSubPackages" value="false"/>
               <!-- 从数据库返回的值是否清理前后的空格 -->
               <property name="trimStrings" value="true"/>
           </javaModelGenerator>
   
           <!-- sqlMapGenerator：sql映射文件的生成策略：对应的mapper.xml文件 -->
           <sqlMapGenerator targetPackage="${generator.targetXmlLocation}"
                            targetProject="./src/main">
               <property name="enableSubPackages" value="false"/>
           </sqlMapGenerator>
   
           <!-- javaClientGenerator:指定mapper接口所在的位置: 对应的Mapper接口类文件 -->
           <javaClientGenerator type="XMLMAPPER" targetPackage="${generator.targetMapperPackage}"
                                targetProject="${generator.targetProject}">
               <property name="enableSubPackages" value="false"/>
           </javaClientGenerator>
   
           <!-- 指定要逆向分析哪些表：根据表要创建javaBean及其mapper接口与映射文件
      domainObjectName：指定生成的JavaBean的类名
      schema：指定数据库
   -->
           <table tableName="lease" domainObjectName="Lease" schema="csgo">
               <!-- 用于指定生成实体类时是否使用实际的列名作为实体类的属性名。false是 Camel Case驼峰命名法风格，如表中last_name字段对应的就是Java类的lastName属性-->
               <property name="useActualColumnNames" value="false"></property>
           </table>
   
       </context>
   </generatorConfiguration>
   ```

4. 配置完配置文件之后，就是运行逆向工程的时候了，由于我们使用的是Maven的方式，那么我们就使用Maven插件的方式来运行逆向工程，插件配置如下：

   ```xml
   <!--以下是所需插件，之前我们是使用Java代码运行逆向工程的-->
   <build>
       <plugins>
           <plugin>
               <groupId>org.mybatis.generator</groupId>
               <artifactId>mybatis-generator-maven-plugin</artifactId>
               <version>1.3.7</version>
               <configuration>
                  <!--指定配置文件的位置--><configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
                   <!--运行的时候不显示详细信息-->
                   <verbose>true</verbose>
                   <!--生成的文件如果存在则进行覆盖，不然会一直创建-->
                   <overwrite>true</overwrite>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

5. 都配置好后，点击Maven插件自动生成文件即可![image-20211116194749943](.\\MyBatisNotes_images\image-20211116194749943.png)![image-20211116194812374](.\\MyBatisNotes_images\image-20211116194812374.png)

   > xxExample类是用来拼装复杂条件的

6. 代码测试

   ```java
   @Test
   public void testMybatis3() {
       SqlSession session = null;
       try {
           SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
           session = sqlSessionFactory.openSession();
           EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
           // xxxExample就是封装查询条件的，那我啥条件都不设置，默认就是查询所有
           List<Employee> employees = mapper.selectByExample(null);
           employees.forEach(System.out::println);
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           session.close();
       }
   }
   ```

   ![image-20211116203508526](.\\MyBatisNotes_images\image-20211116203508526.png)

   ```java
   @Test
   public void testMybatis3() {
       SqlSession session = null;
       try {
           SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
           session = sqlSessionFactory.openSession();
           EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
           // 查询员工姓名包含r字母的，且性别为男生的员工信息
           EmployeeExample example = new EmployeeExample(); // 首先要创建条件对象
           EmployeeExample.Criteria criteria = example.createCriteria();// 通过条件对象创建条件拼装对象Criteria
           // 使用criteria拼装条件
           criteria.andLastNameLike("%r%").andGenderEqualTo("1");
           // 将条件置入进行查询
           List<Employee> employees = mapper.selectByExample(example);
           employees.forEach(System.out::println);
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           session.close();
       }
   }
   ```

   ![image-20211116204307168](.\\MyBatisNotes_images\image-20211116204307168.png)

   ```java
   @Test
   public void testMybatis3() {
       SqlSession session = null;
       try {
           SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
           session = sqlSessionFactory.openSession();
           EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
           // 查询(员工姓名包含r字母的，且性别为男生的员工信息)或者email包含19161，可见这里需要两个条件，符合第一个条件，符合第二个条件都行
           EmployeeExample example = new EmployeeExample(); // 首先要创建条件对象
           EmployeeExample.Criteria criteria_first = example.createCriteria();// 通过条件对象创建第一个条件
           EmployeeExample.Criteria criteria_second = example.createCriteria();// 通过条件对象创建第二个条件
           // 使用criteria_first拼装第一个条件
           criteria_first.andLastNameLike("%r%").andGenderEqualTo("1");
           // 使用criteria_second拼装第二个条件
           criteria_second.andEmailLike("%19161%");
           // 因为这两个条件满足其一即可，所以使用或的关系
           example.or(criteria_second);
           // 将条件置入进行查询
           List<Employee> employees = mapper.selectByExample(example);
           employees.forEach(System.out::println);
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           session.close();
       }
   }
   ```

   ![image-20211116204858443](.\\MyBatisNotes_images\image-20211116204858443.png)

   > 可见Matha满足条件一也满足条件二
   >
   > Beth不满足条件一，但是满足条件二，一样被查询了出来
   >
   > 如果还有或的条件，同理，即创建新的Criteria，拼装完了条件，example.or就行了

# Mybatis底层运行原理

> 了解Mybatis的运行原理，对于插件开发非常有帮助，但即使不开发插件也很有必要了解以下Mybatis的底层原理，我们从最初的HelloMybatis工程开始探索原理

```java
public SqlSessionFactory getSqlSessionFactory() throws Exception {
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    return new SqlSessionFactoryBuilder().build(inputStream);
}
@Test
public void testMapper() {
    SqlSession sqlSession = null;
    try {
        // 1. 获取SqlSessionFactory对象
        SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
        // 2. 获取SqlSession对象
        sqlSession = sqlSessionFactory.openSession();
        // 3. 获取接口的代理对象（MapperProxy）
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        // 4. 通过代理对象执行增删改查方法
        Employee employee = mapper.selectByPrimaryKey(1);
        System.out.println(employee);
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        sqlSession.close();
    }
```

1. 获取SqlSessionFactory对象，解析Mybatis全局配置文件与SQL映射文件的每一个信息，并保存在Configuration对象中，返回包含Configuration的DefaultSqlSession，**注意：MappedStatement代表一个增删改查的详细信息**![image-20211116211646460](.\\MyBatisNotes_images\image-20211116211646460.png)
2. 我们拿到SqlSessionFactory之后，我们需要调用它的openSession方法获取到一个SqlSession对象，其实是返回了一个DefaultSqlSession对象，这个对象中包含Executor和Configuration，这一步主要就是创建Executor对象![image-20211116213412573](.\\MyBatisNotes_images\image-20211116213412573.png)
3. SqlSession通过getMapper获取到接口的代理对象，当我们调用getMapper方法的时候，Mybatis会使用MapperProxyFactory创建一个MapperProxy的代理对象，代理对象中包含了DefaultSqlSession，DefaultSqlSession又包含了执行增删改查的Executor，所以一层一层的包含，我们代理对象最终能执行增删改查最终肯定是会使用到SqlSession中的Executor![image-20211117120757955](.\\MyBatisNotes_images\image-20211117120757955.png)
4. 查询流程如下![image-20211117122820014](.\\MyBatisNotes_images\image-20211117122820014.png)

## 查询流程总结

> 我们知道在第三步会使用代理对象来执行增删改查，而这个代理对象实际执行增删改查使用的是其内部的DefaultSqlSession，而DefaultSqlSession执行增删改查其实使用的是其内部的Executor，Executor会创建出StatementHandler对象，StatementHandler就是用于处理SQL语句预编译，设置SQL参数等相关工作，Statement创建出来的时候，还创建了两个对象，ParameterHandler和ResultSetHandler，ParameterHandler是StatementHandler借助于设置SQL参数及预编译工作的，并且执行查询操作，ResultSetHandler使用来处理查询后的结果集的，ParameterHandler和ResultSetHandler最终都是使用TypeHandler来完成数据库中的数据和JavaBean数据的映射，而他们所有的底层操作都是原生JDBC

![image-20211117145031257](.\\MyBatisNotes_images\image-20211117145031257.png)

## Mybatis原理的最终总结

1. 根据配置文件（包括全局配置文件或者SQL映射文件）初始化出Configuration对象
2. 创建一个DefaultSqlSession对象，它里面包含Configuration以及Executor（Executor会根据全局配置文件中settings的设置项defaultExecutorType创建一个Executor，使用Executor来执行增删改查）
3. 从DefaultSqlSession中getMapper，拿到Mapper接口对应的MapperPorxy，MapperPorxy中包含DefaultSqlSession，DefaultSqlSession中又包含了Executor
4. 接下来通过代理对象执行增删改查方法
   1. 通过调用DefaultSqlSession对象的Executor来进行增删改查
   2. 而Executor会创建一个Statement对象，创建Statement对象的同时也会创建StatementHandler，创建StatementHandler对象的同时也会创建出ParameterHandler（用于设置SQL参数）和ResultSetHandler（用于封装结果）
   3. 调用StatementHandler的预编译参数以及设置参数值，具体是使用ParameterHandler来给SQL设置参数
   4. 设置好参数后，再来调用StatementHandler的增删改查方法
   5. 整个增删改查操作结束后，最后使用ResultSetHandler封装结果

> 注意：
>
> + 四大对象Executor、StatementHandler、ParameterHandler、ResultSetHandler在创建的时候都有一个interceptorChain.pluginAll(parameterHandler)，这也正是插件机制的起源
> + 最后我们应该知道每一个对象都是干什么的

# Mybatis插件

> 在四大对象创建的时候，都会执行interceptorChain.pluginAll(parameterHandler)方法，所以每一个创建出来的对象不是直接返回的，而是调用pluginAll方法后返回，pluginAll它的原理就是拿到所有的Interceptor（拦截器），这个Interceptor就是我们插件需要实现的接口，会在pluginAll方法中执行interceptor.plugin(target)方法后，返回target包装后的对象
>
> 插件机制：我们可以使用插件为我们的目标对象创建一个代理对象，这样的话，因为我们四大对象在创建的时候都会被插件拦截，那么我们假设我们插件拦截到parameterHandler，我为parameterHandler创建一个代理对象，那么之后parameterHandler执行它方法的时候，都会先执行我们这个代理对象的方法，那我们就在代理对象中，即parameterHandler执行方法之前我们都可以做一些事情，这也跟我们Spring的AOP面向切面思想一样
>
> 我们的插件可以为四大对象创建出代理对象，之后代理对象就可以拦截到四大对象的每一个执行方法，接下来就通过代码来了解插件：

## 插件编写步骤

1. 首先要定义一个实现了Interceptor接口的实现类

   ```java
   // 这个Interceptor是org.apache.ibatis.plugin.Interceptor包下的
   public class MyPlugin implements Interceptor{
       // intercept方法就是用于拦截目标对象的目标方法的执行的
       @Override
       public Object intercept(Invocation invocation) throws Throwable {
           // 执行目标方法，相当于放行，我们只有调用了proceed，四大对象的方法才会真正开始执行，要是不调用proceed方法，四大对象的方法就不会被执行，我么就可以在目标方法执行之前做一些更改，比如我拿到当前方法，我将参数改一改，改完之后再让目标方法执行
           Object result = invocation.proceed();
           // 返回执行目标方法后的返回值
           return result;
       }
   
       // plugin方法就是用于包装我们的目标对象的，而我们所谓的包装其实就是为目标对象创建一个代理对象
       @Override
       public Object plugin(Object target) {
           // 我们可以借助Mybatis提供的Plugin类的包装方法使用我们的this（我们当前的拦截器对象）来包装target目标对象
           Object wrap = Plugin.wrap(target,this);
           // 返回的就是包装后的对象，也就是为target目标对象创建的动态代理
           return wrap;
       }
   
       // setProperties 将插件注册时的property属性设置进来
       @Override
       public void setProperties(Properties properties) {
           System.out.println("插件的配信息" + properties);
           Interceptor.super.setProperties(properties);
       }
   }
   ```

   > 自定义好插件类之后，我们就要来具体地告诉Mybatis，我这个插件到底使用来拦截哪个对象的哪个方法的，我们下面来使用@Intercepts注解来告诉Mybatis如何进行拦截

2. 使用@Intercepts完成插件签名

   ```java
   // 使用@Intercepts完成插件签名，告诉Mybatis当前这个MyPlugin插件用于拦截哪个对象的哪个方法，使用一个签名@Signature代表一个拦截对象，type指定某个四大对象的类型，method指定该对象中的方法，args指定方法中的参数类型全类名
   @Intercepts({ @Signature(type=StatementHandler.class,method="parameterize",args=java.sql.Statement)
   })
   public class MyPlugin implements Interceptor{..}
   ```

3. 将写好的插件配置到全局配置文件中

   ```xml
   <configuration>
   	<plugins>
       	<plugin interceptor="com.martha.mybatis.plugin.MyPlugin">
               <!--配置时还可以为这个插件指定一些属性，方便插件运行时可以使用这些属性，如下示例：-->
           	<property name="username" value="root"></property>
               <property name="password" value="123456"></property>
           </plugin>
       </plugins>
   </configuration>
   ```

## 多个插件运行流程

> 两个或多个插件同时拦截一个目标对象的目标方法时，多层插件就会产生多层代理![image-20211117153948659](.\\MyBatisNotes_images\image-20211117153948659.png)
>
> 所以创建动态代理的时候，是按照全局配置文件的插件配置顺序创建层层代理对象的，而执行目标方法的时候，按照逆序执行的，因为我们最大的代理对象是我们最后一个插件包装后的结果，所以从最大的那一层开始层层调用，直到执行到目标方法
>
> **包装是第一个插件先包，执行目标方法是从最后一个插件开始执行，层层往里递进，所以开起来就是逆序执行的**![image-20211117154235867](.\\MyBatisNotes_images\image-20211117154235867.png)

## PageHelper分页插件的使用

> PageHelper是第三方的一个分页插件，我们Mybatis要做分页功能呢，使用它来做就会非常强大和简单，使用步骤如下：

1. 引入分页插件Maven依赖

   ```xml
   <dependency>
       <groupId>com.github.pagehelper</groupId>
       <artifactId>pagehelper</artifactId>
       <version>5.0.0</version>
   </dependency>
   ```

2. 在Mybatis全局配置文件中配置PageHelper插件

   ```xml
   <plugins>
       <plugin interceptor="com.github.pagehelper.PageInterceptor">
           <!-- config params as the following -->
           <!--<property name="param1" value="value1"/>-->
       </plugin>
   </plugins>
   ```

3. 编写代码进行测试

   ```java
   @Test
   public void testMapper() {
       SqlSession sqlSession = null;
       try {
           SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
           sqlSession = sqlSessionFactory.openSession();
           EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
           Page<Object> page = PageHelper.startPage(1, 5); // 我要第一页数据，每一页数据5条记录
           List<Employee> employee = mapper.selectAll();
           employee.forEach(System.out::println);
           // 我们还可以通过page来获取相关分页信息
           System.out.println("当前页码" + page.getPageNum());
           System.out.println("总记录数" + page.getTotal());
           System.out.println("每一页有几条记录" + page.getPageSize());
           System.out.println("总共有多少页呢" + page.getPages());
           // 当然也可以使用PageInfo来包装查询出的结果employee，会有更多详细信息可以获取
           PageInfo<Employee> info = new PageInfo<>(Employee);
           System.out.println("当前页码" + info.getPageNum());
           System.out.println("总记录数" + info.getTotal());
           System.out.println("每一页有几条记录" + info.getPageSize());
           System.out.println("总共有多少页呢" + info.getPages());
           System.out.println("是否第一页" + info.isFirstPage());
           //...
           // 当然PageInfo还有一个非常强大的功能，它的构造器不只能传入一个参数，也能传入两个参数，且第二个参数是指定一次显示多少页，以下是连续显示5页，如果你当前在第五页，那么连续页码就是3 4 5 6 7，如果你当前在第七页，那么连续显示页码就是5 6 7 8 9
           PageInfo<Employee> info = new PageInfo<>(Employee,5);
           
       } catch (Exception e) {
           e.printStackTrace();
       } finally {
           sqlSession.close();
       }
   }
   System.out.println(pageInfo.getPageNum()); // 1 第几页
   System.out.println(pageInfo.getPageSize()); // 10 每页多少行
   System.out.println(pageInfo.getSize()); // 实际上每页多少行
   System.out.println(pageInfo.getStartRow()); //从第几行开始
   System.out.println(pageInfo.getEndRow()); // 结束所在行数
   System.out.println(pageInfo.getPages()); // 总共多少页
   System.out.println(pageInfo.getPrePage()); //上一页
   System.out.println(pageInfo.getNextPage()); //下一页
   System.out.println(pageInfo.isIsFirstPage()); //是否为首页
   System.out.println(pageInfo.isIsLastPage()); //是否为尾页
   System.out.println(pageInfo.getTotal()); //总记录数
   ```

# 扩展Mybatis实用场景

## 批量操作

> 我们之前使用过forEach来实现过批量操作，但是这不能算是批量操作，它会将SQL语句拼接得很长很长，然后一次性交给MySQL去处理的，而过长得SQL语句，MySQL是不能接收的
>
> 但其实Mybatis是有提供批量执行器的，在全局配置文件中有一个设置项就是defaultExecutorType，默认值为SIMPLE，SIMPLE就是创建一个简单的Executor，就只是执行增删改查操作而已，而有一个值叫做BATCH，它能的设置能让底层创建一个能批量操作的批量执行器Executor，但是这个批量执行器设置上之后，所有的SQL都会被认为是批量执行，所以我们不这么做，**我们在创建SqlSession对象的时候，通过构造器指定一个批量执行器，而不是在全局配置文件中配置，这样就实现了在需要批量的时候批量即可**

```java
@Test
public void testMapper() {
    SqlSession sqlSession = null;
    try {
        SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
        // 在创建SqlSession对象的时候，通过构造器指定一个批量执行器，就可以执行批量操作了
        sqlSession = sqlSessionFactory.openSession(ExecutorType.BATCH);
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        for (int i = 1; i < 100; i++) {
            mapper.insert(new Employee(i,"Martha","1","da@qq.com",1));
        }
        sqlSession.commit();
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        sqlSession.close();
    }
}
```

> 那么在与Spring整合的时候，如何进行批量操作的配置呢？

1. 在Spring的配置文件中，配置如下内容：就是在Spring配置文件中创建一个专门用于批量操作的sqlSession

   ```xml
   <bean id="sqlSession" class="com.mybatis.spring.SqlSessionTemplate">
   	<constructor-arg name="sqlSessionFactory" ref="sqlSessionFactoryBean"></constructor-arg>
       <constructor-arg name="executorType" value=“BATCH></constructor-arg>
   </bean>
   ```

2. 在使用它的时候，只需要在Service层自动装配一个SqlSession对象即可获取到一个可以批量处理的SqlSession对象

   ```java
   @Service
   public class EmployeeService{
       @Autowired
       private EmployeeMapper employeeMapper;
   
       @Autowired
       private SqlSession sqlSession; // 注入之后我们呢这一个SqlSession就是一个可以批量处理的SqlSession对象，然后在该业务处理层，进行批量处理操作
   
       public void addBatchEmployees(List<Employee> emps){
           EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
           // 取出集合中一个一个Employee对象
           for (Employee emp : emps) {
               // 调用DAO层进行插入操作
               employeeMapper.insert(emp);
           }
       }
   }
   ```

   > 这个SqlSessionTemplate，其实就是实现了SqlSession接口的SqlSession对象

# 自定义TypeHandler处理枚举类型

> 我们在探究Mybatis底层源码的时候，设置参数和封装结果的时候，都是调用typeHandler.setParameter()设置参数和typeHandler.getResult()方法返回结果的，TypeHandler在这个过程中就担任了处理数据库类型与JavaBean类型的结果映射
>
> 而Mybatis在处理Java枚举类型，比如员工的状态，默认保存到数据库中的是枚举的名称![image-20211117170038371](.\\MyBatisNotes_images\image-20211117170038371.png)，所以Mybatis默认是使用EnumTypeHandler处理的枚举类型，但是我们希望它保存的是枚举索引，那么我们要将Mybatis默认处理枚举类型使用的EnumTypeHandler改为EnumOrdinalTypeHandler，这样我们Mybatis在处理我们Java枚举类型的时候，保存到数据库中的就是Java枚举类型所对应的索引值，我们想要改变Mybatis默认处理枚举类型的处理器，就要在全局配置文件中，使用typeHandlers标签显式告诉Mybatis在处理Java枚举类型的时候使用EnumOrdinalTypeHandler处理器来处理Java枚举类型

```xml
<typeHandlers>
	<typeHandler handler="org.apache.ibatis.type.EnumOrdinalTypeHandler"/>
</typeHandlers>
```

> 再次保存数据后，保存到数据库中的就是索引了![image-20211117170051265](.\\MyBatisNotes_images\image-20211117170051265.png)
>
> 所以我们可以选用不同的枚举处理器，但是不管是选用名称还是选用索引枚举处理器，查询和保存都是没问题的，但是我们使用这种情况的方式一般不多见，我们需要自定义枚举类型处理器

## 使用自定义的枚举类型处理器

> 为什么我们要自定义呢，因为

```java
/**
 * 希望数据库保存的是100,200这些状态码，而不是默认0,1或者枚举的名
 * @author lfy
 *
 */
public enum EmpStatus {
    LOGIN(100,"用户登录"),LOGOUT(200,"用户登出"),REMOVE(300,"用户不存在");


    private Integer code;
    private String msg;
    private EmpStatus(Integer code,String msg){
        this.code = code;
        this.msg = msg;
    }
    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }
    public String getMsg() {
        return msg;
    }
    public void setMsg(String msg) {
        this.msg = msg;
    }

    //按照从数据库中返回的状态码返回枚举对象
    public static EmpStatus getEmpStatusByCode(Integer code){
        switch (code) {
            case 100:
                return LOGIN;
            case 200:
                return LOGOUT;	
            case 300:
                return REMOVE;
            default:
                return LOGOUT;
        }
    }
}
```

> 自定义类型处理器，你要称为类型处理器，首先要实现TypeHandler接口或者继承BaseTypeHandler，其次定义好保存和查询的方法

```java
// 泛型中指定要处理的枚举类对象
public class MyEnumTypeHandler implements TypeHandler<EmpStatus>{
    public class MyEnumTypeHandler implements TypeHandler<T> {
        // 定义当前数据参数如何保存进数据库
        @Override
        public void setParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException {
            ps.setString(i, parameter.getCode().toString); // 我们要将枚举对象的状态码保存进数据库
        }
		
        // 根据列名返回数据
        @Override
        public T getResult(ResultSet rs, String columnName) throws SQLException {
            // 从数据库中获取状态码
            int code = rs.getInt(columnName);
            // 需要根据从数据库中拿到的枚举的状态码返回一个枚举对象
            EmpStatus status = EmpStatus.getEmpStatusByCode(code);
            return status;
        }
		
        // 根据列索引返回数据
        @Override
        public T getResult(ResultSet rs, int columnIndex) throws SQLException {
            // 从数据库中获取状态码
            int code = rs.getInt(columnIndex);
            // 需要根据从数据库中拿到的枚举的状态码返回一个枚举对象
            EmpStatus status = EmpStatus.getEmpStatusByCode(code);
            return status;
        }
		
        // 根据存储过程返回数据
        @Override
        public T getResult(CallableStatement cs, int columnIndex) throws SQLException {
            // 从数据库中获取状态码
            int code = cs.getInt(columnIndex);
            // 需要根据从数据库中拿到的枚举的状态码返回一个枚举对象
            EmpStatus status = EmpStatus.getEmpStatusByCode(code);
            return status;
        }
    }
}
```

> 自定义枚举类型处理器定义好后，就在Mybatis全局配置文件中配置自定义的枚举类型处理器全类名即可

```xml
<typeHandlers>
	<typeHandler handler="com.martha.mybatis.handler.MyEnumTypeHandler"/>
</typeHandlers>
```

> 测试就成功了，保存进数据库的是状态码，查询出来封装为了一个枚举类对象

> 除了在全局配置文件中配置类型处理器，我们也可以在Mybatis处理某个字段的时候指定类型处理器
>
> + 插入的时候，在获取参数的括号内，指定类型处理器#{empStatus,typeHandler=xxx}
>
> + 查询的时候
>
>   ```xml
>   <resultMap id="BaseResultMap" type="com.martha.mybatis.bean.Department">
>       <id column="id" jdbcType="INTEGER" property="id" />
>       <result column="department_name" property="departmentName" typeHandler="xxx"/>
>   </resultMap>
>   ```
>
> **有一点要非常注意，就是如果在参数位置修改类型处理器，应当保证插入数据和查询数据时使用的TypeHandler是同一个，这样就会避免一些问题**

# 配置文件解析

**核心配置文件**【在XML中一定要规范标签顺序】

+ **properties标签**

  + 我们可以通过properties属性来实现引用外部配置文件

  + 这些属性都是可以在外部配置且可动态替换的，既可以在典型的Java属性文件中配置，也可以通过properties元素的子元素来传递【如db.properties文件】

  + properties标签可以直接引入外部文件

  + 可以在其中增加一些属性配置

  + 如果两个文件重复有同一个字段，那么优先使用外部配置文件的字段

    ```properties
    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql:///mybatis
    jdbc.username=root
    jdbc.password=1234
    
    orcl.driver=oracle.jdbc.driver.OracleDriver
    orcl.url=jdbc:oracle:thin:@localhost:1521:orcl
    orcl.username=nzy
    orcl.password=Martha
    ```

    ```xml
    //引入外部配置文件
    <properties resource="db.properties"/>
    	...
    	<environment id="mysql">
        <transactionManager type="JDBC"/>
          <dataSource type="POOLED">
            <property value="${jdbc.driver}" name="driver"/>
            <property value="${jdbc.url}" name="url"/>
            <property value="${jdbc.username}" name="username"/>
            <property value="${jdbc.password}" name="password"/>
          </dataSource>
    </environment>
    ```

+ **类型别名(typeAliases)**

  + 为Java类型设置一个短的名字

    ```xml
    //给实体类起别名，那么resultType之类的就可以引用该别名
    <typeAliases>
      <typeAlias type="mybatis.bean.Employee" alias="employee"/>
    </typeAliases>
    ```

  + 也可以指定一个包名，Mybatis会在包名下面搜索需要的JavaBean,扫描实体类的包，它的默认别名就是这个类的类名，首字母小写

    ```xml
    <typeAliases>
      <package name="mybatis.bean"></package>
    </typeAliases>
    ```

  + 在实体类比较少的时候，使用第一种方式，如果实体类较多则使用第二种，但第一种可以直接起别名，第二种要么默认首字母小写的别名，要么可以在实体类上加上@Alias注解，明确指定在包扫描后该类的别名

    ```java
    @Alias("user")
    public class User(){}
    ```

  + Java内建的相应的类型别名，他们不区分大小写，若是基本数据类型 int，别名为_int，若是包装类Integer，则为int，**总结就是基本数据类型别名具有下划线，包装类直接对应小写**

+ **映射器(mappers)**

  + MapperRegistry：注册绑定我们的Mapper文件

    + ```xml
      //推荐使用，写一个注册一个
      <mappers>
        <mapper resource="mybatis/mapper/EmployeeMapper.xml"/>
      </mappers>
      ```

    + ```xml
      //通过class文件绑定注册
      <mappers>
        <mapper class="com.mybatis.dao.UserMapper"/>
      </mappers>
      ```

    + ```xml
      //通过包名绑定注册
      <mappers>
        <mapper package="com.mybatis.dao"/>
      </mappers>
      ```

  + Mapper配置文件必须与接口同名

  + 接口和其配置文件必须在同一个包下

+ MyBatis可以配置成适应多种环境，学会使用多套配置环境
+ 不过尽管配置多个环境，但每个SqlSessionFactory实例只能选择一种环境
+ MyBatis默认的事务管理器就是JDBC，连接池为POOLED

# 生命周期和作用域

生命周期和作用域是至关重要的，因为使用错误就会导致严重的并发问题

**SqlSessionFactoryBuilder：**

+ 一旦创建了SqlSessionFactory，就不再需要它了
+ 作用域为局部变量，也就是工厂的创造者

**SqlSessionFactory：**

+ 说白了就可以想象为：数据库连接池
+ SqlSessionFactory一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**

+ 因此SqlSessionFactory的最佳作用域就是**应用作用域**(application)，也就是全局作用域，程序开始到程序结束
+ 最简单的就是使用**单例模式**或者静态单例模式，保证全局就只有一个变量

**SqlSession：**

+ 连接到连接池的一个请求
+ 用完之后需要及时关闭，否则资源被占用，Http请求响应之后就应该关闭它
+ 一个SqlSessionFactory可以创建多个SqlSession，一个SqlSession可以创建多个Mapper实例，每一个Mapper就代表每一个业务

# 日志

**日志工厂**

如果一个数据库操作出现了异常，我们需要排错，那么日志就是最好的助手

**STDOUT_LOGGING标准日志输出**

+ 在MyBatis核心文件(mybaits-config.xml)中配置我们的日志即可生效

  ```xml
  <settings>
    <!--标准的日志工厂实现-->
  	<setting name="logImpl" value="STDOUT_LOGGING"/>
  </settings>
  ```

**Log4j**

+ 导入Maven依赖

  ```xml
  <dependencies>
  	<dependency>
    	<groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
  </dependencies>
  ```

+ 在MyBatis核心文件(mybaits-config.xml)中配置log4j日志实现

  ```xml
  <settings>
  	<setting name="logImpl" value="LOG4J"/>
  </settings>
  ```

+ 在类路径下创建log4j.properties并复制以下信息

  ```properties
  #将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
  log4j.rootLogger=DEBUG,console,file
  
  #控制台输出的相关设置
  log4j.appender.console = org.apache.log4j.ConsoleAppender
  log4j.appender.console.Target = System.out
  log4j.appender.console.Threshold = DEBUG
  log4j.appender.console.layout = org.apache.log4j.PatternLayout
  log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
  
  #文件输出的相关设置
  log4j.appender.file = org.apache.log4j.RollingFileAppender
  log4j.appender.file.File = ./log/Martha.log
  log4j.appender.file.MaxFileSize=10mb
  log4j.appender.file.Threshold=DEBUG
  log4j.appender.file.layout=org.apache.log4j.PatternLayout
  log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
  
  #日志输出级别
  log4j.logger.org.mybatis=DEBUG
  log4j.logger.java.sql=DEBUG
  log4j.logger.java.sql.Statement=DEBUG
  log4j.logger.java.sql.ResultSet=DEBUG
  log4j.logger.java.sql.PreparedStatement=DEBUG
  ```

+ 直接测试运行，简单使用

  + 在要使用Log4j的类中，导入包 import org.apache.log4j.Logger;

  + 日志对象加载，参数为当前类的class

    ```java
    static Logger logger = Logger.getLogger(UserDaoTest.class);
    ```

  + 日志级别

    ```java
    logger.info("INFO:进入了testLog4j");//可用作提示信息
    logger.debug("DEBUG:进入了testLog4j");//可用作于测试信息
    logger.error("ERROR:进入了testLog4j");//可用作于异常代码块catch中
    //之前都是使用System.out.print()，现在就是高级点而已
    ```

# 分页

**为什么要分页？**

+ 减少数据的处理量

**使用Limit分页**

```sql
//语法 startIndex从第几条开始查  pageSize指定每页显示几条记录
select * from user limit startIndex,pageSize;
select * from user limit 3;就相当于 0 , 3从第0条开始查询，每页显示3条记录
```

**PageHelper分页插件**

# 使用注解开发

+ 注解在接口上实现

  ```java
  @Select("select * from user")
  List<User> getUsers();
  ```

+ 在核心配置文件中绑定接口

  ```xml
  <!--绑定接口-->
  <mappers>
  	<mapper class="com.martha.dao.UserMapper" />
  </mappers>
  ```

+ 测试

+ 关于@Param()注解

  + 基本类型的参数或者String类型，需要加上
  + 引用类型不需要加
  + 如果只有一个基本类型的话，可以忽略，但是建议还是加上比较好
  + 我们Sql中引用的就是@Param注解中设定的属性名

# Lombok

使用步骤

+ 在IDEA中安装Lombok插件

+ 在项目中导入lombok依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.12</version>
      <scope>provided</scope>
  </dependency>
  ```

+ 相关注解

  ```txt
  @Getter and @Setter
  @ToString
  @EqualsAndHashCode
  @AllArgsConstructor
  @NoArgsConstructor
  @Data
  @Accessors
  ```

  + @Data最常用，会自动生成 getter、setter、无参构造器、hashCode、equals方法 

# 补充：解决Maven导出资源失败的问题

```xml
<!--在build中配置resources，防止我们资源导出失败的问题-->
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```



