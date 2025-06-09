

### `PageHelper`分页插件

`PageHelper`是第三方提供的`Mybatis`框架中的一款功能强大、方便易用的分页插件，支持任何形式的单标、多表的分页查询。

官网：<https://pagehelper.github.io/>

当使用了`PageHelper`分页插件进行分页，就无需再`Mapper`中进行手动分页了。 在`Mapper`中我们只需要进行正常的列表查询即可。在`Service`层中，调用`Mapper`的方法之前设置分页参数，在调用`Mapper`方法执行查询之后，解析分页结果，并将结果封装到`PageResult`对象中返回。

1. 在`pom.xml`引入依赖
```XML
<!--分页插件PageHelper-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.7</version>
</dependency>
```

2. `EmpMapper`
```Java
/**
 * 查询所有的员工及其对应的部门名称
 */
@Select("select e.*, d.name deptName from emp as e left join dept as d on e.dept_id = d.id")
public List<Emp> list();
```

3.  `EmpServiceImpl`
```Java
@Override
public PageResult page(Integer page, Integer pageSize) {
    //1. 设置分页参数
    PageHelper.startPage(page,pageSize);

    //2. 执行查询
    List<Emp> empList = empMapper.list();
    Page<Emp> p = (Page<Emp>) empList;

    //3. 封装结果
    return new PageResult(p.getTotal(), p.getResult());
}
```

***

### 事务管理


#### 事务基础

注解：`@Transactional`

作用：就是在当前这个方法执行开始之前来开启事务，方法执行完毕之后提交事务。如果在这个方法执行的过程当中出现了异常，就会进行事务的回滚操作。

位置：业务层的方法上、类上、接口上

- 方法上：当前方法交给spring进行事务管理
    
- 类上：当前类中所有的方法都交由spring进行事务管理
    
- 接口上：接口下所有的实现类当中所有的方法都交给spring 进行事务管理


接下来，我们就可以在业务方法save上加上 `@Transactional` 来控制事务 。

```Java
@Transactional
@Override
public void save(Emp emp) {
    //1.补全基础属性
    emp.setCreateTime(LocalDateTime.now());
    emp.setUpdateTime(LocalDateTime.now());
    //2.保存员工基本信息
    empMapper.insert(emp);

    int i = 1/0;

    //3. 保存员工的工作经历信息 - 批量
    Integer empId = emp.getId();
    List<EmpExpr> exprList = emp.getExprList();
    if(!CollectionUtils.isEmpty(exprList)){
        exprList.forEach(empExpr -> empExpr.setEmpId(empId));
        empExprMapper.insertBatch(exprList);
    }
}
```

  

@Transactional注解：我们一般会在业务层当中来控制事务，因为在业务层当中，一个业务功能可能会包含多个数据访问的操作。在业务层来控制事务，我们就可以将多个数据访问操作控制在一个事务范围内。

  

说明：可以在`application.yml`配置文件中开启事务管理日志，这样就可以在控制看到和事务相关的日志信息了

```yml
[[spring事务管理日志]]
logging: 
  level: 
    org.springframework.jdbc.support.JdbcTransactionManager: debug
```


#### 事务进阶

前面我们通过spring事务管理注解`@Transactional`已经控制了业务层方法的事务。接下来我们要来详细的介绍一下`@Transactional`事务管理注解的使用细节。我们这里主要介绍`@Transactional`注解当中的两个常见的属性：

- 异常回滚的属性：`rollbackFor`
    
- 事务传播行为：`propagation`


##### `rollbackFor`

我们在之前编写的业务方法上添加了`@Transactional`注解，来实现事务管理。

```Java
@Transactional
@Override
public void save(Emp emp) {
    //1.补全基础属性
    emp.setCreateTime(LocalDateTime.now());
    emp.setUpdateTime(LocalDateTime.now());
    //2.保存员工基本信息
    empMapper.insert(emp);
        
    int i = 1/0;
        
    //3. 保存员工的工作经历信息 - 批量
    Integer empId = emp.getId();
    List<EmpExpr> exprList = emp.getExprList();
    if(!CollectionUtils.isEmpty(exprList)){
        exprList.forEach(empExpr -> empExpr.setEmpId(empId));
        empExprMapper.insertBatch(exprList);
    }
}
```

以上业务功能`save`方法在运行时，会引发除0的算术运算异常(运行时异常)，出现异常之后，由于我们在方法上加了`@Transactional`注解进行事务管理，所以发生异常会执行rollback回滚操作，从而保证事务操作前后数据是一致的。

