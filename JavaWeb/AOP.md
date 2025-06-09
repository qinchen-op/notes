### 什么是`AOP`？

`AOP`：`Aspect Oriented Programming`（面向切面编程、面向方面编程），简单来说就是面向方法编程。即将方法作为一个对象。

解决需求：对于一些业务方法要进行相同的业务优化，如统计方法执行的耗时。
解决思路：在每个方法的最前面和最后面各加上一个记时功能，再最后计算时间之差。但每个方法都这样做会增加工作量且不便于管理。

于是，`AOP`的功能便是简化这项工作。

`AOP`的优势主要体现在以下四个方面：
- 减少重复代码：不需要在业务方法中定义大量的重复性的代码，只需要将重复性的代码抽取到`AOP`程序中即可。
- 代码无侵入：在基于`AOP`实现这些业务功能时，对原有的业务代码是没有任何侵入的，不需要修改任何的业务代码。
- 提高开发效率。
- 维护方便。

****



### `AOP`基础
****

#### `AOP`入门
1. 为使用`AOP`我们必须导入它的相关依赖（在pom.xml文件中）：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

2. 编写`AOP`程序：针对特定方法根据业务需要进行编程
```Java
@Component
@Aspect //当前类为切面类
@Slf4j
public class RecordTimeAspect {

    @Around("execution(* com.itheima.service.impl.DeptServiceImpl.*(..))")
    public Object recordTime(ProceedingJoinPoint pjp) throws Throwable {
        //记录方法执行开始时间
        long begin = System.currentTimeMillis();

        //执行原始方法
        Object result = pjp.proceed();

        //记录方法执行结束时间
        long end = System.currentTimeMillis();

        //计算方法执行耗时
        log.info("方法执行耗时: {}毫秒",end-begin);
        return result;
    }
}
```

3. 我们通过`AOP`入门程序完成了业务方法执行耗时的统计，`AOP`的应用还有以下场景：
	- 记录系统的操作日志
	- 权限控制
	- 事务管理：关于`Spring`事务管理，其底层就是通过`AOP`实现的。


#### `AOP`核心概念

1. 连接点：`JoinPoint`，可以被`AOP`的方法（暗含方法执行时的相关信息）
2. 通知：`Advice`，指那些重复的逻辑，也就是共性功能（最终体现为一个方法）
3. 切入点：`PointCut`，匹配连接点的条件，通知仅会在切入点方法执行时被应用，在`AOP`中，我们通常会使用切入点表达式来描述切入点
4. 切面：`Aspect`，描述通知与切入点的对应关系（通知+切入点）
5. 目标对象：`Target`，通知所对应的对象

`AOP`底层是基于动态代理技术来实现的，也就是说在程序运行的时候，会自动的基于动态代理技术生成一个对应的代理对象。在代理对象中就会对目标对象的原始方法进行功能的增强。


****



### `AOP`进阶
***

#### 通知类型

|                   |                                      |
| ----------------- | ------------------------------------ |
| `Spring AOP`通知类型  | 说明                                   |
| `@Around`         | 环绕通知，此注解标注的通知方法在目标方法前、后都被执行          |
| `@Before`         | 前置通知，此注解标注的通知方法在目标方法前被执行             |
| `@After`          | 后置通知，此注解标注的通知方法在目标方法后被执行，无论是否有异常都会执行 |
| `@AfterReturning` | 返回后通知，此注解标注的通知方法在目标方法后被执行，有异常不会执行    |
| `@AfterThrowing`  | 异常后通知，此注解标注的通知方法发生异常后执行              |

```Java
@Slf4j
@Component
@Aspect
public class MyAspect1 {
    //前置通知
    @Before("execution(* com.itheima.service.*.*(..))")
    public void before(JoinPoint joinPoint){
        log.info("before ...");

    }

    //环绕通知
    @Around("execution(* com.itheima.service.*.*(..))")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        log.info("around before ...");

        //调用目标对象的原始方法执行
        Object result = proceedingJoinPoint.proceed();
        
        //原始方法如果执行时有异常，环绕通知中的后置代码不会在执行了
        
        log.info("around after ...");
        return result;
    }

    //后置通知
    @After("execution(* com.itheima.service.*.*(..))")
    public void after(JoinPoint joinPoint){
        log.info("after ...");
    }

    //返回后通知（程序在正常执行的情况下，会执行的后置通知）
    @AfterReturning("execution(* com.itheima.service.*.*(..))")
    public void afterReturning(JoinPoint joinPoint){
        log.info("afterReturning ...");
    }

    //异常通知（程序在出现异常的情况下，执行的后置通知）
    @AfterThrowing("execution(* com.itheima.service.*.*(..))")
    public void afterThrowing(JoinPoint joinPoint){
        log.info("afterThrowing ...");
    }
}
```

