# SpringMVC总体配置流程

## pom.xml

```xml
<packaging>war</packaging>

<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>

    <!-- 日志 -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>

    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>

    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>

    <!-- 处理JSON数据交互格式的依赖 -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.12.1</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload 用于文件上传操作 -->
    <dependency>
        <groupId>commons-fileupload</groupId>
        <artifactId>commons-fileupload</artifactId>
        <version>1.3.1</version>
    </dependency>
</dependencies>
```

## web.xml

```xml
<!--配置前端控制器DispatcherServlet-->
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>

<!--配置POST请求字符集编码：解决浏览器POST请求发送中文参数值后端接收后乱码问题-->
<filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

<!--配置请求方式转换过滤器：配置后可将原有的请求方式替换为PUT或DELETE请求方式-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## springmvc.xml

```xml
<!--配置注解扫描：生效相关注解-->
<context:component-scan base-package="com.martha.mvc.controller"/>

<!--配置注解驱动：避免因mvc:view-controller标签等造成的控制器方法映射失效的问题-->
<mvc:annotation-driven/>

<!--配置对静态资源请求访问的处理：前端控制器找不到请求资源的它来找，它找不到就404-->
<mvc:default-servlet-handler/>

<!--配置Thymeleaf视图解析器：用于转发至HTML页面-->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8"/>
                    <property name="cacheable" value="false"></property>
                </bean>
            </property>
        </bean>
    </property>
</bean>

<!--配置文件上传解析器，将上传的文件封装为MultipartFile对象-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
```

# 一、SpringMVC简介

### 1、什么是MVC

+ MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分，当我们实现一个Web工程的时候，我们需要按照模型、视图、控制器的划分来实现当前完整的Web功能

  + `M`：指Model，也就是`模型层`，指的是工程中的JavaBean（此处的JavaBean指定的当前工程中处理数据的这个类，可不是一般的实体类），JavaBean的作用就是处理数据

  > JavaBean分为两类：

  + 一类称为实体类Bean：专门**存储业务数据**的，如 Student、User类等
  + 一类称为业务处理Bean：指的是 Service 或 Dao 对象，专门用于**处理业务逻辑和数据访问**

  + `V`：View，`视图层`，指工程中的html或jsp等页面，作用是**与用户进行交互，展示数据**
  + `C`：Controller，`控制层`，指工程中的servlet，作用是**接收请求和响应浏览器**

+ MVC的工作流程：

  > 用户通过视图层发送请求到服务器，在服务器中请求被Controller层接收，Controller调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果找到相应的View视图，渲染数据后最终响应给浏览器

### 2、什么是SpringMVC

+ SpringMVC是Spring的一个后续产品，是Spring的一个子项目，SpringMVC就是Spring的一个MVC模块内容

+ SpringMVC是干什么的？SpringMVC 是 Spring 为`表述层`开发提供的一整套完备的解决方案。在表述层框架历经 Strust、WebWork、Strust2 等诸多产品的历代更迭之后，目前业界普遍选择了 SpringMVC 作为 Java EE 项目表述层开发的**首选方案**

> 注：三层架构分为表述层（或表示层）、业务逻辑层（Service层）、数据访问层（Dao层），**表述层表示前台页面和后台servlet**

### 3、SpringMVC的特点

- SpringMVC是**Spring 家族原生产品**，**与 IOC 容器**等基础设施**无缝对接**
- SpringMVC是**基于原生的Servlet**（本质就是Servlet），通过功能强大的**前端控制器DispatcherServlet**，对请求和响应进行统一处理
- SpringMVC为表述层各细分领域需要解决的问题**全方位覆盖**，提供**全面解决方案**
- SpringMVC**代码清新简洁**，大幅度提升开发效率
- SpringMVC内部组件化程度高，可插拔式组件**即插即用**，想要什么功能配置相应组件到SpringMVC核心配置文件中即可
- SpringMVC**性能卓著**，尤其适合现代大型、超大型互联网项目要求

# 二、创建第一个SpringMVC工程

## 1、开发环境的准备

+ IDE：idea 2019.2

+ 构建工具：maven3.5.4

+ 服务器：tomcat7

+ Spring版本：5.3.1

## 2、IDEA创建maven工程

1. 创建module之Maven工程![image-20211107230259964](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107230259964.png)![image-20211107230520900](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107230520900.png)

2. 在新建的Maven工程的pom.xml文件中，添加打包方式：war![image-20211107230725240](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107230725240.png)

3. 引入下方依赖到工程pom.xml文件中

   ```xml
   <dependencies>
       <!-- SpringMVC -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-webmvc</artifactId>
           <version>5.3.1</version>
       </dependency>
   
       <!-- 日志 -->
       <dependency>
           <groupId>ch.qos.logback</groupId>
           <artifactId>logback-classic</artifactId>
           <version>1.2.3</version>
       </dependency>
   
       <!-- ServletAPI -->
       <dependency>
           <groupId>javax.servlet</groupId>
           <artifactId>javax.servlet-api</artifactId>
           <version>3.1.0</version>
           <scope>provided</scope>
       </dependency>
   
       <!-- Spring5和Thymeleaf整合包 -->
       <dependency>
           <groupId>org.thymeleaf</groupId>
           <artifactId>thymeleaf-spring5</artifactId>
           <version>3.0.12.RELEASE</version>
       </dependency>
   </dependencies>
   ```

   ![image-20211107231214694](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107231214694.png)![image-20211107231543981](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107231543981.png)

> 注：由于 Maven 的传递性，我们不必将所有需要的包全部配置依赖，而是配置最顶端的依赖，其他靠传递性导入

![images](SpringMVCNotes_images\img001.png)

4. 在该Maven工程的main目录下创建一个Directory，名称**固定为webapp**，通过项目结构的手动设置，**在webapp目录下生成web.xml文件**，步骤如下：![image-20211107232236106](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107232236106.png)![image-20211107232531188](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107232531188.png)![image-20211107232823655](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107232823655.png)

   > 指定完路径 ok→apply→ok即可完成web.xml文件的创建

   ![image-20211107232935824](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211107232935824.png)

5. 到此，Maven工程就搭建完成了

## 3、配置web.xml

> 在web.xml文件中配置SpringMVC的前端控制器DispatcherServlet，使得所有的请求都要由该Servlet进行统一管理，有以下两种配置方式：

### 默认配置方式

> 如使用这种默认的配置，**SpringMVC的配置文件**将默认位于WEB-INF目录下，默认名称就是\<servlet-name>-servlet.xml，也就是以下的**servlet-name标签的值与-servlet.xml做拼接**得到的SpringMVC的配置文件名称，例如，以下配置所对应SpringMVC的配置文件位于WEB-INF下，文件名就是为springMVC-servlet.xml，但是有一点，我们使用Maven工程，所有的配置文件应该放在resources目录下，所以这就引出了第二种配置方式，扩展配置方式【即默认的配置方式，使得SpringMVC的配置文件**位置默认**，**名称默认**】

```xml
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
        设置springMVC的核心控制器所能处理的请求的请求路径
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

