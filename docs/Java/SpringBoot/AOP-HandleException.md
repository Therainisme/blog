---
layout: default
title: "Spring Boot | @Aspect AOP之美丽的注解"
description: "通过AOP进行安全检测"
---

如果我们需要在一个Service中多次检测某个实例的值是否合法

按照以往的习惯，都是写个Util或者其他的类似与检测实例是否合法的类，然后再Service中不断的反复调用它

现在我们只需要自定义一个注解，利用SpringBoot提供的AOP注解，即可简易的实现

以用户的注册为例子：

# 第一步：我们可以先自定义一个专用的注解

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface UserFieldRequired {
    boolean idRequired() default false;
    boolean nameRequired() default false;
    boolean passwordRequired() default false;
    boolean emailRequired() default false;
    boolean emailOrNameRequired() default false;
}
```

> @Target

注解可标记的目标，样例规定了标记的是一个方法

> @Retention

定义标记注解的生命周期，样例规定了该注解能在运行期间一直存活

# 第二步：写一个扫描此注解的Aspect类

```java
@Aspect
@Order(1)
@Component
public class ValidUserNameAspect {

    @Pointcut("@annotation(com.therainisme.magicwood.aspect.user.UserPropertyRequired)")
    public void thePointCut() {
    }

    @Before("thePointCut()")
    public void doBefore(JoinPoint joinPoint) {
        UserDTO userDTO = (UserDTO) joinPoint.getArgs()[0];
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        UserPropertyRequired userPropertyRequired = method.getAnnotation(UserPropertyRequired.class);
        if (userPropertyRequired.idRequired()) {
            if (userDTO.getId() == null) throw new AspectException(CustomExceptionEnum.USER_INFO_INVALID);
        }
        if (userPropertyRequired.passwordRequired()) {
            if (userDTO.getPassword() == null) throw new AspectException(CustomExceptionEnum.USER_INFO_INVALID);
        }
        if (userPropertyRequired.emailOrNameRequired()) {
            if (userDTO.getName() == null && userDTO.getEmail() == null)
                throw new AspectException(CustomExceptionEnum.USER_INFO_INVALID);
        } else {
            if (userPropertyRequired.nameRequired()) {
                if (userDTO.getName() == null) throw new AspectException(CustomExceptionEnum.USER_INFO_INVALID);
            }
            if (userPropertyRequired.emailRequired()) {
                if (userDTO.getEmail() == null) throw new AspectException(CustomExceptionEnum.USER_INFO_INVALID);
            }
        }
    }
}
```

> @Aspect

标记此类是一个切面

> @Order

设置运行顺序。在有多个切面指向同一个方法的时候，规定他们的执行顺序，数字越小，优先级越大

> @Component

标记此类归Spring管理

> @PointCut

样例中的切面是只向一个注解

> doBefore()

通过JoinPoint获取该方法及该方法的参数，进而获取需要验证的数值

既然方法都获取到了，那么再通过反射来获取注解的内容即可

之后就是验证的事情了

# 第三步：在有需要的地方打上注解

尽情享用

```java
@Service
public class UserService{
    @Resource
    private UserMapper userMapper;

    @UserFieldRequired(nameRequired = true, passwordRequired = true, emailRequired = true)
    public User register(User user) {
        user.setToken(UUID.randomUUID().toString().replace("-", "").toUpperCase());
        user.setGmtCreate(new Date());
        user.setGmtModified(user.getGmtCreate());
        userMapper.register(user);
        return user;
    }
}
```

局限性也有，例如对方法传入的参数有确切的规定