程序发生异常的情况下：

- `@AfterReturning`标识的通知方法不会执行，`@AfterThrowing`标识的通知方法执行了
    
- `@Around`环绕通知中原始方法调用时有异常，通知中的环绕后的代码逻辑也不会在执行了 （因为原始方法调用已经出异常了）

在使用通知时的注意事项：

- `@Around`环绕通知需要自己调用 `ProceedingJoinPoint.proceed()`来让原始方法执行，其他通知不需要考虑目标方法执行
    
- `@Around`环绕通知方法的返回值，必须指定为`Object`，来接收原始方法的返回值，否则原始方法执行完毕，是获取不到返回值的

上述代码中存在一个问题，那就是切入点表达式重复，那么`AOP`为简化代码编写，提供了*抽取*。

`@PointCut`注解的作用就是将公共的切入点表达式抽取出来，需要时应用该切入点表达式的方法即可。

```Java
@Slf4j
@Component
@Aspect
public class MyAspect1 {

    //切入点方法（公共的切入点表达式）
    @Pointcut("execution(* com.itheima.service.*.*(..))")
    private void pt(){}

    //前置通知（引用切入点）
    @Before("pt()")
    public void before(JoinPoint joinPoint){
        log.info("before ...");

    }

    //环绕通知
    @Around("pt()")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        log.info("around before ...");

        //调用目标对象的原始方法执行
        Object result = proceedingJoinPoint.proceed();
        //原始方法在执行时：发生异常
        //后续代码不在执行

        log.info("around after ...");
        return result;
    }

    //后置通知
    @After("pt()")
    public void after(JoinPoint joinPoint){
        log.info("after ...");
    }

    //返回后通知（程序在正常执行的情况下，会执行的后置通知）
    @AfterReturning("pt()")
    public void afterReturning(JoinPoint joinPoint){
        log.info("afterReturning ...");
    }

    //异常通知（程序在出现异常的情况下，执行的后置通知）
    @AfterThrowing("pt()")
    public void afterThrowing(JoinPoint joinPoint){
        log.info("afterThrowing ...");
    }
}
```

需要注意的是：当切入点方法使用`private`修饰时，仅能在当前切面类中引用该表达式，当外部需要引用当前类中的切入点表达式时就需要把`private`改为`public`。


#### 通知顺序

在项目开发中，我们定义多个切面类时，当多个切面类中多个切入点都匹配到同一个目标方法时（即为同一个方法增加多个业务功能时），这些切面方法都会运行，但谁先运行，谁后运行的问题就产生了。

其运行的顺序默认是按照*切面类的类名字母*排序：
- 目标方法前的通知方法：字母排名靠前的先执行
- 目标方法后的通知方法：字母排名靠前的后执行

如果我们想控制通知的执行顺序有两种方式：

1. 修改切面类的类名（这种方式非常繁琐、而且不便管理）
    
2. 使用Spring提供的`@Order`注解

使用`@Order`注解的案例：

```Java
@Slf4j
@Component
@Aspect
@Order(2)  //切面类的执行顺序（前置通知：数字越小先执行; 后置通知：数字越小越后执行）
public class MyAspect2 {
    //前置通知
    @Before("execution(* com.itheima.service.*.*(..))")
    public void before(){
        log.info("MyAspect2 -> before ...");
    }

    //后置通知 
    @After("execution(* com.itheima.service.*.*(..))")
    public void after(){
        log.info("MyAspect2 -> after ...");
    }
}
```

```Java
@Slf4j
@Component
@Aspect
@Order(3)  //切面类的执行顺序（前置通知：数字越小先执行; 后置通知：数字越小越后执行）
public class MyAspect3 {
    //前置通知
    @Before("execution(* com.itheima.service.*.*(..))")
    public void before(){
        log.info("MyAspect3 -> before ...");
    }

    //后置通知
    @After("execution(* com.itheima.service.*.*(..))")
    public void after(){
        log.info("MyAspect3 ->  after ...");
    }
}
```

