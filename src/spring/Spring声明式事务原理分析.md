Spring声明式事务底层原理分析

1. 使用@EnableTransactionManagement开启事务

2. 再注解中@Import(TransactionManagementConfigurationSelector.class)导入事务选择器类

3. 再AutoProxyRegistrar类中将InfrastructureAdvisorAutoProxyCreator类注入到ioc容器中

4. 再ProxyTransactionManagementConfiguration对象中将TransactionInterceptor类注入到ioc容器中，这个类就是事务的核心类

5. InfrastructureAdvisorAutoProxyCreator类的根类是BeanPostProcessor，由此可以得出这个地方和aop的原理也是一样的，再开启事务注解的时候会帮我们开启aop的注解

6. 在InfrastructureAdvisorAutoProxyCreator的父类AbstractAutoProxyCreator中的后置处理器postProcessAfterInitialization()方法中来判断是否需要创建代理类

7. 执行到目标方法的时候会执行到jdk动态代理中的invoke()方法在invoke()方法中获取到我们的调用链chain

8. 拿到事务的TransactionInterceptor，调用TransactionInterceptor中的invoke()方法，执行invoke()方法的invokeWithinTransaction()方法

9. 在invokeWithinTransaction()方法中进行事务的具体操作

   ![](https://raw.githubusercontent.com/itlixudong/Image/main/img/20210301223350.png)

10. 