> / 所匹配的请求可以是/login或.html或.js或.css方式的请求路径
> 但是/不能匹配.jsp请求路径的请求，.jsp为后缀的请求路径需要由特定的Servlet来处理
> 如果.jsp后缀的请求路径被前端控制器所处理，那么前端控制器就会将该路径当作一个普通的请求来处理，而不会找到对应的JSP页面，所以这时候访问JSP就找不到对应的JSP页面了，所以我们要将.jsp后缀的路径排除在外，其它的请求都要交给前端控制器来处理（顺便提及，如果使用/*作为前端控制器处理的请求的请求路径，那么所有路径包括.jsp后缀的路径都会包含进来，所以这里使用/而不是/\*，因为/会排除掉.jsp后缀的请求）

### 扩展配置方式（常用）

> 我们**希望配置文件不要放到WEB-INF目录下**，因为WEB-INF下我们只存放页面，也就是我们的HTML页面，所以我们现在就**需要自定义SpringMVC配置文件的位置和名称**，所以可以通过`init-param`标签设置SpringMVC配置文件的位置和名称，通过`load-on-startup`标签设置SpringMVC前端控制器DispatcherServlet的初始化时间，配置内容与步骤如下：

```xml
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
    <init-param>
        <!-- contextConfigLocation为固定值，是SpringMVC自己的定义好的，我们为他赋值即可 -->
        <param-name>contextConfigLocation</param-name>
        <!-- 使用classpath:表示从类路径下查找配置文件，例如maven工程中的src/main/resources，指定SpringMVC的配置文件名称为springMVC.xml，当然配置归配置，这个springMVC.xml的文件必须存在，所以我们需要在src/main/resources目录下新建springMVC.xml文件 ，新建springMVC.xml的方式与创建Spring配置文件的方式是一样的-->
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!-- 
 		作为框架的核心组件，在启动过程中有大量的初始化操作要做
		而这些操作放在第一次请求时才执行会严重影响访问速度
		因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时间
		即在服务器启动时就进行前端控制器的初始化
        就不会影响我们第一次请求的速度了
	-->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
        设置springMVC的核心控制器所能处理的请求的请求路径
        /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
        但是/不能匹配.jsp请求路径的请求
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

1. 首先在`src/main/resources`目录下新建名称为`springMVC.xml`的SpringMVC的配置文件，创建方式与创建Spring配置文件的方式相同![image-20211108124427825](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108124427825.png)![image-20211108124534009](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108124534009.png)
2. 在`web.xml`文件中将该SpringMVC的配置文件作为参数赋值给前端控制器`DispatcherServlet`的`contextConfigLocation`参数![image-20211108124846699](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108124846699.png)
3. 在服务器启动时就前端控制器的初始化![image-20211108125651231](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108125651231.png)

4. 到此，扩展方式的配置就结束了

> 注：
>
> \<url-pattern>标签中使用/和/*的区别：
>
> /所匹配的请求可以是/login或.html或.js或.css方式的请求路径，但是/不能匹配.jsp请求路径的请求
>
> 因此就可以避免在访问jsp页面时，该请求被DispatcherServlet处理，从而找不到相应的页面
>
> /*则能够匹配所有请求，例如在使用过滤器时，若需要对所有请求进行过滤，就需要使用/\*的写法

## 4、创建请求控制器Controller

> 由于前端控制器对浏览器发送的请求进行了统一的处理，但是不同的请求有着不同的处理过程（**不同的请求路径或参数对应着不同的方法执行**），因此需要创建处理具体请求的类，即请求控制器，也就是**Controller层**
>
> 请求控制器中每一个处理请求的方法称为控制器方法
>
> 因为SpringMVC的控制器由一个POJO（普通的Java类）担任，因此需要通过@Controller注解将其标识为一个控制层组件，交给Spring的IoC容器管理，此时SpringMVC才能够识别控制器的存在，代码模板如下：

```java
@Controller
public class HelloController {
    
}
```

1. 在src/main/java目录下创建一个包，包名为`com.martha.mvc.controller`，然后在controller包下新建一个HelloController类，并在该类上添加@Controller注解，标识该类是一个控制层组件，交由IOC容器管理![image-20211108132304431](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108132304431.png)
2. 配置完@Controller注解后，还需要在SpringMVC配置文件中配置**注解扫描**后，SpringMVC才能知道这个类是一个控制层组件，才可以将我们这个类中的方法作为控制器方法，来处理当前的请求和响应![image-20211108132856956](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108132856956.png)
3. 配置完后，还需要进行以下配置，因为SpringMVC中还有一个视图解析器，这个视图解析器负责我们页面的跳转，往下一个章节`配置springMVC的配置文件`走↓

## 5、配置springMVC的配置文件

```xml
<!-- 自动扫描包 -->
<context:component-scan base-package="com.martha.mvc.controller"/>

<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <!--设置当前视图解析器的优先级，从这里就可以看出，视图解析器是可以配置多个的，如果有多个视图解析器，优先由谁解析-->
    <property name="order" value="1"/>
    <!--解析视图所使用的编码-->
    <property name="characterEncoding" value="UTF-8"/>
    <!--模板引擎-->
    <property name="templateEngine">
        <!--内部bean，为当前templateEngine属性赋值-->
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                 <!--内部bean，为当前templateResolver属性赋值-->
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
    				<!--Thymeleaf在进行解析的时候，必须要设置一个前缀和后缀
						将我们当前所设置的视图名称加上前缀和后缀，最终就可以跳转到指定的页面
						如果我们当前的视图名称符合我们当前的我条件的话，他就会被当前的视图解析器解析，来找到相对应的页面，实现页面跳转
					-->
                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>
    
                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <!--模板模型，用的是HTML5-->
                    <property name="templateMode" value="HTML5"/>
                    <!--当前页面中的编码，UTF-8-->
                    <property name="characterEncoding" value="UTF-8" />
                    <!--合在一起的意思就是，我们当前要访问的Thymeleaf的视图应该是在/WEB-INF/templates/目录下，且页面的后缀为.html，且应该是一个H5页面-->
                    <!--将thymeleaf缓存关闭，可使得IDEA热部署生效-->
                    <property name="cacheable" value="false"></property>
                </bean>
            </property>
        </bean>
    </property>
</bean>

<!--以下的后面用到的时候再导入-->
<!-- 
   处理静态资源，例如html、js、css、jpg
  若只设置该标签，则只能访问静态资源，其他请求则无法访问
  此时必须设置<mvc:annotation-driven/>解决问题
 -->
<mvc:default-servlet-handler/>

<!-- 开启mvc注解驱动 -->
<mvc:annotation-driven>
    <mvc:message-converters>
        <!-- 处理响应中文内容乱码 -->
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="defaultCharset" value="UTF-8" />
            <property name="supportedMediaTypes">
                <list>
                    <value>text/html</value>
                    <value>application/json</value>
                </list>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

1. 在SpringMVC配置文件中配置Thymeleaf视图解析器![image-20211108135024475](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108135024475.png)

2. 由于我们当前要访问的Thymeleaf的视图应该是在/WEB-INF/templates/目录下，且页面的后缀为.html，且应该是一个H5页面，所以我们要**在WEB-INF目录下创建templates目录**，随后**在templates目录下新建一个index.html**，表示首页![image-20211108135310234](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108135310234.png)

   > ​	该index.html所处的位置在WEB-INF/templates目录下，你也知道WEB-INF目录下的资源通过浏览器是不能够直接进行访问的，通过重定向也是不能够进行访问的，只能通过转发才能进行访问，我们所使用的视图解析器是Thymeleaf，所以我们的页面中可能会存在Thymeleaf的语法，而Thymeleaf的语法是要被Thymeleaf进行解析的，所以我们必须要通过我们当前所配置的视图解析器来解析我们的视图

3. 在index.html页面中添加Thymeleaf的命名空间，在页面中就可以使用Thymeleaf的语法了![image-20211108140741872](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108140741872.png)

   ```
   xmlns:th="http://www.thymeleaf.org"
   ```

   > 当然，每次创建HTML都要手动添加thymeleaf的命名空间太麻烦了，直接如下图，在HTML File模板中添加即可实现每次创建都会自动生成thymeleaf的命名空间![image-20211108140529536](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108140529536.png)

## 6、实现对首页的访问

> JavaWeb中，我们访问上下文路径（`即 / 根路径`），可以自动地访问到index.html，或者是index.jsp，而我们当前的index.html是处在WEB-INF/templates目录下，所以是访问不到的，那么我们就需要写一个请求，写一个当前通过SpringMVC来处理的请求，当我们访问的是上下文路径时，就跳转访问我们的index.html首页，想要访问上下文路径，那么我们的请求路径就应该是一个 / ，我们要访问的页面就应该是/WEB-INF/templates/index.html，以下是具体步骤

1. 在请求控制器中`Controller层`创建处理请求的方法

   ```java
   // 方法名可自定义
   public String accessIndex() {
       //返回视图名称，视图名称就决定了我们要跳转的页面，我们返回的视图名称就会被我们的Thymeleaf视图解析器进行解析，加上前缀和后缀就是我们最终要访问的页面，解析后拼接起来就是/WEB-INF/templates/index.html，所以视图名称就是去掉前缀和后缀后的名称，所以这个方法只需要返回一个index就OK了
       return "index";
   }
   ```

2. 光定义了方法还不够，还需要在方法上边添加一个`@RequestMapping注解`叫做请求映射的注解，这个注解的作用就是将我们当前的请求和控制器方法来创建二者之间的映射关系，该注解中有一个value属性用于指定请求路径，那么由于我们要访问上下文路径，相应地，value属性就等于` / `，表示当发送过来的请求是` / `的时候，也就是我们的上下文路径的时候，它就会来执行我们注解所标识的方法，而这个方法返回的是视图名称，被视图解析器解析后，即可跳转到最终的页面

   ```java
   // @RequestMapping注解：处理请求和控制器方法之间的映射关系
   // @RequestMapping注解的value属性可以通过请求地址匹配请求，/表示的当前工程的上下文路径
   // 以下注解的意思就是，当浏览器请求地址栏中的地址为: localhost:8080/springMVC/ 时，执行accessIndex方法
   @RequestMapping("/") // 只为value属性赋值的话，value属性可以省略不写
   public String accessIndex(){
       return "index";
   }
   ```

   ![image-20211108143147900](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108143147900.png)

   > @RequestMapping注解除了可以通过请求地址来匹配请求控制器方法之外，还可以通过请求方式、请求参数、请求报文等一些信息来进行匹配控制器方法

3. 一切准备就绪后，我们就来配置Tomcat服务器，通过Tomcat来访问我们当前的工程![image-20211108143812529](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108143812529.png)![image-20211108144056737](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108144056737.png)![image-20211108144145672](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108144145672.png)![image-20211108152505624](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108152505624.png)![image-20211108144541416](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108144541416.png)

4. 配置完Tomcat之后，使用Debug模式启动Tomcat，老师建议尽量使用Debug模式进行启动，方便调试

## 7、通过超链接跳转到指定页面

> 在首页中添加如下超链接内容，如`<a href="/target">访问target页面</a>`，首先这里的路径以`/`开头，以`/`开头的路径称为绝对路径，而绝对路径又分为浏览器解析的和服务器解析的，那此处超链接中所写的绝对路径经过浏览器解析后，全路径为`localhost:8080/target`，少了SpringMVC上下文路径，所以它不能表示改绝对路径是从上下文路径开始访问的，我们可以使用相对路径，但是相对路径有风险，但我们就是要使用绝对路径，但是少一个上下文路径咋办，第一种方式就是我们将上下文路径给他加上，如`<a href="/SpringMVC/target">访问target页面</a>`，但是我们也知道，上下文路径是可修改的，可以改的话，我们就最好不要写死，否则日后修改起来就会很麻烦
>
> 在这里就要引出我们的Thymeleaf了，首先HTML页面中肯定是必须得有Thymeleaf的命名空间，有了之后我们就可以使用Thymeleaf的语法了，我们要想通过Thymeleaf去解析某一个HTML标签的属性，我们可以在该属性前面加上`th:`，加上之后，该属性就会被Thymeleaf解析，那么我们就可以使用Thymeleaf的语法来设置该属性的值了，使用`@{}`，`{}`中存放访问路径，当Thymeleaf检测到`{}`中使用的路径为绝对路径的时候，**Thymeleaf就会自动帮我们在该绝对路径上动态添加上当前工程的上下文路径**，如`<a th:href="@{/target}">访问target页面</a>`就相当于`<a href="/SpringMVC/target">访问target页面</a>`
>
> + 通过`th:`修饰标签属性
> + 通过`@{}`直接设置绝对路径

1. 在WEB-INF/templates目录下创建target.html页面，并在主页index.html中设置访问该页面的超链接，使用Thymeleaf语法动态解析超链接访问路径，即在当前`{}`中绝对路径前动态添加当前工程的上下文路径

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>首页</title>
   </head>
   <body>
       <h1>首页</h1>
       <a th:href="@{/target}">访问target页面</a><br/>
   </body>
   </html>
   ```

   ![image-20211108160131269](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108160131269.png)![image-20211108160302304](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108160302304.png)

2. 在请求控制器Controller层中创建处理该超链接请求的控制器方法，返回target页面

   ```java
   @RequestMapping("/target")
   public String accessTarget() {
       return "target";
   }
   ```

   ![image-20211108160556932](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108160556932.png)

3. 启动Tomcat服务器，通过首页的超链接成功访问target.html页面

## 8、总结

1. 浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理

2. 前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法

3. 处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过Thymeleaf对视图进行渲染，最终**转发**到视图所对应页面，因为是转发，浏览器的地址栏是不会发生变化的

# 三、@RequestMapping注解

> 浏览器发送请求到服务器，首先被我们的前端控制器处理之后，就是通过我们的@RequestMapping注解找到对应的控制器方法

## 多个控制器对应同一个请求的情况

1. 先看代码

   ```java
   @Controller
   public class Controller01{
       @RequestMapping("/")
       public String accessIndex(){
           return "index";
       }
   }
   
   @Controller
   public class Controller02{
       @RequestMapping("/")
       public String accessIndex(){
           return "index";
       }
   }
   ```

2. 很明显，两个控制层中的请求映射发生了冲突，SpringMVC它不知道该把请求交给哪一个控制器方法来执行，所以如果有多个控制器，且当前@RequestMapping所处理的请求地址是相同的，那就会报错，所以我们一**定要保证在所有的控制器中，@RequestMapping所能够匹配的请求地址是唯一的**，**即在所有的控制器中，一个请求地址只能对应一个@RequestMapping**

## 1、@RequestMapping注解的功能

+ 从注解名称上我们可以看到，@RequestMapping叫做请求映射，请求就是浏览器发送的请求，映射就和该注解标识的控制器方法进行映射，所以@RequestMapping注解的作用就是**将浏览器请求和处理请求的控制器方法关联起来**，建立映射关系，每一次浏览器所发送的请求，如果能和我们的@RequestMapping进行匹配，这个注解所标识的方法就是来处理该请求的方法

+ SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求

## 2、@RequestMapping注解的位置

> 通过查阅@RequestMapping注解的源码，我们可以发现，如下图：![image-20211108165509131](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211108165509131.png)

+ @RequestMapping**标识一个类**：表示设置映射请求的请求路径的`初始信息`

+ @RequestMapping**标识一个方法**：表示设置映射请求请求路径的`具体信息`

```java
@Controller
@RequestMapping("/test")
public class RequestMappingController {

	//如果当前控制器类上有@RequestMapping注解且方法上也有@RequestMapping注解，那么我们就需要先访问到初始信息才能访问到具体信息，所以此时该方法请求映射所映射的请求的请求路径为：/test/testRequestMapping
    @RequestMapping("/testRequestMapping")
    public String testRequestMapping(){
        return "success";
    }

}
```

> 我们标识在类上方的@RequestMapping的路径首先要被进行匹配，匹配之后才能匹配到类中方法上所设置的@RequestMapping
>
> @RequestMapping加到类上方通常意味着不同模块的控制器，如/user/list、/order/list，这里的user和order就可以作为请求路径设置在类上方，不同模块`不同路径`下的方法的请求路径可以相同，就比如都是这里的list

## 3、@RequestMapping注解的value属性

+ @RequestMapping注解的value属性通过请求的请求地址匹配请求映射

+ @RequestMapping注解的value属性是一个**字符串类型的数组**，表示该请求映射**能够匹配多个请求地址**所对应的请求

+ @RequestMapping注解的value属性**必须设置**，即至少通过请求地址匹配请求映射，我们在页面中最直观的能查看到的其实就是请求地址，所以@RequestMapping匹配请求的时候，value是必须设置的，其它属性都可以不写，但至少要通过请求地址来匹配

```html
<a th:href="@{/testRequestMapping}">测试@RequestMapping的value属性-->/testRequestMapping</a><br>
<a th:href="@{/test}">测试@RequestMapping的value属性-->/test</a><br>
```

```java
// 当前请求映射设置多个值后意味着可以处理多个请求，只要满足数组中的任意一个，就可以被当前的请求映射所处理，这里就是可以处理/testRequestMapping和/test的请求，即只要浏览器发送的请求地址匹配这两个中的一个，就会跳转到成功页面
@RequestMapping(
        value = {"/testRequestMapping", "/test"}
)
public String testRequestMapping(){
    return "success";
}
```

## 4、@RequestMapping注解的method属性

> 每一次我们在匹配请求的时候，@RequestMapping注解value属性是必不可少的，那我们现在又了解了@RequestMapping注解的method的属性，若这两个属性结合起来一起使用的话，那就表示既要满足当前value属性所对应的请求地址，还要满足我们当前method属性所设置的请求方式，是与的关系，若都为真那么就执行该注解所标识的控制器方法`value和method的组合使用体现在RESTful章节`
>
> 所以说@RequestMapping注解中设置的属性越多，那它要满足的请求条件就越多，所以说它所匹配的请求就越精确
>
> 在通过使用method属性匹配请求方式的方式匹配映射关系的同时，我们还需要知道常用的请求方式有哪些：
>
> + `GET`：每当我们提交请求参数时，这个请求参数就会拼接在浏览器请求地址栏中，且参数以问号进行拼接，问号后边就是请求参数名=请求参数值&请求参数名1=请求参数值1，其实可以理解为就是表单元素中的name=value，GET请求相对来说不是很安全；但GET传输的速度比较快，因为它是伴随着我们的请求地址传输过去的；GET传输的数据量有限
> + `POST`：每当我们提交请求参数时，会将我们的请求参数放在**请求体**中，GET请求是没有请求体的，但就算是放在请求体中，参数格式依然是name=value&name1=value1，POST请求是安全的，POST传输的速度就相对比较慢；POST传输的数据量比较大，可以认为是无限制的；如文件上传就是以POST方式上传的，如果使用GET方式来做文件上传，那么地址栏中拼接的参数可不是文件，而是你的文件名，你的文件压根儿就提交不到服务器中，所以在上传文件的时候，只能使用POST方式
> + PUT
> + DELETE

+ @RequestMapping注解的method属性是**通过请求的请求方式（GET或POST）来匹配请求映射**
+ @RequestMapping注解的method属性是一个**RequestMethod类型的数组**（RequestMethod是一个枚举类），表示该请求映射能够匹配多种请求方式的请求
+ 若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错405：Request method 'POST' not supported
+ 若不设置method属性的值，那就是不以method属性为条件，而不是所谓的默认值，即任何请求方式都能够匹配，都OK

```html
<!--a标签请求方式为GET-->
<a th:href="@{/test}">测试@RequestMapping的value属性-->/test</a><br>
<!--form表单请求方式为POST-->
<form th:action="@{/test}" method="post">
    <input type="submit">
</form>
```

```java
// 在以下两个路径匹配到的前提下，只要请求方式满足GET或POST，即可执行响应的控制器方法
@RequestMapping(
        value = {"/testRequestMapping", "/test"},
        method = {RequestMethod.GET, RequestMethod.POST}
)
public String testRequestMapping(){
    return "success";
}
```

> 注：
>
> 1、对于处理特定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解，@RequestMapping的派生注解的意思就是这个注解是由@RequestMapping注解产生的
>
> 处理get请求的映射-->@GetMapping相当于
>
> @RequestMapping(value="xx"  method={RequestMethod.GET})，以下同理
>
> 处理post请求的映射-->@PostMapping
>
> 处理put请求的映射-->@PutMapping
>
> 处理delete请求的映射-->@DeleteMapping
>
> 2、常用的请求方式有get，post，put，delete
>
> 但是目前浏览器只支持get和post，若在form表单提交时，为method设置了其他请求方式的字符串，如（put或delete），则按照**默认的请求方式get处理**
>
> 若要发送put和delete请求，则需要通过spring提供的过滤器HiddenHttpMethodFilter，在RESTful部分会讲到

```java
// 派生注解的演示，首先要求请求方式必须为GET，其次匹配请求地址
@GetMapping("/testGetMapping")
public String testGetMapping(){
    return "success";
}

// 上面这种方式就相当于
@RequestMapping(
	value="/testGetMapping",
    method={Request.GET}
    )
public String testGetMapping(){
    return "success";
}

// 这时候以下超链接就可以访问到以上控制器方法，首先a标签的请求方式就是GET，其次请求地址也匹配
<a th:href="@{/testGetMapping}"></a>
```

## 5、@RequestMapping注解的params属性（了解）

> params顾名思义就是参数的意思，就比如说我们在servlet里边获取请求参数应该用的是request.getParameter()方法，所以params就是Parameter的前几个字母，所以既然@RequestMapping注解是用于匹配我们浏览器所发送的请求的，那么这个params属性的作用就应该是**通过请求参数去匹配请求**，但这个params属性与value和method属性不同，value和method属性只需要满足其中的某一个值即可找到相对应的控制器方法，**params属性值和我们马上要学习的headers属性值必须全部同时满足**才能匹配控制器方法

+ @RequestMapping注解的params属性**通过请求的请求参数匹配请求映射**

+ @RequestMapping注解的params属性是一个**字符串类型的数组**，可以通过以下四种表达式设置请求参数和请求映射的匹配关系
  + params={"username"}：要求请求映射所匹配的请求**一定必须携带**username请求参数，不关注属性值

  + params={"!username"}：要求请求映射所匹配的请求必须**一定不能携带**username请求参数，不关注属性值
  + params={"username=Martha"}：要求请求映射所匹配的请求**必须携带**username请求参数并且username属性的值一定必须=Martha

  + params={"username!=Martha"}：要求请求映射所匹配的请求**必须携带**username请求参数但是username属性的值一定不能=Martha

```html
<!--Thymeleaf语法在请求地址后拼接请求参数，使用?的话可以实现拼接，主要就是?会报红，看得很不舒服，所以推荐使用括号的方式拼接请求参数-->
<a th:href="@{/test?username=admin&password=123456)">测试@RequestMapping的params属性-->/test</a><br>

<!--Thymeleaf语法使用括号的方式拼接请求参数，属性值是字符串就是用''单引号引起-->
<a th:href="@{/test(username='admin',password=123456)">测试@RequestMapping的params属性-->/test</a><br>
```

```java
@RequestMapping(
        value = {"/testRequestMapping", "/test"}
        ,method = {RequestMethod.GET, RequestMethod.POST}
        ,params = {"username","password!=123456"}
)
public String testRequestMapping(){
    return "success";
}
```

> 注：
>
> 若当前请求满足@RequestMapping注解的value和method属性，但是不满足params属性，此时页面会报错400：Parameter conditions "username, password!=123456" not met for actual request parameters: username={admin}, password={123456}

## 6、@RequestMapping注解的headers属性（了解）

> 我们知道，当前浏览器和服务器之间的交互必须要按照HTTP协议`超文本传输协议`进行交互，那它就要求浏览器向服务器发送请求要以请求报文的形式发送，而服务器向浏览器响应数据也要以响应报文的方式发送，而请求报文的话，分为请求头`请求头就是键值对结构`、请求体、请求空行，因为请求头的结构是键值对结构，所以headers的使用与params是一模一样的

+ @RequestMapping注解的headers属性**通过请求的请求头信息匹配请求映射**

+ @RequestMapping注解的headers属性是一个**字符串类型的数组**，可以通过四种表达式设置请求头信息和请求映射的匹配关系
  + "header"：要求请求映射所匹配的请求必须携带header请求头信息
  + "!header"：要求请求映射所匹配的请求必须不能携带header请求头信息
  + "header=value"：要求请求映射所匹配的请求必须携带header请求头信息且header=value
  + "header!=value"：要求请求映射所匹配的请求必须携带header请求头信息且header!=value

> 若当前请求满足@RequestMapping注解的value和method属性，但是不满足headers属性，此时页面显示404错误，即资源未找到

## 7、SpringMVC支持ant风格的路径(基于value属性)

> ant风格可以理解为是一个模糊匹配路径的意思

**@RequestMapping中value属性包含以下符号对应着不同意思**

+ `?`：表示任意的**单个字符**，可匹配的请求地址中，`已知浏览器栏中的/和?是不可被解析为单个字符的，而是具有特殊的意义，所以不能匹配占位符`

+ `*`：表示任意的**0个或多个字符**

+ `**`：表示任意的**一层或多层目录**，要注意的是：在使用\**时，只能使用`/**/xxx`的方式，如下代码：

  ```java
  // 合理的**使用
  @RequestMapping("/**/test")
  public String accessIndex(){
      return "index";
  }
  
  // 如果**前后都存在内容，那么**就会被解析为单独的路径，也就是说要执行到该控制器方法，浏览器请求路径就得是:  /上下文路径/a**a/test
  @RequestMapping("/a**a/test")
  public String accessIndex(){
      return "index";
  }
  ```

## 8、SpringMVC支持路径中的占位符（重点）(基于value属性)

> 我们浏览器GET请求传参数都是使用`?`拼接参数，这是原始方式，但是在我们使用`rest`风格传参数的时候，我们就不会通过`?`来进行参数的传递，但这也不意味着学习了`rest`风格之后，就完全抛弃了`?`传参的形式，总的来说，他俩只是两种不同的传参方式，若在往后开发中，使用的是rest风格开发，那么最好使用的是rest方式来传参，rest方式传参的风格就是将我们的请求参数全部以请求路径的方式拼接在我们的请求路径中

+ 原始方式：/deleteUser?id=1

+ rest方式：/deleteUser/1

> SpringMVC路径中的占位符常用于RESTful风格中，当请求路径中将某些**参数数据**通过路径的方式传输到服务器中，就可以在相应的@RequestMapping注解的value属性中**通过占位符{xxx}表示传输的数据**，但是之前我们GET请求和POST请求的传输的参数都是以键值对类型进行传输的，可以通过name来获取value，但是我们通过RESTful风格传输的参数直接就是一个值，要获取这个值就**需要且只能**通过@PathVariable注解，将占位符所表示的参数数据赋值给控制器方法的形参变量，代码如下：

```html
<a th:href="@{/testRest/1/admin}">测试路径中的占位符-->/testRest</a><br>
```

```java
// {}是一个占位符，对应一个参数，可在{}中为当前参数值取一个名称来表示该参数值，存在占位符就必须存在相应的参数值，也就是必须要有这一层路径，否则404报错，如若请求只发送/testRest，那么不好意思，报404，说明当前的请求路径根本就不匹配我们当前的请求映射路径
@RequestMapping("/testRest/{id}/{username}")
// @PathVariable注解可以将参数值自动赋值给对应的形参变量
public String testRest(@PathVariable("id") String id, @PathVariable("username") String username){
    System.out.println("id:"+id+",username:"+username);
    return "success";
}
//最终输出的内容为-->id:1,username:admin
```

# 四、SpringMVC获取请求参数

> @RequestMapping就已经学完了，我们当前完全可以通过请求映射来匹配浏览器所发送过来的请求，所以若我们当前能够匹配到请求了，那我们就需要在控制器方法中来处理当前请求，那么请求该如何处理呢？那不就是获取请求、处理编码、获取请求参数、然后调用service处理业务逻辑、处理完业务逻辑肯定能得到业务处理后的结果，再根据结果响应到浏览器，是这样的一个过程
>
> 既然我们现在已经能够去匹配到请求了，那我们下面就需要来处理请求，即继续学习`SpringMVC获取请求参数`章节，请求参数是很重要的，之所以传过来请求参数，我们的功能实现就是要依赖于请求参数来实现的

## 1、回顾通过ServletAPI获取请求参数

> 我们当前所写的控制器方法是由谁调用的呢？我们已知SpringMVC运行的大概流程，我们从浏览器发送的请求，会被@RequestMapping注解进行匹配，若匹配成功，那么就会由该注解所标识的控制器方法来处理请求，但是在此之前我们是要在web.xml文件中注册前端控制器的，所以我们浏览器发送的请求都要先被前端控制器统一处理，之后呢，我们又执行了浏览器请求相对应的控制器方法，所以说将我们浏览器请求和@RequestMapping进行匹配来找到我们控制器方法的过程就是由我们的前端控制器即`DispatcherServlet`来完成的，所以说当我们的`DispatcherServlet`前端控制器调用控制器方法的时候，当然了我们这里所说的调用是间接调用，其实并不是由我们的`DispatcherServlet`直接去调用我们的控制器方法的，中间呢还有几个其他的组件，等我到我们讲解源码的时候我们再深入了解，所以当我们的`DispatcherServlet`在调用我们当前控制器方法的过程中，很显然这个处理过程应该是在`DispatcherServlet`里面执行的，那它是怎么来执行的呢，那就是通过浏览器请求地址和@RequestMapping注解匹配后找到该控制器方法进入执行的，所以说在我们的`DispatcherServlet`中，它为我们就封装了很多的数据，**当它调用我们当前的控制器方法的时候，就会根据我们当前控制器方法的形参，为我们控制器方法来注入相关参数，也就是为参数赋值**

> 如将`HttpServletRequest`作为控制器方法的形参，此时`HttpServletRequest`类型的参数表示封装了当前请求的请求报文的对象，`DispatcherServlet`检测我们有声明这个参数，表示我们有这个需求，它就会自动将它自己的封装好的**且表示当前请求的**`HttpServletRequest`对象传入给到我们当前的控制器方法，**表示当前请求**的`HttpServletRequest`对象就是和当前请求映射路径相匹配的当前浏览器的请求，该请求被DispatcherServlet封装为了一个`HttpServletRequest`对象，说的通俗点**，如果当前我的控制器方法形参中，如果你写了一个`HttpServletRequest`对象的参数，那当前这个参数表示的就是当前请求，所以就可以直接获取到当前请求的参数了**

```html
<a th:href="@{/testServletAPI(username='Martha',password=123456)}">测试使用ServletAPI获取请求参数</a>
```

```java
HttpServletRequest@RequestMapping("/testParam")
// 形参位置的request对象就表示当前请求
public String testParam(HttpServletRequest request){
    String username = request.getParameter("username");
    String password = request.getParameter("password");
    System.out.println("username:"+username+",password:"+password);
    return "success";
}
```

> 原生的ServletAPI，我们在SpringMVC一般是不用的，我们要操作的那些数据，其实SpringMVC已经帮助我们获取过了，而且他也会提供给我们一些非常简单的方式去获取这些数据，我们根本就没必要使用原生的ServletAPI再多此一举了，以下就是在SpringMVC中如何来获取请求参数 `↓`

## 2、通过控制器方法的形参获取请求参数

> 在控制器方法的形参位置，**设置和请求参数同名的形参**，当浏览器发送请求，匹配到请求映射时，在**DispatcherServlet**中就**会将请求参数赋值给相应的形参**

```html
<a th:href="@{/testParam(username='admin',password=123456)}">测试获取请求参数-->/testParam</a><br>
```

```java
@RequestMapping("/testParam")
public String testParam(String username, String password){
    System.out.println("username:"+username+",password:"+password);
    return "success";
}
```

> 注意：
>
> 若请求所传输的请求参数中有多个**同名的请求参数**，此时可以在控制器方法的形参中**设置字符串数组**或者**字符串类型的形参**接收此请求参数
>
> 若使用字符串数组类型的形参，此参数的数组中**包含了每一个数据**
>
> 若使用字符串类型的形参，此参数的值为每个**数据中间使用逗号拼接的结果**

```html
<!--发送多个同名请求参数-->
<form th:action="@{/testParam}" method="POST">
    爱好：<input type="checkbox" name="hobby" value="a" />
    	<input type="checkbox" name="hobby" value="b" />
    	<input type="checkbox" name="hobby" value="c" />
