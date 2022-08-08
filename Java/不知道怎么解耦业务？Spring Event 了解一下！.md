# 不知道怎么解耦业务？Spring Event 了解一下！

- **写在最前**

- **Spring Event 同步使用**

- - **1.自定义事件**
  - **2.定义监听器**
  - **3.定义发布者**
  - **4.单测执行**

- **Spring Event 异步使用**

- - **1.自定义事件**
  - **2.定义监听器**
  - **3.定义发布者**
  - **4.单测执行（同步）**
  - **5.开启异步**
  - **6.单测执行（异步）**

------

## **写在最前**

实际业务开发过程中，业务逻辑可能非常复杂，核心业务 + N个子业务。如果都放到一块儿去做，代码可能会很长，耦合度不断攀升，维护起来也麻烦，甚至头疼。还有一些业务场景不需要在一次请求中同步完成，比如邮件发送、短信发送等。

> MQ 确实可以解决这个问题，但 MQ 重啊，非必要不提升架构复杂度。针对这些问题，我们了解一下 Spring Event。

## **Spring Event 同步使用**

Spring Event（`Application Event`）其实就是一个观察者设计模式，一个 Bean 处理完成任务后希望通知其它 Bean 或者说一个 Bean 想观察监听另一个Bean 的行为。

Spring Event 用来解耦业务真的贼好用！

> Demo 地址：https://gitee.com/csps/mingyue-springboot-learning

### **1.自定义事件**

定义事件，继承 `ApplicationEvent` 的类成为一个事件类

```
import lombok.Data;
import lombok.ToString;
import org.springframework.context.ApplicationEvent;

/**
 * @author Strive
 * @date 2022/4/22 18:00
 * @description
 */
@Data
@ToString
public class OrderProductEvent extends ApplicationEvent {

  /** 该类型事件携带的信息 */
  private String orderId;

  public OrderProductEvent(Object source, String orderId) {
    super(source);
    this.orderId = orderId;
  }
}
```

### **2.定义监听器**

监听并处理事件，实现 `ApplicationListener` 接口或者使用 `@EventListener` 注解

```
import com.csp.mingyue.event.events.OrderProductEvent;
import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

/**
 * 实现 ApplicationListener 接口，并指定监听的事件类型
 *
 * @author Strive
 * @date 2022/4/24 09:09
 * @description
 */
@Slf4j
@Component
public class OrderProductListener implements ApplicationListener<OrderProductEvent> {

  /** 使用 onApplicationEvent 方法对消息进行接收处理 */
  @SneakyThrows
  @Override
  public void onApplicationEvent(OrderProductEvent event) {
    String orderId = event.getOrderId();
    long start = System.currentTimeMillis();
    Thread.sleep(2000);
    long end = System.currentTimeMillis();
    log.info("{}：校验订单商品价格耗时：({})毫秒", orderId, (end - start));
  }
}
```

### **3.定义发布者**

发布事件，通过 `ApplicationEventPublisher` 发布事件

```
import com.csp.mingyue.event.events.OrderProductEvent;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Service;

/**
 * @author Strive
 * @date 2022/4/24 09:25
 * @description
 */
@Slf4j
@Service
@RequiredArgsConstructor
public class OrderService {

  /** 注入ApplicationContext用来发布事件 */
  private final ApplicationContext applicationContext;

  /**
   * 下单
   *
   * @param orderId 订单ID
   */
  public String buyOrder(String orderId) {
    long start = System.currentTimeMillis();
    // 1.查询订单详情

    // 2.检验订单价格 （同步处理）
    applicationContext.publishEvent(new OrderProductEvent(this, orderId));

    // 3.短信通知（异步处理）

    long end = System.currentTimeMillis();
    log.info("任务全部完成，总耗时：({})毫秒", end - start);
    return "购买成功";
  }
}
```

### **4.单测执行**

```
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

/**
 * @author Strive
 * @date 2022/4/24 09:28
 * @description
 */
@SpringBootTest
public class OrderServiceTest {
  @Autowired private OrderService orderService;

  @Test
  public void buyOrderTest() {
    orderService.buyOrder("732171109");
  }
}
```

执行结果如下：

```
2022-04-24 10:13:17.535  INFO 44272 --- [           main] c.c.m.e.listener.OrderProductListener    : 732171109：校验订单商品价格耗时：(2008)毫秒
2022-04-24 10:13:17.536  INFO 44272 --- [           main] c.c.mingyue.event.service.OrderService   : 任务全部完成，总耗时：(2009)毫秒
```

