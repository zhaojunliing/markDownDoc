# Spring @Async的异常处理

通常，如果我们要在程序中做一个耗时的操作（例如调用其他外部模块），一般会通过异步的方式执行。

有这2种方法：

自行生成线程池ThreadPoolExecutor，提交任务执行
更方便地，使用Spring @Async注解，修饰在需要异步执行的方法上
对于第一种方法的异常处理，楼主已经在“Java子线程中的异常处理（通用）”这篇文章中介绍了，也就是提交任务后获取到Future对象，通过future.get()获取返回值的时候能够捕获到ExcecutionException。

对于Spring @Async注解的方法，如何进行异常处理呢？

方法一：配置AsyncUncaughtExceptionHandler（对于无返回值的方法）
通过AsyncConfigurer自定义线程池，以及异常处理。

```java
@Configuration
@EnableAsync
public class SpringAsyncConfiguration implements AsyncConfigurer {
 private static final Logger logger = LoggerFactory.getLogger(getClass());

 @Bean
 @Override
 public Executor getAsyncExecutor() {
     ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
     executor.setCorePoolSize(8);
      executor.setMaxPoolSize(16);
      executor.setQueueCapacity(64);
      executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
      executor.setThreadNamePrefix("SpringAsyncThread-");

      return executor;
  }

  @Override
  public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
      return new SpringAsyncExceptionHandler();
  }

  class SpringAsyncExceptionHandler implements AsyncUncaughtExceptionHandler {
      @Override
      public void handleUncaughtException(Throwable throwable, Method method, Object... obj) {
          logger.error("Exception occurs in async method", throwable.getMessage());
      }
  }
}
```


方法二：通过AsyncResult捕获异常（对于有返回值的方法）
如果异步方法有返回值，那就应当返回AsyncResult类的对象，以便在调用处捕获异常。

因为AsyncResult是Future接口的子类，所以也可以通过future.get()获取返回值的时候捕获ExcecutionException。

异步方法：
```java
@Service
public class AsyncService {

    @Async
    public AsyncResult<String> asyncMethodWithResult() {
        // do something（可能发生异常）
     
        return new AsyncResult("hello");
    }

}
```
调用处捕获异常：
```java
public class Test {

    private Logger logger = LoggerFactory.getLogger(getClass());

    @Autowired
    AsyncService asyncService;

    public void test() {
        try {
             Future future = asyncService.asyncMethodWithResult();
             future.get();
         } catch (ExecutionException e) {
             logger.error("exception occurs", e);
         } catch (InterruptedException e) {
             logger.error("exception occurs", e);
         }
    }
}
```

@Async调用中的事务处理机制

在@Async标注的方法，同时也适用了@Transactional进行了标注；在其调用数据库操作之时，将无法产生事务管理的控制，原因就在于其是基于异步处理的操作。
那该如何给这些操作添加事务管理呢？可以将需要事务管理操作的方法放置到异步方法内部，在内部被调用的方法上添加@Transactional.
    
例如：方法A，使用了@Async/@Transactional来标注，但是无法产生事务控制的目的。
    方法B，使用了@Async来标注，B中调用了C、D，C/D分别使用@Transactional做了标注，则可实现事务控制的目的。