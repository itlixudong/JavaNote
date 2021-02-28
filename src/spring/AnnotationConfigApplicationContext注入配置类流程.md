## AnnotationConfigApplicationContext启动流程

1. 调用有参的构造函数进行初始化

   ```java
   public AnnotationConfigApplicationContext(Class<?>... componentClasses) {
   	this();
   	register(componentClasses);
   	refresh();
   }
   ```

   

2. 有参构造函数调用this，执行AnnotationConfigApplicationContext()，此时会执行到父类的无参构造函数进行初始化

   ```java
   public GenericApplicationContext() {
       this.beanFactory = new DefaultListableBeanFactory();
   }
   ```

3. 在父类的无参构造函数中初始化DefaultListableBeanFactory对象

4. 在自身的无参构造函数中初始化AnnotatedBeanDefinitionReader和ClassPathBeanDefinitionScanner对象，前者是加载注解的ioc容器，后者是加载扫包的ioc容器

5. 调用register方法，对启动配置类进行注册，最终执行到doRegisterBean()方法

   ```java
   private <T> void doRegisterBean(Class<T> beanClass, @Nullable String name,
                                   @Nullable Class<? extends Annotation>[] qualifiers, @Nullable Supplier<T> supplier,
                                   @Nullable BeanDefinitionCustomizer[] customizers) {
   	//创建一个注解方式启动注入配置类
       AnnotatedGenericBeanDefinition abd = new AnnotatedGenericBeanDefinition(beanClass);
     	//判断是否有使用condition条件方式注入bean
       if (this.conditionEvaluator.shouldSkip(abd.getMetadata())) {
           return;
       }
   	//设置回调方式
       abd.setInstanceSupplier(supplier);
     	//判断config类上是否加上@scope作用域如果没有就是默认的单例
       ScopeMetadata scopeMetadata = this.scopeMetadataResolver.resolveScopeMetadata(abd);
     	//设置scope的值
       abd.setScope(scopeMetadata.getScopeName());
     	//判断是否传入了beanName 如果没有，就去生成
       String beanName = (name != null ? name : this.beanNameGenerator.generateBeanName(abd, this.registry));
   	//获取类上面是否添加了 Lazy/Primary/DependsOn/Role/Description 注解 
       AnnotationConfigUtils.processCommonDefinitionAnnotations(abd);
       if (qualifiers != null) {
           for (Class<? extends Annotation> qualifier : qualifiers) {
               if (Primary.class == qualifier) {
                   abd.setPrimary(true);
               }
               else if (Lazy.class == qualifier) {
                   abd.setLazyInit(true);
               }
               else {
                   abd.addQualifier(new AutowireCandidateQualifier(qualifier));
               }
           }
       }
       if (customizers != null) {
           for (BeanDefinitionCustomizer customizer : customizers) {
               customizer.customize(abd);
           }
       }
   	//对我们的启动配置类进行包装
       BeanDefinitionHolder definitionHolder = new BeanDefinitionHolder(abd, beanName);
       definitionHolder = AnnotationConfigUtils.applyScopedProxyMode(scopeMetadata, definitionHolder, this.registry);
       //注册我们的BeanDefinition
     	BeanDefinitionReaderUtils.registerBeanDefinition(definitionHolder, this.registry);
   }
   ```

   <img src="https://raw.githubusercontent.com/itlixudong/Image/main/img/20210227205035.png"  />