</form>
```

```java
@RequestMapping("/testParam")
// 使用字符串类型接收多个同名请求参数
public String testParam(String hobby){
    System.out.println("hobby=" + hobby);
    return "success";
}
// 输出：hobby=a,b,c

@RequestMapping("/testParam")
// 使用字符串数组类型接收多个同名请求参数
public String testParam(String[] hobby){
    System.out.println("hobby=" + Arrays.toString(hobby));
    return "success";
}
// hobby=[a,b,c]
```

## 3、@RequestParam

> 通过控制器形参的方式获取请求参数，硬性要求就是形参变量名要与请求参数名称相同，但是如果出现了不匹配的情况呢，就不能为我们的形参进行赋值了，没赋值的话，参数值就是形参变量的默认值，若是String类型的形参，那么就会为null，可能会给后续程序造成空指针异常的影响
>
> 所以在这里我们引出了@RequestParam注解，这个注解用于与我们控制器方法的形参创建映射关系，也就是说你要拿我们当前的哪一个请求参数去对应我们的哪一个形参，即给我们的形参进行赋值，@RequestParam有一个required属性，默认为true，意思就是是否必须传输value属性所对应的请求参数，通俗的来说，我们当前@RequestParam注解的value属性所对应的请求参数默认就必须要传输，请求参数名对不上或者根本没传这个参数**且没有设置defaultValue属性**，就会报错，这就保证了我们的形参变量不会被赋值为null或其它默认值，**即要么我们的形参变量有参数值，要么就报错**，就是这个意思，当我们将required=false，就意味着你有请求参数就传过来，没有就为形参默认值，适合那些不重要的属性

+ @RequestParam是将请求参数和控制器方法的形参创建映射关系
+ @RequestParam注解一共有三个属性：

  + value：指定为形参赋值的请求参数的参数名

  + required：设置是否必须传输此请求参数，默认值为true

    > 若required设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；若设置为false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null

  + defaultValue：不管required属性值为true或false，当value所指定的请求参数**没有传输**或**传输的值为`""`时或请求参数名与value属性不匹配**，则使用默认值为形参赋值

    ```java
    @RequestMapping("/testParam")
    public String testParam(@RequestParam(value = "username",required = false,defaultValue = "Martha") String username){
        System.out.println(username);
        return "success";
    }
    // 浏览器发送以下请求，请求参数名相符，对应控制器方法形参username输出Maserati
    <a th:href="@{/testParam(username=‘Maserati’)}"><a>
    // 浏览器发送以下请求，请求参数名不符，对应控制器方法形参username输出默认值Martha
    <a th:href="@{/testParam(user_name=‘Maserati’)}"><a>
    // 浏览器发送以下请求，没有请求参数，对应控制器方法形参username输出默认值Martha
    <a th:href="@{/testParam}"><a>
    ```

## 4、@RequestHeader

> @RequestHeader、@CookieValue、@RequestParam三个注解的使用方式相同、属性相同且属性required默认值相同，就是三个注解彼此的作用不同罢了

+ @RequestHeader是**将请求头信息和控制器方法的形参创建映射关系**

+ @RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

```java
@RequestMapping("/testHeader")
// value设置为请求头信息中键值对的键，即可获取对应的值
public String testHeader(@RequestHeader(value = "Host") String host){
    System.out.println(host);
    return "success";
}
```

> @RequestParam即使不写，控制器形参也能与请求参数有着默认的映射关系，即通过控制器形参的方式获取请求参数，要求形参变量名与请求参数名相一致，但是如果要获取当前请求头的请求头信息，那么就必须显式加上@RequestHeader注解来获取

## 5、@CookieValue

+ @CookieValue是**将cookie数据和控制器方法的形参创建映射关系**

+ @CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

```java
// 因为我们这个是HTML页面，不是JSP页面，若是JSP页面默认就有Session对象，所以要先执行下面的控制器方法创建session
@RequestMapping("/getSession")
public String getSession(HttpServletRequest request){
    HttpSession session = request.getSession(); // 如果浏览器(客户端)是第一次访问，那么则为创建Session，若不是第一次则是获取session
    return "success";
}

// 上面创建完了，这里就可以进行获取了
@RequestMapping("/testCookie")
// value设置为请求头信息中键值对的键，即可获取对应的值
public String testCookie(@CookieValue(value = "JSESSIONID") String JSESSIONID){
    System.out.println(JSESSIONID);
    return "success";
}
```

> 我们的session/cookie是会话技术，会话技术默认的生命周期就是浏览器(客户端)开启到浏览器(客户端)关闭，所以只要浏览器(客户端)不关闭，那我们的session/cookie将一直存在
>
> 当我们浏览器第一次请求getSession，因为是第一次访问getSession()方法，所以说Cookie应该是存在于服务器的响应报文中，因为服务器在执行getSeesion方法的时候，首先会检测一下浏览器(客户端)发送的请求报文中是否有携带JSESSIONID的Cookie，如果说没有的话，则在这一次会话中，我们是第一次来创建session对象的，所以服务器就会为浏览器(客户端)创建一个新的session对象，然后将该session放在服务器所维护的Map集合中，并且去创建一个Cookie，且这个Cookie的键是固定的，叫做JSESSIONID，它的值是一个随机序列，与我们常说的UUID很像，这个时候服务器还会干一件事，就是将我们的HttpSession对象存储到我们当前的服务器所维护的Map集合中，以及JSESSIONID也就是Cookie的值，即随机序列值作为Map集合的键，把我们session对象作为Map集合的值进行存储，存储在服务器内部，这时候，再把Cookie响应到浏览器(客户端)
>
> 所以说，在第一次执行getSession方法的时候，这个JSESSIONID的Cookie是会存在于服务器的响应报文中的，浏览器(客户端)接收后，它将存在于浏览器(客户端)的请求报文中【就不会存在于服务器的响应报文中了，因为不是第一次请求session了】，因为Cookie就是这样的原理，由我们的服务器创建并且响应到浏览器(客户端)之后，之后每一次浏览器(客户端)向服务器发送请求都会携带Cookie

## 6、通过POJO获取请求参数

> 若在我们获取请求参数的时候，有的时候如果我们的请求参数非常的多，如我们在做一个添加或修改功能的时候，我们当前要做一个完整的功能，那么就要来使用我们当前的MySQL数据库，那我们当前所添加的这些数据就要对应我们当前数据库中的字段，当然了，我们的数据库同样也会对应着我们的一个实体类对象，所以说，我们在页面中添加的这些数据与我们实体类中的属性也是一一对应的，这个时候，SpringMVC又为我们提供了一个非常简单的方式，如果我们当前浏览器请求提交的请求参数与我们当前实体类对象的属性一一对应，那我们就可以直接在控制器方法形参的位置直接去声明一个实体类对象类型的形参变量，这个时候我们只需要保证，我们浏览器的请求参数名与我们实体类对象的属性名称保持一致就可以自动通过实体类对象收集这些请求参数的值

+ 可以在控制器方法的形参位置设置一个**实体类类型的形参**，此时若浏览器**传输的请求参数的参数名**和**实体类中的属性名一致**，那么请求参数就会为此属性赋值

```java
public class User{
    private Integer id;
    private String username;
    private String password;
    private Integer age;
    private String sex;
    private String email;
    // 在框架中，好多技术都是通过反射来实现的，而通过反射来创建一个类型的对象时，默认调用的就是无参构造器，所以要保证至少保留一个无参构造器
    public User(){}
    
    public User(Integer id,String username,String password,Integer age,String sex,String email){
        super();
        this.id = id;
        this.username = username;
        this.password = password;
        this.age = age;
        this.sex = sex;
        this.email = email;
    }
}
```

```html
<form th:action="@{/testBean}" method="post">
    用户名：<input type="text" name="username" value="admin"><br>
    密码：<input type="password" name="password" value="123"/>
    性别：<input type="radio" name="sex" value="男">男<input type="radio" name="sex" value="女">女<br>
    年龄：<input type="text" name="age" value="23"><br>
    邮箱：<input type="text" name="email" value="123@qq.com"><br>
    <input type="submit" value="使用实体类对象接收请求参数">
</form>
```

```java
@RequestMapping("/testBean")
public String testBean(User user){
    System.out.println(user);
    return "success";
}
//最终结果-->User{id=null, username='admin', password='123', age=23, sex='??.', email='123@qq.com'}
// 可以看到sex属性在接收中文字符的时候出现了乱码问题，在后面一章可以得到解决↓
```

> 如果说，我们没有学习通过实体类对象来获取请求参数的话，那我们无非也就写的麻烦一点，将参数都声明为控制器参数来获取，但是随着请求参数越来越多，当前方法的参数也越来越多，而且就算我们一个一个地获取了，在之后的操作中，还是要将这些参数放入一个实体类对象中，所以直接通过实体类对象获取请求参数的方法方便了很多

## 7、解决获取请求参数的乱码问题

> 为什么会出现乱码问题？就是字符编码不一致，在原生Serlvet中，我们出现乱码是怎么解决的？是通过`request.setCharacterEncoding("UTF-8");`，那么在SpringMVC中，有request对象吗？有，但有也没有用啊，因为在我们设置编码的时候，有一个前提，就是在设置编码之前，你已经获取到了请求参数，然后我们再设置请求编码是没有任何效果的，只能在获取请求参数之前就设置请求的编码，才能起作用，所以在我们的控制器方法中，通过注入`HttpServletRequest`对象来设置请求编码时，已经晚了，因为前端控制器通过浏览器请求匹配过来找到的控制器方法，相当于请求在控制器方法执行之前就已经完成了，相应地，请求参数也被前端控制器保存好了，这时候你在控制器方法中通过`request`对象设置请求编码，就没有任何效果可言
>
> 其实我们在获取请求参数的时候，出现的乱码问题有两种，一种是GET请求的乱码，一种是POST请求的乱码
>
> + 对于GET请求的乱码问题，其实是Tomcat8版本之前的服务器造成的，我们要想解决GET请求的乱码，我们需要找到当前Tomcat服务器安装目录下的配置文件`/conf/server.xml`，然后我们需要在我们改端口号的地方加上一个属性——URIEncoding="UTF-8"，就可以将GET请求的乱码进行一次性的解决，当然前提是项目是在当前这个Tomcat服务器上运行的**但是get方式在tomcat8版本开始得到了解决，因为tomcat8已经对get方式中文乱码进行了处理，url的默认编码方式就是utf-8. 这在tomcat8中的server.xml中就可以看出来，所以就不用手动声明URIEncoding了**
> + 对于POST请求乱码的问题，因为前端控制器在间接调用Controller层的控制器方法之前，就已经将浏览器请求的相关信息包括请求参数获取到了，那么我们是不是要赶在前端控制器`DispatcherServlet`之前将字符集设置上才能起作用，而前端控制器是一个Servlet的配置且loadonstartup=1，在服务器启动时就进行初始化好，那么有什么内容能比我们的Servlet执行更早的呢？那我们就要找到服务器中的一个组件，该组件的初始化执行时间是要比servlet组件的初始化执行时间更早的，我们知道我们在JavaWeb阶段学习过三大组件，`Servlet`、`Filter`(过滤器)、`Listener`(监听器)，谁的加载时间最早？当然是我们的`ServletContextListener`(Servlet上下文监听器)加载时间是最早的，然后再往下就是我们的过滤器`Filter`，最后才是我们的`Servlet`，但是我们监听器是监听的作用，就是用来监听我们Servlet的创建和销毁，这创建和销毁两方法都是只执行一次，而我们的请求有多次，显然不太适合；对于我们的过滤器来说，只要我们设置了过滤路径且只要我们当前访问的路径满足是我们所要过滤的路径，都会被过滤器进行过滤，**所以我们应该使用过滤器来完成这艰巨的任务**！通过过滤器来设置我们当前的编码，每一次我们浏览器发送请求都要先经过过滤器的处理，过滤器处理完之后，再交由前端控制器DispatcherServlet进行处理，这时候，配置的字符集就能够生效了，**当然，这个过滤器也不用我们来手写，我们SpringMVC已经为我们写好了，既然已经提供好了，我们想要过滤器产生作用，就需要在web.xml中配置过滤器**

+ 解决获取请求参数的乱码问题，可以使用SpringMVC提供的编码过滤器`CharacterEncodingFilter`，但是必须**在web.xml中进行注册**，若在服务器启动之后注册完，需要重启Tomcat服务器，重新加载web.xml文件

  ```xml
  <!--配置POST方式请求编码过滤器-->
  <filter>
      <filter-name>CharacterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <!--设置过滤请求的编码，参数名与CharacterEncodingFilter的属性名一致-->
      <init-param>
          <param-name>encoding</param-name>
          <param-value>UTF-8</param-value>
      </init-param>
      <!--设置响应的编码也随着生效为encoding的值，建议也加上-->
      <init-param>
          <param-name>forceResponseEncoding</param-name>
          <param-value>true</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>CharacterEncodingFilter</filter-name>
      <!--我们要对所有的请求进行过滤，所有请求都有可能是POST方式发送的，所以这里也包括了.jsp文件-->
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

> 注：**SpringMVC中处理编码的过滤器一定要配置到其他过滤器之前，否则无效**

**CharacterEncodingFilter过滤器类的源码的初步了解**![image-20211110143422912](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110143422912.png)![image-20211110143841030](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110143841030.png)

# 五、域对象共享数据

## 回顾域对象

> 当我们能够获取请求参数了，那么我们下一步就是将请求参数作为条件，去调用Service层来处理业务逻辑，然后Service层调用Dao层来访问数据库，Dao将最终的结果返回给Service，Service再返回给Controller层，这个时候呢，如果说我们有数据是要往我们的页面中去发送的，那么我们就需要将这些数据，在我们的域对象中进行共享，那我们在JavaWeb阶段所学习的域对象一共有几种？本身呢是应该有四种，但是呢我们在SpringMVC中没有学习JSP，所以在SpringMVC中呢就只有三种，因为被排除的那一种就是我们范围最小的PageContext域对象，这个域对象指的是我们JSP页面的范围，但是我们现在也不使用JSP了，所以我们只需要知道以下三种域对象即可
>
> + request：一次请求的范围
> + session：一次会话的范围，即从浏览器(客户端)开启到浏览器(客户端)关闭
> + application（ServletContext）：整个应用的范围，即从服务器开启到服务器关闭

> **session中的数据与服务器是否关闭无关，只跟浏览器是否关闭有关**，因为我们的session中有钝化和活化
>
> + 钝化：当服务器关闭了，但是浏览器没有关闭，说明会话仍然在继续当中，这个时候我们存储在session中的数据就会经过序列化，序列化到浏览器(客户端)本地磁盘上
> + 活化：如果浏览器仍然没有关闭，但是服务器重启了，这个时候服务器就会将之前钝化后的文件中的内容重新读取到session中
>
> 所以session中的数据与服务器是否关闭无关，只与浏览器是否关闭有关系

> **application与浏览器是否关闭无关，与服务器是否关闭有关**，因为我们的ServletContext表示的是上下文对象，这个对象只在服务器启动时创建，在服务器关闭时销毁，所以这个对象自始自终只创建了一次，我们之所以能在域对象中共享数据，就是因为我们使用的对象都是同一个，所以说我们才能够在同一个对象中设置共享数据和获取共享数据
>
> 如：
>
> + 我们通过转发也可以获取到请求域中的数据，因为转发自始自终就属于一次请求，即同一个request域对象
> + 我们在session中共享数据，就是因为session的原理，即只要当前JSESSIONID的Cookie存在，那么getSession方法创建的session对象就永远是同一个，所以我们就可以在一次会话中共享数据了
> + 因为我们在当前整个应用中就创建了一次的ServletContext对象，所以我们就可以在当前整个工程中共享数据了

> 其实这三种域对象中，我们常用的就是request和session域对象，ServletContext也就是application我们一般都不使用，因为它的范围太大了，而我们在使用域对象的时候，不能选择得太小，因为太小就没有多大效果，但是太大一定是有效果的，但是没有必要
>
> 我们要想一下，因为域对象的范围不一样，那么它们所对应的生命周期也就不一样，比如说我本来是把数据放在请求域request域对象中，我们想要实现查询列表或者表单回显的时候，我们一般使用的都是request域对象，既然能放到request中，那么放到session和application中也一样好使，但是没那种必要啊，我们每一次要查询数据的时候，我们都要重新查询，因为我们既有查询功能也有增删改功能啊，所以数据是一直在发生变化的状态，所以我们每一次获取都要重新获取，才能获取到一个最新的真实数据，所以就没有必要将查询条件放到session或者application中，我们在一次请求中就只用这一次，在其他时候可能就不再使用了，如果放到session或者application中，就会很浪费空间，**所以在选择域对象的时候，应当选择能实现功能的且范围尽可能小的那个域对象**

## 1、使用ServletAPI向request域对象共享数据

> 因为我们上面说到，DispatcherServlet前端控制器会检测我们的控制器参数，将request对象为我们进行注入，这时候，我们就可以在控制器方法中使用request对象设置数据实现在一次请求中共享数据

```html
<a th:href="@{/testServletAPI}">测试请求域对象</a>
<!--th:text用于代表当前标签的文本节点内容，${}中指明请求域中的键即可获取到对应的值
	注意：${}是不能直接写在标签体中的，若写在了标签体中，会被标签直接当作文本来进行解析
只有th:所对应的属性中的内容才会被thymeleaf进行解析，所以需要放在属性中
-->
<p>request请求域中的数据为<span th:text="${testRequestScope}"></span></p>
```

```java
@RequestMapping("/testServletAPI")
public String testServletAPI(HttpServletRequest request){
    request.setAttribute("testRequestScope", "hello,servletAPI");
    return "success"; // Thymeleaf解析后进行转发操作，转发到success.html页面，所以在html页面中就可以获取当前request域对象中的数据了
}
```

+ 如果`${}`访问的是request即请求域中的数据，那么直接就**直接指定键名**即可获取对应的值
+ 如果`${}`访问的是session即会话域中的数据，那么直接就需要**session.键名**即可获取对应的值
+ 如果`${}`访问的是servletContext即应用域中的数据，那么直接就需要**application.键名**即可获取对应的值

> 我们可以看到，thymeleaf语法这里有个小瑕疵就是会报红，其实这种报红并不影响正常运行![image-20211110184625743](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110184625743.png)
>
> 要想不看见这种报红，设置如下：![image-20211110185233376](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110185233376.png)

