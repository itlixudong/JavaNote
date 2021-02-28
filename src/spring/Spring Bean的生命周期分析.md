## bean的生命周期

Spring Bean的生命周期，简单可以理解为，对象创建，初始化，销毁。

- 我们可以自定义初始化和销毁的方法

  - 通过@Bean指定 initMethod和destroyMethod方法来实现。

    ```java
    @Bean(initMethod = "initMethod",destroyMethod = "destroyMethod")
        public SpringEntity springEntity(){
        return new SpringEntity();
    }
    ```

  - 通过让Bean实现initializingBean（定义初始化逻辑），DisposableBean（定义销毁逻辑）。

    ```java
    public class BeanEntity implements InitializingBean, DisposableBean {
        @Override
        public void afterPropertiesSet() throws Exception {
        }
    
        @Override
        public void destroy() throws Exception {
        }
    }
    ```

  - 可以使用JSR250
    - @PostConstruct：在bean创建完成并且属性赋值完成，来执行初始化方法。
    - @PreDestroy：在容易销毁bean之前通知我们进行清理工作。

## 具体源码分析

1. 在容器初始化的时候执行到refresh()方法，然后执行到finishBeanFactoryInitialization(beanFactory)方法，进行实例化所有的剩余的单例（非延迟加载）。

2. 执行到beanFactory.preInstantiateSingletons();方法，实例化剩余的单例。

3. 获取到所有要初始化的bean的name，然后循环根据beanName获取到bean转换成RootBeanDefinition，判断RootBeanDefinition是否是 FactoryBean，不是的话走到 getBean(beanName)方法

4. 进入doGetBean()方法 根据getSingleton(beanName)去查询我们的bean是否在缓存中拥有，没有查询到进入else之中 

5. 根据beanName将bean转换成RootBeanDefinition对象，判断 mbd.isSingleton() 是否是单例，返回true执行到createBean(beanName, mbd, args)方法

6. 执行doCreateBean(beanName, mbdToUse, args)方法

7. 执行populateBean(beanName, mbd, instanceWrapper)方法给对象中的属性设置值，接着执行initializeBean(beanName, exposedObject, mbd)方法

8. 执行invokeAwareMethods(beanName, bean)方法来判断我们的类是否实现了aware接口，执行到applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName)方法进行前置处理，在inti方法之前执行，对类做增强，接着执行到invokeInitMethods(beanName, wrappedBean, mbd)方法，然后执行到applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName)方法进行后置处理

   ![](https://raw.githubusercontent.com/itlixudong/Image/main/img/20210228172527.png)

## 简单描述

1. 实例化bean对象
2. 设置对象的属性
3. 检查是否实现了aware接口
4. 执行相应的aware接口，BeanNameAware/BeanClassLoaderAware/BeanFactoryAware
5. 执行BeanPostProcessorsBefore 前置处理
6. 执行invokeInitMethods ，init方法
7. 执行BeanPostProcessorsAfter 后置处理
8. 使用
9. 销毁