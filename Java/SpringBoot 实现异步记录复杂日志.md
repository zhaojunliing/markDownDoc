# SpringBoot 实现异步记录复杂日志

## 1、背景

最近接手一个任务，需要给当前项目加一个较为复杂的日志。有多复杂呢? 要有日志类型、不同日志类型要有不同的操作和备注等。作为小白的我最开始的做法是在业务层写代码记录日志，好处就是方便，坏处就是这种做法直接侵袭Service层，Service层无法做到职责单一了。

经导师点拨，改用自定义注解+AOP切面异步日志

## 2、技术方案-自定义注解

注解（Annotation），也叫元数据。

### 2.1 注解介绍

注解其实就是代码里的特殊标记,这些标记可以在编译、类加载、运行时被读取，并执行相应的处理。通过使用注解，程序员可以在不改变原有逻辑的情况下，在源文件中嵌入一些补充信息。

### 2.2 元注解

元注解用于修饰其他注解的注解，在JDK5.0中提供了四种元注解：`Retention、Target、Documented、Inherited`

##### （1） Retention

> 用于修饰注解，用于指定修饰的哪个注解的声明周期。@Rentention包含一个RetentionPolicy枚举类型的成员变量，使用@Rentention时必须为该value成员变量指定值

**RetentionPolicy.SOURCE**：在源文件中有效（即源文件保留），编译器直接丢弃这种策略的注释，在.class文件中不会保留注解信息

**RetentionPolicy.CLASS**：在class文件中有效（即class保留），保留在.class文件中，但是当运行java程序时，他就不会继续加载了，不会保留在内存中，JVM不会保留注解。如果注解没有加Retention元注解，那么相当于默认的注解就是这种状态。

**RetentionPolicy.RUNTIME**：在运行有效（即运行时保留），当运行Java程序时，JVM会保留注释，加载在内存中，那么程序可以通过反射获取该注释。

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
 
    RetentionPolicy value();
}
 
public enum RetentionPolicy {
 
    SOURCE,
 
    CLASS,
 
    RUNTIME
}
```

##### （2）Target

用于修饰注解的注解，定义当前注解能够修饰程序中的哪些元素（类、属性、方法，构造器等等）

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
 
    ElementType[] value();
}
```

##### （3）Documented

用于指定被该元注解修饰的注解类将被javadoc工具提取成文档。默认情况下，javadoc是不包括注解的，但是加上这个注解生成的文档中就会带着注解了

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Documented {
}
```

##### （4）Inherited

被它修饰的Annotation将具有继承性。如果某个类使用了被`@Inherited`修饰的Annotation，则其子类将自动具有该注解。

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Inherited {
}
```

注解的基础知识基本介绍完毕，我们开始写起来吧！！！

### 2.3 实现自定义注解

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
public @interface Log {
    // 日志类型
    LogType logType() ;
    // 操作类型
    OperateType operate();
    // 备注
    String note() default "";
}
```

## 3、技术方案-AOP切面

AOP切面编程一般可以帮助我们在不修改现有代码的情况下，对程序的功能进行拓展, 往往用于实现 日志处理，权限控制，性能检测，事务控制等。AOP实现的原理就是动态代理。在有接口的情况下，使用JDK动态代理，在没有接口的情况下使用cglib动态代理。

### 3.1 AOP术语解析

- **Joint point**：类里面那些可以被增强的方法，这些方法被称之为连接点
- **PointCut**：实际被增强的方法，这些方法被称之为连接点
- **Advice**：实际增加的逻辑部分称为通知
- **Target**：被增强功能的对象（被代理的对象）
- **Aspect**：Aspect 声明类似与Java中的类声明，在Aspect中会包含着一些PointCut以及相应的Advice.
- **Weaving**：创建代理对象并实现功能增强的声明并运行过程将Aspect和其他对象连接起来，并创建`Adviced object`的过程

### 3.2 切入点表达式

切入点表达式：通过一个表达式来确定AOP要增强的是哪个或者哪些方法.

### 3.2 ADVICE通知类型

- **前置通知**：@Before 执行前置通知，并通过JointPoint获取方法中的参数

```java
@Aspect
@Component
public class DaoAspect {
 