> 当然我们之前就说过了，既然我们使用了SpringMVC，那么我们就最好不要使用原生的ServletAPI了，**SpringMVC提供了特殊的方式帮助我们往域对象中保存数据** ↓

## 2、使用ModelAndView向request域对象共享数据（建议使用）

> ModelAndView就是我们SpringMVC为我们提供的API了，在这我们需要使用ModelAndView对象，该对象是非常厉害且非常重要的，首先顾名思义，该对象应该是有两个功能的
>
> + Model(模型)：主要指的就是**向request域对象中共享数据**的功能
> + View(视图)：最终所设置的视图名称，经过视图解析器解析，跳转到指定页面的过程

```html
<a th:href="@{/testModelAndView}">测试请求域对象</a>
<p>request请求域中的数据为<span th:text="${testRequestScope}"></span></p>
```

```java
@RequestMapping("/testModelAndView")
// 首先控制器方法的返回值必须是ModelAndView，也就是将这个封装了模型和视图的对象，交给前端控制器DispatcherServlet解析，因为你在控制器方法体内创建的ModelAndView对象，没返回给前端控制器，前端控制器是不知道你创建了这个对象的，所以方法的返回值需为ModelAndView
public ModelAndView testModelAndView(){
    /**
     * 首先创建ModelAndView对象
     * ModelAndView有Model和View的功能
     * Model主要用于向请求域共享数据
     * View主要用于设置视图，实现页面跳转
     */
    ModelAndView mav = new ModelAndView();
    //处理模型数据：即向请求域--request共享数据
    mav.addObject("testRequestScope", "hello,ModelAndView");
    //设置视图名称，实现页面跳转
    mav.setViewName("success"); // 取代了return "success";
    return mav; // 在这里一同进行返回
}
```

> 注意：
>
> + 在控制器方法体中使用ModelAndView对象，**控制器方法的返回值必须是ModelAndView**
> + **不管是使用1、2、3、4、5方式的任何一个方式向request域对象中存放共享数据，最终在底层都会将其封装为一个ModelAndView对象交由前端控制器解析，所以建议还是使用ModelAndView**

## 3、使用Model向request域对象共享数据

> 我们的ModelAndView本身就是两种功能，一种是Model，一种就是View，所以这里的Model指的就是ModelAndView中的Model，我们要单单使用这个对象的话，我们要如何来实现向request域对象共享数据呢？与ServletAPI相似，在控制器形参的位置创建一个Model对象的形参变量，在控制器方法体中通过形参变量调用addAttribute方法设置request域对象中的共享数据，代码如下：

```html
<a th:href="@{/testModel}">测试请求域对象</a>
<p>request请求域中的数据为<span th:text="${testRequestScope}"></span></p>
```

```java
@RequestMapping("/testModel")
public String testModel(Model model){
    model.addAttribute("testRequestScope", "hello,Model");
    return "success";
}
```

> 在此处，我们设置视图的方式与之前是一样的，返回类型为String，交由Thymeleaf解析

## 4、使用map向request域对象共享数据

> 使用完Model向request域对象中存放共享数据，我们也可以通过Map集合的方式来向request域对象中存放共享数据

```html
<a th:href="@{/testMap}">测试请求域对象</a>
<p>request请求域中的数据为<span th:text="${testRequestScope}"></span></p>
```

```java
@RequestMapping("/testMap")
// 键一般是字符串类型，值一般是各种各样的数据，所以泛型使用<String,Object>
public String testMap(Map<String, Object> map){
    // 在此处，我们往map集合中所存储的键值对数据，就是我们向request域对象中存放的共享数据
    map.put("testRequestScope", "hello,Map");
    return "success";
}
```

## 5、使用ModelMap向request域对象共享数据

> ModelMap的用法，与Model很像

```html
<a th:href="@{/testModelMap}">测试请求域对象</a>
<p>request请求域中的数据为<span th:text="${testRequestScope}"></span></p>
```

```java
@RequestMapping("/testModelMap")
public String testModelMap(ModelMap modelMap){
    modelMap.addAttribute("testScope", "hello,ModelMap");
    return "success";
}
```

## 控制器方法执行后都会返回统一的ModelAndView对象（源码）

> 不管我们使用以上1、2、3、4、5哪一种方式向request域对象中存放共享数据，在底层中，最终都会将其封装为一个ModelAndView对象

1. IDEA----DEBUG模式介绍![image-20211110200155416](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110200155416.png)

### ServletAPI方式源码描述

> 使用ServletAPI方式向request域对象共享数据，DispatcherServlet前端控制器中的源码如下：

![image-20211110201340529](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110201340529.png)

> 因为使用的是原生ServletAPI向request域对象共享数据，所以在此处model={}，但使用的return "success"返回的视图名称仍然会被封装为一个view

### ModelAndView方式源码描述

> 使用ModelAndView方式向request域对象共享数据，DispatcherServlet前端控制器中的源码如下：

![image-20211110202119110](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110202119110.png)

> 使用ModelAndView方式向request域对象共享数据，model有值，view也有值，model指的是往请求域中共享的数据

### Model方式源码描述

> 使用Model方式向request域对象共享数据，DispatcherServlet前端控制器中的源码如下：

![image-20211110202410121](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110202410121.png)

> 也是挺好使的，也是将数据封装到了ModelAndView中

### Map方式源码描述

> 使用Map方式向request域对象共享数据，DispatcherServlet前端控制器中的源码如下：

![image-20211110202519188](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110202519188.png)

> 所以当我们使用Map方式向request域对象共享数据，最终也是一个ModelAndView

### ModelMap源码描述

> 使用Map方式向request域对象共享数据，DispatcherServlet前端控制器中的源码如下：

![image-20211110202656527](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211110202656527.png)

> **所以说不管我们使用`ServletAPI、ModelAndView、Model、Map、ModelMap`这五种方式的任何一种方式，最终呢，我们都是要将request域对象中的共享数据，和视图名称封装在ModelAndView中的**

## 6、Model、Map、ModelMap之间的关系

> Model、Map、ModelMap类型的参数其实本质上都是 BindingAwareModelMap 类型的，BindingAwareModelMap 类是这三者的实现类

```java
// Model是一个接口
public interface Model{}

// ModelMap类间接继承了Map接口
public class ModelMap extends LinkedHashMap<String, Object> {}

// ExtendedModelMap类继承了ModelMap实现了Model接口
public class ExtendedModelMap extends ModelMap implements Model {}

// BindingAwareModelMap就可以作为Model、Map、ModelMap类型的实现类了
public class BindingAwareModelMap extends ExtendedModelMap {}
```

> 关系就是
>
> + ModelMap是Map的子类
> + BindingAwareModelMap是Model、Map、ModelMap它们三个共同的实现类

## 7、向session域中共享数据

> **在这种方式和以下的方式我们都建议使用ServletAPI的方式来设置域对象的共享数据**
>
> 其实SpringMVC也提供了一个@SessionAttribute注解，就是数据共享在请求域中的时候，再复制一份共享在session域中，所以不太好用，所以在session中和application中共享数据，建议使用Servlet原生API的方式来实现

```html
<a th:href="@{/testSession}">测试Session请求域对象</a>
<p>session请求域中的数据为<span th:text="${session.testSessionScope}"></span></p>
```

```java
@RequestMapping("/testSession")
public String testSession(HttpSession session){
    session.setAttribute("testSessionScope", "hello,session");
    return "success";
}
```

## 8、向application域中共享数据

> application就是ServletContext对象，要在ServletContext对象中设置共享数据，就要获取到该对象，获取该对象有很多种方式
>
> + 在JSP中使用pageContext获取
> + 使用request对象获取
> + 使用session获取
> + 使用servletConfig对象获取
>
> 在这里，我们使用session来获取ServletContext对象，并在其中设置共享数据

```html
<a th:href="@{/testApplication}">测试Session请求域对象</a>
<p>application请求域中的数据为<span th:text="${application.testApplicationScope}"></span></p>
```

```java
@RequestMapping("/testApplication")
public String testApplication(HttpSession session){
    // 获取ServletContext对象
	ServletContext application = session.getServletContext();
    application.setAttribute("testApplicationScope", "hello,application");
    return "success";
}
```

> application范围太大了，除非迫不得已需要在整个工程中需要去共享数据，我们才会放到application中，一般情况下，我们使用的都是request和session
>
> 使用request共享数据的场景
>
> + 列表的功能
> + 修改、回显功能
> + 错误信息提示功能，这一次错了我们就保存这一次的错误信息，下一次不一定还会错啊，所以就不需要将其记录在更大的域对象中
>
> 使用session共享数据的场景
>
> + 保存用户的登录状态：我登陆成功之后，只要浏览器不关，在当前的会话中就一直是登录成功的状态的，除非超过三十分钟没有访问session中的数据，那么session就会自动失效，session的最大不操作时间，默认30分钟，一般我们也不修改它，因为30分钟比较合理

# 六、SpringMVC的视图

> 学完了共享数据，按照我们处理请求的过程，下一步就应该要来实现页面跳转了，跳转到指定页面，就是我们要响应到浏览器的信息，就比如说，我们的转发和重定向是不是都会以一个页面为响应的结果响应到浏览器
>
> 这里我们要学习的就是SpringMVC的视图，对应的就是ModelAndView中的View，既然Model可以单独存在，如Model可以单独作为控制器参数向request域对象中设置共享数据，那么我们的View也是可以单独存在的，而这个View指的就是我们SpringMVC视图的接口，它的作用就是渲染数据，将模型Model中的数据展示给用户

+ SpringMVC中的视图是View接口，视图的作用渲染数据，将模型Model中的数据展示给用户

+ SpringMVC视图的种类很多，**默认**有：前两种是默认的视图
  + `转发视图(InternalResourceView)`
  + `重定向视图(RedirectView)`
  + 若当工程引入jstl的依赖，**转发视图**会自动转换为`JstlView`，此时大家所看到的所有的转发视图就都是`JstlView`
  + 但我们现在不使用JSP了，我们使用的是Thymeleaf，若使用的视图技术为Thymeleaf，在SpringMVC的配置文件中配置了Thymeleaf的视图解析器，视图由此视图解析器解析之后所得到的是`ThymeleafView`，但是并不是所有的情况，你获取的都是一个`ThymeleafView`，因为我们在设置视图的时候有三种方式
    1. return "success"，这种方式没有添加任何前缀，直接就是一个视图名称
    2. return "forward:/success"，这种方式以`forward:`为前缀
    3. return "redirect:/success"：这种方式以`redirect:`为前缀
    
    > 所以，只有当你的视图被Thymeleaf视图解析器解析之后，创建的才是ThymeleafView，就是当前的第一种方式，即返回的视图名称**没有任何前缀**的时候，才会被Thymeleaf的视图解析器解析，这个时候创建的才是**ThymeleafView**
    >
    > 如果使用以`forward:`为前缀的视图名称，那么这个时候创建的就是一个**转发视图**
    >
    > 如果使用以`redirect:`为前缀的视图名称，那么这个时候创建的就是一个**重定向视图**
    >
    > **所以创建的视图对象是什么，只跟视图名称有关系**

## 1、ThymeleafView

> 当控制器方法中所设置的**视图名称没有任何前缀**时，此时的视图名称会被SpringMVC配置文件中所配置的视图解析器解析，视图名称拼接视图前缀和视图后缀所得到的最终路径，会**通过转发的方式实现跳转**

```java
@RequestMapping("/testHello")
public String testHello(){
    return "hello";
}
```

> 源码如下，是通过viewName来解析并创建出的视图对象

![](SpringMVCNotes_images/img002.png)

## 2、转发视图

> 其实我们能够使用的前缀也就两个
>
> + `forward:`
> + `redirect:`
>
> `forward:`的作用是用来干什么的？比如说，大家都知道，我们当前所访问的好多页面其实有的时候是不满足我们当前Thymeleaf视图解析器解析的规则的，比如我有如下需求：
>
> ```java
> @RequestMapping("/testThymeleafView")
> public String testThymeleafView(){
>     return "success";
> }
> 
> // 通过这个控制器方法，让他转发到/testThymeleafView请求中，我们转发可以转发到一个页面，也可以转发到一个请求，所以这是可行的，但是怎么实现呢？
> @RequestMapping("/testForward")
> public Strnig testForward(){
>     return "/testThymeleafView"; // 如果这样写，那么根据Thymeleaf视图解析器的解析规则，就会去匹配相对应的页面，所以说有的时候，在我们实现转发的效果的时候，我们所访问的页面，或者说我们最终要转发到的这个资源，它可能并不符合Thymeleaf视图解析器的解析规则，所以只能往下走咯 ↓
> }
> ```

+ SpringMVC中默认的转发视图是`InternalResourceView`

+ SpringMVC中创建转发视图的情况：

  > 当控制器方法中所设置的视图名称以`forward:`为前缀时，创建`InternalResourceView`视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀`forward:`去掉，**剩余部分作为最终路径通过转发的方式实现跳转**

+ 例如"forward:/"，"forward:/employee"

  ```java
  // 第一个方法
  @RequestMapping("/testThymeleafView")
  public String testThymeleafView(){
      return "success";
  }
  
  // 第二个方法
  @RequestMapping("/testForward")
  public String testForward(){
      // 添加上转发前缀，再添加上当前要转发的资源，该资源不可以是一个页面，如果希望访问的是一个页面，要么你就不加 forward: 前缀，要么你就转发到一个能跳转到页面的请求去
      return "forward:/testThymeleafView";
  }
  
  // 当我浏览器访问首先 /testForward 时，步骤是这样的
  /*
  	首先 forward:/testThymeleafView 在被解析到时，会将前缀截掉，剩下的 /testThymeleafView 部分直接通过转发跳转到相对应的资源，这个时候，我们创建的视图就不再是ThymeleafView了，当然啦，在这整个的过程中，它会帮我们创建两次视图，首先第一次就是解析 forward:/testThymeleafView 创建转发视图InternalResourceView，因为当前 /testThymeleafView 也是一个请求，所以会匹配第一个方法的请求映射，第一个方法返回的视图名称success 正好符合Thymeleaf视图解析器的解析规则，所以会创建一个ThymeleafView
  */
  ```

![image-20210706201316593](SpringMVCNotes_images/img003.png)

> 这个转发视图说一般使用的不是很多，但是重定向使用的很多，因为重定向可以改变浏览器地址栏中的地址，相当于浏览器再一次发送了请求，就比如说我们添加、修改、删除成功之后，和我们原来的请求就没有关系了，所以这个时候就需要通过重定向来跳转到我们的下一个功能了，如果还使用转发，那么地址栏中就还是保留了上一次请求，比如删除时候的请求地址，所以说在我们的业务逻辑操作成功之后，我们都要通过重定向来实现页面跳转

## 3、重定向视图

> 转发和重定向的区别：
>
> + 转发为浏览器发送一次请求；重定向为浏览器发送了两次请求，第一次访问Servlet，第二次访问重定向的地址，所以**重定向默认就是GET请求**
> + 转发后浏览器地址栏不变；重定向会改变浏览器地址栏的地址为重定向后的地址
> + 转发可以获取请求域中的数据；重定向不行
> + 转发能访问WEB-INF中的资源；重定向不行
> + 转发不可以跨域；重定向可以【转发是在服务器内部发生的，所以只能访问服务器内部的资源；重定向是浏览器发出的，那么浏览器可以访问任何资源，比如我在我当前的工程中使得浏览器访问到百度，这就是跨域】

+ SpringMVC中默认的重定向视图是RedirectView

  > 当控制器方法中所设置的视图名称以"redirect:"为前缀时，创建RedirectView视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀"redirect:"去掉，剩余部分作为最终路径通过重定向的方式实现跳转

+ 例如"redirect:/"，"redirect:/employee"

```java
@RequestMapping("/testRedirect")
public String testRedirect(){
    return "redirect:/testHello";
}
```

![image-20210706201602267](SpringMVCNotes_images/img004.png)

> 注：
>
> 重定向视图在解析时，会先将redirect:前缀去掉，然后会判断剩余部分是否以/开头，若是则会自动拼接上下文路径

## 4、视图控制器view-controller

> 这个指的是我们SpringMVC配置文件中的一个标签，作用就是帮助我们实现请求地址和视图之间的一个映射关系的，比如我们下面这个请求映射关系
>
> ```java
> @RequestMapping("/")
> public String accessIndex(){
>     return "index";
> }
> ```
>
> 这种只进行跳转操作的请求映射关系，不需要有任何的请求处理操作，只需要写一个请求映射对应一个请求路径，在其对应的控制器方法中返回一个视图名称即可，**像这种在控制器方法中不需要有其它请求的过程的处理的时候，我们就可以通过使用视图控制器view-controller标签来处理这个请求映射的关系**

> 当控制器方法中，**仅仅用来实现页面跳转**，即只需要设置视图名称时，可以将处理器方法使用view-controller标签进行表示：在SpringMVC配置文件中配置如下内容

```xml
<!--
	path：设置处理的请求地址，与@RequestMapping注解的value属性相同
	view-name：设置请求地址所对应的视图名称，没有前缀的视图名称还是交由Thymeleaf来解析
-->
<mvc:view-controller path="/" view-name="index"></mvc:view-controller>
```

> 注：
>
> 当SpringMVC中设置任何一个`view-controller`时，其他控制器中的请求映射将全部失效，此时需要在SpringMVC的核心配置文件中**设置开启mvc注解驱动**的标签：这个标签的功能非常多，因而该标签也是非常重要的，essential！
>
> `<mvc:annotation-driven />`
>
> 功能非常多，比如说：我们马上要学习的RESTful案例中，我们是由JS、CSS文件要使用的，这个时候就需要开放我们对静态资源的访问，这个时候我们需要用到的一个标签就是默认的Servlet来处理静态资源，与我们当前`view-controller`情况非常相似，如果我们加上了默认的Servlet处理静态资源的话，那仅仅只有我们的静态资源能够被处理，我们控制器方法所对应的请求映射也会全部失效，那这个时候就同样需要来配置`<mvc:annotation-driven />`，可见它多么重要
>
> 还有就是之后我们要学习的将Java对象转换为JSON对象，这个时候我们同样需要设置我们的`<mvc:annotation-driven />`，这个时候对象转换才能实现效果，所以**这个驱动是必配的**一个标签
>
> **但是还是要注意在不同情况下，配置这个驱动它具体的功能是什么，这个一定要有清晰的认识！**

## 5、InternalResourceViewResolver

> 我们在之前，SpringMVC所使用的视图解析器都是Thymeleaf视图解析器，因为我们现在所使用的技术就是Thymeleaf视图技术，其实在之前我们一直使用的是JSP，对于JSP，我们SpringMVC该如何进行访问呢？如果我们的视图使用的是JSP，那我们的SpringMVC该如何去解析我们JSP视图呢

**将Thymeleaf解析器换成InternalResourceViewResolver解析器即可**

```xml
<!--配置InternalResourceView视图解析器-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/templates/"></property>
    <property name="suffix" value=".jsp"></property>
</bean>
```

```java
@RequestMapping("/testJsp")
public String accessIndex(){
    // 没有任何前缀和以forward:前缀开头的时候，SpringMVC创建的是InternalResourceView
    return "success";
    // 当以redirect:前缀开头的时候创建的就是RedirectView
}
```

