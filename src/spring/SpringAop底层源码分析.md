## aop类图

![](https://raw.githubusercontent.com/itlixudong/Image/main/img/20210228204235.png)

## 底层源码分析

1. 使用@EnableAspectJAutoProxy注解开始aop，注解会将@Import(AspectJAutoProxyRegistrar.class)类注入到ioc容器中
2. AspectJAutoProxyRegistrar 中，手动的注册我们的切面类
3. 在registerOrEscalateApcAsRequired()方法中将**AnnotationAwareAspectJAutoProxyCreator**类注册到ioc容器中
4. 在AbstractAutoProxyCreator类中重写了BeanPostProcessor类中的postProcessBeforeInitialization方法和postProcessAfterInitialization方法，并且在postProcessAfterInitialization方法中执行wrapIfNecessary(bean, beanName, cacheKey)判断是否需要创建代理类对象
5. wrapIfNecessary中调用getAdvicesAndAdvisorsForBean()方法判断是否要创建代理对象，需要创建则执行createProxy()方法去创建 ，判断被代理的对象是否实现了接口，如果实现了接口就执行jdk动态代理