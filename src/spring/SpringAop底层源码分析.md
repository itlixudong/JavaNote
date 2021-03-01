## aop类图

![](https://raw.githubusercontent.com/itlixudong/Image/main/img/20210228204235.png)

## 底层源码分析

1. 使用@EnableAspectJAutoProxy注解开始aop，注解会将@Import(AspectJAutoProxyRegistrar.class)类注入到ioc容器中
2. AspectJAutoProxyRegistrar 中，手动的注册我们的切面类
3. 在registerOrEscalateApcAsRequired()方法中将**AnnotationAwareAspectJAutoProxyCreator**类注册到ioc容器中
4. 在AbstractAutoProxyCreator类中重写了BeanPostProcessor类中的postProcessBeforeInitialization()方法和postProcessAfterInitialization()方法，并且在**postProcessAfterInitialization()**方法中执行wrapIfNecessary(bean, beanName, cacheKey)判断是否需要创建代理类对象
5. wrapIfNecessary中调用getAdvicesAndAdvisorsForBean()方法判断是否要创建代理对象，需要创建则执行createProxy()方法去创建 ，判断被代理的对象是否实现了接口，如果实现了接口就执行jdk动态代理
6. 在我们调用到被aop拦截的方法的时候，会执行到invoke方法
7. 再invoke方法中 通过 List<Object> chain = this.advised.getInterceptorsAndDynamicInterceptionAdvice(method, targetClass)拿到我们定义的通知
8. 调用invocation.proceed()方法进行不断的调用，执行我们的通知。（使用的是责任链设计模式）