> 所以在当前没有Thymeleaf视图解析器的工程中，创建转发视图的方式有两种，就是不加前缀和加`forward:`前缀

# 七、RESTful

> 我们学习了SpringMVC中的视图，我们就知道了在什么情况下创建了什么视图，创建的视图类型是什么，最重要的是，我们知道了该怎么转发，该怎么重定向，其实在之前，我们用的没有任何前缀的视图名称也是转发，但是要注意，我们当前这个转发是满足我们视图解析器的前缀与后缀，也就是说该视图名称是满足视图解析器解析的规则的，我们才可以不加任何的前缀；但是如果我们要访问的请求不符合我们当前视图解析器的前缀和后缀，那我们就需要加上相对应的前缀（forward: 或 redirect:）了

## 1、RESTful简介

> RESTful是一种风格，是一种软件架构的风格，什么是风格？风格其实就是一种格式，所以这个东西用也行，不用也行，但是在之后开发的过程中，RESTful风格一定是用的最多的
>
> 那什么是REST呢？REST指的是表现层（表述层，即视图层和控制层，前端页面到我们后端的Controller层就是我们的表现层）资源状态转移，这个资源又怎么理解呢？我们原来在学习面向对象的时候，知道了万物皆对象，当我们学习了Tomcat服务器之后，我们将Web工程放到服务器上的过程叫做部署，当我们将工程部署到Tomcat服务器上之后，那么我们当前工程中的内容在服务器上它都叫做资源，及万物皆资源，比如一个类、HTML页面、JSP页面、JS、css、图片，它们全部都是资源，但是这些资源的状态是互不相同的，资源的状态指的就是当前资源的一个表现形式，就比如说我们当前所访问的资源可以是一个HTML页面，也可以是一个CSS样式，也可以是JS脚本，也可以是一张图片，也可以是视频或音频，我们访问的资源不一样，资源所对应状态就不一样，即表现就不一样；那什么是状态转移呢？向下走 ↓

REST：**Re**presentational **S**tate **T**ransfer，表现层资源状态转移

### a>资源

> 资源是一种看待服务器的方式，即，将服务器看作是由很多离散的资源组成。每个资源是服务器上一个可命名的抽象概念。因为资源是一个抽象的概念，所以它不仅仅能代表服务器文件系统中的一个文件、数据库中的一张表等等具体的东西，可以将资源设计的要多抽象有多抽象，只要想象力允许而且客户端应用开发者能够理解。与面向对象设计类似，资源是以名词为核心来组织的，首先关注的是名词。一个资源可以由一个或多个URI来标识。URI既是资源的名称，也是资源在Web上的地址。对某个资源感兴趣的客户端应用，可以通过资源的URI与其进行交互。

### b>资源的表述

> 资源的表述是一段对于资源在某个特定时刻的状态的描述。可以在客户端-服务器端之间转移（交换）。资源的表述可以有多种格式，例如HTML/XML/JSON/纯文本/图片/视频/音频等等。资源的表述格式可以通过协商机制来确定。请求-响应方向的表述通常使用不同的格式。

### c>状态转移

> 状态转移说的是：在客户端和服务器端之间转移（transfer），客户端和服务器端之间的转移就是浏览器发送请求到服务器，我请求的是什么，服务器就要给我响应什么，这个时候就要将服务器当中的资源，转移到浏览器(客户端)，代表资源状态的表述，通过转移(浏览器请求服务器)和“操作资源的表述”(即请求路径)，来间接实现操作资源的目的

> 说了这么多，无非描述的就是当前浏览器和服务器进行交互的一种状态一种方式，将当前所访问的内容由服务器转移到浏览器客户端，我们所访问什么，服务器就给我们什么，但是在这里面，有一个非常重要的问题，我们在此之前访问资源的方式五花八门各种各样，就比如获取一个用户信息，我们浏览器发送请求来请求服务器资源的时候，我们都是使用一个URL叫做统一资源定位符，也就是请求路径，而我们在请求路径中就表示我当前我要干什么，我要来访问的资源是什么，比如selectUserById、deleteUserById...
>
> 但是我们既然我们的工程已经部署到了Tomcat服务器上之后，那我们的某一个事物对应的就必须是同一个资源，不管是添加还是删除还是修改用户信息，我们所操作的资源理应都是用户信息，**也就是浏览器的请求路径是不会变的，就是操作方式在变**，所以当我们使用了RESTful之后，既然我们操作的资源相同，那我们当前请求路径就一样，那当我们的请求路径一样，我们要怎么来表示我们当前如何去操作这些资源呢？

## 2、RESTful的实现

> 现在好多好多互联网的网站使用的都是RESTful风格的资源操作方式
>
> 那既然我们的请求路径是一样的，那我们怎么来表示我们操作资源的方式呢？
>
> 具体说，就是在 HTTP 协议里面，有四个表示操作（请求）方式的动词：`GET、POST、PUT、DELETE`这四个动词就代表了我们当前操作资源的方式
>
> + GET：查询资源(数据)
> + POST：添加资源(数据)
> + PUT：修改资源(数据)
> + DELETE：删除资源(数据)
>
> 所以为什么要使用RESTful？其实就是就是为了统一使用，有一个统一的规则，统一使用了RESTful之后，我们就不会因为我们要对一个资源进行操作的功能，考虑该功能如何去命名而产生分歧
>
> **RESTful说白了就是通过同一个请求路径、不同的请求方式、进行不同的操作**，这个时候请求路径就是统一的

+ 它们分别对应四种基本操作：**GET 用来获取资源(数据)，POST 用来新建资源(数据)，PUT 用来更新资源(数据)，DELETE 用来删除资源(数据)**

+ REST 风格**提倡 URL 地址使用统一的风格设计**，从前到后各个单词使用斜杠分开，**不使用问号键值对方式携带请求参数**，而是将要发送给服务器的**数据作为 URL 地址的一部分**，以**保证整体风格的一致性**

| 操作     | 传统方式         | REST风格                |
| -------- | ---------------- | ----------------------- |
| 查询操作 | getUserById?id=1 | user/1-->get请求方式    |
| 保存操作 | saveUser         | user-->post请求方式     |
| 删除操作 | deleteUser?id=1  | user/1-->delete请求方式 |
| 更新操作 | updateUser       | user-->put请求方式      |

### 使用RESTful模拟操作用户资源

1. html页面

   ```html
   <a th:href="@{/user}">使用GET方式查询用户信息</a><br>
   <a th:href="@{/user/1}">使用GET方式查询用户id为1的用户信息</a>
   <form th:action="@{/user}" method="post">
       username: <input type="text" name="username"><br>
       password: <input type="password" name="password"><br>
       <input type="submit" value="提交">
   </form>
   ```

2. Controller层处理请求

   ```java
   @Controller
   @RequestMapping("/user") //统一处理的都是用户信息user
   public class UserController {
       @GetMapping // GET请求处理user
       public String getAllUser() {
           System.out.println("查询所有用户信息");
           return "success";
       }
   
       @GetMapping("/{id}") // GET请求+id参数处理user
       public String getUserById(@PathVariable("id") int id) {
           System.out.println("查询id为" + id + "的用户信息");
           return "success";
       }
   
       @PostMapping // POST请求处理user
       public String addUser(@RequestParam("username") String username, @RequestParam("password") String password) {
           System.out.println("插入用户信息");
           System.out.println(username);
           System.out.println(password);
           return "success";
       }
   }
   ```

   > 可以看到GET请求和POST请求浏览器是可以发送的，那么PUT请求和DELETE请求，浏览器没有，那怎么办，那么就要使用到我们的`HiddenHttpMethodFilter`过滤器了 ↓

## 3、HiddenHttpMethodFilter

> 我们不能直接在form表单的method属性中直接指定PUT和DELETE请求方式，form表单的method的属性只能支持GET和POST，如果指明为PUT和DELETE请求方式就默认会变为GET请求方式，当然Ajax可以发送PUT和DELETE请求，但是仅支持部分浏览器使用，所以也不太好使，**所以我们需要使用SpringMVC为我们提供的`HiddenHttpMethodFilter`过滤器来转换请求**

+ 由于浏览器只支持发送get和post方式的请求，那么该如何发送`put`和`delete`请求呢？

+ SpringMVC 提供了 **HiddenHttpMethodFilter** 帮助我们**将 POST 请求转换为 DELETE 或 PUT 请求**

+ **HiddenHttpMethodFilter** 处理`put`和`delete`请求的**条件**：

  + 当前请求的请求方式必须为`post`

  + 当前请求必须传输请求参数`_method`

+ 满足以上条件，**HiddenHttpMethodFilter** 过滤器就会**将当前请求的请求方式转换为请求参数**_method的值，因此**请求参数\_method的值才是最终的请求方式**

+ 在`web.xml`中注册**HiddenHttpMethodFilter**，从名字中就可以看出，这个过滤器的作用就应该是一个隐藏的HTTP的请求方式

```xml
<!--配置请求方式转换过滤器-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <!--可对所有请求生效-->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

1. 配置完过滤器之后，我们就来HTML页面根据该过滤器的条件来发送一个POST请求进行一个**修改PUT操作**

   ```html
   <form th:action="@{/user}" method="post">
       <!--设置请求参数，因为这个input不与用户交互，所以可设置为隐藏域，value值可大写可小写，因为底层过滤器源码中最终会将其转换为大写-->
       <input type="hidden" name="_method" value="put">
       username: <input type="text" name="username"><br>
       password: <input type="password" name="password"><br>
       <input type="submit" value="提交">
   </form>
   ```

2. 后端接收请求进行PUT修改操作

   ```java
   @Controller
   @RequestMapping("/user") // 匹配请求路径
   public class UserController {
       // 匹配PUT请求
       @PutMapping
       public String updateUser(@RequestParam("username") String username, @RequestParam("password") String password){
           System.out.println("PUT方式修改用户信息");
           return "success";
       }
   }
   ```

3. 此时我们要进行一个删除DELETE操作，显然删除操作应当是一个超链接，那么应该如何来处理，如下

   > 通过Vue在超链接上绑定一个点击事件，在事件中先阻止超链接的默认行为，即先阻止超链接的默认跳转，然后再获取当前某一个表单，这个表单中我们不需要有任何的数据，只需要写一个form表单，然后将其请求方式设置为post，里面写一个隐藏域name="_method" value="delete"，提交按钮不需要写，因为我们现在要做的就是通过超链接去控制表单的提交，因为转换请求方式的过滤器的必要条件就是请求方式必须为POST，那么我们不适用Ajax的情况下，我们只能通过表单method="POST"，才是POST方式，所以我们要做的就是**通过超链接的方式控制表单的提交**，这个放到后面`RESTful案例章节`来演示操作

> 注：当我们配置了多个过滤器的时候，过滤器执行的先后顺序由filter-mapping的定义顺序决定，先配置了filter-mapping的过滤器先执行，即你过滤器的filter-mapping配置得越靠前，那么该过滤器则先于其他过滤器执行
>
> 目前为止，SpringMVC中提供了两个过滤器：`CharacterEncodingFilter`和`HiddenHttpMethodFilter`
>
> **在web.xml中注册时，必须先注册`CharacterEncodingFilter`，再注册`HiddenHttpMethodFilter`**，所以我们要将HiddenHttpMethodFilter的filter-mapping配置在CharacterEncodingFilter对应的filter-mapping之后即可
>
> 原因：
>
> - 在 CharacterEncodingFilter 中通过 request.setCharacterEncoding(encoding) 方法设置字符集的
>
> - request.setCharacterEncoding(encoding) 方法要求**前面不能有任何获取请求参数的操作**
>
> - 而 HiddenHttpMethodFilter 恰恰有一个获取请求方式的操作：即获取了隐藏域中的`_method`参数
>
> - ```java
>   String paramValue = request.getParameter(this.methodParam);
>   ```
>
> + 所以**需要将它们按配置顺序进行初始化**

![image-20211111132632699](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111132632699.png)

# 八、RESTful案例

### 1、准备工作

和传统 CRUD 一样，实现对员工信息的增删改查

- 搭建环境

- 准备实体类，Employee员工类对象

  ```java
  public class Employee {
      private Integer id;
      private String lastName;
  
      private String email;
      //1 male, 0 female
      private Integer gender;
  
      public Integer getId() {
          return id;
      }
  
      public void setId(Integer id) {
          this.id = id;
      }
  
      public String getLastName() {
          return lastName;
      }
  
      public void setLastName(String lastName) {
          this.lastName = lastName;
      }
  
      public String getEmail() {
          return email;
      }
  
      public void setEmail(String email) {
          this.email = email;
      }
  
      public Integer getGender() {
          return gender;
      }
  
      public void setGender(Integer gender) {
          this.gender = gender;
      }
  
      public Employee(Integer id, String lastName, String email, Integer gender) {
          super();
          this.id = id;
          this.lastName = lastName;
          this.email = email;
          this.gender = gender;
      }
  
      public Employee() {
      }
  }
  ```
  
- 准备dao层模拟数据

  ```java
  package com.atguigu.mvc.dao;
  
  import java.util.Collection;
  import java.util.HashMap;
  import java.util.Map;
  
  import com.atguigu.mvc.bean.Employee;
  import org.springframework.stereotype.Repository;
  
  
  @Repository
  public class EmployeeDao {
  
     private static Map<Integer, Employee> employees = null;
     
     static{
        employees = new HashMap<Integer, Employee>();
  
        employees.put(1001, new Employee(1001, "E-AA", "aa@163.com", 1));
        employees.put(1002, new Employee(1002, "E-BB", "bb@163.com", 1));
        employees.put(1003, new Employee(1003, "E-CC", "cc@163.com", 0));
        employees.put(1004, new Employee(1004, "E-DD", "dd@163.com", 0));
        employees.put(1005, new Employee(1005, "E-EE", "ee@163.com", 1));
     }
     
     private static Integer initId = 1006;
     
     public void save(Employee employee){
        if(employee.getId() == null){
           employee.setId(initId++);
        }
        employees.put(employee.getId(), employee);
     }
     
     public Collection<Employee> getAll(){
        return employees.values();
     }
     
     public Employee get(Integer id){
        return employees.get(id);
     }
     
     public void delete(Integer id){
        employees.remove(id);
     }
  }
  ```
  
- 因为当前没有使用Service层，所以直接使用Controller层直接调用Dao层，所以要在Controller层中注入Dao层的对象

  ```java
  @Controller
  @RequestMapping("/employee")
  public class EmployeeController {
      @Autowired
      private EmployeeDao employeeDao; // 注入Dao层对象
  }
  ```

  

### 2、功能清单

| 功能                | URL 地址    | 请求方式 |
| ------------------- | ----------- | -------- |
| 访问首页√           | /           | GET      |
| 查询全部数据√       | /employee   | GET      |
| 删除√               | /employee/2 | DELETE   |
| 跳转到添加数据页面√ | /toAdd      | GET      |
| 执行保存√           | /employee   | POST     |
| 跳转到更新数据页面√ | /employee/2 | GET      |
| 执行更新√           | /employee   | PUT      |

### 3、具体功能：访问首页

1. 在SpringMVC中配置view-controller，因为它仅仅只是一个跳转功能，没有处理请求的过程方法体

   ```xml
   <mvc:view-controller path="/" view-name="index"/>
   ```

2. 首页页面如下

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8" >
       <title>Title</title>
   </head>
   <body>
   <h1>首页</h1>
   <a th:href="@{/employee}">访问员工信息</a>
   </body>
   </html>
   ```

### 4、具体功能：查询所有员工数据

1. 控制器方法

   ```java
   @RequestMapping(value = "/employee", method = RequestMethod.GET)
   public String getEmployeeList(Model model){
       Collection<Employee> employeeList = employeeDao.getAll();
       model.addAttribute("employeeList", employeeList);
       return "employee_list";
   }
   ```

2. 创建employee_list.html，显示查询出的所有的员工数据

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>Employee Info</title>
       <script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
   </head>
   <body>
   
       <table border="1" cellpadding="0" cellspacing="0" style="text-align: center;" id="dataTable">
           <tr>
               <th colspan="5">Employee Info</th>
           </tr>
           <tr>
               <th>id</th>
               <th>lastName</th>
               <th>email</th>
               <th>gender</th>
               <th>options(<a th:href="@{/toAdd}">add</a>)</th>
           </tr>
           <tr th:each="employee : ${employeeList}">
               <td th:text="${employee.id}"></td>
               <td th:text="${employee.lastName}"></td>
               <td th:text="${employee.email}"></td>
               <td th:text="${employee.gender}"></td>
               <td>
                   <a class="deleteA" @click="deleteEmployee" th:href="@{'/employee/'+${employee.id}}">delete</a>
                   <a th:href="@{'/employee/'+${employee.id}}">update</a>
               </td>
           </tr>
       </table>
   </body>
   </html>
   ```

### 5、具体功能：删除

1. 在employ_list.html中创建处理delete请求方式的表单

   ```html
   <!-- 作用：通过超链接控制表单的提交，将post请求转换为delete请求 -->
   <form id="delete_form" method="post">
       <!-- HiddenHttpMethodFilter要求：必须传输_method请求参数，并且值为最终的请求方式 -->
       <input type="hidden" name="_method" value="delete"/>
   </form>
   ```

2. 删除超链接绑定点击事件

   1. 在employ_list.html中引入vue.js，当然也可以使用原生JS，这里我们使用Vue，将Vue.js文件放在webapp/static/js目录下，目录需要手动创建，切记不要放在WEB-INF目录下，不然只能通过转发访问![image-20211111154120222](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111154120222.png)

      ```html
      <!--在employ_list.html中在引入vue.js-->
      <script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
      ```
   
   2. 删除超链接，在路径后拼接时，需要先将前面的路径使用`''`单引号引起来，解析完路径再解析值，这样就不会把`${employee.id}`一起当成路径解析了，就可以实现路径后拼接的是解析后的id值，那么就满足我们的RESTful风格了
   
      ```html
      <a class="deleteA" @click="deleteEmployee" th:href="@{'/employee/'+${employee.id}}">delete</a>
      ```
   
   3. 通过vue处理点击事件
   
      ```html
      <script type="text/javascript">
          var vue = new Vue({
              el:"#dataTable",
              methods:{
                  //event表示当前事件
                  deleteEmployee:function (event) {
                      //通过id获取表单标签
                      var delete_form = document.getElementById("delete_form");
                      //将触发事件的超链接的href属性为表单的action属性赋值
                      delete_form.action = event.target.href;
                      //提交表单
                      delete_form.submit();
                      //阻止超链接的默认跳转行为
                      event.preventDefault();
                  }
              }
          });
      </script>
      ```
      
      > 进一步剖析：
      
      ```js
      <script type="text/javascript">
          // new 一个Vue容器，{}中则是它的属性，首先要绑定Vue的容器（我们要设置的容器必须要包括我们要操作的元素节点），
          // 因为我们当前要操作的超链接节点在table中标签中，所以可以在table标签中设置一个id属性，比如叫做dataTable
          var vue = new Vue({
              el: "#dataTable", // 获取到该容器，然后在删除的超链接中添加点击事件@Click="deleteEmployee"
              methods: { // 使用methods属性来声明点击事件执行的函数
                  deleteEmployee: function (event) { //event表示当前的点击事件传入的参数
                      // 1.获取当前form表单  【要获取肯定要在form表单上做个标记id，如form表单的id="formForDelete"】
                      var formForDelete = document.getElementById("formForDelete");
                      /* 2.为该表单的action属性赋值为当前超链接的href属性所对应的地址  【因为我们的form表单没有显式指明action属性，没有action我们要先给action属性赋值，
                       表单才能够进行提交，当我们不设置action的话，表单会默认提交到当前页面，即如果一个表单的action没有值或者没有指明action属性，
                       那我们表单提交后会提交到当前页面，所以我们一定要给表单的action属性赋值】*/
                      formForDelete.action = event.target.href; // 将表单action属性赋值为当前触发点击事件的目标元素（即超链接）的属性href的值【因为th:href是先被Thymeleaf先解析的，解析之后就是href属性，所以在这里可以这样获取】
                      // 3. 提交表单 【action有值之后就可以进行表单提交了】
                      formForDelete.submit();
                      // 4. 阻止超链接的跳转行为【取消超链接的默认行为】
                      event.preventDefault();
                  }
              }
          });
      </script>
      ```

3. Controller层控制器方法

   ```java
   @RequestMapping(value = "/employee/{id}", method = RequestMethod.DELETE)
   public String deleteEmployee(@PathVariable("id") Integer id){
       employeeDao.delete(id);
       return "redirect:/employee";
   }
   ```

> 这里说啊，Vue找不到

![image-20211111162321676](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111162321676.png)

> 因为我们的静态资源请求被我们的前端控制器处理了，而前端控制器拿着这个请求去找了我们控制层中的请求映射了，找不到他就会404![image-20211111162533667](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111162533667.png)
>
> 所以在这里我们不能将静态资源交给前端控制器来处理，我们在JavaWeb阶段讲过一个默认的Servlet，叫做DefaultServlet，这个东西才是处理我们静态资源的Servlet，所以要在SpringMVC配置文件中添加一个标签`<mvc:default-servlet-handler/>`

```xml
<!--开放对静态资源的访问-->
<mvc:default-servlet-handler/>
```

> 该标签的对静态资源的处理过程如下：
>
> ​	我们的静态资源在被请求访问的时候，会先被前端控制器所处理，如果前端控制器在Controller层中找不到该请求相对应的请求映射，他就会交给我们默认的Servlet来处理，如果默认的Servlet能够找到相对应的资源，我们就可以访问资源，如果说默认的Servlet都找不到对应的资源的话，那就报错404
>
> 有一点要注意的是，当SpringMVC也就是我们的前端控制器找不到资源，将该请求转交给默认的Servlet处理的时候，就不会在IDEA output控制台输出404报错日志了，如果默认的Servlet也找不到，默认的Servlet会在output控制台输出404报错日志了，但大前提肯定是有日志依赖，不然啥也看不到

### 6、具体功能：跳转到添加数据页面

1. 配置view-controller

   ```xml
   <mvc:view-controller path="/toAdd" view-name="employee_add"></mvc:view-controller>
   ```

2. 创建employee_add.html

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>Add Employee</title>
   </head>
   <body>
   
   <form th:action="@{/employee}" method="post">
       lastName:<input type="text" name="lastName"><br>
       email:<input type="text" name="email"><br>
       gender:<input type="radio" name="gender" value="1">male
       <input type="radio" name="gender" value="0">female<br>
       <input type="submit" value="add"><br>
   </form>
   
   </body>
   </html>
   ```

