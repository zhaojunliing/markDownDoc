# @Retryable注解，优雅实现重处理功能

Memory小峰 [Java励志架构师](javascript:void(0);) *2022-04-22 17:00*

文章来源：http://a.nxw.so/1j1TQn



**目录**

- 前言
- @Retryable 是什么？
- 使用步骤
- 总结

**前言**

在实际工作中，重处理是一个非常常见的场景，比如:

- 发送消息失败。
- 调用远程服务失败。
- 争抢锁失败。



这些错误可能是因为网络波动造成的，等待过后重处理就能成功。通常来说，会用 try/catch，while 循环之类的语法来进行重处理，但是这样的做法缺乏统一性，并且不是很方便，要多写很多代码。

然而 spring-retry 却可以通过注解，在不入侵原有业务逻辑代码的方式下，优雅的实现重处理功能。

**@Retryable 是什么？**

Spring 系列的 spring-retry 是另一个实用程序模块，可以帮助我们以标准方式处理任何特定操作的重试。在 spring-retry 中，所有配置都是基于简单注释的。

**使用步骤**

##### ①POM 依赖：

```
 <dependency>
  <groupId>org.springframework.retry</groupId>
  <artifactId>spring-retry</artifactId>
 </dependency>
```

##### ②启用 @Retryable：

```
@EnableRetry
@SpringBootApplication
public class HelloApplication {

    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class, args);
    }

}
```

##### ③在方法上添加 @Retryable：

```
import com.mail.elegant.service.TestRetryService;
import org.springframework.retry.annotation.Backoff;
import org.springframework.retry.annotation.Retryable;
import org.springframework.stereotype.Service;
import java.time.LocalTime;

@Service
public class TestRetryServiceImpl implements TestRetryService {

    @Override
    @Retryable(value = Exception.class,maxAttempts = 3,backoff = @Backoff(delay = 2000,multiplier = 1.5))
    public int test(int code) throws Exception{
        System.out.println("test被调用,时间："+LocalTime.now());
          if (code==0){
              throw new Exception("情况不对头！");
          }
        System.out.println("test被调用,情况对头了！");

        return 200;
    }
}
```

来简单解释一下注解中几个参数的含义：

- **value：**抛出指定异常才会重试
- **include：**和 value 一样，默认为空，当 exclude 也为空时，默认所有异常
- **exclude：**指定不处理的异常
- **maxAttempts：**最大重试次数，默认 3 次
- **backoff：**重试等待策略，默认使用 @Backoff，@Backoff 的 value 默认为 1000L，我们设置为 2000L；multiplier（指定延迟倍数）默认为 0，表示固定暂停 1 秒后进行重试，如果把 multiplier 设置为 1.5，则第一次重试为 2 秒，第二次为 3 秒，第三次为 4.5 秒。



当重试耗尽时还是失败，会出现什么情况呢？当重试耗尽时，RetryOperations 可以将控制传递给另一个回调，即 RecoveryCallback。

Spring-Retry 还提供了 @Recover 注解，用于 @Retryable 重试失败后处理方法。如果不需要回调方法，可以直接不写回调方法，那么实现的效果是，重试次数完了后，如果还是没成功没符合业务判断，就抛出异常。

##### ④@Recover：

```
@Recover
public int recover(Exception e, int code){
   System.out.println("回调方法执行！！！！");
   //记日志到数据库 或者调用其余的方法
    return 400;
}
```

可以看到传参里面写的是 Exception e，这个是作为回调的接头暗号（重试次数用完了，还是失败，我们抛出这个 Exception e 通知触发这个回调方法）。

对于 @Recover 注解的方法，需要特别注意的是：

- 方法的返回值必须与 @Retryable 方法一致
- 方法的第一个参数，必须是 Throwable 类型的，建议是与 @Retryable 配置的异常一致，其他的参数，需要哪个参数，写进去就可以了（@Recover 方法中有的）
- 该回调方法与重试方法写在同一个实现类里面

##### ⑤注意事项：

- 由于是基于 AOP 实现，所以不支持类里自调用方法
- 如果重试失败需要给 @Recover 注解的方法做后续处理，那这个重试的方法不能有返回值，只能是 void
- 方法内不能使用 try catch，只能往外抛异常
- @Recover 注解来开启重试失败后调用的方法(注意,需跟重处理方法在同一个类中)，此注解注释的方法参数一定要是 @Retryable 抛出的异常，否则无法识别，可以在该方法中进行日志处理。

**总结**

本篇主要简单介绍了 SpringBoot 中的 Retryable 的使用，主要的适用场景和注意事项，当需要重试的时候还是很有用的。