---
layout: default
title: "Spring Boot | AOP的奇怪应用"
description: "There's nothing here."
---

# AOP的奇怪应用

在设计接口的时候，如果返还的实例中，不想让某个实例的某个字段返还，可以将其设为`null`，然后在SpringBoot的配置文件中写上`spring.jackson.default-property-inclusion=non_null`

返回的数据中`null`的字段就不会返回了

还有一种方法，直接找到对应的字段，打上`@JsonIgnore`，这样它也不会在JSON中出现。但是呢，如果你想让它在某个接口的中又需要暴露出来，那么这个方法显然不太合适

第一种方法太丑了，想优雅一点（偷懒

那么打注解通过反射在返回时给需要的字段设为`null`就行了

老样子搞一个注解
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ResultIgnore {
    String[] value();
}
```

拿到对象后通过反射，获取对应字段，然后设为`null`，没了（因为想修改父类私有字段，所以写丑一点无所谓，写递归2就算了

```java
@Aspect
@Component
public class ResultIgnoreAspect {

    @Pointcut("@annotation(com.therainisme.magicwood.aspect.common.ResultIgnore)")
    public void thePointCut() {
    }

    @AfterReturning(pointcut = "thePointCut()", returning = "res")
    public void doAfterReturning(JoinPoint joinPoint, Object res) throws NoSuchFieldException, IllegalAccessException {
        ResultIgnore resultIgnore = ((MethodSignature) joinPoint.getSignature())
                .getMethod().getAnnotation(ResultIgnore.class);
        for (String value : resultIgnore.value()) {
            Field field = null;
            try {
                field = res.getClass().getDeclaredField(value);
            } catch (Exception ignored) {
            }
            try {
                field = res.getClass().getSuperclass().getDeclaredField(value);
            } catch (Exception ignored) {
            }
            if (field == null) {
                //todo throw an exception
            }
            field.setAccessible(true);
            field.set(res, null);
        }
    }
}
```