### 7、具体功能：执行保存

控制器方法

```java
@RequestMapping(value = "/employee", method = RequestMethod.POST)
public String addEmployee(Employee employee){
    employeeDao.save(employee);
    return "redirect:/employee";
}
```

### 8、具体功能：跳转到更新数据页面

1. 修改超链接

   ```html
   <a th:href="@{'/employee/'+${employee.id}}">update</a>
   ```

2. 控制器方法实现更新之前的数据回显

   ```java
   @RequestMapping(value = "/employee/{id}", method = RequestMethod.GET)
   public String getEmployeeById(@PathVariable("id") Integer id, Model model){
       Employee employee = employeeDao.get(id);
       model.addAttribute("employee", employee);
       return "employee_update";
   }
   ```

3. 创建employee_update.html

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>Update Employee</title>
   </head>
   <body>
   
   <form th:action="@{/employee}" method="post">
       <input type="hidden" name="_method" value="put">
       <input type="hidden" name="id" th:value="${employee.id}">
       lastName:<input type="text" name="lastName" th:value="${employee.lastName}"><br>
       email:<input type="text" name="email" th:value="${employee.email}"><br>
       <!--
           th:field="${employee.gender}"的field即字段可用于单选框或复选框的回显
   th:field它会自动进行判断
           若单选框的value和employee.gender的值一致，则添加checked="checked"属性，以这种原理实现单选框和复选框的回显功能
       -->
       gender:<input type="radio" name="gender" value="1" th:field="${employee.gender}">male
       <input type="radio" name="gender" value="0" th:field="${employee.gender}">female<br>
       <input type="submit" value="update"><br>
   </form>
   
   </body>
   </html>
   ```

### 9、具体功能：执行更新

1. 控制器方法

   ```java
   @RequestMapping(value = "/employee", method = RequestMethod.PUT)
   public String updateEmployee(Employee employee){
       employeeDao.save(employee);
       return "redirect:/employee";
   }
   ```

## 处理静态资源的过程

> 在RESTful案例中，我们在处理静态资源的时候，我们的Vue.js有两次是找不到的，第一次给我们报的就是404，为什么是404，是因为我们最后才添加的\static\js\vue.js，在添加之前我们的工程已经运行过了，所以所我们之后添加进去的资源就不存在我们打包之后的结果里，也就是target目录中，所以我们需要执行Maven的重新打包即package
>
> 当我们重新打完包后，我们再次运行工程时，发现它还是找不到，为什么找不到呢？因为我们在JavaWeb阶段的时候，这个静态资源是交给默认的Servlet来处理的；这就得从Tomcat安装目录中的\conf\web.xml说起，**在Tomcat安装目录中的web.xml文件的作用范围是作用在所有部署到该Tomcat中的所有工程的**，而我们当前工程中的web.xml文件仅对我们当前工程有效，这时候如果我们当前工程中的web.xml文件的配置与Tomcat中的web.xml文件的配置发生了冲突，以我们当前工程中的配置为准，即就近原则；以下是Tomcat中web.xml的默认配置![image-20211111170539297](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111170539297.png)![image-20211111171054800](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111171054800.png)
>
> 但是前端控制器处理请求的原理明显不符合对静态资源请求的处理，因为前端控制器每一次都会将请求拿去Controller层中去匹配我们的请求映射，但是我们的控制器中没有定义对静态资源的访问，所以就找不到，就报错404
>
> 所以我们需要在SpringMVC的配置文件中开放对静态资源的访问，所以需要配置上默认的Servlet，即`<mvc:default-servlet-handler/>`，当然**要结合注解驱动标签一起使用**，如果没有配置注解驱动标签，那么当前所有的请求都会被默认的Servlet进行处理
>
> **配置完后，如果浏览器发送的请求，DispatcherServlet处理不了，就会转交给我们当前配置的DefaultServlet来进行处理，能处理则处理，处理不了就是404**

# 八、HttpMessageConverter

> 首先看名称就知道Http就是HTTP协议，里面包含了请求报文和响应报文，其次Message是信息，Converter是转换的意思，具体解释如下：

+ `HttpMessageConverter`，称为报文信息转换器，如将**请求报文转换为Java对象**，或将**Java对象转换为响应报文**

  > 请求报文就是浏览器发送给服务器，也就是一个request对象，在服务器中当然是通过Java语言来接收请求报文，所以就需要转换器咯
  >
  > 响应报文就是服务器响应给浏览器，也就是一个response对象，在浏览那里就只能接收响应报文，所以我们就应该将我们服务器中的Java对象转换为响应报文

+ `HttpMessageConverter`提供了**两个注解**和**两个类型**：@RequestBody，@ResponseBody，RequestEntity，ResponseEntity

  > 两个关于请求报文的：@RequestBody，RequestEntity
  >
  > 两个关于响应报文的：@ResponseBody，ResponseEntity

### 1、@RequestBody

> @RequestBody顾名思义是请求体，所以这个注解的作用就是将请求报文中的请求体转换为Java数据
>
> @RequestBody可以获取请求体，需要在控制器方法设置一个形参，使用@RequestBody进行标识，当前请求的请求体就会为当前注解所标识的形参赋值
>
> 请求报文长什么样？请求报文由三部分组成：请求头、请求空行、请求体三部分组成，但是我们的请求体只有在POST请求时才会携带，GET请求是没有请求体的，请求体中存放的其实就是请求参数数据，就像我们原来所说的，GET请求方式会将请求参数拼接在请求地址后，而POST不会，POST就是将请求参数放到了请求体中
>
> 所以**要想发送一个有请求体的请求，需要发送的请求的请求方式必须为POST请求**

```html
<!--请求方式一定得要是POST请求，所以我们使用form表单发送请求-->
<form th:action="@{/testRequestBody}" method="post">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    <input type="submit">
</form>
```

```java
@RequestMapping("/testRequestBody")
public String testRequestBody(@RequestBody String requestBody){
    System.out.println("requestBody:"+requestBody);
    return "success";
}
```

输出结果：

requestBody:username=admin&password=123456

> 不管使用的是GET还是POST，无非就是请求参数传输的位置不一样，但是它传输数据的格式是一样的，都是name=value&name1=value1

### 2、RequestEntity

> RequestEntity顾名思义是请求实体，所以这个类型的作用可用于接收整个请求报文，即它既可以接收请求头，也可以接收请求体
>
> RequestEntity**是封装请求报文的一种类型**，它封装的是整个请求报文，需要在控制器方法的形参中设置该类型的形参，当前请求的请求报文就会赋值给该形参，可以通过getHeaders()可获取请求报文中的请求头信息，通过getBody()获取请求体信息

```html
<!--请求方式一定得要是POST请求，所以我们使用form表单发送请求-->
<form th:action="@{/testRequestEntity}" method="post">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    <input type="submit">
</form>
```

```java
@RequestMapping("/testRequestEntity")
// 泛型为String，表示以String即字符串的方式来获取请求报文
public String testRequestEntity(RequestEntity<String> requestEntity){
    // 当前requestEntity就包含了整个请求报文的信息
    System.out.println("requestHeader:"+requestEntity.getHeaders());
    System.out.println("requestBody:"+requestEntity.getBody());
    return "success";
}
```

输出结果：
requestHeader:[host:"localhost:8080", connection:"keep-alive", content-length:"37", cache-control:"max-age=0", sec-ch-ua:""Google Chrome";v="95", "Chromium";v="95", ";Not A Brand";v="99"", sec-ch-ua-mobile:"?0", sec-ch-ua-platform:""Windows"", upgrade-insecure-requests:"1", origin:"http://localhost:8080", **user-agent**:"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36", accept:"text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9", sec-fetch-site:"same-origin", sec-fetch-mode:"navigate", sec-fetch-user:"?1", sec-fetch-dest:"document", **referer**:"http://localhost:8080/SpringMVC/", accept-encoding:"gzip, deflate, br", accept-language:"zh-CN,zh;q=0.9", cookie:"Webstorm-9770a2b9=fa81ec91-641d-4d88-84e7-ef0fb3459759", Content-Type:"application/x-www-form-urlencoded;charset=UTF-8"]

requestBody:username=admin&password=123

> 其中referer请求头对应的地址为请求来源页面的地址，可以通过获取该请求头的值，可以实现从哪个页面发送的请求再回到原来发送请求的那个页面功能
>
> 其中user-agent请求头，这个请求头代表当前浏览器的信息，因为我们的浏览器是存在兼容性的，所以我们就需要通过这个请求头来判断浏览器的种类，如果是谷歌我们该如何解决，如果是火狐我们该如何解决，所以说**请求头信息还是比较重要的**

### 3、@ResponseBody（最常用）

> @RequestBody顾名思义是响应体，所以这个注解的作用就是将Java对象转换为响应体，就是将控制器方法的返回值作为响应浏览器的响应体内容
>
> @ResponseBody用于**标识一个控制器方法**，可以**将该方法的返回值**直接**作为响应报文的响应体**响应到浏览器
>
> 之前我们要对浏览器进行响应，有转发和重定向方式，还有response.getWriter().write()方法响应浏览器，那么我们要在SpringMVC中来实现响应浏览器的功能，我们要如何实现呢？我们就可以通过ServletAPI来响应

```html
<a th:href="@{/testResponse}">通过ServletAPI的response对象来响应给浏览器数据</a>
```

```java
@RequestMapping("testResponse")
// 此处使用void类型，说明不需要跳转视图
public void testResponse(HttpServletResponse response) throws IOException{
    response.getWriter().print("hello,response");
}
```

> 我们ServletAPI能干的事情，我们的SpringMVC同样提供了对应的方法去实现，所以我们采用SpringMVC的方式来进行响应浏览器数据

```java
@RequestMapping("/testResponseBody")
// 当前方法没有添加@ResponseBody注解的时候，返回的是一个视图名称，会被视图解析器解析
public String testResponseBody(){
    return "success";
}