 /*
 前置通知：在执行addUser方法之前，执行前置通知，并通过JointPoint获取addUser()方法中的参数
 */
    @Before("execution(* com.xzit.dao.Impl.UserDaoImpl.addUser(..))")
    public void methodBefore(JoinPoint joinPoint){
        System.out.println("methodBefore invoked ... ...");
        Object[] args = joinPoint.getArgs();
        System.out.println(Arrays.toString(args));
 
    }
}
```

- **后置通知**：`@After` 切点方法是否出现异常，后置通知都会执行
- **返回通知**：`@AfterReturning` 切点出现异常，返回通知不会执行
- **异常通知**：`@AfterThrowing` 切点方法出现异常就执行，不出现异常就不执行
- **环绕通知**：`@Around` 在切点方法之前和之后执行。是`@Before`和`@AfterReturing` 相结合

### 3.3 技术实现

根据任务背景，我选择了返回通知`@AfterReturning`。使用返回通知一定要注意的是：由于我需要用到返回值，所以会用`@AfterReturning`注解中的returning属性来获取方法的返回值

`returning`指定的**名称必须和切面方法参数中的名称相同**。例如在下面代码中指定的"cId"都是相同的

```java
@AfterReturning(pointcut = "@annotation(com.xxx.xxx.xxx.Log)",
        returning = "cId")
public void handleRdLogs(JoinPoint joinPoint, int cId) 
```

**切面方法参数中的参数类型必须和方法返回类型一致**

```java
@AfterReturning(pointcut = "@annotation(com.xxx.xxx.xxx.Log)",
        returning = "cId")
public void handleRdLogs(JoinPoint joinPoint, int cId) {
    // 获取方法签名
    MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
    // 获取@Log注解内容
    if (!methodSignature.getMethod().isAnnotationPresent(Log.class)) {
        log.error("获取注解@Log失败");
        throw new Exception("获取注解@Log失败");
    }
    Log log = methodSignature.getMethod().getAnnotation(Log.class);
    // 输出日志的备注
    System.out.println(log.note())
}
```

### 3.4 相关操作

（1） 获取方法签名

```java
MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();`
```

（2）根据方法签名获取自定义注解

```java
Log log = methodSignature.getMethod().getAnnotation(Log.class);
```

（3）根据自定义注解获取，注解内部的参数

```java
System.out.println(log.note())
```

## 4、高级操作

场景：不仅需要获取返回值，还得知道方法参数的值,而且方法参数的值不能作为返回值，这个该怎么办呢？

> 秀操作开始：

第一步： 在注解上写 "#" + "方法参数的名"

```java
@Log(note = "#note")
public int rdAuditReturn(String note) {
     System.out.println(note)
     xxxx.....
     xxxx.....
     业务代码.....
     xxxx.....
     xxxx....
    return cId;
}
```

第二步： 实现切面，只要调用这个方法，就可以得到note的值了

```java
private final ExpressionParser parser = new SpelExpressionParser();
private final EvaluationContext evaluationContext = new StandardEvaluationContext();
 
 
private void getNote(JoinPoint joinPoint, StringBuilder noteBuilder, String note) throws NoSuchMethodException {
    if (!StringUtils.isBlank(note)) {
        Class<?> targetCls = joinPoint.getTarget().getClass();
        MethodSignature ms = (MethodSignature) joinPoint.getSignature();
        Method targetMethod = targetCls.getDeclaredMethod(ms.getName(), ms.getParameterTypes());
        ParameterNameDiscoverer pnd = new DefaultParameterNameDiscoverer();
        String[] parameterNames = pnd.getParameterNames(targetMethod);
        Object[] args = joinPoint.getArgs();
        for (int i = 0; i < args.length; ++i) {
            int index = i;
            Optional.ofNullable(args[i]).ifPresent(param -> {
                String paramName = parameterNames[index];
                this.evaluationContext.setVariable(paramName, param);
            });
        }
        Optional.ofNullable(this.parser.parseExpression(note).getValue(this.evaluationContext)).ifPresent(k ->
                noteBuilder.append((String) k)
        );
    }
}
```