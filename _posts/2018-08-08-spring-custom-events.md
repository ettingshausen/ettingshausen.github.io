---
layout: cayman
title:  "Spring 自定义事件实践"
date:   2018-08-08 22:15 +0800
categories: Spring
published: true
author: ettingshausen
---   
![](https://wx2.sinaimg.cn/large/685ea4faly1fu2rzd650rj20dw0a0gm3.jpg)  
*Event Driven Programming*  
{:.image-caption}  

事件驱动编程( Event-driven Programming )， 在图形化界面 (GUI) 编程中广泛应用。如浏览器、客户端程序(winform) 移动应用 (Android)程序的开发中都会大量的使用到事件。事件驱动编程模型有诸多优点，首先事件的发布者与订阅者无直接关联，低耦合。其次，事件支持异步任务，由事件触发的逻辑可以并行进行，性能高。  Spring 事件体系是观察者模式的典型应用，这个功能是Spring众多功能中经常被忽略的功能之一。业务实现的过程，可以借鉴图形化界面编程中的事件，简化逻辑，优化代码。

# 实现要领
---  
实现Spring 自定义事件，需要遵循以下几个要领：
+ 事件必须继承 `ApplicationEvent`
+ 事件发布者需注入并使用 `ApplicationEventPublisher` 发布事件
+ 监听器需实现 `ApplicationListener` 接口  

`ApplicationContext` 已经实现了 `ApplicationEventPublisher` 接口，也可以直接使用 `ApplicationContext` 发布事件。对于Spring 版本高于4.2的，也可以使用 `@EventListener` 注解来实现事件监听。

# 事务与异步事件 
---
默认Spring 创建的事件是同步的。这样做的好处是，订阅了事件的监听器(Listener) 可以参与到发布者的事务当中。如果发布者启用了事务，监听器中任何一个异常，都会触发发布者事务回滚。如果追求性能，事件的发布者和订阅者无事务控制的要求，则可以启用异步事件。  具体操作为先开启Spring异步支持，然后再监听器方法上加上 `@Async` 注解。  

对于Spring Boot 项目开启异步支持，可以在启动类上直接加 `@EnableAsync` 注解，开启异步支持。
```java
@SpringBootApplication 
@EnableAsync
public class CareApplication {
	public static void main(String[] args)  {
	    SpringApplication.run(CareApplication.class, args);
    }
}
```  
对于传统的Spring项目，需要在Spring 配置中配置 `applicationEventMulticaster`
```xml
<task:executor id="asyncExecutor" pool-size="5"  />
<bean id='applicationEventMulticaster' 
    class='org.springframework.context.event.SimpleApplicationEventMulticaster'>
    <property name='taskExecutor' ref='asyncExecutor' />
</bean>
```
监听方法上添加 `@EventListener` 注解：
```java
@Component
public class EventListener {
	private static final Logger log = LoggerFactory.getLogger(EventListener.class);
	@EventListener
	@Async
	public void handler(CustomEvent event){
		try {
			Thread.sleep(2000); 
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```
# 实践
---
以HIS系统出院场景为例，患者在出院动作完成之后，系统需要完成许多的操作。比如，停止医嘱，生成费用清单，生成领药单，结算费用等等。如果采用常规的编码方法，那不可避免要在出院的方法中去调用各种方法，或者逻辑控制。如果后续，医院提出在出院的时候在加一个操作，就得再向这个方法中添加代码。久而久之，这个方法就会越来越臃肿。如果各地不同的医院在出院时的操作不同，控制起来也极不容易。  

现在通过自定义事件来实现这个功能，先定义一个出院的事件：
```java
public class DischargeEvent extends ApplicationEvent {

    public DischargeEvent(Object source) {
        super(source);
    }
}
```
创建监听器：
```java
@Component
public class DischargeEventListener implements ApplicationListener<DischargeEvent> {

    @Override
    public void onApplicationEvent(DischargeEvent event) {

        // 停止医嘱
        stopPrescription();
        // 其他操作
    }
}
```

发布事件：
```java
@Service
public class DischargeServiceImpl implements DischargeService {
    @Autowired
    private ApplicationEventPublisher applicationEventPublisher;

    @override
    @Transactional(rollbackFor = Exception.class)
    public void discharge(Patient patient) {

        // 出院检查等操作
        applicationEventPublisher.publishEvent(new DischargeEvent(patient));
    }
}
``` 

默认情况下，创建的自定义事件时同步执行的，当订阅监听器中有一个发生异常，都会触发异常回滚。在这个例子中，如果停止医嘱的方法抛出了异常，那么 `discharge` 这个方法上的事务就会回滚。  
如果后续医院提出个新需求，在患者出院时，通知收费处。要求，保证出院流程，即便时通知失败也能完成出院手续。这种情况，可以新增加一个监听器，实现通知的逻辑，而不影响原来的业务逻辑，原来的代码也不需改动，只需要新增加一个类就能实现。
```java
@Component
public class DischargeEventNotifyListener implements ApplicationListener<DischargeEvent> {

    @Override
    @Async
    public void onApplicationEvent(DischargeEvent event) {

        // 通知收费处
        notify();
    }
}
```

# 优点
---
从业务逻辑来看，通过事件编程的方式，更容易拆分业务模块，能使单个模块逻辑简单，整体逻辑清晰。易于维护，对于新人非常友好。实际项目，大多数的业务非常繁琐，导致新入职的员工刚开始，不熟悉整体业务，无从下手。拆分精细之后，维护的人员只需要掌握小模块的业务逻辑，而无需了解完整的业务，即便是新人，也能容易上手。  

从代码管理上来看，事件的发布者和监听者并无直接联系，两部分的代码没有耦合，无需关注对方的逻辑，符合单一原则 (SRP：Single responsibility principle)。  通过注解灵活的控制监听器是否需要同步执行，同步和异步的监听器可以混合使用，减少自定义线程的代码量。

对于测试来讲，多个监听者与发布者之间并无直接关联，新增的监听方法，并不需要改动原有代码，所以只需要对新增加的代码进行测试即可。

# 参考资料
--- 
1. [Spring Events](https://www.baeldung.com/spring-events)
1. [使用事件驱动模型实现高效稳定的网络服务器程序](https://www.ibm.com/developerworks/cn/linux/l-cn-edntwk/index.html)
1. [微服务：从设计到部署](http://oopsguy.com/books/microservices/5-event-driven-data-management-for-microservices.html#microservices-and-the-problem-of-distributed-data-management) 
1. [Spring的事件与异步事件](https://www.limisky.com/125.html)
1. [程序设计思想与方法](https://wizardforcel.gitbooks.io/sjtu-cs902-courseware/content/165.html)
1. [Asynchronous and Transactional Event Listeners in Spring](https://www.javacodegeeks.com/2017/10/asynchrouns-transactional-event-listeners-spring.html)