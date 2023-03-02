| 名称 | @Component/@Controller/@Service/@Repository |
| ---- | ------------------------------------------- |
| 类型 | 类注解                                      |
| 位置 | 类定义上方                                  |
| 作用 | 设置该类为spring管理的bean                  |
| 属性 | value（默认）：定义bean的id                 |

| 名称 | @Configuration              |
| ---- | --------------------------- |
| 类型 | 类注解                      |
| 位置 | 类定义上方                  |
| 作用 | 设置该类为spring配置类      |
| 属性 | value（默认）：定义bean的id |

| 名称 | @ComponentScan                                           |
| ---- | -------------------------------------------------------- |
| 类型 | 类注解                                                   |
| 位置 | 类定义上方                                               |
| 作用 | 设置spring配置类扫描路径，用于加载使用注解格式定义的bean |
| 属性 | value（默认）：扫描路径，此路径可以逐层向下扫描          |

| 名称 | @Scope                                                       |
| ---- | ------------------------------------------------------------ |
| 类型 | 类注解                                                       |
| 位置 | 类定义上方                                                   |
| 作用 | 设置该类创建对象的作用范围<br/>可用于设置创建出的bean是否为单例对象 |
| 属性 | value（默认）：定义bean作用范围，<br/>==默认值singleton（单例），可选值prototype（非单例）== |

| 名称 | @PostConstruct         |
| ---- | ---------------------- |
| 类型 | 方法注解               |
| 位置 | 方法上                 |
| 作用 | 设置该方法为初始化方法 |
| 属性 | 无                     |

| 名称 | @PreDestroy          |
| ---- | -------------------- |
| 类型 | 方法注解             |
| 位置 | 方法上               |
| 作用 | 设置该方法为销毁方法 |
| 属性 | 无                   |

| 名称 | @Autowired                                                   |
| ---- | ------------------------------------------------------------ |
| 类型 | 属性注解  或  方法注解（了解）  或  方法形参注解（了解）     |
| 位置 | 属性定义上方  或  标准set方法上方  或  类set方法上方  或  方法形参前面 |
| 作用 | 为引用类型属性设置值                                         |
| 属性 | required：true/false，定义该属性是否允许为null               |

| 名称 | @Qualifier                                           |
| ---- | ---------------------------------------------------- |
| 类型 | 属性注解  或  方法注解（了解）                       |
| 位置 | 属性定义上方  或  标准set方法上方  或  类set方法上方 |
| 作用 | 为引用类型属性指定注入的beanId                       |
| 属性 | value（默认）：设置注入的beanId                      |

| 名称 | @Value                                               |
| ---- | ---------------------------------------------------- |
| 类型 | 属性注解  或  方法注解（了解）                       |
| 位置 | 属性定义上方  或  标准set方法上方  或  类set方法上方 |
| 作用 | 为  基本数据类型  或  字符串类型  属性设置值         |
| 属性 | value（默认）：要注入的属性值                        |

| 名称 | @PropertySource                                              |
| ---- | ------------------------------------------------------------ |
| 类型 | 类注解                                                       |
| 位置 | 类定义上方                                                   |
| 作用 | 加载properties文件中的属性值                                 |
| 属性 | value（默认）：设置加载的properties文件对应的文件名或文件名组成的数组 |

| 名称 | @Bean                                  |
| ---- | -------------------------------------- |
| 类型 | 方法注解                               |
| 位置 | 方法定义上方                           |
| 作用 | 设置该方法的返回值作为spring管理的bean |
| 属性 | value（默认）：定义bean的id            |

| 名称 | @Import                                                      |
| ---- | ------------------------------------------------------------ |
| 类型 | 类注解                                                       |
| 位置 | 类定义上方                                                   |
| 作用 | 导入配置类                                                   |
| 属性 | value（默认）：定义导入的配置类类名，<br/>当配置类有多个时使用数组格式一次性导入多个配置类@Import(Xxx.class) |

| 名称 | @RunWith                                                     |
| ---- | ------------------------------------------------------------ |
| 类型 | 测试类注解                                                   |
| 位置 | 测试类定义上方                                               |
| 作用 | 设置JUnit运行器                                              |
| 属性 | value（默认）：运行所使用的运行期 @RunWith(SpringJUnit4ClassRunner.class) |

