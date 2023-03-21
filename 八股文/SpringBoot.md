# SpringBoot

## 一、SpringBoot入门

### 1、Spring Boot 简介

> 简化Spring应用开发的一个框架；
> 
> 整个Spring技术栈的一个大整合；
> 
> J2EE开发的一站式解决方案；

### 2、微服务

2014，martin fowler

微服务：架构风格（服务微化）

一个应用应该是一组小型服务；可以通过HTTP的方式进行互通；

单体应用：ALL IN ONE

微服务：每一个功能元素最终都是一个可独立替换和独立升级的软件单元；

[详细参照微服务文档](https://martinfowler.com/articles/microservices.html#MicroservicesAndSoa)

### 3、环境准备

http://www.gulixueyuan.com/ 谷粒学院

环境约束

–jdk1.8：Spring Boot 推荐jdk1.7及以上；java version "1.8.0_112"

–maven3.x：maven 3.3以上版本；Apache Maven 3.3.9

–IntelliJIDEA2017：IntelliJ IDEA 2017.2.2 x64、STS

–SpringBoot 1.5.9.RELEASE：1.5.9；

统一环境；

#### 1、MAVEN设置；

给maven 的settings.xml配置文件的profiles标签添加

```xml
<profile>
  <id>jdk-1.8</id>
  <activation>
    <activeByDefault>true</activeByDefault>
    <jdk>1.8</jdk>
  </activation>
  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
  </properties>
</profile>
```

#### 2、IDEA设置

整合maven进来；

![idea设置](D:/rubbish/01尚硅谷SpringBoot核心技术篇/Spring Boot 笔记+课件/images/搜狗截图20180129151045.png)

![images/](D:/rubbish/01尚硅谷SpringBoot核心技术篇/Spring Boot 笔记+课件/images/搜狗截图20180129151112.png)

### 4、Spring Boot HelloWorld

一个功能：

浏览器发送hello请求，服务器接受请求并处理，响应Hello World字符串；

#### 1、创建一个maven工程；（jar）

#### 2、导入spring boot相关的依赖

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

#### 3、编写一个主程序；启动Spring Boot应用

```java
/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {

        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }
}
```

#### 4、编写相关的Controller、Service

```java
@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World!";
    }
}

```

#### 5、运行主程序测试

#### 6、简化部署

```xml
 <!-- 这个插件，可以将应用打包成一个可执行的jar包；-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

将这个应用打成jar包，直接使用java -jar的命令进行执行；

### 5、Hello World探究

#### 1、POM文件

##### 1、父项目

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
</parent>

他的父项目是
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>1.5.9.RELEASE</version>
  <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
他来真正管理Spring Boot应用里面的所有依赖版本；

```

Spring Boot的版本仲裁中心；

以后我们导入依赖默认是不需要写版本；（没有在dependencies里面管理的依赖自然需要声明版本号）

##### 2、启动器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**spring-boot-starter**-==web==：

​    spring-boot-starter：spring-boot场景启动器；帮我们导入了web模块正常运行所依赖的组件；

Spring Boot将所有的功能场景都抽取出来，做成一个个的starters（启动器），只需要在项目里面引入这些starter相关场景的所有依赖都会导入进来。要用什么功能就导入什么场景的启动器

#### 2、主程序类，主入口类

```java
/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {

        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }
}

```

@**SpringBootApplication**:    Spring Boot应用标注在某个类上说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
      @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

@**SpringBootConfiguration**:Spring Boot的配置类；

​        标注在某个类上，表示这是一个Spring Boot的配置类；

​        @**Configuration**:配置类上来标注这个注解；

​            配置类 -----  配置文件；配置类也是容器中的一个组件；@Component

@**EnableAutoConfiguration**：开启自动配置功能；

​        以前我们需要配置的东西，Spring Boot帮我们自动配置；@**EnableAutoConfiguration**告诉SpringBoot开启自动配置功能；这样自动配置才能生效；

```java
@AutoConfigurationPackage
@Import(EnableAutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

​          @**AutoConfigurationPackage**：自动配置包

​        @**Import**(AutoConfigurationPackages.Registrar.class)：

​        Spring的底层注解@Import，给容器中导入一个组件；导入的组件由AutoConfigurationPackages.Registrar.class；

==将主配置类（@SpringBootApplication标注的类）的所在包及下面所有子包里面的所有组件扫描到Spring容器；==

​    @**Import**(EnableAutoConfigurationImportSelector.class)；

​        给容器中导入组件？

​        **EnableAutoConfigurationImportSelector**：导入哪些组件的选择器；

​        将所有需要导入的组件以全类名的方式返回；这些组件就会被添加到容器中；

​        会给容器中导入非常多的自动配置类（xxxAutoConfiguration）；就是给容器中导入这个场景需要的所有组件，并配置好这些组件；        ![自动配置类](D:/rubbish/01尚硅谷SpringBoot核心技术篇/Spring Boot 笔记+课件/images/搜狗截图20180129224104.png)

有了自动配置类，免去了我们手动编写配置注入功能组件等的工作；

​        SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class,classLoader)；

==Spring Boot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入到容器中，自动配置类就生效，帮我们进行自动配置工作；==以前我们需要自己配置的东西，自动配置类都帮我们；

J2EE的整体整合解决方案和自动配置都在spring-boot-autoconfigure-1.5.9.RELEASE.jar；

​        

==Spring注解版（谷粒学院）==

### 6、使用Spring Initializer快速创建Spring Boot项目

#### 1、IDEA：使用 Spring Initializer快速创建项目

IDE都支持使用Spring的项目创建向导快速创建一个Spring Boot项目；

选择我们需要的模块；向导会联网创建Spring Boot项目；

默认生成的Spring Boot项目；

- 主程序已经生成好了，我们只需要我们自己的逻辑
- resources文件夹中目录结构
  - static：保存所有的静态资源； js css  images；
  - templates：保存所有的模板页面；（Spring Boot默认jar包使用嵌入式的Tomcat，默认不支持JSP页面）；可以使用模板引擎（freemarker、thymeleaf）；
  - application.properties：Spring Boot应用的配置文件；可以修改一些默认设置；

#### 2、STS使用 Spring Starter Project快速创建项目

## 二、配置文件

### 1、配置文件

SpringBoot使用一个全局的配置文件，配置文件名是固定的；

- application.properties
- application.yml

配置文件作用：修改SpringBoot自动配置的默认；SpringBoot在底层都自动配置好；

标记语言：

以前的配置文件：大多数使用的是  `.xml`文件；

YAML：以数据为中心，比`json`、`.xml`文件等更适合做配置文件。

> YAML配置实例：(application.yml)

```YAML
server:
  port: 8081
```

> application.properties

```properties
server.port=8081
```

> XML:

```xml
<server>
    <port>8081</port>
</server>    
```

### 2、YAML语法：

#### 1、基本语法

k:==(space)==v   表示一对键值对（空格必须有）；

以空格的缩进来控制层级关系；只要左对齐的一列数据，都是同一层级的

```yaml
server:
    port: 8081
    path: /hello
```

属性和值也是大小写敏感；

#### 2、值的写法

**字面量：普通的值（数字、字符、布尔）**

k: v  :字面直接来写；

​        字符串默认不用加上单引号或双引号；

​        “ ”：双引号；不会转义字符串里面的特殊字符；特殊字符表示原有功能

​                `name: “zhangsan \n lisi”`     输出：zhangsan 换行 lisi

         ‘ ‘:单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据

​                `name: ‘zhangsan \n lisi’`    输出：zhangsan \n lisi

**对象、Map(属性和值）（键值对）：**

k:v :在下一行来写对象的属性和值的关系；注意缩进

​        对象还是k:v的方式

```yaml
friends:
    lastname: zhangsan
    age: 20
```

行内写法：

```yaml
friends: {lastName: zhangsan,age: 18}
```

**数组（List、Set)**

用-值表示数组中的一个元素

```yaml
pets:
 - cat
 - dog
 - pig
```

行内写法

```yaml
pets: [cat,dog,pig]
```

### 3、配置文件值注入

配置文件(application.yml)

```yaml
person:
    lastName: zhangsan
    age: 18
    boss: false
    birth: 2020/1/1
    maps: {k1: v1,k2: 12}
    lists:
      - lisi
      - zhaoliu
    dog:
      name: xiaogou
      age: 2
```

javaBean:

```java
/*
* 将配置文件中配置的每一个属性值，映射到这个组件中
* @ConfigurationProperties:告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
*           prefix = "person":配置文件中哪个下面的所有属性进行一一映射；
*/
@Component  //将组件加入容器
@ConfigurationProperties(prefix = "person")
public class Person {
    private String lastName;
    private  Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

    //get set toString等方法
```

我们可以导入配置文件处理器，以后编写配置就有提示了

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

#### 1、properties配置文件在idea中默认utf-8可能会乱码

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200719102015528.png" alt="image-20200719102015528" style="zoom: 50%;" />

#### 2、@Value获取值和@ComfigurationProperties获取值比较

|            | @ComfigurationProperties | @Value |
| ---------- | ------------------------ | ------ |
| 功能         | 批量注入配置文件中的属性             | 一个个指定  |
| 松散绑定（松散语法） | 支持                       | 不支持    |
| SpEL       | 不支持                      | 支持     |
| JSR303数据校验 | 支持                       | 不支持    |
| 复杂类型封装     | 支持                       | 不支持    |

配置文件yml还是properties他们都能获取到值；

如果说，我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用@Value；

如果说，我们专门编写了一个javaBean来和配置文件进行映射，我们就直接使用@ConfigurationProperties；

#### 3、配置文件注入值数据校验

```java
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person {

    /**
     * <bean class="Person">
     *      <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#{SpEL}"></property>
     * <bean/>
     */

   //lastName必须是邮箱格式
    @Email
    //@Value("${person.last-name}")
    private String lastName;
    //@Value("#{11*2}")
    private Integer age;
    //@Value("true")
    private Boolean boss;

    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
```

#### 4、@PropertySource&@ImportResource&@Bean

@**PropertySource**：加载指定的配置文件；

person.java:

```java
@PropertySource(value = {"classpath:person.properties"})
/*
* 将配置文件中配置的每一个属性值，映射到这个组件中
* @ConfigurationProperties:告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
*           prefix = "person":配置文件中哪个下面的所有属性进行一一映射；
*/
@Component  //将组件加入容器
@ConfigurationProperties(prefix = "person")
public class Person {

//    @Value("${person.last-name}")
    private String lastName;
//    @Value("#{11*2}")
    private  Integer age;
//    @Value("true")
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
```

**@PropertySource**:导入Spring的配置文件，让配置文件里面的内容生效，==对`.yml`文件无效==

@**ImportResource**：导入Spring的配置文件，让配置文件里面的内容生效；

Spring Boot里面没有Spring的配置文件，我们自己编写的配置文件，也不能自动识别；

想让Spring的配置文件生效，加载进来；@**ImportResource**标注在一个配置类上

```java
@ImportResource(locations = {"classpath:beans.xml"})
导入Spring的配置文件让其生效
```

不来编写Spring的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="helloService" class="com.atguigu.springboot.service.HelloService"></bean>
</beans>
```

SpringBoot推荐给容器中添加组件的方式；推荐使用全注解的方式

1、配置类**@Configuration**------>Spring配置文件

2、使用**@Bean**给容器中添加组件

```java
/**
 * @Configuration：指明当前类是一个配置类；就是来替代之前的Spring配置文件
 *
 * 在配置文件中用<bean><bean/>标签添加组件
 *
 */
@Configuration
public class MyAppConfig {

    //将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
    @Bean
    public HelloService helloService02(){
        System.out.println("配置类@Bean给容器中添加组件了...");
        return new HelloService();
    }
}
```

### 4、配置文件占位符

#### 1、随机数

```java
${random.value}、${random.int}、${random.long}
${random.int(10)}、${random.int[1024,65536]}
```

#### 2、占位符获取之前配置的值，如果没有可以是用:指定默认值

```properties
person.last-name=张三${random.uuid}
person.age=${random.int}
person.birth=2017/12/15
person.boss=false
person.maps.k1=v1
person.maps.k2=14
person.lists=a,b,c
person.dog.name=${person.hello:hello}_dog
person.dog.age=15
```

### 5、Profile

#### 1、多Profile文件

我们在主配置文件编写的时候，文件名可以是   application-{profile}.properties/yml

默认使用application.properties的配置；

![image-20200719113431257](C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200719113431257.png)

#### 2、yml支持多文档块方式

application.yml：

```yml
server:
  port: 8081

spring:
  profiles:
    active: dev  #指定使用哪个环境
---

server:
  port: 8082
spring:
  profiles: dev
---

server:
  port: 8083
spring:
  profiles: pro
```

#### 3、激活指定profile

​    1、在配置文件中指定  spring.profiles.active=dev

​    2、命令行：

​        java -jar spring-boot-02-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev；

​        可以直接在测试的时候，配置传入命令行参数

​    3、虚拟机参数；

​        -Dspring.profiles.active=dev

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200719115006294.png" alt="image-20200719115006294" style="zoom: 50%;" />

### 6、配置文件加载位置

**D:\Java\SSM\springboot-02**

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

–file:./config/

–file:./

–classpath:/config/

–classpath:/

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

SpringBoot会从这四个位置全部加载主配置文件；**互补配置**（优先级高的配置文件中没有的参数由低优先级文件补齐）；

==我们还可以通过spring.config.location来改变默认的配置文件位置==

**项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默认加载的这些配置文件共同起作用形成互补配置；**

java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --spring.config.location=G:/application.properties

### 7、外部配置加载顺序

**==SpringBoot也可以从以下位置加载配置； 优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置==**

**1.命令行参数**

所有的配置都可以在命令行上进行指定

java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8087  --server.context-path=/abc

多个配置用空格分开； --配置项=值

2.来自java:comp/env的JNDI属性

3.Java系统属性（System.getProperties()）

4.操作系统环境变量

5.RandomValuePropertySource配置的random.*属性值

==**由jar包外向jar包内进行寻找；**==

==**优先加载带profile**==

**6.jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

**7.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

==**再来加载不带profile**==

**8.jar包外部的application.properties或application.yml(不带spring.profile)配置文件**

**9.jar包内部的application.properties或application.yml(不带spring.profile)配置文件**

10.@Configuration注解类上的@PropertySource

11.通过SpringApplication.setDefaultProperties指定的默认属性

所有支持的配置加载来源；

[参考官方文档](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/htmlsingle/#boot-features-profiles)

## 三、日志

#### 1、日志框架

小张；开发一个大型系统；

​        1、System.out.println("")；将关键数据打印在控制台；去掉？写在一个文件？

​        2、框架来记录系统的一些运行时信息；日志框架 ；  zhanglogging.jar；

​        3、高大上的几个功能？异步模式？自动归档？xxxx？  zhanglogging-good.jar？

​        4、将以前框架卸下来？换上新的框架，重新修改之前相关的API；zhanglogging-prefect.jar；

​        5、JDBC---数据库驱动；

​            写了一个统一的接口层；日志门面（日志的一个抽象层）；logging-abstract.jar；

​            给项目中导入具体的日志实现就行了；我们之前的日志框架都是实现的抽象层；

**市面上的日志框架；**

JUL、JCL、Jboss-logging、logback、log4j、log4j2、slf4j....

| 日志门面  （日志的抽象层）                                                                                       | 日志实现                                               |
| ---------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| ~~JCL（Jakarta  Commons Logging）~~    SLF4j（Simple  Logging Facade for Java）    **~~jboss-logging~~** | Log4j  JUL（java.util.logging）  Log4j2  **Logback** |

左边选一个门面（抽象层）、右边来选一个实现；

日志门面：  SLF4J；

日志实现：Logback；

SpringBoot：底层是Spring框架，Spring框架默认是用JCL；‘

​    **==SpringBoot选用 SLF4j和logback；==**

#### 2、SLF4j使用

##### 1、如何在系统中使用SLF4j   https://www.slf4j.org

以后开发的时候，日志记录方法的调用，不应该来直接调用日志的实现类，而是调用日志抽象层里面的方法；

给系统里面导入slf4j的jar和  logback的实现jar

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

图示；

<img src="D:/rubbish/01尚硅谷SpringBoot核心技术篇/Spring Boot 笔记+课件/images/concrete-bindings.png" alt="images/concrete-bindings.png" style="zoom: 67%;" />

每一个日志的实现框架都有自己的配置文件。使用slf4j以后，**配置文件还是做成日志实现框架自己本身的配置文件；**

##### 2、遗留问题

a（slf4j+logback）: Spring（commons-logging）、Hibernate（jboss-logging）、MyBatis、xxxx

统一日志记录，即使是别的框架和我一起统一使用slf4j进行输出？

![](D:/rubbish/01尚硅谷SpringBoot核心技术篇/Spring Boot 笔记+课件/images/legacy.png)

**如何让系统中所有的日志都统一到slf4j；**

==1、将系统中其他日志框架先排除出去；==

==2、用中间包来替换原有的日志框架；==

==3、我们导入slf4j其他的实现==

#### 3、日志使用

##### 1、默认配置

SpringBoot默认帮我们配置好了日志；

```java
//记录器
    Logger logger = LoggerFactory.getLogger(getClass());
    @Test
    public void contextLoads() {
        //System.out.println();

        //日志的级别；
        //由低到高   trace<debug<info<warn<error
        //可以调整输出的日志级别；日志就只会在这个级别以以后的高级别生效
        logger.trace("这是trace日志...");
        logger.debug("这是debug日志...");
        //SpringBoot默认给我们使用的是info级别的，没有指定级别的就用SpringBoot默认规定的级别；root级别
        logger.info("这是info日志...");
        logger.warn("这是warn日志...");
        logger.error("这是error日志...");
    }
```

>   日志输出格式：
>         %d表示日期时间，
>         %thread表示线程名，
>         %-5level：级别从左显示5个字符宽度
>         %logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
>         %msg：日志消息，
>         %n是换行符
>     -->
>     %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n

SpringBoot修改日志的默认配置（application.properties）

```properties
logging.level.com.example = trace

# 不指定路径在当前项目下生成springboot.log日志
# 可以指定完整的路径；
#logging.file.name=springboot.log

# 在当前磁盘的根路径下创建spring文件夹和里面的log文件夹；使用 spring.log 作为默认文件
logging.file.path=/spring/log

#  在控制台输出的日志的格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
# 指定文件中日志输出的格式
logging.pattern.file=%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} ==== %msg%n
```

## 四、Web开发

### 1、简介

使用SpringBoot；

**1）、创建SpringBoot应用，选中我们需要的模块；**

**2）、SpringBoot已经默认将这些场景配置好了，只需要在配置文件中指定少量配置就可以运行起来**

**3）、自己编写业务代码；**

**自动配置原理？**

这个场景SpringBoot帮我们配置了什么？能不能修改？能修改哪些配置？能不能扩展？xxx

```
xxxxAutoConfiguration：帮我们给容器中自动配置组件；
xxxxProperties:配置类来封装配置文件的内容；

```

### 2、SpringBoot对静态资源的映射规则；

```java
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
public class ResourceProperties implements ResourceLoaderAware {
  //可以设置和静态资源有关的参数，缓存时间等
```

```java
    WebMvcAuotConfiguration：
        @Override
        public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if (!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
                return;
            }
            Integer cachePeriod = this.resourceProperties.getCachePeriod();
            if (!registry.hasMappingForPattern("/webjars/**")) {
                customizeResourceHandlerRegistration(
                        registry.addResourceHandler("/webjars/**")
                                .addResourceLocations(
                                        "classpath:/META-INF/resources/webjars/")
                        .setCachePeriod(cachePeriod));
            }
            String staticPathPattern = this.mvcProperties.getStaticPathPattern();
              //静态资源文件夹映射
            if (!registry.hasMappingForPattern(staticPathPattern)) {
                customizeResourceHandlerRegistration(
                        registry.addResourceHandler(staticPathPattern)
                                .addResourceLocations(
                                        this.resourceProperties.getStaticLocations())
                        .setCachePeriod(cachePeriod));
            }
        }

        //配置欢迎页映射
        @Bean
        public WelcomePageHandlerMapping welcomePageHandlerMapping(
                ResourceProperties resourceProperties) {
            return new WelcomePageHandlerMapping(resourceProperties.getWelcomePage(),
                    this.mvcProperties.getStaticPathPattern());
        }

       //配置喜欢的图标
        @Configuration
        @ConditionalOnProperty(value = "spring.mvc.favicon.enabled", matchIfMissing = true)
        public static class FaviconConfiguration {

            private final ResourceProperties resourceProperties;

            public FaviconConfiguration(ResourceProperties resourceProperties) {
                this.resourceProperties = resourceProperties;
            }

            @Bean
            public SimpleUrlHandlerMapping faviconHandlerMapping() {
                SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
                mapping.setOrder(Ordered.HIGHEST_PRECEDENCE + 1);
                  //所有  **/favicon.ico 
                mapping.setUrlMap(Collections.singletonMap("**/favicon.ico",
                        faviconRequestHandler()));
                return mapping;
            }

            @Bean
            public ResourceHttpRequestHandler faviconRequestHandler() {
                ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
                requestHandler
                        .setLocations(this.resourceProperties.getFaviconLocations());
                return requestHandler;
            }

        }

```

==1）、所有 /webjars/** ，都去 classpath:/META-INF/resources/webjars/ 找资源；==

​    webjars：以jar包的方式引入静态资源；

http://www.webjars.org/

![](D:/rubbish/01尚硅谷SpringBoot核心技术篇/Spring Boot 笔记+课件/images/搜狗截图20180203181751.png)

localhost:8080/webjars/jquery/3.3.1/jquery.js

```xml
<!--引入jquery-webjar-->在访问的时候只需要写webjars下面资源的名称即可
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
            <version>3.3.1</version>
        </dependency>
```

==2）、"/**" 访问当前项目的任何资源，都去（静态资源的文件夹）找映射==

```
"classpath:/META-INF/resources/", 
"classpath:/resources/",
"classpath:/static/", 
"classpath:/public/" 
"/"：当前项目的根路径
```

localhost:8080/abc ===  去静态资源文件夹里面找abc

==3）、欢迎页； 静态资源文件夹下的所有index.html页面；被"/**"映射；==

​    localhost:8080/   找index页面

==4）、所有的 **/favicon.ico  都是在静态资源文件下找；==

### 3、模板引擎

JSP、Velocity、Freemarker、Thymeleaf

![](D:/rubbish/01尚硅谷SpringBoot核心技术篇/Spring Boot 笔记+课件/images/template-engine.png)

SpringBoot推荐的Thymeleaf；

语法更简单，功能更强大；

#### 1、引入thymeleaf；

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
切换thymeleaf版本
<properties>
        <thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>
        <!-- 布局功能的支持程序  thymeleaf3主程序  layout2以上版本 -->
        <!-- thymeleaf2   layout1-->
        <thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>
  </properties>

<!-- SpringBoot2.X及以上自动装配最新版 -->
```

#### 2、Thymeleaf使用

```java
@ConfigurationProperties(prefix = "spring.thymeleaf")
public class ThymeleafProperties {

    private static final Charset DEFAULT_ENCODING = Charset.forName("UTF-8");

    private static final MimeType DEFAULT_CONTENT_TYPE = MimeType.valueOf("text/html");

    public static final String DEFAULT_PREFIX = "classpath:/templates/";

    public static final String DEFAULT_SUFFIX = ".html";
      //
```

只要我们把HTML页面放在classpath:/templates/，thymeleaf就能自动渲染；

![image-20200721112836924](C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200721112836924.png)

使用：

1、导入thymeleaf的名称空间

```xml
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

2、使用thymeleaf语法；

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>成功！</h1>
    <!--th:text 将div里面的文本内容设置为 -->
    <div th:text="${hello}">这是显示欢迎信息</div>
</body>
</html>
```

#### 3、语法规则

1）、th:text；改变当前元素里面的文本内容；

​    th：任意html属性；来替换原生属性的值

![](D:/rubbish/01尚硅谷SpringBoot核心技术篇/Spring Boot 笔记+课件/images/2018-02-04_123955.png)

2）、表达式？

```properties
Simple expressions:（表达式语法）
    Variable Expressions: ${...}：获取变量值；OGNL；
            1）、获取对象的属性、调用方法
            2）、使用内置的基本对象：
                #ctx : the context object.
                #vars: the context variables.
                #locale : the context locale.
                #request : (only in Web Contexts) the HttpServletRequest object.
                #response : (only in Web Contexts) the HttpServletResponse object.
                #session : (only in Web Contexts) the HttpSession object.
                #servletContext : (only in Web Contexts) the ServletContext object.

                ${session.foo}
            3）、内置的一些工具对象：
#execInfo : information about the template being processed.
#messages : methods for obtaining externalized messages inside variables expressions, in the same way as they would be obtained using #{…} syntax.
#uris : methods for escaping parts of URLs/URIs
#conversions : methods for executing the configured conversion service (if any).
#dates : methods for java.util.Date objects: formatting, component extraction, etc.
#calendars : analogous to #dates , but for java.util.Calendar objects.
#numbers : methods for formatting numeric objects.
#strings : methods for String objects: contains, startsWith, prepending/appending, etc.
#objects : methods for objects in general.
#bools : methods for boolean evaluation.
#arrays : methods for arrays.
#lists : methods for lists.
#sets : methods for sets.
#maps : methods for maps.
#aggregates : methods for creating aggregates on arrays or collections.
#ids : methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).

    Selection Variable Expressions: *{...}：选择表达式：和${}在功能上是一样；
        补充：配合 th:object="${session.user}：
   <div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
    </div>

    Message Expressions: #{...}：获取国际化内容
    Link URL Expressions: @{...}：定义URL；
            @{/order/process(execId=${execId},execType='FAST')}
    Fragment Expressions: ~{...}：片段引用表达式
            <div th:insert="~{commons :: main}">...</div>

Literals（字面量）
      Text literals: 'one text' , 'Another one!' ,…
      Number literals: 0 , 34 , 3.0 , 12.3 ,…
      Boolean literals: true , false
      Null literal: null
      Literal tokens: one , sometext , main ,…
Text operations:（文本操作）
    String concatenation: +
    Literal substitutions: |The name is ${name}|
Arithmetic operations:（数学运算）
    Binary operators: + , - , * , / , %
    Minus sign (unary operator): -
Boolean operations:（布尔运算）
    Binary operators: and , or
    Boolean negation (unary operator): ! , not
Comparisons and equality:（比较运算）
    Comparators: > , < , >= , <= ( gt , lt , ge , le )
    Equality operators: == , != ( eq , ne )
Conditional operators:条件运算（三元运算符）
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
Special tokens:
    No-Operation: _ 
```

### 4、SpringMVC自动配置

还未学习

### 5、如何修改SpringBoot的默认配置

模式：

​    1）、SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean、@Component）如果有就用用户配置的，如果没有，才自动配置；如果有些组件可以有多个（ViewResolver）将用户配置的和自己默认的组合起来；

​    2）、在SpringBoot中会有非常多的xxxConfigurer帮助我们进行扩展配置

​    3）、在SpringBoot中会有很多的xxxCustomizer帮助我们进行定制配置

### 6、RestFulCRUD

**D:\Java\springboot-04-webcurd**

#### 1、默认访问首页

**Spring Boot** 项目启动后，默认会去查找 **index.html** 文件作为首页面。当然我们可以指定其它页面作为首页面，下面通过样例进行演示。

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200722170259437.png" alt="image-20200722170259437" style="zoom:50%;" />

##### 一、使用index.html作为首页

当前SpringBoot版本：2.3.1

WebMvcConfigurationAdapter已失效，使用WebMvcConfigurer接口

###### 1、静态页面

**Spring Boot** 项目在启动后，首先回去静态资源路径（**resources/static**）下查找 **index.html** 作为首页文件。

[![原文:SpringBoot - 设置项目默认的首页（欢迎页）](https://www.hangge.com/blog_uploads/201907/2019072514240527958.png)](https://www.hangge.com/blog/cache/detail_2528.html#)

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200722170526470.png" alt="image-20200722170526470" style="zoom:50%;" />

###### 2，动态首页

  如果在静态资源路径（**resources/static**）下找不到 **index.html**，则会到 **resources/templates** 目录下找 **index.html**（使用 **Thymeleaf** 模版）作为首页文件。

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200722170650672.png" alt="image-20200722170650672" style="zoom:50%;" />

##### 二、使用非index.html的文件作为首页

###### 1、静态页面

想让静态文件夹下的index.html文件作为首页。

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200722170526470.png" alt="image-20200722170526470" style="zoom:50%;" />

（1）一种方法通过自定义一个Controller来进行转发：

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200722171624555.png" alt="image-20200722171624555" style="zoom: 50%;" />

```java
@Controller
public class HelloController {

    @RequestMapping("/")
    public String hey(){
        return "forward:index.html";
    }
}
```

（2）另一种方式是通过自定义一个 **MVC** 配置，并重写 **addViewControllers** 方法进行转发：

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("forward:index.html");
    }
}
```

###### 2、动态页面

假设我们在 **resources/templates** 目录下有个 **login.html** 文件（使用 **Thymeleaf** 模版），想让它作为首页。

![image-20200722171946056](C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200722171946056.png)

（1）一种方式通过自定义一个 **Controller** 来实现，在 **Controller** 中返回逻辑视图名即可：

```java
@Controller
public class HelloController {
     @RequestMapping("/")
     public String hello(){
         return "login";
     }
}
```

（2）另一种方式是通过自定义一个 **MVC** 配置，并重写 **addViewControllers** 方法进行映射关系配置即可。

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");
    }
}
```

#### 2、国际化

**1）、编写国际化配置文件；**

2）、使用ResourceBundleMessageSource管理国际化资源文件

3）、在页面使用fmt:message取出国际化内容

步骤：

在**application.properties**文件中配置国际化文件：

```properties
application.properties
```

（1）、编写国际化配置文件，抽取页面需要显示的国际化消息

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200724095536349.png" alt="image-20200724095536349" style="zoom:67%;" />

<img src="C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200724095631399.png" alt="image-20200724095631399" style="zoom:67%;" />

2）、SpringBoot自动配置好了管理国际化资源文件的组件；

```java
@ConfigurationProperties(prefix = "spring.messages")
public class MessageSourceAutoConfiguration {

    /**
     * Comma-separated list of basenames (essentially a fully-qualified classpath
     * location), each following the ResourceBundle convention with relaxed support for
     * slash based locations. If it doesn't contain a package qualifier (such as
     * "org.mypackage"), it will be resolved from the classpath root.
     */
    private String basename = "messages";  
    //我们的配置文件可以直接放在类路径下叫messages.properties；

    @Bean
    public MessageSource messageSource() {
        ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
        if (StringUtils.hasText(this.basename)) {
            //设置国际化资源文件的基础名（去掉语言国家代码的）
            messageSource.setBasenames(StringUtils.commaDelimitedListToStringArray(
                    StringUtils.trimAllWhitespace(this.basename)));
        }
        if (this.encoding != null) {
            messageSource.setDefaultEncoding(this.encoding.name());
        }
        messageSource.setFallbackToSystemLocale(this.fallbackToSystemLocale);
        messageSource.setCacheSeconds(this.cacheSeconds);
        messageSource.setAlwaysUseMessageFormat(this.alwaysUseMessageFormat);
        return messageSource;
    }
```

（3）、页面获取国际化的值

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <meta name="description" content="">
        <meta name="author" content="">
        <title>Signin Template for Bootstrap</title>
        <!-- Bootstrap core CSS -->
        <link href="asserts/css/bootstrap.min.css" rel="stylesheet">
        <!-- Custom styles for this template -->
        <link href="asserts/css/signin.css" rel="stylesheet">
    </head>

    <body class="text-center">
        <form class="form-signin" action="dashboard.html" th:action="@{/login}" method="post">
            <img class="mb-4" src="asserts/img/bootstrap-solid.svg" alt="" width="72" height="72">
            <h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
            <p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
            <label class="sr-only" th:text="#{login.username}"}>Username</label>
            <input type="text" name="username" class="form-control" placeholder="Username" th:placeholder="#{login.username}" required="" autofocus="">
            <label class="sr-only" th:text="#{login.password}">Password</label>
            <input type="password" name="password" class="form-control" placeholder="Password" th:placeholder="#{login.password}" required="">
            <div class="checkbox mb-3">
                <label>
          <input type="checkbox" value="remember-me"> [[#{login.rem}]]
        </label>
            </div>
            <button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.btn}">Sign in</button>
            <p class="mt-5 mb-3 text-muted">© 2017-2018</p>
            <a class="btn btn-sm" th:href="@{/index(l='zh_CN')}">中文</a>
            <a class="btn btn-sm" th:href="@{/index(l='en_US')}">English</a>
        </form>

    </body>

</html>
```

实现效果：会根据浏览器语言变更语言；

（4）、点击按钮实现国际化

```java
//获取地区信息
public class MyLocalResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest httpServletRequest) {
        String l = httpServletRequest.getParameter("l");
        Locale locale=Locale.getDefault();
        if (!StringUtils.isEmpty(l)){
            String[] split = l.split("_");
            locale = new Locale(split[0],split[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {

    }
}

```

```java
//在配置其中将国际化组件注入容器，实现国际化
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocalResolver();
    }
```

#### 3、登录

开发期间模板引擎以后，要实时生效

（1）、禁用模板引擎缓存

```properties
spring.thymeleaf.cache=false
```

2）、页面修改完成以后ctrl+f9：重新编译；

登陆错误消息的显示

```html
<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
```

#### 4、拦截器进行登陆检查

拦截器：

```java
/*
* 拦截器，对未登录状态进行拦截，禁止访问
* */
public class LoginHandlerInterceptor implements HandlerInterceptor {
    //目标方法执行前
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object user = request.getSession().getAttribute("loginUsername");
        if(user==null){
            request.setAttribute("msg","请先登录");
            request.getRequestDispatcher("/index").forward(request,response);
            return false;
        }else {
            return true;
        }
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```

注册拦截器：

```java
@Configuration
public class DefaultViewConfig implements WebMvcConfigurer {
    //在SpringBoot1.X中默认放行静态资源，在2.X中需手动配置放行静态资源

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
                .excludePathPatterns("/index","/","/login","/asserts/**","/webjar/**");
    }
}
```

#### 5、CRUD员工列表

实验要求：

1）、RestfulCRUD：CRUD满足Rest风格；

URI：  /资源名称/资源标识       HTTP请求方式区分对资源CRUD操作

|     | 普通CRUD（uri来区分操作）        | RestfulCRUD       |
| --- | ----------------------- | ----------------- |
| 查询  | getEmp                  | emp---GET         |
| 添加  | addEmp?xxx              | emp---POST        |
| 修改  | updateEmp?id=xxx&xxx=xx | emp/{id}---PUT    |
| 删除  | deleteEmp?id=1          | emp/{id}---DELETE |

2）、实验的请求架构;

| 实验功能               | 请求URI | 请求方式   |
| ------------------ | ----- | ------ |
| 查询所有员工             | emps  | GET    |
| 查询某个员工(来到修改页面)     | emp/1 | GET    |
| 来到添加页面             | emp   | GET    |
| 添加员工               | emp   | POST   |
| 来到修改页面（查出员工进行信息回显） | emp/1 | GET    |
| 修改员工               | emp   | PUT    |
| 删除员工               | emp/1 | DELETE |

3）、员工列表：

#### thymeleaf公共页面元素抽取

```html
1、抽取公共片段
<div th:fragment="copy">
© 2011 The Good Thymes Virtual Grocery
</div>

2、引入公共片段
<div th:insert="~{footer :: copy}"></div>
~{templatename::selector}：模板名::选择器
~{templatename::fragmentname}:模板名::片段名

3、默认效果：
insert的公共片段在div标签中
如果使用th:insert等属性进行引入，可以不用写~{}：
行内写法可以加上：[[~{}]];[(~{})]；
```

三种引入公共片段的th属性：

**th:insert**：将公共片段整个插入到声明引入的元素中

**th:replace**：将声明引入的元素替换为公共片段

**th:include**：将被引入的片段的内容包含进这个标签中

```html
<footer th:fragment="copy">
© 2011 The Good Thymes Virtual Grocery
</footer>

引入方式
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>

效果
<div>
    <footer>
    © 2011 The Good Thymes Virtual Grocery
    </footer>
</div>

<footer>
© 2011 The Good Thymes Virtual Grocery
</footer>

<div>
© 2011 The Good Thymes Virtual Grocery
</div>
```

引入片段时传入参数：（实现对css的动态实现）

bar.html（公共模板）

`th:class="${activeUri=='main'?'nav-link active':'nav-link'}"`当页面上传的请求为`main`时采用`nav-link active`样式（高亮）

否则使用`nav-link`样式

```html
<li class="nav-item">
                <a class="nav-link active" href="#" th:href="@{/main.html}"
                   th:class="${activeUri=='main'?'nav-link active':'nav-link'}">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-home">
                        <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
                        <polyline points="9 22 9 12 15 12 15 22"></polyline>
                    </svg>
                    Dashboard <span class="sr-only">(current)</span>
                </a>
            </li>
```

![image-20200727105410313](C:\Users\Lin\AppData\Roaming\Typora\typora-user-images\image-20200727105410313.png)

![image-20200727105648092](D:\Java\SSM\SpringBoot\image-20200727105648092.png)

```html
<li class="nav-item">
                <a class="nav-link" href="#" th:href="@{/emps}"
                th:class="${activeUri=='emps'?'nav-link active':'nav-link'}">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-users">
                        <path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path>
                        <circle cx="9" cy="7" r="4"></circle>
                        <path d="M23 21v-2a4 4 0 0 0-3-3.87"></path>
                        <path d="M16 3.13a4 4 0 0 1 0 7.75"></path>
                    </svg>
                    员工管理
                </a>
            </li>
```

#### 6、员工添加

## 六、SpringBoot与数据访问

### 1、JDBC

D:\Java\SpringBoot\spring-boot-06-data-jdbc

导入 SQL.API    MySQL Driver   

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
        <scope>runtime</scope>
</dependency>
```

application.yml:

```yml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://192.168.31.190:3306/jdbc?serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
```

效果：

默认是用com.zaxxer.hikari.HikariDataSource作为数据源；

数据源的相关配置都在DataSourceProperties里面；

自动配置原理：

org.springframework.boot.autoconfigure.jdbc：

1、参考DataSourceConfiguration，根据配置创建数据源，默认使用Tomcat连接池；可以使用spring.datasource.type指定自定义的数据源类型；

2、SpringBoot默认可以支持；

```
org.apache.tomcat.jdbc.pool.DataSource、HikariDataSource、BasicDataSource
```

执行.sql文件：

1、将sql文件放在resource下；

```properties
schema-*.sql、data-*.sql
默认规则：schema.sql，schema-all.sql；

或者可以使用在application.properties中配置   
    schema:
      - classpath:department.sql
      指定位置
```

5、操作数据库：自动配置了JdbcTemplate操作数据库

### 2、整合Druid数据源

SpringBoot2.0支持自动配置。

**1、在pom.xml中引入druid依赖**

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.23</version>
</dependency>
```

2、在application.yml中配置

```yml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://192.168.31.190:3306/jdbc?serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
    initialization-mode: always
    type: com.alibaba.druid.pool.DruidDataSource

#    hikari连接池使用
#    schema:
#      - classpath:department.sql

    #config druid
    #连接池的设置
    druid:
      #初始化时建立物理连接的个数
      initial-size: 5
      #最小连接池数量
      min-idle: 5
      #最大连接池数量 maxIdle已经不再使用
      max-active: 20
      #获取连接时最大等待时间，单位毫秒
      max-wait: 60000
      #申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。
      test-while-idle: true
      #既作为检测的间隔时间又作为testWhileIdel执行的依据
      time-between-eviction-runs-millis: 60000
      #销毁线程时检测当前连接的最后活动时间和当前时间差大于该值时，关闭当前连接
      min-evictable-idle-time-millis: 30000
      #用来检测连接是否有效的sql 必须是一个查询语句
      #mysql中为 select 'x'
      #oracle中为 select 1 from dual
      validation-query: select 'x'
      #申请连接时会执行validationQuery检测连接是否有效,开启会降低性能,默认为true
      test-on-borrow: false
      #归还连接时会执行validationQuery检测连接是否有效,开启会降低性能,默认为true
      test-on-return: false
      #是否缓存preparedStatement,mysql5.5+建议开启
      pool-prepared-statements: true
      #当值大于0时poolPreparedStatements会自动修改为true
      max-pool-prepared-statement-per-connection-size: 20
      #配置监控统计拦截的filters，去掉监控界面sql无法统计，"wall"用于防火墙
      filters: stat,wall
      #通过connectProperties属性来打开mergeSql功能；慢SQL记录
      connection-properties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
      #合并多个DruidDataSource的监控数据
      use-global-data-source-stat: true
      #设置访问druid监控页的账号和密码,默认没有
      stat-view-servlet:
        url-pattern: "/druid/*"
        enabled: true
        login-username: admin
        login-password: 123456
```