@RequestMapping("/testResponseBody")
@ResponseBody
// 当前方法标识了@ResponseBody注解的时候，返回的就不是一个视图名称了，而是当前响应的响应体
public String testResponseBody(){
    return "我是响应体内容文本";
}
```

结果：浏览器页面显示"我是响应体内容文本"

> 浏览器只能接收字符串文本的响应内容，不能接收一个Java对象，比如我的响应到浏览器的内容是一个Java对象，即控制器方法的返回值是一个Java对象，那么浏览器就会报错

```java
@RequestMapping("/testResponseBody")
@ResponseBody
// 当响应给浏览器的数据是一个Java对象的时候，浏览器就会报错，因为浏览器只接收字符串文本的响应内容
public User testResponseBody(){ //此处方法的返回值为一个 User Java类对象
    return new User(1001,"Martha",21);
}
```

![image-20211111202259815](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111202259815.png)

> 因为浏览器不能接收一个User对象，所以我们需要将User对象转换为一个相对应的类型，就比如说，我们既要来保留我们User对象中的各个数据，同时还要能够正常地将其响应到浏览器，那我们怎么做呢？
>
> 我们就可以将Java类对象转换为一个JSON字符串，JSON不是说只能和Ajax一起使用，JSON是一种数据交互格式，可不光只能用在Ajax中，再说了Ajax中，也是把JSON作为一种数据交互格式来使用的，所以说JSON仅仅就是一种交互格式，所以我们需要将当前的User对象转换为JSON的，那怎么来转换呢？我们当前SpringMVC是如何来处理JSON的呢？ ↓

### 4、SpringMVC处理json

> 我们要来处理JSON，就需要加入一些相对应的 jar 包	

**@ResponseBody处理json的步骤**：

1. 在工程pom.xml文件中引入jackson的依赖![image-20211111203441011](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111203441011.png)

   ```xml
   <dependency>
       <groupId>com.fasterxml.jackson.core</groupId>
       <artifactId>jackson-databind</artifactId>
       <version>2.12.1</version>
   </dependency>
   ```

2. 在SpringMVC的核心配置文件中开启mvc的注解驱动，此时在`HandlerAdaptor`中会自动装配一个消息转换器：`MappingJackson2HttpMessageConverter`，**可以将响应到浏览器的Java对象转换为Json格式的字符串**

   ```
   <mvc:annotation-driven />
   ```

3. 在处理器方法上使用`@ResponseBody`注解进行标识

4. **将Java对象直接作为控制器方法的返回值返回**，就会**自动转换为Json格式的字符串**

   ```java
   @RequestMapping("/testResponseUser")
   @ResponseBody
   public User testResponseUser(){
       return new User(1001,"admin","123456",23,"男");
   }
   ```

浏览器的页面中展示的结果：

{"id":1001,"username":"admin","password":"123456","age":23,"sex":"男"}

#### 回顾JSON

> JSON是一种数据交互格式，XML也是一种数据交互格式，但是现在使用最多的就是JSON，XML最多的还是来作为配置文件，因为JSON数据结构比较简单，解析起来比较简单，生成的数据量比较少

### 5、SpringMVC处理ajax

> Ajax无非也就是发送请求的方式不一样，服务器中该怎么处理就怎么处理，ajax本身就是异步请求，也就是不刷新页面与服务器进行交互，所以我们服务器中就不能使用转发和重定向来处理ajax的请求，只能**使用响应浏览器数据的方式响应ajax的请求**，以下就是处理SpringMVC处理ajax请求的步骤：

1. 请求超链接：通过超链接发送一个ajax请求

   ```html
   <!--.app就是包裹超链接的外层容器-->
   <div id="app">
   	<a th:href="@{/testAjax}" @click="testAjax">SpringMVC处理ajax请求</a><br>
   </div>
   ```

2. 还是老步骤，在webapp下创建static\js目录用于保存vue.js文件和axios.min.js文件（即ajax文件），通过vue和axios处理点击事件：![image-20211111205453958](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211111205453958.png)

   ```html
   <script type="text/javascript" th:src="@{/static/js/vue.js}"></script>
   <script type="text/javascript" th:src="@{/static/js/axios.min.js}"></script>
   <script type="text/javascript">
       var vue = new Vue({
           el:"#app",
           methods:{
               testAjax:function (event) {
                   axios({
                       method:"post",
                       url:event.target.href,
                       params:{
                           username:"admin",
                           password:"123456"
                       }
                   }).then(function (response) {
                       alert(response.data);
                   });
                   event.preventDefault();
               }
           }
       });
   </script>
   ```

3. 控制器方法：处理ajax请求肯定不能使用转发和重定向，所以需要使用@ResponseBody直接将数据响应给浏览器

   ```java
   @RequestMapping("/testAjax")
   @ResponseBody
   public String testAjax(String username, String password){
       System.out.println("username:"+username+",password:"+password);
       return "hello,ajax";
   }
   ```

### 6、@RestController注解

> 我们以后学习到微服务架构，微服务与微服务之间通信就是使用HTTP+JSON的方式进行的，以后在微服务中我们控制器层中的每一个控制器方法都要加上@ResponseBody，所以说这个注解我们以后使用得非常频繁
>
> 所以在SpringMVC中就为我们提供了一个派生注解（复合注解）即`@RestController`注解，这个注解就相当于我们的`@Controller`和`@ResponseBody`两个注解的组合体，RestController顾名思义就是用于标识Controller层即控制层组件的，所以该注解的一个作用就是将一个类标识为Controller层，并且该Controller层中的所有方法默认全部都会被加上@ResponseBody注解，所以当前Controller层中的所有控制器方法，也就是所有控制器方法的返回值全部都是作为响应浏览器数据的响应体存在的，所以**这个注解非常非常重要**，使用非常非常频繁

@RestController注解是springMVC提供的一个复合注解，用于标识在控制器的类上方，就**相当于为类添加了@Controller注解**，**并且为其中的每个方法添加了@ResponseBody注解**

### 7、ResponseEntity

> ResponseEntity顾名思义是响应实体，这个类型的作用就是将ResponseEntity对象类型的数据转换为响应报文

ResponseEntity**用于控制器方法的返回值类型**，换句话说就是我们自定义的一个响应报文，该控制器方法的返回值就是响应到浏览器的响应报文，这东西能干什么，能干文件的下载！↓

# 九、文件上传和下载

### 1、文件下载

使用ResponseEntity实现下载文件的功能

```java
@RequestMapping("/testDown")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
    //获取ServletContext对象
    ServletContext servletContext = session.getServletContext();
    //获取服务器中文件的真实路径，getRealPath()获取工程部署后在服务器上路径结构下的路径
    String realPath = servletContext.getRealPath("/static/img/1.jpg");
    //创建输入流
    InputStream is = new FileInputStream(realPath);
    //创建字节数组，is.available()获取当前输入流中对应的文件的总字节数
    byte[] bytes = new byte[is.available()];
    //将流读到字节数组中，即将所有的字节全部读到数组中，没有用循环读入，然后我们将这个字节数组响应到浏览器，这就是我要下载的文件，因为我们当前控制器方法的返回值为ResponseEntity对象，那我们就一定要将我们的字节数组，即我们的文件数据，转换为我们的ResponseEntity对象，所以它下面创建了一个ResponseEntity对象进行封装必要数据
    is.read(bytes);
    //创建HttpHeaders对象设置响应头信息，MultiValueMap其实就是一个Map集合
    MultiValueMap<String, String> headers = new HttpHeaders();
    //设置要下载方式以及下载文件的名字，不管请求头还是响应头都是键值对，要注意下面的键值对内容，只有1.jpg能修改，其它一律不能改，当前这种响应头键值对的意思是用来设置下载文件的方式的，attachment表示以附件的方式下载
    headers.add("Content-Disposition", "attachment;filename=1.jpg");
    //设置响应状态码
    HttpStatus statusCode = HttpStatus.OK;
    //创建ResponseEntity对象
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers, statusCode);//第一个参数bytes，bytes存放了我们当前浏览器客户端要下载的文件的所有字节，换句话说这就是响应体；第二个参数就是响应头用于设置下载方式；第三个参数就是响应必须要有的响应状态码
    //关闭输入流
    is.close();
    return responseEntity;
}
```

### 2、文件上传

> 上传是不能使用GET请求的

+ 文件上传要求form表单的请求方式必须为post，并且添加属性enctype="multipart/form-data"

+ SpringMVC中将上传的文件封装到MultipartFile对象中，通过此对象可以获取文件相关信息

**上传步骤**：

1. 添加文件上传依赖：这个依赖只有上传会用到，下载是用不到的

   ```xml
   <!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
   <dependency>
       <groupId>commons-fileupload</groupId>
       <artifactId>commons-fileupload</artifactId>
       <version>1.3.1</version>
   </dependency>
   ```

   **上传页面**

   ```html
   <!--设置enctype="multipart/form-data"，那么表单就不会以name=value键值对的方式传输数据，而是以二进制方式传输数据到服务器的，这时候服务器才能接收到我们上传的文件-->
   <form th:action="@{/testFileUpload}" method="post" enctype="multipart/form-data">
       <!--文件域：用于点击选择上传文件-->
   头像：<input type="file" name="photo"/><br>
       <input type="submit" value="上传"/>
   </form>
   ```

2. 在SpringMVC的配置文件中添加配置：

   ```xml
   <!--配置文件上传解析器，将上传的文件封装为MultipartFile对象，
       这时候控制器方法就可以使用MultipartFile类型的形参（形参名必须与文件的请求参数名相同，即与file域中的name属性相同）来接收我们上传的文件了
       这个bean我们不访问，是SpringMVC自动去访问它的，我们当前配置的是MultipartFile接口的实现类对象，SpringMVC是通过id获取的，所以id必不可少
       且id必须叫做multipartResolver-->
       <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
   ```

3. 控制器方法：

   > 在SpringMVC中对我们的上传操作进行了封装，封装进了MultipartFile类中，所以我们控制器方法形参请求参数名变量所对应的类型应当是一个MultipartFile类型，但我们当前上传的文件默认是不能直接转换成MultipartFile对象的，所以为了使用该封装类，我们就需要实现上传的文件转换MultipartFile对象类型，所以我们**需要去SpringMVC配置文件中配置文件上传解析器**

   ```java
   @RequestMapping("/testUp")
   public String testUp(MultipartFile photo, HttpSession session) throws IOException {
       //获取上传的文件的文件名
       String fileName = photo.getOriginalFilename();
       //处理文件重名问题
       String hzName = fileName.substring(fileName.lastIndexOf("."));
       // 将UUID作为文件名
       fileName = UUID.randomUUID().toString() + hzName;//随机序列+文件后缀名
       //获取服务器中photo目录的路径
       ServletContext servletContext = session.getServletContext();
       String photoPath = servletContext.getRealPath("photo");
       // 如果是第一次上传，photo目录不存在则创建
       File file = new File(photoPath);
       if(!file.exists()){
           file.mkdir();
       }
       String finalPath = photoPath + File.separator + fileName;
       //实现上传功能
       photo.transferTo(new File(finalPath));
       return "success";
   }
   ```

# 十、拦截器

> 拦截器是对我们当前的请求进行拦截的，那我们知道，过滤器也能够拦截请求，但拦截器与过滤器不同的是，浏览器发送请求经过过滤器过滤之后，到前端控制器被DispatcherServlet进行处理，所以过滤器是作用在浏览器与DispatcherServlet之间的，作用在浏览器与前端控制器二者中间的位置；而在前端控制器拿到请求后，会对请求进行处理，根据当前的请求信息去匹配我们的请求映射，找到了相对应的请求映射，那请求映射所对应的控制器方法就是我们处理请求的方法，所以我们的DispatcherServlet会去调用控制器方法，而我们的拦截器就是作用在我们控制器执行的前后的，就比如说在我们的拦截器中一共有三个抽象方法：preHandle、postHandle、afterCompletion，这三个方法的第一个叫做preHandle，这个pre就是..之前的意思，Handle就是处理的意思，而之前我们就说过我们的Controller层也可以叫做Handler，也就是控制器方法也叫做处理器方法，所以**`preHandle`就是表示在我们当前控制器方法之前执行**，**`postHandle`则是在我们控制器方法执行之后执行**，因为postHandle方法一旦执行就说明我们的控制器方法已经被调用了，即已经执行过了，那控制器方法被调用之后，在底层返回值是一个ModelAndView对象，ModelAndView中有Model有View，Model保存模型数据，View保存视图相关信息，它的作用就是将模型数据填充到我们的视图中，为用户展示数据，也就是渲染视图，所以**`afterCompletion`就会在渲染视图完毕之后来执行的**

### 1、拦截器的配置

+ SpringMVC中的**拦截器用于拦截`控制器方法`的执行**

+ SpringMVC中自定义的的拦截器类需要实现`HandlerInterceptor`

+ SpringMVC的拦截器必须**在SpringMVC的配置文件中进行配置**：

  ```xml
  <bean class="com.atguigu.interceptor.FirstInterceptor"></bean>
  <ref bean="firstInterceptor"></ref>
  <!-- 以上两种配置方式都是对DispatcherServlet所处理的所有的请求进行拦截，因为只有DispatcherServlet能处理的请求才会对应我们的控制器方法，而拦截器就是作用于对控制器方法的拦截的 -->
  <mvc:interceptor>
      <mvc:mapping path="/**"/>
      <mvc:exclude-mapping path="/testRequestEntity"/>
      <ref bean="firstInterceptor"></ref>
  </mvc:interceptor>
  <!-- 
  	以上配置方式可以通过ref或bean标签设置拦截器，通过mvc:mapping设置需要拦截的请求，通过mvc:exclude-mapping设置需要排除的请求，即不需要拦截的请求
  -->
  ```

1. 先创建一个包com.martha.mvc.interceptors用于存放拦截器类，因为拦截器不止一个，在包下创建MyInteceptor类，该类需要实现`HandlerInterceptor`，因为该接口中声明的三个方法都是默认的方法，所以我们在FirstInterceptor中快捷键Ctrl+O，选择这三个方法来重写这三个方法，先去掉其中的方法体

   ```java
   public class MyInteceptor implements HandlerInterceptor {
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           System.out.println("在控制器方法之前我执行了");
           return false;
       }
   
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           System.out.println("在控制器方法之后我执行了");
       }
   
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           System.out.println("在渲染视图完毕之后我执行了");
       }
   }
   ```

2. 拦截器定义好了之后，要想使其生效，就需要“介绍”给`SpringMVC`，所以就需要在SpringMVC配置文件中配置我们定义好的拦截器

   ```xml
   // 第一种方式
   <!--配置自定义的拦截器，mvc:interceptors是一个复数标签，那么里面就可以写多个拦截器-->
   <mvc:interceptors>
       <!--通过一个bean标签指明全类名，即可完成一个拦截器的配置，但如果是使用bean标签的方式来配置一个拦截器，那么所有的请求都会被进行拦截处理-->
       <bean class="com.martha.mvc.interceptors.MyInteceptor"></bean>
   </mvc:interceptors>
   ```

   ```xml
   // 第二种方式
   <mvc:interceptors>
       <!--引用IOC容器中的拦截器bean的id，来配置拦截器，id默认为类名首字母小写-->
   	<ref bean="myInterceptor"></ref>
       <!--但是这种配置无异于上面那种直接配置bean的方式，只设置它的时候我们没有办法去定义我们的拦截规则，他就是一个拦截器bean，将其配置进来，拦截器就生效了，所以bean方式和ref方式都默认对所有请求进行拦截-->
   </mvc:interceptors>
   
   前提是拦截器上方标注了注解，或在配置文件中配置了bean标签，此处演示注解的方式
   @Component
   public class MyInteceptor implements HandlerInterceptor{..}
   ```

   ```xml
   // 第三种方式（常用）
   <mvc:interceptors>
       <!--这个方式就可以来自定义我们拦截器的拦截规则-->
   	<mvc:interceptor>
           <!--设置该拦截器的拦截路径：/* 在这里不表示拦截所有请求，而是拦截上下文路径下的一层目录，如请求路径为/a、/test、/login的一层请求路径才会被拦截，而/user/login像这种两层以上目录请求路径的就不会被拦截，那我们要表示拦截所有请求则需要使用 /** 来匹配所有请求-->
       	<mvc:mapping path="/**"></mvc:mapping>
           <!--设置该拦截器的不拦截路径：不拦截访问首页的请求-->
           <mvc:exclude-mapping path="/"></mvc:exclude-mapping>
           <!--定义好规则后，还是要将我们的拦截器配置进来，记得给拦截器bean加注解-->
           <ref bean="myInterceptor"></ref>
       </mvc:interceptor>
   </mvc:interceptors>
   ```

   

3. 运行工程，我们发现![image-20211112104954377](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112104954377.png)![image-20211112105103698](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112105103698.png)

   > 因为拦截器之所以叫拦截器，因为它也有两种操作，一种是拦截，一种是放行，什么是拦截什么是放行呢，在我们的过滤器中，FilterChain指的是过滤器链，其中就有一个doFilter方法是来调用下一个过滤器的doFilter方法，那既然要调用下一个过滤器的doFilter方法，那不就是说我们当前这个过滤器已经过滤完了，要放行了；
   >
   > 很明显执行了preHandle方法，拦截器并没有给我们放行，也就没能执行控制器方法，所以浏览器啥也没有，如果访问进去控制器方法了，那么我们的浏览器就会显示超链接表单等元素，所以就**说明在控制器方法执行之前就被拦截了**，所以要想拦截控制器方法的执行，它一定是拦截器中的preHandle方法，我们可以看到preHandle方法的返回值为boolean类型，返回值为true就放行，false就拦截住

### 2、拦截器的三个抽象方法

SpringMVC中的拦截器有三个抽象方法：

+ preHandle：控制器方法执行之前执行preHandle()，其boolean类型的返回值表示是否拦截或放行，返回true为放行，即调用控制器方法；返回false表示拦截，即不调用控制器方法

+ postHandle：控制器方法执行之后执行postHandle()

+ afterCompletion：处理完视图和模型数据，渲染视图完毕之后执行afterCompletion()

### 3、多个拦截器的执行顺序

> 若现在我们定义了两个拦截器，这两个拦截器同时去拦截我们当前的请求，那他们的顺序应该是什么样的？首先我们配置拦截器的顺序决定了谁的preHandle方法先执行，如下图：![image-20211112112759183](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112112759183.png)![image-20211112112741938](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112112741938.png)
>
> 所以说我们把配置拦截器的顺序作为一个标准：**`preHandle`是按照我们的配置的`顺序`来执行的**，而我们的**`postHandle`和`afterCompletion`是按照我们配置的`反序`来执行的**![image-20211112113843901](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112113843901.png)
>
> 这里面可以看到，我们自己配置了两个拦截器，还有一个是SpringMVC创建的
>
> 

+ 若每个拦截器的preHandle()都返回true

此时多个拦截器的执行顺序和拦截器在SpringMVC的配置文件的配置顺序有关：

preHandle()会按照配置的顺序执行，而postHandle()和afterCompletion()会按照配置的反序执行![image-20211112114657364](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112114657364.png)

![image-20211112114803887](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112114803887.png)![image-20211112114909456](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112114909456.png)

+ 若某个拦截器的preHandle()返回了false

preHandle()返回false和它之前的拦截器的preHandle()都会执行，postHandle()都不执行，返回false的拦截器之前的拦截器的afterCompletion()会执行

> 也就是如果有1、2、3、4、5个拦截器同时运作，其中如果拦截器3的preHandle方法返回了false，那么
>
> + 拦截器1、2、3的preHandle都会执行，拦截器3的preHandle也会执行，不然怎么知道返回false了
> + 拦截器1、2、3、4、5的postHandle方法都不会执行
> + 拦截器1、2的afterCompletion()会执行，当然要注意顺序是反序的

# 十一、异常处理器

> 什么是异常处理器呢？就是我们的SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口，叫做`HandlerExceptionResolver`，顾名思义这也是一个解析器

### 1、基于配置的异常处理

SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口：HandlerExceptionResolver

HandlerExceptionResolver接口的实现类常用的有以下两种：`DefaultHandlerExceptionResolver`和`SimpleMappingExceptionResolver`

> `DefaultHandlerExceptionResolver`是**SpringMVC默认使用的一个异常处理器**，也就是平常我们遇到的一些异常，其实SpringMVC都给我们处理过了，看源码：![image-20211112123329639](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112123329639.png)
>
> 可以看到该方法就是默认的异常处理器处理异常的方法，但是我们发现该方法的返回值是一个ModelAndView，所以我们当前这个异常处理的方法就是说，如果我们在控制器方法中出现了指定的异常，那么这个异常处理方法就会返回一个新的ModelAndView来替代我们原来控制器方法返回的ModelAndView，所以就可以帮助我们进行比如错误页面的跳转等等，我们可以看到这个异常处理方法中包含了很多异常类型的判断![image-20211112123811683](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112123811683.png)
>
> 所以我们之所以能看到有时候405啊等错误页面啊，其实**有很多都是我们SpringMVC帮我们处理了异常然后跳转过去的页面**
>
> 而`SimpleMappingExceptionResolver`是让我**自定义异常处理**的，就比如说，我们可能遇到一些我们经常遇到的异常，如果我们遇到了这些异常，我们就可以让其跳转到指定页面来展示错误信息，所以这个东西就是SpringMVC交给我们自己去使用的，那么我们如果想要使用`SimpleMappingExceptionResolver`，有**两种配置异常处理的方式** ↓，配置完后，我们就不会在页面中看到控制器方法给我们报的异常信息了，我们就可以将我们可能会出现的异常将其配置在异常映射中，这个时候出现指定的异常就会跳转至相对应的页面，就不会再给用户展示一个500异常信息页面了

SpringMVC提供了自定义的异常处理器`SimpleMappingExceptionResolver`，使用基于SpringMVC配置文件的方式配置异常处理：

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
        	<!--
        		properties的键表示处理器方法执行过程中出现的异常
        		properties的值表示若出现指定异常时，设置一个新的视图名称，跳转到指定页面
        	-->
            <prop key="java.lang.ArithmeticException">error</prop>
        </props>
    </property>
    <!--
    	exceptionAttribute属性设置一个属性名，将出现的异常信息在请求域中进行共享
就可以通过错误页面进行获取异常信息了
    -->
    <property name="exceptionAttribute" value="ex"></property>
</bean>
```

1. 首先在templates下创建一个error.html代表错误页面，即控制器方法发生异常要跳转的目标页面![image-20211112125315800](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112125315800.png)

2. 进行异常处理的配置，比如我配置控制器方法出现了一个算数异常，就跳转到错误页面![image-20211112125406951](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112125406951.png)

3. 在控制器方法中定义异常进行测试

   ```java
   @RequestMapping("/testException")
   public String testException(){
       int i = 10 / 0;
       return "success";
   }
   ```

4. 当我们访问/testException时，就会出现异常，随后跳转到error.html![image-20211112125840158](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112125840158.png)

> 但是呢，我们不单单要来跳转到一个错误页面，还要展示我们的错误信息，那么就需要在SpringMVC配置文件中的异常配置中再设置一个属性，叫做exceptionAttribute，我们就可以来设置一个键来存储我们当前的异常信息，该异常信息**默认是存储在请求域中**的，**value保存的就是异常信息的键**，那么我们就可以**在错误信息页面通过键来获取异常信息了**，如下：

```xml
<!--配置异常处理-->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
            <!--有算数异常进行跳转至错误页面-->
            <prop key="java.lang.ArithmeticException">error</prop>
        </props>
    </property>
    <!--携带对应异常信息，异常信息的键为exception，键名自定义-->
    <property name="exceptionAttribute" value="exception"></property>
</bean>
```

```html
<!--错误页面通过获取请求域中的exception，来获取对应的异常信息-->
<body>
错误页面
    异常信息为：<p th:text="${exception}"></p>
</body>
```

![image-20211112130656334](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112130656334.png)

### 2、基于注解的异常处理

> SpringMVC同样也为我们提供了一套注解@ControllerAdvice、@ExceptionHandler，通过注解也能够实现异常处理的配置，其实思路与配置文件一样
>
> @ControllerAdvice这个注解包含了@Controller注解的功能，所以它也具有将一个普通的类标识为组件的功能，即将该类交由IOC容器进行管理

在controller包下定义一个处理异常的类，在类的上方标识@ControllerAdvice注解，标识该类为一个异常处理的组件

```java
//@ControllerAdvice将当前类标识为异常处理的组件
@ControllerAdvice
public class ExceptionController {

    //@ExceptionHandler注解的value属性用于设置所标识方法处理的异常
    //当我们原有的控制器方法出现了value属性中包含的异常时，@ExceptionHandler注解所标识的方法就会替代我们原有的控制器方法来执行，返回一个新的ModelAndView，即携带着异常信息去往错误页面
    @ExceptionHandler(value={ArithmeticException.class,NullPointerException.class})
    //ex表示当前请求处理中出现的异常对象，这里我们需要手动将异常信息放入请求域对象
    public String handleArithmeticException(Exception ex, Model model){
        model.addAttribute("exception", ex);
        return "error";
    }

}
```

# 十二、注解配置SpringMVC

> 我们可以通过使用配置类和注解的方式代替web.xml和SpringMVC.xml文件，实现相关的配置
>
> 也就是我们使用注解来配置SprignMVC**我们就不需要创建web.xml和SpringMVC的配置文件了**
>
> 就像我们以后SSM框架整合的时候，Spring的配置文件也是可以不用写的

使用配置类和注解代替web.xml和SpringMVC配置文件的功能

### 1、创建初始化类，代替web.xml

+ 在Servlet3.0环境中，IOC容器会在类路径（**src或resources**）中查找实现了`javax.servlet.ServletContainerInitializer`接口的类，如果找到的话就**用该类来配置Servlet容器，即Tomcat服务器**
+ Spring提供了这个接口的实现，也就是Spring实现了`javax.servlet.ServletContainerInitializer`，名为`SpringServletContainerInitializer`，这个类反过来又会查找实现了`WebApplicationInitializer`的类并**将配置的任务交给它们来完成**，Spring3.2引入了一个便利的`WebApplicationInitializer`基础实现，名为`AbstractAnnotationConfigDispatcherServletInitializer`，当我们的类扩展了`AbstractAnnotationConfigDispatcherServletInitializer`并将其部署到**Servlet3.0**容器的时候，容器会自动发现它，并**用它来配置Servlet上下文即web.xml**，**大前提就是Servlet版本是3.0**

> AbstractAnnotationConfigDispatcherServletInitializer它为什么能代替web.xml呢？我们知道web.xml文件是作为我们当前Web工程的描述符，每当我们去启动我们的Tomcat服务器，我们当前要加载的第一个配置文件就是web.xml文件，那如果我们自定义了一个类继承了`AbstractAnnotationConfigDispatcherServletInitializer`，那这个类会在第一时间直接执行吗，对于我们原来所学习过的内容，在我们的服务器中，有服务器的三大组件：Servlet、Filter、Listener，这三大组件是由服务器直接调用的，那么**我们这里定义的代替web.xml文件的这个类，也是会在我们服务器启动时自动去执行自动去加载的**

```java
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {

    /**
     * 指定spring的配置类
     * @return
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    /**
     * 指定SpringMVC的配置类
     * @return
     */
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    /**
     * 指定DispatcherServlet的映射规则，即url-pattern
     * @return
     */
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    /**
     * 添加过滤器
     * @return
     */
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
        encodingFilter.setEncoding("UTF-8");
        encodingFilter.setForceRequestEncoding(true);
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{encodingFilter, hiddenHttpMethodFilter};
    }
}
```

1. 创建一个新的Module，导入pom依赖，在main目录下直接创建webapp目录，在webapp目录下直接创建一个WEB-INF目录![image-20211112133624551](C:\Users\Administrator\Desktop\后端学习\SpringMVCNotes_images\image-20211112133624551.png)

2. 在main\java目录下创建com.martha.mvc.config.WebInit类，即**替代web.xml文件**的配置类，该类首先要继承`AbstractAnnotationConfigDispatcherServletInitializer`，重写其中的三个方法

   ```java
   // 当前web工程的初始化来，用于替代web.xml
   public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
   	
       // getRootConfigClasses方法用于获取我们当前工程的一个根配置，根配置指的就是Spring的配置类
       // 指定Spring的配置类
       @Override
       protected Class<?>[] getRootConfigClasses() {
           return new Class[]{SpringConfig.class};
       }
   	
       // 指定SpringMVC的配置类
       @Override
       protected Class<?>[] getServletConfigClasses() {
           return new Class[]{WebConfig.class};
       }
   	
       // 用于指定DispatcherServlet的映射规则路径，即url-pattern
       @Override
       protected String[] getServletMappings() {
           return new String[]{"/"};
       }
       
       // 我们在我们的web.xml文件中，除了要指定DispatcherServlet，还要指定过滤器
       /**
        * 添加过滤器
        * @return
        */
       @Override
       protected Filter[] getServletFilters() {
           CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
           encodingFilter.setEncoding("UTF-8");
           encodingFilter.setForceRequestEncoding(true);
           HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
           return new Filter[]{encodingFilter, hiddenHttpMethodFilter};
       }
   }
   ```

   > 经过以上配置可以发现，Spring配置文件可以有多个，SpringMVC配置文件可以有多个，DispatcherServlet即Servlet的映射路径可以有多个，过滤器也可以有多个，虽然是废话

### 2、创建SpringConfig配置类，代替spring的配置文件

```java
@Configuration
public class SpringConfig {
	//ssm整合之后，spring的配置信息写在此类中
}
```

### 3、创建WebConfig配置类，代替SpringMVC的配置文件

SpringMVC的配置类的配置信息：

+ 扫描组件：@ComponentScan("")
+ 视图解析器
+ view-controller
+ 默认的Servlet
+ mvc注解驱动：@EnableWebMvc
+ 文件上传解析器
+ 异常处理
+ 拦截器

```java
@Configuration
//扫描组件
@ComponentScan("com.martha.mvc.controller")
//开启MVC注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    //使用默认的servlet处理静态资源
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    //配置文件上传解析器
    @Bean
    public CommonsMultipartResolver multipartResolver(){
        return new CommonsMultipartResolver();
    }

    //配置拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        FirstInterceptor firstInterceptor = new FirstInterceptor();
        registry.addInterceptor(firstInterceptor).addPathPatterns("/**");
    }
    
    //配置视图控制
    
    /*@Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
    }*/
    
    //配置异常映射
    /*@Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
        Properties prop = new Properties();
        prop.setProperty("java.lang.ArithmeticException", "error");
        //设置异常映射
        exceptionResolver.setExceptionMappings(prop);
        //设置共享异常信息的键
        exceptionResolver.setExceptionAttribute("ex");
        resolvers.add(exceptionResolver);
    }*/

    //配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过WebApplicationContext 的方法获得
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(
                webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }

    //生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }

    //生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }


}
```

### 4、测试功能

```java
@RequestMapping("/")
public String index(){
    return "index";
}
```

# 十三、SpringMVC执行流程

### 1、SpringMVC常用组件

- DispatcherServlet：**前端控制器**，不需要工程师开发，由框架提供

作用：统一处理请求和响应，整个流程控制的中心，由它调用其它组件处理用户的请求

- HandlerMapping：**处理器映射器**，不需要工程师开发，由框架提供

作用：根据请求的url、method等信息查找Handler，即控制器方法

- Handler（Controller）：**处理器**，需要工程师开发

作用：在DispatcherServlet的控制下Handler对具体的用户请求进行处理

- HandlerAdapter：**处理器适配器**，不需要工程师开发，由框架提供

作用：通过HandlerAdapter对处理器（控制器方法）进行执行

- ViewResolver：**视图解析器**，不需要工程师开发，由框架提供

作用：进行视图解析，得到相应的视图，例如：ThymeleafView、InternalResourceView、RedirectView

- View：**视图**

作用：将模型数据通过页面展示给用户

### 2、DispatcherServlet初始化过程

DispatcherServlet 本质上是一个 Servlet，所以天然的遵循 Servlet 的生命周期。所以宏观上是 Servlet 生命周期来进行调度

![images](SpringMVCNotes_images/img005.png)

##### a>初始化WebApplicationContext

所在类：org.springframework.web.servlet.FrameworkServlet

```java
protected WebApplicationContext initWebApplicationContext() {
    WebApplicationContext rootContext =
        WebApplicationContextUtils.getWebApplicationContext(getServletContext());
    WebApplicationContext wac = null;

    if (this.webApplicationContext != null) {
        // A context instance was injected at construction time -> use it
        wac = this.webApplicationContext;
        if (wac instanceof ConfigurableWebApplicationContext) {
            ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) wac;
            if (!cwac.isActive()) {
                // The context has not yet been refreshed -> provide services such as
                // setting the parent context, setting the application context id, etc
                if (cwac.getParent() == null) {
                    // The context instance was injected without an explicit parent -> set
                    // the root application context (if any; may be null) as the parent
                    cwac.setParent(rootContext);
                }
                configureAndRefreshWebApplicationContext(cwac);
            }
        }
    }
    if (wac == null) {
        // No context instance was injected at construction time -> see if one
        // has been registered in the servlet context. If one exists, it is assumed
        // that the parent context (if any) has already been set and that the
        // user has performed any initialization such as setting the context id
        wac = findWebApplicationContext();
    }
    if (wac == null) {
        // No context instance is defined for this servlet -> create a local one
        // 创建WebApplicationContext
        wac = createWebApplicationContext(rootContext);
    }

    if (!this.refreshEventReceived) {
        // Either the context is not a ConfigurableApplicationContext with refresh
        // support or the context injected at construction time had already been
        // refreshed -> trigger initial onRefresh manually here.
        synchronized (this.onRefreshMonitor) {
            // 刷新WebApplicationContext
            onRefresh(wac);
        }
    }

    if (this.publishContext) {
        // Publish the context as a servlet context attribute.
        // 将IOC容器在应用域共享
        String attrName = getServletContextAttributeName();
        getServletContext().setAttribute(attrName, wac);
    }

    return wac;
}
```

##### b>创建WebApplicationContext

所在类：org.springframework.web.servlet.FrameworkServlet

```java
protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
    Class<?> contextClass = getContextClass();
    if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
        throw new ApplicationContextException(
            "Fatal initialization error in servlet with name '" + getServletName() +
            "': custom WebApplicationContext class [" + contextClass.getName() +
            "] is not of type ConfigurableWebApplicationContext");
    }
    // 通过反射创建 IOC 容器对象
    ConfigurableWebApplicationContext wac =
        (ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);

    wac.setEnvironment(getEnvironment());
    // 设置父容器
    wac.setParent(parent);
    String configLocation = getContextConfigLocation();
    if (configLocation != null) {
        wac.setConfigLocation(configLocation);
    }
    configureAndRefreshWebApplicationContext(wac);

    return wac;
}
```

##### c>DispatcherServlet初始化策略

FrameworkServlet创建WebApplicationContext后，刷新容器，调用onRefresh(wac)，此方法在DispatcherServlet中进行了重写，调用了initStrategies(context)方法，初始化策略，即初始化DispatcherServlet的各个组件

所在类：org.springframework.web.servlet.DispatcherServlet

```java
protected void initStrategies(ApplicationContext context) {
   initMultipartResolver(context);
   initLocaleResolver(context);
   initThemeResolver(context);
   initHandlerMappings(context);
   initHandlerAdapters(context);
   initHandlerExceptionResolvers(context);
   initRequestToViewNameTranslator(context);
   initViewResolvers(context);
   initFlashMapManager(context);
}
```

### 3、DispatcherServlet调用组件处理请求

##### a>processRequest()

FrameworkServlet重写HttpServlet中的service()和doXxx()，这些方法中调用了processRequest(request, response)

所在类：org.springframework.web.servlet.FrameworkServlet

```java
protected final void processRequest(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {

    long startTime = System.currentTimeMillis();
    Throwable failureCause = null;

    LocaleContext previousLocaleContext = LocaleContextHolder.getLocaleContext();
    LocaleContext localeContext = buildLocaleContext(request);

    RequestAttributes previousAttributes = RequestContextHolder.getRequestAttributes();
    ServletRequestAttributes requestAttributes = buildRequestAttributes(request, response, previousAttributes);

    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
    asyncManager.registerCallableInterceptor(FrameworkServlet.class.getName(), new RequestBindingInterceptor());

    initContextHolders(request, localeContext, requestAttributes);

    try {
		// 执行服务，doService()是一个抽象方法，在DispatcherServlet中进行了重写
        doService(request, response);
    }
    catch (ServletException | IOException ex) {
        failureCause = ex;
        throw ex;
    }
    catch (Throwable ex) {
        failureCause = ex;
        throw new NestedServletException("Request processing failed", ex);
    }

    finally {
        resetContextHolders(request, previousLocaleContext, previousAttributes);
        if (requestAttributes != null) {
            requestAttributes.requestCompleted();
        }
        logResult(request, response, failureCause, asyncManager);
        publishRequestHandledEvent(request, response, startTime, failureCause);
    }
}
```

##### b>doService()

所在类：org.springframework.web.servlet.DispatcherServlet

```java
@Override
protected void doService(HttpServletRequest request, HttpServletResponse response) throws Exception {
    logRequest(request);

    // Keep a snapshot of the request attributes in case of an include,
    // to be able to restore the original attributes after the include.
    Map<String, Object> attributesSnapshot = null;
    if (WebUtils.isIncludeRequest(request)) {
        attributesSnapshot = new HashMap<>();
        Enumeration<?> attrNames = request.getAttributeNames();
        while (attrNames.hasMoreElements()) {
            String attrName = (String) attrNames.nextElement();
            if (this.cleanupAfterInclude || attrName.startsWith(DEFAULT_STRATEGIES_PREFIX)) {
                attributesSnapshot.put(attrName, request.getAttribute(attrName));
            }
        }
    }

    // Make framework objects available to handlers and view objects.
    request.setAttribute(WEB_APPLICATION_CONTEXT_ATTRIBUTE, getWebApplicationContext());
    request.setAttribute(LOCALE_RESOLVER_ATTRIBUTE, this.localeResolver);
    request.setAttribute(THEME_RESOLVER_ATTRIBUTE, this.themeResolver);
    request.setAttribute(THEME_SOURCE_ATTRIBUTE, getThemeSource());

    if (this.flashMapManager != null) {
        FlashMap inputFlashMap = this.flashMapManager.retrieveAndUpdate(request, response);
        if (inputFlashMap != null) {
            request.setAttribute(INPUT_FLASH_MAP_ATTRIBUTE, Collections.unmodifiableMap(inputFlashMap));
        }
        request.setAttribute(OUTPUT_FLASH_MAP_ATTRIBUTE, new FlashMap());
        request.setAttribute(FLASH_MAP_MANAGER_ATTRIBUTE, this.flashMapManager);
    }

    RequestPath requestPath = null;
    if (this.parseRequestPath && !ServletRequestPathUtils.hasParsedRequestPath(request)) {
        requestPath = ServletRequestPathUtils.parseAndCache(request);
    }

    try {
        // 处理请求和响应
        doDispatch(request, response);
    }
    finally {
        if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
            // Restore the original attribute snapshot, in case of an include.
            if (attributesSnapshot != null) {
                restoreAttributesAfterInclude(request, attributesSnapshot);
            }
        }
        if (requestPath != null) {
            ServletRequestPathUtils.clearParsedRequestPath(request);
        }
    }
}
```

##### c>doDispatch()

所在类：org.springframework.web.servlet.DispatcherServlet

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    boolean multipartRequestParsed = false;

    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

    try {
        ModelAndView mv = null;
        Exception dispatchException = null;

        try {
            processedRequest = checkMultipart(request);
            multipartRequestParsed = (processedRequest != request);

            // Determine handler for the current request.
            /*
            	mappedHandler：调用链
                包含handler、interceptorList、interceptorIndex
            	handler：浏览器发送的请求所匹配的控制器方法
            	interceptorList：处理控制器方法的所有拦截器集合
            	interceptorIndex：拦截器索引，控制拦截器afterCompletion()的执行
            */
            mappedHandler = getHandler(processedRequest);
            if (mappedHandler == null) {
                noHandlerFound(processedRequest, response);
                return;
            }

            // Determine handler adapter for the current request.
           	// 通过控制器方法创建相应的处理器适配器，调用所对应的控制器方法
            HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

            // Process last-modified header, if supported by the handler.
            String method = request.getMethod();
            boolean isGet = "GET".equals(method);
            if (isGet || "HEAD".equals(method)) {
                long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
                    return;
                }
            }
			
            // 调用拦截器的preHandle()
            if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                return;
            }

            // Actually invoke the handler.
            // 由处理器适配器调用具体的控制器方法，最终获得ModelAndView对象
            mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

            if (asyncManager.isConcurrentHandlingStarted()) {
                return;
            }

            applyDefaultViewName(processedRequest, mv);
            // 调用拦截器的postHandle()
            mappedHandler.applyPostHandle(processedRequest, response, mv);
        }
        catch (Exception ex) {
            dispatchException = ex;
        }
        catch (Throwable err) {
            // As of 4.3, we're processing Errors thrown from handler methods as well,
            // making them available for @ExceptionHandler methods and other scenarios.
            dispatchException = new NestedServletException("Handler dispatch failed", err);
        }
        // 后续处理：处理模型数据和渲染视图
        processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
    }
    catch (Exception ex) {
        triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
    }
    catch (Throwable err) {
        triggerAfterCompletion(processedRequest, response, mappedHandler,
                               new NestedServletException("Handler processing failed", err));
    }
    finally {
        if (asyncManager.isConcurrentHandlingStarted()) {
            // Instead of postHandle and afterCompletion
            if (mappedHandler != null) {
                mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
            }
        }
        else {
            // Clean up any resources used by a multipart request.
            if (multipartRequestParsed) {
                cleanupMultipart(processedRequest);
            }
        }
    }
}
```