| 名称 | @ContextConfiguration                                        |
| ---- | ------------------------------------------------------------ |
| 类型 | 测试类注解                                                   |
| 位置 | 测试类定义上方                                               |
| 作用 | 设置JUnit加载的Spring核心配置                                |
| 属性 | classes：核心配置类，可以使用数组的格式设定加载多个配置类<br/>locations:配置文件，可以使用数组的格式设定加载多个配置文件名称<br/>如果测试的是注解配置类，则使用`@ContextConfiguration(classes = 配置类.class)`<br/>如果测试的是配置文件，则使用`@ContextConfiguration(locations={配置文件名,...})` |

| 名称 | @EnableAspectJAutoProxy |
| ---- | ----------------------- |
| 类型 | 配置类注解              |
| 位置 | 配置类定义上方          |
| 作用 | 开启注解格式AOP功能     |

| 名称 | @Aspect               |
| ---- | --------------------- |
| 类型 | 类注解                |
| 位置 | 切面类定义上方        |
| 作用 | 设置当前类为AOP切面类 |

| 名称 | @Pointcut                   |
| ---- | --------------------------- |
| 类型 | 方法注解                    |
| 位置 | 切入点方法定义上方          |
| 作用 | 设置切入点方法              |
| 属性 | value（默认）：切入点表达式 |

| 名称 | @Before                                                      |
| ---- | ------------------------------------------------------------ |
| 类型 | 方法注解                                                     |
| 位置 | 通知方法定义上方                                             |
| 作用 | 设置当前通知方法与切入点之间的绑定关系，当前通知方法在原始切入点方法前运行 |

| 名称 | @EnableTransactionManagement           |
| ---- | -------------------------------------- |
| 类型 | 配置类注解                             |
| 位置 | 配置类定义上方                         |
| 作用 | 设置当前Spring环境中开启注解式事务支持 |

| 名称 | @Transactional                                               |
| ---- | ------------------------------------------------------------ |
| 类型 | 接口注解  类注解  方法注解                                   |
| 位置 | 业务层接口上方  业务层实现类上方  业务方法上方               |
| 作用 | 为当前业务层方法添加事务（如果设置在类或接口上方则类或接口中所有方法均添加事务） |

---

##  IoC 的实现机制

**简单工厂 + 反射**

什么是工厂模式：很简单，就是调用一个方法（工厂方法）根据传入的参数返回一个对象。

IOC简单工厂+反射：

简单工厂就是调用这个方法 BeanFactory.getBean(name)；

反射就是在工厂模式getBean()方法中通过反射的方式来创建Bean。

 -- 反射会根据getBean(name), 传入的name来通过以下两种配置方式找到类全路径然后进行创建Bean对象。

```java
1、 通过spring.xml配置
<bean id="userDao" class="com.test.***.userDao"/>
    
2、通过注解配置
@Component("userDao")、@Service等    
```



---

## IOC优点

1. 集中管理对象，方便维护
2. 降低耦合度

---

## BeanFactory的作用

- BeanFactory是Spring中非常核心的一个==顶层接口==
- 它是Bean的“工厂”、它的主要职责就是==生产Bean==;
- 它实现了==简单工厂==的设计模式，通过调用==getBean==传入标识生产一个Bean;
- 它有非常多的实现类、每个工厂都有不同的职责（单一职责）功能，最强大的工厂是：DefaultListableBeanFactory Spring底层就是使用的该实现工厂进行生产Bean的
- BeanFacotry他也是容器

---

## ApplicationContext启动过程整个启动过程

<img src="https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302281449706.png" alt="请添加图片描述" style="zoom:150%;" />

![image-20230228145732807](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302281457926.png)

---

## BeanDefinition的作用

它主要负责存储Bean的==定义信息==：决定Bean的生产方式。

![image-20230228144241439](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302281442565.png)

后续BeanFactory根据这些信息就行生产Bean:比如实例化可以通过class进行反射进而得到实例对象，比如lazy则不会在ioc加载时创建Bean

---

## BeanFactory和ApplicationContext的区别

![img](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302281504517.jpeg)

