## Spring IOC

控制反转，不用再使用new创建对象，这个控制权交给框架。

### Spring Bean

Bean代指被IoC容器管理的对象。

需要告诉IoC容器帮助我们管理哪些对象，通过配置元数据来实现。配置元数据可以是XML文件、注解或者Java配置类。

### 将一个类声明为Bean的注解有哪些？

- `@Component`：通用的注解，可标注任意类为`Spring`组件。如果一个Bean不知道属于哪一层，可以使用`@Component`注解标注。
- `@Repository`：对应持久层即Dao层，主要用于数据库相关操作，现多出现在Mapper中。
- `@Service`：对于服务层
- `@Controller`：对应Spring MVC控制层，主要勇于接受用户请求并调用`Service`层返回数据给前端页面。

### `@Component`和`@Bean`的区别是什么？

- `@Component` 注解作用于类，而`@Bean`注解作用于方法。

### 注入Bean的注解有哪些？

Spring内置的`@Autowired`以及JDK内置的`@Resource`和`@Inject`都可以用于注入Bean。

### `@Autowired`和`@Resource`的区别是什么？

`@Autowired`属于Spring内置的注解，默认注入方式为`byType`（根据类型匹配）