下面我们在做一个测试，我们修改业务功能代码，在模拟异常的位置上直接抛出`Exception`异常（编译时异常）

```Java
@Transactional
@Override
public void save(Emp emp) {
    //1.补全基础属性
    emp.setCreateTime(LocalDateTime.now());
    emp.setUpdateTime(LocalDateTime.now());
    //2.保存员工基本信息
    empMapper.insert(emp);
        
    //模拟：异常发生
    if(true){
        throw new Exception("出现异常了~~~");
    }
        
    //3. 保存员工的工作经历信息 - 批量
    Integer empId = emp.getId();
    List<EmpExpr> exprList = emp.getExprList();
    if(!CollectionUtils.isEmpty(exprList)){
        exprList.forEach(empExpr -> empExpr.setEmpId(empId));
        empExprMapper.insertBatch(exprList);
    }
}
```

> 说明：在`service`中向上抛出一个`Exception`编译时异常之后，由于是`controller`调用`service`，所以在`controller`中要有异常处理代码，此时我们选择在`controller`中继续把异常向上抛。

**默认情况下，只有出现`RuntimeException`(运行时异常)才会回滚事务。**

假如我们想让所有的异常都回滚，需要来配置`@Transactional`注解当中的`rollbackFor`属性，通过`rollbackFor`这个属性可以指定出现何种异常类型回滚事务。

```Java
@Transactional(rollbackFor = Exception.class)
@Override
public void save(Emp emp) throws Exception {
    //1.补全基础属性
    emp.setCreateTime(LocalDateTime.now());
    emp.setUpdateTime(LocalDateTime.now());
    //2.保存员工基本信息
    empMapper.insert(emp);
        
    //int i = 1/0;
    if(true){
        throw new Exception("出异常啦....");
    }
        
    //3. 保存员工的工作经历信息 - 批量
    Integer empId = emp.getId();
    List<EmpExpr> exprList = emp.getExprList();
    if(!CollectionUtils.isEmpty(exprList)){
        exprList.forEach(empExpr -> empExpr.setEmpId(empId));
        empExprMapper.insertBatch(exprList);
    }
}
```

>结论：
>	1、在Spring的事务管理中，默认只有运行时异常 `RuntimeException`才会回滚。
>	2、如果还需要回滚指定类型的异常，可以通过`rollbackFor`属性来指定。


##### `propagation`

我们接着继续学习`@Transactional`注解当中的第二个属性`propagation`，这个属性是用来配置事务的传播行为的。

什么是事务的传播行为呢？

- 就是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行事务控制。

例如：两个事务方法，一个A方法，一个B方法。在这两个方法上都添加了`@Transactional`注解，就代表这两个方法都具有事务，而在A方法当中又去调用了B方法。

![](https://heuqqdmbyk.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDNiZmMwOGJhZmI1OGJmZjhhMmYwOGQyNGQxYTZiYzVfRFBOU05iUGJCTE1lajJraHA2N0gwMUJLemVvZ3hlanBfVG9rZW46RDBXRGJzTjJIb29ITEF4WnRacmN1ZnVSbklOXzE3NDc2NDAxMTc6MTc0NzY0MzcxN19WNA)

所谓事务的传播行为，指的就是在A方法运行的时候，首先会开启一个事务，在A方法当中又调用了B方法， B方法自身也具有事务，那么B方法在运行的时候，到底是加入到A方法的事务当中来，还是B方法在运行的时候新建一个事务？这个就涉及到了事务的传播行为。

  

我们要想控制事务的传播行为，在`@Transactional`注解的后面指定一个属性`propagation`，通过 `propagation` 属性来指定传播行为。接下来我们就来介绍一下常见的事务传播行为。

| 属性值           | 含义                                 |
| ------------- | ---------------------------------- |
| REQUIRED      | 【默认值】需要事务，有则加入，无则创建新事务             |
| REQUIRES_NEW  | 需要新事务，无论有无，总是创建新事务                 |
| SUPPORTS      | 支持事务，有则加入，无则在无事务状态中运行              |
| NOT_SUPPORTED | 不支持事务，在无事务状态下运行,如果当前存在已有事务,则挂起当前事务 |
| MANDATORY     | 必须有事务，否则抛异常                        |
| NEVER         | 必须没事务，否则抛异常                        |
| …             |                                    |

> 对于这些事务传播行为，我们只需要关注以下两个就可以了：
> 
> 1. REQUIRED（默认值）
>     
> 2. REQUIRES_NEW
>