##### d>processDispatchResult()

```java
private void processDispatchResult(HttpServletRequest request, HttpServletResponse response,
                                   @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv,
                                   @Nullable Exception exception) throws Exception {

    boolean errorView = false;

    if (exception != null) {
        if (exception instanceof ModelAndViewDefiningException) {
            logger.debug("ModelAndViewDefiningException encountered", exception);
            mv = ((ModelAndViewDefiningException) exception).getModelAndView();
        }
        else {
            Object handler = (mappedHandler != null ? mappedHandler.getHandler() : null);
            mv = processHandlerException(request, response, handler, exception);
            errorView = (mv != null);
        }
    }

    // Did the handler return a view to render?
    if (mv != null && !mv.wasCleared()) {
        // 处理模型数据和渲染视图
        render(mv, request, response);
        if (errorView) {
            WebUtils.clearErrorRequestAttributes(request);
        }
    }
    else {
        if (logger.isTraceEnabled()) {
            logger.trace("No view rendering, null ModelAndView returned.");
        }
    }

    if (WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
        // Concurrent handling started during a forward
        return;
    }

    if (mappedHandler != null) {
        // Exception (if any) is already handled..
        // 调用拦截器的afterCompletion()
        mappedHandler.triggerAfterCompletion(request, response, null);
    }
}
```

### 4、SpringMVC的执行流程

1) 用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获。

2) DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI），判断请求URI对应的映射：

a) 不存在

i. 再判断是否配置了mvc:default-servlet-handler

ii. 如果没配置，则控制台报映射查找不到，客户端展示404错误

![image-20210709214911404](SpringMVCNotes_images/img006.png)

![image-20210709214947432](SpringMVCNotes_images/img007.png)

iii. 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404错误

![image-20210709215255693](SpringMVCNotes_images/img008.png)

![image-20210709215336097](SpringMVCNotes_images/img009.png)

b) 存在则执行下面的流程

3) 根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及Handler对象对应的拦截器），最后以HandlerExecutionChain执行链对象的形式返回。

4) DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。

5) 如果成功获得HandlerAdapter，此时将开始执行拦截器的preHandler(…)方法【正向】

6) 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)方法，处理请求。在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：

a) HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息

b) 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等

c) 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等

d) 数据验证（一般是前端的事）： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中

7) Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。

8) 此时将开始执行拦截器的postHandle(...)方法【逆向】。

9) 根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，则执行HandlerExceptionResolver进行异常处理）选择一个适合的ViewResolver进行视图解析，根据Model和View，来渲染视图。

10) 渲染视图完毕执行拦截器的afterCompletion(…)方法【逆向】。

11) 将渲染结果返回给客户端。