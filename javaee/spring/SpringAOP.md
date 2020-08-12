# Spring AOP 

https://www.cnblogs.com/joy99/p/10941543.html

超级详细版：http://shouce.jb51.net/spring/aop.html

---

## AOP 原理

### 面向切面

( Aspect Orient Programming ) 面向切面编程，是面向对象编程(OOP) 的一种补充。

在 Java 程序自上而下处理主业务时，也会经常处理一些和主业务逻辑无关的问题（比如在接收用户访问请求时，计算程序响应该请求的运行时间）。这些代码如果和主逻辑代码混淆，会导致后期难以维护。

AOP 就是将这些横切性问题和主逻辑解耦。保证开发者不修改主逻辑代码的前提下，能为系统中的业务组件添加删除、或复用某种功能。

### 代理模式

AOP 的本质是修改业务组件实际执行方法的源代码。即代理类 A 封装了目标类 B ，外部调用 B 的目标方法时会被代理类 A 拦截，代理类 A 一方面执行切面逻辑，一方面把调用转发给目标类 B ，执行目标方法。

该过程是代理模式的实现，代理方式有以下两种：

- **静态 AOP** ：在编译阶段对程序源代码进行修改，生成静态的 AOP 代理类（字节码文件已被修改）。性能更好。
  
- **动态 AOP** ：在运行阶段动态生成代理对象。灵活性更好。

#### 动态代理

Spring 中的 AOP 是通过动态代理实现的，有以下两种方式：

- **JDK 动态代理**

利用反射机制生成一个实现代理接口的类，在调用具体方法前调用 InvokeHandler 来处理。

JDK 代理只能对实现接口的类生成代理。代理生成的是一个接口对象，因此代理类必须实现了接口，否则会抛出异常。
   
- **CGlib 动态代理**

直接操作字节码对代理对象类的字节码文件加载并修改，生成子类来处理。

CGlib 代理针对类实现代理，对指定的类生成一个子类并覆盖其中的方法，因此不能代理 final 类。

---

## AOP 注解详解

### 配置

对负责扫描组件的配置文件类(@Configuration) 添加 `@EnableAspectJAutoProxy` 注解，启用 AOP 功能。

**默认通过 JDK 动态代理方式进行织入。**但必须代理一个实现接口的类，否则会抛出异常。

注解改为 `@EnableAspectJAutoProxy(proxyTargetClass = true)`：

通过 cglib 的动态代理方式进行织入。但如果拓展类的方法被 final 修饰，则织入无效。

```java
@Configuration
@ComponentScan(basePackageClasses = {com.company.project.service.Meal.class})
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class AppConfig {
}
```

### 切面

对组件类(@component) 添加 `@Aspect` 注解，表示该类为切面类。

#### 增强类型

**前置通知**

切面方法注解 `@Before` 表示目标方法调用前，执行该切面方法。

```java
@Before("execution(* com.company.project.service.Meal.eat(..))")
public void cook() {
    System.out.println("cook");
}
```

**后置通知**

- 切面方法注解 `@After` 表示目标方法返回或抛出异常后，执行该切面方法。
- 切面方法注解 `@AfterReturning` 只在目标方法返回后，执行该切面方法。
- 切面方法注解 `@AfterThrowing` 只在目标方法抛出异常后，执行该切面方法。

```java
@AfterReturning("execution(* com.company.project.service.Meal.eat(..))")
public void clean() {
    System.out.println("clean");
}
```

**环绕通知**

切面方法注解 `@Around` 表示切面方法执行过程中，执行目标方法。

传入参数为 ProceedingJoinPoint 类对象，表示目标方法。在切面方法中调用其 proceed 方法来执行。

```java
@Around("execution(* com.company.project.service.Meal.eat(..))")
public void party(ProceedingJoinPoint pj) {
    try {
        System.out.println("cook");
        pj.proceed();
        System.out.println("clean");
    } catch (Throwable throwable) {
        throwable.printStackTrace();
    }
}
```

#### 切点声明

在切面方法中需要声明切面方法要切入的目标方法，execution 指示器是我们定义切点时最主要使用的指示器。

格式为： `execution(返回数据类型 路径.类.方法(传入参数类型))`

参数 | 功能
- | -
`execution(* com.company.project.service.Meal.eat(..))` | 执行 Meal 类的 eat 方法时切入
`execution(* com.company.project.service.Meal.eat(int,String))`	| 执行 Meal 类的 eat(int,String) 方法时切入
`execution(* com.company.project.service.Meal.*(..))`	| 执行 Meal 类的所有方法时切入
`execution(* com.company.project.service.*.*(..))` | 执行 service 包内的任意方法时切入（不包含子包）
`execution(* com.company.project.service..*.*(..))` | 执行 service 包内的任意方法时切入（包含子包）
`execution(public * *(..))`	| 执行所有目标类的所有 public 方法时切入
`execution(* pre*(...))` |	执行所有目标类所有以 pre 为前缀的方法时切入

还有一些其他指示器：

参数 | 功能
- | -
`within(com.company.project.service.*)` | 执行 service 包内的任意方法时切入
`this(com.company.project.service.AccountService)` | 执行实现 AccountService 接口的代理对象的任意方法时切入
`target(com.company.project.service.AccountService)` | 执行实现 AccountService 接口的目标对象的任意方法时切入
`args(java.io.Serializable)`| 任何一个只接受一个参数，并且运行时所传入的参数是 Serializable 接口的方法


- 多个匹配条件之间使用链接符连接： `&&`、`||`、`!` 。
- within 指示器表示可以选择的包，bean 指示器可以在切点中选择 bean 。