## **Spring Event 异步使用**

有些业务场景不需要在一次请求中同步完成，比如邮件发送、短信发送等。

### **1.自定义事件**

```
import lombok.AllArgsConstructor;
import lombok.Data;

/**
 * @author Strive
 * @date 2022/4/24 10:18
 * @description
 */
@Data
@AllArgsConstructor
public class MsgEvent {

  /** 该类型事件携带的信息 */
  public String orderId;
}
```

### **2.定义监听器**

推荐使用 `@EventListener` 注解

```
import com.csp.mingyue.event.events.MsgEvent;
import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

/**
 * @author Strive
 * @date 2022/4/24 10:20
 * @description
 */
@Slf4j
@Component
public class MsgListener {

  @SneakyThrows
  @EventListener(MsgEvent.class)
  public void sendMsg(MsgEvent event) {
    String orderId = event.getOrderId();
    long start = System.currentTimeMillis();
    log.info("开发发送短信");
    log.info("开发发送邮件");
    Thread.sleep(4000);
    long end = System.currentTimeMillis();
    log.info("{}：发送短信、邮件耗时：({})毫秒", orderId, (end - start));
  }
}
```

### **3.定义发布者**

```
/**
  * 下单
  *
  * @param orderId 订单ID
  */
public String buyOrder(String orderId) {
    long start = System.currentTimeMillis();
    // 1.查询订单详情

    // 2.检验订单价格 （同步处理）
    applicationContext.publishEvent(new OrderProductEvent(this, orderId));

    // 3.短信通知（异步处理）
    applicationContext.publishEvent(new MsgEvent(orderId));

    long end = System.currentTimeMillis();
    log.info("任务全部完成，总耗时：({})毫秒", end - start);
    return "购买成功";
}
```

### **4.单测执行（同步）**

```
@Test
public void buyOrderTest() {
    orderService.buyOrder("732171109");
}
```

执行结果如下：

```
2022-04-24 10:24:13.905  INFO 54848 --- [           main] c.c.m.e.listener.OrderProductListener    : 732171109：校验订单商品价格耗时：(2004)毫秒
2022-04-24 10:24:13.906  INFO 54848 --- [           main] c.c.mingyue.event.listener.MsgListener   : 开发发送短信
2022-04-24 10:24:13.907  INFO 54848 --- [           main] c.c.mingyue.event.listener.MsgListener   : 开发发送邮件
2022-04-24 10:24:17.908  INFO 54848 --- [           main] c.c.mingyue.event.listener.MsgListener   : 732171109：发送短信、邮件耗时：(4002)毫秒
2022-04-24 10:24:17.908  INFO 54848 --- [           main] c.c.mingyue.event.service.OrderService   : 任务全部完成，总耗时：(6008)毫秒
```

### **5.开启异步**

启动类增加 `@EnableAsync` 注解

```
@EnableAsync
@SpringBootApplication
public class MingYueSpringbootEventApplication {

  public static void main(String[] args) {
    SpringApplication.run(MingYueSpringbootEventApplication.class, args);
  }
}
```

`Listener` 类需要开启异步的方法增加 `@Async` 注解

```
@Async
@SneakyThrows
@EventListener(MsgEvent.class)
public void sendMsg(MsgEvent event) {
    String orderId = event.getOrderId();
    long start = System.currentTimeMillis();
    log.info("开发发送短信");
    log.info("开发发送邮件");
    Thread.sleep(4000);
    long end = System.currentTimeMillis();
    log.info("{}：发送短信、邮件耗时：({})毫秒", orderId, (end - start));
}
```

### **6.单测执行（异步）**

发送短信的线程显示 `task-1`，主线程结束后（总耗时：(2017)毫秒）控制台停止打印了

```
2022-04-24 10:30:59.002  INFO 59448 --- [           main] c.c.m.e.listener.OrderProductListener    : 732171109：校验订单商品价格耗时：(2009)毫秒
2022-04-24 10:30:59.009  INFO 59448 --- [           main] c.c.mingyue.event.service.OrderService   : 任务全部完成，总耗时：(2017)毫秒
2022-04-24 10:30:59.028  INFO 59448 --- [         task-1] c.c.mingyue.event.listener.MsgListener   : 开发发送短信
2022-04-24 10:30:59.028  INFO 59448 --- [         task-1] c.c.mingyue.event.listener.MsgListener   : 开发发送邮件

```