```Java
@Slf4j
@Component
@Aspect
@Order(1) //切面类的执行顺序（前置通知：数字越小先执行; 后置通知：数字越小越后执行）
public class MyAspect4 {
    //前置通知
    @Before("execution(* com.itheima.service.*.*(..))")
    public void before(){
        log.info("MyAspect4 -> before ...");
    }

    //后置通知
    @After("execution(* com.itheima.service.*.*(..))")
    public void after(){
        log.info("MyAspect4 -> after ...");
    }
}
```

**通知的执行顺序主要知道两点即可：**

1. **不同的切面类当中，默认情况下通知的执行顺序是与切面类的类名字母排序是有关系的**
    
2. **可以在切面类上面加上@Order注解，来控制不同的切面类通知的执行顺序**


#### 切入点表达式

切入点表达式：描述切入点方法的一种表达式
- 作用：主要用来决定项目中的哪些方法需要加入通知
- 常见形式：
	1. `execution(......)`：根据方法的签名来匹配
	2. `@annotation(......)`：根据注解匹配

##### `execution`

`execution`主要根据方法的返回值、包名、类名、方法名、方法参数等信息来匹配，语法为：
```Java
execution(访问修饰符?  返回值  包名.类名.?方法名(方法参数) throws 异常?)
```

其中带`?`的表示可以省略的部分
- 访问修饰符
- 包名.类名
- `throw` 异常

可以使用通配符描述切入点
- `*`：单个独立的任意符号，可以通配任意返回值、包名、类名、方法名、任意类型的一个参数，也可以通配包、类、方法名的一部分。
- `..`：多个连续的任意符号，可以通配任意层级的包，或任意类型、任意个数的参数。

切入点表达式的语法规则：

1. 方法的访问修饰符可以省略
    
2. 返回值可以使用`*`号代替（任意返回值类型）
    
3. 包名可以使用`*`号代替，代表任意包（一层包使用一个`*`）
    
4. 使用`..`配置包名，标识此包以及此包下的所有子包
    
5. 类名可以使用`*`号代替，标识任意类
    
6. 方法名可以使用`*`号代替，表示任意方法
    
7. 可以使用 `*` 配置参数，一个任意类型的参数
    
8. 可以使用`..` 配置参数，任意个任意类型的参数


注意事项：
- 根据业务需要，可以使用 且（`&&`）、或（`||`）、非（`!`） 来组合比较复杂的切入点表达式。
```Java
execution(* com.itheima.service.DeptService.list(..)) 
|| 
execution(* com.itheima.service.DeptService.delete(..))
```


##### `@annotation`

实现步骤：

1. 编写自定义注解
    
2. 在业务类要做为连接点的方法上添加自定义注解
    

  

**自定义注解**：`LogOperation`

```Java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogOperation{
}
```

  

**业务类**：`DeptServiceImpl`

```Java
@Slf4j
@Service
public class DeptServiceImpl implements DeptService {
    @Autowired
    private DeptMapper deptMapper;

    @Override
    @LogOperation //自定义注解（表示：当前方法属于目标方法）
    public List<Dept> list() {
        List<Dept> deptList = deptMapper.list();
        //模拟异常
        //int num = 10/0;
        return deptList;
    }

    @Override
    @LogOperation //自定义注解（表示：当前方法属于目标方法）
    public void delete(Integer id) {
        //1. 删除部门
        deptMapper.delete(id);
    }


    @Override
    public void save(Dept dept) {
        dept.setCreateTime(LocalDateTime.now());
        dept.setUpdateTime(LocalDateTime.now());
        deptMapper.save(dept);
    }

    @Override
    public Dept getById(Integer id) {
        return deptMapper.getById(id);
    }

    @Override
    public void update(Dept dept) {
        dept.setUpdateTime(LocalDateTime.now());
        deptMapper.update(dept);
    }
}
```


**切面类**

```Java
@Slf4j
@Component
@Aspect
public class MyAspect6 {
    //针对list方法、delete方法进行前置通知和后置通知

    //前置通知
    @Before("@annotation(com.itheima.anno.LogOperation)")
    public void before(){
        log.info("MyAspect6 -> before ...");
    }
    
    //后置通知
    @After("@annotation(com.itheima.anno.LogOperation)")
    public void after(){
        log.info("MyAspect6 -> after ...");
    }
}
```