- **BeanFactory：**
  
  - 是Spring里面最底层的接口，提供了最简单的容器的功能，只提供了实例化对象和拿对象的功能；
- **ApplicationContext：**
  - 应用==上下文==，继承BeanFactory接口，它不生产Bean而是通知FactoryBean来进行生产，getBean是一个门面方法
  - 它是Spring的一各更高级的容器，提供了更多的有用的功能；
    - 国际化（MessageSource）
    - 访问资源，如URL和文件（ResourceLoader）
    - 载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层
    - 消息发送、响应机制（ApplicationEventPublisher）
    - AOP（拦截器）
- 共同点
  
- 都可以作为容器
  
- **区别**

  - **BeanFactory：**BeanFactory在启动的时候不会去实例化Bean，中有从容器中拿Bean的时候才会去实例化；

  - **ApplicationContext：**ApplicationContext在启动的时候就把所有的Bean全部实例化了。它还可以为Bean配置lazy-init=true来让Bean延迟实例化；

    ![image-20230228150205689](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302281502722.png)

- 延迟实例化的优缺点

  - 延迟实例化的优点：（BeanFactory）
    - 应用启动的时候占用资源很少；对资源要求较高的应用，比较有优势；
  - 不延迟实例化的优点： （ApplicationContext）
    - 所有的Bean在启动的时候都加载，系统运行的速度快；
    - 在启动的时候所有的Bean都加载了，我们就能在系统启动的时候，尽早的发现系统中的配置问题
    - 建议web应用，在启动的时候就把所有的Bean都加载了。（把费时的操作放到系统启动中完成）

---

## IOC容器的加载过程--需要看源码

![image-20230228145732807](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302281457926.png)

- 从什么时候开始的
  - new ApplicationContext（）的时候开始
- 概念态调用一个**Bean工厂的后置处理器**（invokeBeanFacotryPostProcessors）注册成为定义态
- BeanFactory调用getBena（）方法

---

## 什么是SpringBean？JavaBean和SpringBean和对象的区别？

- SpringBean
  - 在Spring中，构成应用程序主干并由Spring IOC容器管理的对象称为bean。
  - Bean是一个由SpringIOC容器实例化、组装和管理的对象
- JavaBean
  - java的类就是javabean

---

## 配置Bean有几种方式

1、基于XML形式的装配；

```xml
<bean id="postservice" class="com.bbs.service.impl.PostserviceImpl">  </bean>
```

2、基于注解形式的装配；

- @Component普通的对象

- @Service业务层对象

- @Controller控制层对象