如参数 `execution(String com.company.project.service.test1.IBuy.buy(double)) && args(price) && bean(girl)` 

要求返回类型为 String ；参数类型为 double ；参数名为 price ；调用目标方法的 bean 名称为 girl 。

**简化代码**

对于类中要频繁要切入的目标方法，我们可以使用 `@Pointcut` 注解声明切点表达式，简化代码。


```java
@Aspect
@Component
public class EatPlus {

    @Pointcut("execution(* com.company.project.service.Meal.eat(..))")
    public void point(){}

    @Before("point()")
    public void cook() {
        System.out.println("cook");
    }

    @Around("point()")
    public void party(ProceedingJoinPoint pj) {
        try {
            System.out.println("cook");
            pj.proceed();
            System.out.println("clean");
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
    }

    @Pointcut("execution(String com.company.project.service.Meal.eat(double)) && args(price) && bean(people)")
    public void point2(double price) {
    }

    @Around("point2(price)")
    public String pay(ProceedingJoinPoint pj, double price){
        try {
            pj.proceed();
            if (price > 100) {
                System.out.println("can not afford");
                return "没有购买";
            }
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        return "购买";
    }
}
```


---

## 常用 AOP

### 异常处理

- `@ControllerAdvice` / `@RestControllerAdvice`: 标注当前类为所有 Controller 类服务

- `@ExceptionHandler`: 标注当前方法处理异常（默认处理 RuntimeException）
  `@ExceptionHandler(value = Exception.class)`: 处理所有异常

```java
@RestControllerAdvice
public class ControllerExceptionHandler {

    @ExceptionHandler(Throwable.class)
    public ResultBean handleOtherException(Throwable e) {
        String message = String.format("错误=%s,位置=%s", e.toString(), e.getStackTrace()[0].toString());
        return ResultBean.error(ErrorCode.UNKNOWN_ERROR.getErrorCode(), message);
    }

    @ExceptionHandler(StreamPlatformException.class)
    public ResultBean handleVenusException(StreamPlatformException e) {
        return ResultBean.error(e.getErrorCode(), e.getMessageToUser());
    }

    @ExceptionHandler(FormValidationException.class)
    public ResultBean handleFormValidationException(FormValidationException e) {
        StringBuilder message = new StringBuilder();
        e.getResult().getAllErrors().forEach(objectError -> {
            if (objectError instanceof FieldError) {
                FieldError fieldError = (FieldError) objectError;
                message.append("参数").append(fieldError.getField())
                        .append("错误值为").append(fieldError.getRejectedValue())
                        .append(fieldError.getDefaultMessage());
            } else {
                message.append(objectError.getDefaultMessage());
            }
        });
        return ResultBean.error(ErrorCode.PARAMETER_VALIDATION_ERROR.getErrorCode(),
                String.format(ErrorCode.PARAMETER_VALIDATION_ERROR.getMessage(), message));
    }
}
```

### 拦截器

- **拦截器(Interceptor)**

Java Web 中，在执行 Controller 方法前后对 Controller 请求进行拦截和处理。依赖于 web 框架，在 Spring 配置。在实现上基于 Java 的反射机制。

- **过滤器(Filter)**

Java Web 中，在 request/response 传入 Servlet 前，过滤信息或设置参数。依赖于 servlet 容器，在 web.xml 配置。在实现上基于函数回调。

> 两者常用于修改字符编码、删除无用参数、登录校验等。Spring 框架中优先使用拦截器：功能接近、使用更加灵活。


拦截器配置

```java
// 在配置中引入拦截器对象（单独编写拦截器类）

@Override
public void addInterceptors(InterceptorRegistry registry) {
    // 导入拦截器对象，默认拦截全部
    InterceptorRegistration addInterceptor = registry.addInterceptor(new myInterceptor());

    // 排除配置
    addInterceptor.excludePathPatterns("/error","/login","/user/login");               
    addInterceptor.excludePathPatterns("/asserts/**");                       
    addInterceptor.excludePathPatterns("/webjars/**");
    addInterceptor.excludePathPatterns("/public/**");
    // 拦截配置
    addInterceptor.addPathPatterns("/**");
}
```

拦截器类通过实现 HandlerInterceptor 接口或者继承 HandlerInterceptorAdapter 类。

```java
// 定义拦截器
public class myInterceptor extends HandlerInterceptorAdapter {

    // Session key
    public final static String SESSION_KEY = "user";

    // preHandle 预处理
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 检查 session
        HttpSession session = request.getSession();
        if (session.getAttribute(SESSION_KEY) != null) return true;
        // 重定向到登录页面
        request.setAttribute("message","登录失败，请先输入用户名和密码。");
        request.getRequestDispatcher("login").forward(request,response);
        return false;
    }

    // postHandle 善后处理
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
                           ModelAndView modelAndView) {
        System.out.println("INTERCEPTOR POSTHANDLE CALLED");
    }

}
```

过滤器类通过继承 Filter 类实现，直接添加注解即可。

```java
@Component                                                                // 作为组件，交给容器处理
@ServletComponentScan                                                     // 扫描组件
@WebFilter(urlPatterns = "/login/*",filterName = "loginFilter")           // 设定过滤路径和名称
@Order(1)                                                                 // 设定优先级（值小会优先执行）
public class LoginFilter implements Filter{

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // 过滤器前执行
        System.out.println("before");
        // 执行内部逻辑
        filterChain.doFilter(servletRequest,servletResponse);
        // 过滤器后执行
        System.out.println("after");
    }

    @Override
    public void destroy() {
    }
}
```


**调用顺序**

![filter](filter.png)



