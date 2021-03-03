1. 在SpringMVC中核心的类是DispatcherServlet类，DispatcherServlet类类图如下

   ![](https://raw.githubusercontent.com/itlixudong/Image/main/img/20210302221154.png)

2. 代码执行会先执行到HttpServlet类中的service()方法，在service()方法中去判断我么的请求类型，然后调用doGet()方法执行到FrameworkServlet类中的doGet()方法

3. 在doGet()中调用processRequest(request, response)方法

4. processRequest()方法中调用到DispatcherServlet类中的doService(request, response)方法

5. 在doService(request, response)方法中又调用到我们的**doDispatch**(request, response)方法

6. 在doDispatch方法中通过我们请求的url路径调用**getHandler**(processedRequest)方法，去查询相应的handler，在getHandler方法中通过循环遍历我们的handlerMappings，在我们的map中查找我们的方法，在handlerMappings中有两种HandlerMapping，一种是通过注解的方式配置的使用RequestMappingHandlerMapping类，另一种是通过xml方式配置的使用BeanNameUrlHandlerMapping类

7. 通过url只要在一个HandlerMapping中找到，就返回**HandlerExecutionChain**类，HandlerExecutionChain类中存放了我们的具体的类和方法

8. 然后调用**getHandlerAdapter**(mappedHandler.getHandler())方法根据我们的handler去查找处理器适配器，在这里适配器一共有三种，分别是： SimpleControllerHandlerAdapter（如果我们的类继承了controller类，使用此适配器）， HttpRequestHandlerAdapter（HTTP请求处理器适配器）， **RequestMappingHandlerAdapter**（使用注解（@Controller）的处理器适配器）

9. 通过supports()方法传递我们的handler去判断使用哪一个适配器，当返回true的时候就返回相应的适配器

10. 接着执行到mappedHandler.applyPreHandle(processedRequest, response)方法，在次方法中循环遍历所有的拦截器去执行我们的拦截器前置方法，如果为false就return掉

11. 当返回true的时候，接着就执行我们的目标方法，调用**handle**()方法执行到RequestMappingHandlerAdapter类中的handleInternal()方发，然后判断synchronizeOnSession，执行到invokeHandlerMethod(request, response, handlerMethod)方法，紧接着调用**invokeAndHandle**(webRequest, mavContainer)方法去执行到我们controller中的目标方法，最终返回ModelAndView对象

12. 接着执行mappedHandler.applyPostHandle(processedRequest, response, mv)方法，去指定拦截器的PostHandle方法，此方法在请求处理完成，视图渲染之前执行，执行完成之后

13. 接着执行**processDispatchResult**(processedRequest, response, mappedHandler, mv, dispatchException)方法，判断ModelAndView对象不为null，然后调用render(mv, request, response)方法去渲染视图

14. 视图渲染完成之后判断HandlerExecutionChain类不为null，然后执行mappedHandler.triggerAfterCompletion(request, response, null)方法，在方法中回去到我们的拦截器，执行拦截器的afterCompletion()方法