- @Repository是[dao层](https://so.csdn.net/so/search?q=dao层&spm=1001.2101.3001.7020)对象 

  **记得配置扫描包<component-scan>**

  通过反射进行调用

3.javaConfig：

​	@Bean注解

​	需要返回一个对象，这个返回的对象就是Bean

4.@Import

 	1）在配置类上配置@Import注解，并定义要创建的bean.class；容器启动后就会自动创建

​    2）使用ImportSelector，实现ImportSelector接口

​    3）使用 ImportBeanDefinitionRegistrar，实现ImportBeanDefinitionRegistrar接口

---

## Bean有几种作用域

- 配置方式

  - 配置文件 scope属性

  ```xml
  <bean id="accountService" class="x.y.z.AccountService" scope="prototype"/>
  ```

  - 注解
    - @Scope

- 作用域

  ​	[【Java第34期】：Bean的六种作用域_bean的作用域_有只小猪飞走啦的博客-CSDN博客](https://blog.csdn.net/m0_62262008/article/details/128050330)

  ![image-20230228170924835](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302281709890.png)

---

## 单例Bean的优势？（bean的设计模式的优势）

- 由于**不会每次都新创建新对象**所以有一下几个性能上的优势：
  1. 减少了新生成实例的消耗
     - 新生成实例消耗包括两方面，
       - 第一，spig会通过反射或者cglib:来生成bean实例这都
         是耗性能的操作，
       - 其次给对象分配内存也会涉及复杂算法。
  2. 减少jvm垃圾回收由于不会给每个请求都新生成bean实例，所以自然回收的对象少了。
  3. 可以快速获取到bean因为单例的获取bean操作除了第一次生成之外其余的都是从缓存里获取的所以很快。

---

## Bean是线程安全的吗

默认是单例，单例只会实例化一次

若是单例中，声明的类中有共享的成员变量，并且有会存在读写的操作，并发情况下就会出现线程不安全的问题

- 单例Bean的情况
  - 如果在类中声明成员变量，并且有读写操作（有状态），就会线程不安全
  - 但是，只需要把成员变量声明在方法中，那么单例Bean就是安全的

---

## spring如何处理线程并发问题？

1. 设置为多例

2. 将成员变量声明在方法当中

3. 将成员变量放在ThreadLocal中

   ![image-20230228172850344](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302281728420.png)

4. 使用同步锁

---

## 实例化bean 有几种方式

1. 构造器方式（反射）

   <bean class="类路径">

   @Component

   会将class的完整类路径存在BeanDefinition.beanClass,spring在创建bean的时候，会根据benaClass进行反射，通过反射拿到实例

2. 静态工厂方式

3. 实例工厂方式

4. FactoryBean方式

---

## 什么是bean装配（注入）？什么是自动装配（自动注入）？

DI

autowire

---

## 自动装配方式有几种

1.byName

        通过参数名自动装配，Spring容器在配置文件中发现bean的autowire属性被设置成byname之后，容器试图匹配、装配和该bean的属性具有相同名字的bean。

2.byType

        通过参数类型自动装配，Spring容器在配置文件中发现bean的autowire属性被设置成byType之后，容器试图匹配、装配和该bean的属性具有相同类型的bean，当有多个bean符合条件，则抛出错误。

3.constructor

        这个方式类似于byType,但是要提供给构造器参数，当没有确定的带参数的构造器参数类型,将会抛出异常。---已被弃用

4.autodetect 

        首先尝试使用constructor来自动装配，当无法工作，则使用byType方式。

5.no

        默认的方式是不进行自动装配,通过显式设置ref属性来进行装配。

---

## Bean有哪些生命周期回调方法？有哪几种实现方式？

![image-20230228212150495](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282147634.png)

1. 初始化方法

   - @PostConstruct注解

     ```java
     @Component
     @Slf4j
     public class Animal {
     
         @PostConstruct
         public void afterCons(){
             log.info("Animal 后置方法调用");
         }
     }
     ```

   - 实现[InitializingBean](https://so.csdn.net/so/search?q=InitializingBean&spm=1001.2101.3001.7020)接口

     - 通过实现InitializingBean接口的`afterPropertiesSet()`方法

     ```java
     import lombok.extern.slf4j.Slf4j;
     import org.springframework.beans.factory.InitializingBean;
     import org.springframework.stereotype.Component;
     
     @Component
     @Slf4j
     public class Person implements InitializingBean {
     
         public Person(){
             log.info("Person 构造方法调用");
         }
         @Override
         public void afterPropertiesSet() throws Exception {
           log.info("Person 创建完毕，执行回调方法");
         }
     }
     
     ```

   - Xml配置

     - 在Xml文件中配置bean，bean标签中加上init属性，值为对应类的回调方法

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
             
          <bean id ="car" class="com.ti.service.Car" init-method="initMethod"></bean>   
             
     </beans>
     
     ```

     ```java
     @Slf4j
     @Component
     public class Car {
     
         public void initMethod(){
             System.out.println("Car初始化方法调用 ");
         }
     }
     ```

2. 销毁前

   - @PreDestroy 注解

     ```java
     @Component
     public class Instance {
         public Instance() {
             System.out.println("Instance's Constructor..");
         }
      
         @PostConstruct // 使用java注解
         public void PostConstruct(){
             System.out.println("Instance's PostConstruct...");
         }
     }
     ```

   - DisposableBean 接口

     ```java
     public interface DisposableBean {
     	 void destroy() throws Exception;
     }
     ```

   - xml配置

     ```xml
     <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd"
       init-method="init" destroy-method="destroy">
      
     </beans>
     ```

3. Spring允许我们混合使用上述介绍的三种方式来指定对应的回调方法。

   - 当对于同一个bean使用三种方式指定了同一个方法作为初始化后的回调方法或销毁前的回调方法，则对应的回调方法只会被执行一次。
   - 然而，当对于同一个bean使用两种或三种方式指定的回调方法不是同一个方法时，Spring将依次执行使用不同的方式指定的回调方法。
   - 对于初始化后的回调方法而言，具体规则如下：
     - 使用@PostConstruct标注的方法。
     - 实现InitializingBean接口后的回调方法afterPropertiesSet()方法。
     - 通过init-method或default-init-method指定的方法。
   - 对于销毁前的回调方法而言，其规则是一样的：
     - 使用@PreDestroy标注的方法。
     - 实现DisposableBean接口后的回调方法destroy()方法。
     - 通过destroy-method或default-destroy-method指定的方法。

---

## Spring在加载过程中Bean有哪几种形态？

![image-20230228213946277](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282147637.png)

---

## Bena的生命周期

![img](https://img-blog.csdnimg.cn/img_convert/cca6c0e44f26a90ac985b8a6acc6dc12.png)

[bean的生命周期_Been Doing的博客-CSDN博客](https://blog.csdn.net/weixin_45723046/article/details/124546319)

![在这里插入图片描述](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282147319.png)

![在这里插入图片描述](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282148370.png)

1. Bean的生命周期：Bean从创建到销毁的过程

2. 过程分为四大步：

   1. 实例化

      1. 通过反射去推断构造函数进行实例化

   2. 属性赋值

      1. 解析自动装配，DI的体现

   3. 初始化

      1. 调用Aware回调
      2. 调用初始化生命线周期回调（三种）
      3. 如果bean实现了AOP 创建动态代理

      ![image-20230228215212125](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282152173.png)

   4. 销毁

      1. 在spring容器在关闭时进行调用
      2. 调用销毁声明周期回调

---

## 如何解决Bean的循环依赖？

[spring源码bean生命周期篇 五 如何解决循环依赖_爱弹古筝的程序媛的博客-CSDN博客](https://blog.csdn.net/weixin_39024580/article/details/128870837)

[SpringBean循环依赖及解决办法_springbean循环依赖怎么解决_吖土豆的博客-CSDN博客](https://blog.csdn.net/qq_42878176/article/details/127728974)

1. 什么是循环依赖

   ![image-20230228220817996](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282208062.png)

   - 以先创建bean a为例，实例化后填充属性b时，发现bean b还没被创建不在单例池(singletonObjects)中，这时侯会去创建bean b，在实例化对象之后，需要填充属性a，这时候发现spring 的单例池(singletonObjects) 中也没有bean a，又需要创建bean A；
   - 创建bean a 需要创建 bean b ，创建bean a 需要创建 bean b，不管先创建bean a 还是先创建bean b这样造成了死循环

2. 如何解决循环依赖

   - 采用**三级缓存**解决的
   - 三级缓存实际上就是三个map
     - 一级缓存：存储完整的Bean
     - 二级缓存：避免多重循环依赖的情况下重复创建动态代理
     - 三级缓存：
       - 存放函数接口（将Bean的实例和名字传进去，会进行AOP的创建）
       - 不会立即调用（如果在实例化后立即调用的话，所有aop不管bean时候循环依赖都会在实例化后创建proxy）
       - 第二次A对象getBean才会调用三级缓存（创建动态代理），然后放到二级缓存当中（避免重复创建）

   ![在这里插入图片描述](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282216699.png)

   <img src="https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282212430.png" alt="在这里插入图片描述" style="zoom: 150%;" />

![img](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282213753.png)

![image-20230228225401068](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282254184.png)

1. ​	二级缓存能不能解决循环依赖
   1. 如果只是死循环的问题：一级缓存就可以解决，但是无法避免在并发下获取不完整的Bean
   2. 二级缓存也可以解决循环依赖：如果出现出现重复循环依赖，会多次创建AOP的动态代理
      - A同时依赖B、C，B、C也依赖A

---

## 如何避免在并发下获取不完整的Bean

在实例化之后，并且还没有进行属性注入的情况就会获取到不完整的bean

解决：双重检查锁	

- ​	两个同步锁，两次检查一级缓存

---

## Bean的生产顺序是由什么决定的？

- 创建顺序是由BeanDefiniton的注册顺序来决定的，依赖关系也会影响bean的创建顺序（比如说A依赖B）

BeanDefinition的注册顺序是由什么决定的？

- 主要是由注解（配置）的解析顺序来决定的

  ![](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282310140.png)

---

## Spring有哪几种配置方式

1. XML配置文集
2. 基于注解的配置
3. 基于java的配置-javaConfig
   - @Baan
   - @Configuration

----

## JavaConfig是如何替代spring.xml的？

[07、Spring中XML配置和JavaConfig配置比较_javaconfig 和xml_执手天涯@的博客-CSDN博客](https://blog.csdn.net/m0_37911124/article/details/128227974)



----

## @Import有几种用法

[@Import注解的四种使用方式_my_sky_的博客-CSDN博客](https://blog.csdn.net/bluemysky/article/details/128827769)

---

## 如何让自动注入没有找到依赖Bean时不报错？

```xml
@Autowired(require=false)
```

---

## 如何让自动注入找到多个依赖Bean时不报错

- 设置一个bean为主要的bean
  - 类上面加注解@Primary
- 可以设置Bena名称，配合@Qualifier注解对Bena进行指定

---

## @Autowired注入有什么用

自动注入并且不需要getter/setter方法

默认按照类型进行匹配，如果类型匹配到了多个，那就再按照名字进行匹配

---

## @Autowired和@Resource区别

[@Autowired与@Resource区别_@resource和@autowired的区别_骑个小蜗牛的博客-CSDN博客](https://blog.csdn.net/JokerLJG/article/details/123971153)

 ![image-20230228232900721](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282329778.png)

- @Autowired装配流程

  ![请添加图片描述](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282329882.png)

- @Resource装配流程

  ![请添加图片描述](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282329871.png)

---

## @Configuration加与不加的区别是什么？

1. 告诉spring这是一个配置类，相当于spring的xml配置文件
2. 被@Configuration 注解的类，会被cglib代理进行增强
3. @Conﬁguration类允许通过调用同一类中的其他@Bean方法来定义bean之间的依赖关系，保证@Bean的对象作用域受到控制，避免多例

---

## @Bean的方法调用是怎么保证单例的

1. 如果希望@bean的方法返回是对象是单例需要在类上面加上@Configuration,
2. Spring会在invokeBeanFactoryPostProcessor通过内置BeanFactoryPostProcessor中会==**CGLib**==生成代理
3. 当@Bean方法进行互调时，则会通过CGLIB进行增强，通过调用的方法名作为bean的名称去ioc容器中获取，进而保证了@Bean方法的单例

---

## 配置第三方的类成为Bean有几种方式

1. 通过xml的方式配置

   ![](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282337688.png)

2. 通过注解配置：

   记得配置扫描包<component-scan>

   ![img](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282338275.png)

3. javaConfig：@Bean注解

   ![img](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282339651.png)

   

4. @Import  3种方式

   1. 在配置类上配置@Import注解，并定义要创建的bean.class；容器启动后就会自动创建
      - 不可以干预实例化过程
   2. 使用ImportSelector，实现ImportSelector接口
      - 不可以干预实例化过程
   3. 使用 ImportBeanDefinitionRegistrar，实现ImportBeanDefinitionRegistrar接口



---

## 为什么@ComponentScan不设置basePackage也会扫描？

因为Spring在解析@ComponentScan的时候，会去解析basePackage的路径， 如果没有设置则会将你的类所在的包的地址作为扫描包的地址

---

## Spring通知有哪些通知

[Spring AOP的5种通知类型_aop通知类型有几种_杨 戬的博客-CSDN博客](https://blog.csdn.net/weixin_45525272/article/details/125955962)

---

## JDK动态代理和CGLIB动态代理的区别

![image-20230228235302718](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282353856.png)

---

## JavaConfig方式如何启用AOP

![image-20230228235436223](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202302282354266.png)

- ==TODO:==xml中怎么启动AOP?


---

## AOP有几种实现方式

---

## AOP是哪里创建的动态代理

![image-20230301171017937](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011710044.png)

---

## 事务的四大特性

- **原子性:**

  事务中所有操作是不可再分割的原子单位。事务中所有操作要么全部执行成功，要么全部执行失败。一个事务内的操作要么全部成功要么全部失败.

- **一致性:**

   事务执行后，数据库状态与其它业务规则保持一致。其他特性都是为了给一致性服务的. 例如买东西,张三买李四的东西, 买卖前和买卖后张三和李四的所有钱数之和是保持不变的.

- 隔离性:

  事务和事务之间是隔离开的. 一个事务看不到另一个事务正在操作的数据(正在进行中的状态)(两个人在两个房间考试)

- **持久性:**

  一旦事务提交成功，事务中所有的数据操作都必须被持久化到数据库中，即使提交事务后，数据库马上崩溃，在数据库重启时，也必须能保证通过某种机制将数据恢复到提交后的状态。 举例: 一般的数据操作只是在事务中记录需要进行这样的操作, 即使看到了表中的数据发生了改变, 实际上表中的数据也没有发生改变只是在事务中记录需要进行这样的操作, 真正提交了事务才去表中改变表中的数据.

---

## Spring支持的事务管理类型和实现方式

[spring事务实现的几种方式_spring事务实现方式_沉泽·的博客-CSDN博客](https://blog.csdn.net/weixin_41979002/article/details/119103633)

- **编程式事务**：在代码中进行事务控制。优点：精度高。缺点：代码耦合度高
- **声明式事务**：通过@Transactional注解实现事务控制

![image-20230301172323793](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011723879.png)

---

## Spring的事务传播行为

事务的传播行为是指蛋一个事务方法被另一个事务方法调用时，这个事务方法哟应该如何进行？

![image-20230301172815201](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011728285.png)

主要记忆前三种

---

## 说一下spring的事务隔离

==TODO==：需要学习事务隔离

- 脏读
- 不可重复读
- 。。。



---

## Spring事务的实现原理

---

## Spring多线程事务是否能保证事务的一致性

![image-20230301185438481](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011854543.png)

---

## spring源码用到了哪些涉及模式

- ![image-20230301185549683](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011855738.png)

---

## spring如何管理Mybatis的mapper接口的

![image-20230301185943131](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011859194.png)

---

## 如何解决get和post乱码问题

![image-20230301190149057](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011901145.png)

![image-20230301190256044](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011902107.png)

---

## 68、Spring MVC的控制器是不是单例模式,如果是,有什么问题,怎么解决？

控制器实际上也是一个bean，bean默认就是单例，单例中有状态的对象就可能会有线程安全问题

---

## spring mvc工作流程？/DispatcherServlet的工作流程

![image-20230301191257882](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011912012.png)

---

## SpringMvc中处理AJAX或者JSON数据的原理

![image-20230301191706605](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011917728.png)

---

## Spring和SpringMVC为什么需要父子容器？

---

## 时候可以把SpringMVC所有Bean都交给Spring容器来管理

不行

---

## SpringMVC的拦截器和过滤器有什么区别？执行顺序是什么？

- 拦截器不依赖与servlet容器，过滤器依赖与servlet容器。
- 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
- 拦截器可以访问action.上下文、值栈里的对象，而过滤器不能访问。

---

## 谈谈SpringBoot的理解，它有哪些特点？

![image-20230301192848131](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303011928205.png)

---

## Spring和SpringBoot的关系和区别

![image-20230301201534286](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20230301201534286.png)

---

## SpringBoot的核心注解

![image-20230301201710920](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303012017984.png)

![image-20230301201736792](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303012017841.png)

---

## ==SpringBoot自动装配原理==

![image-20230301201824512](https://raw.githubusercontent.com/macwbh9527/Note/main/image/202303012018566.png)

---

## SpringBoot的启动原理

---

## SpringBoot内置Tomcat启动原理

----

## springBoot外部Tomcat启动原理

---

## 读取配置文件原理和加载顺序优先级

---

## 微服务架构的优缺点

---

## 微服务怎么拆

---

## 常用微服务组件及作用

---

## 介绍注册中心的核心功能

---

## 谈谈配置中心

---

## 说说服务网关可以做什么

---

## 服务雪崩？服务限流

---

## 熔断？降级？

---

## 说说seata的实现原理

---

## 微服务架构排错

---

## Ribbon有哪些负载均衡策略

---

