1. 在SpringMVC中核心的类是DispatcherServlet类，DispatcherServlet类类图如下

   ![](https://raw.githubusercontent.com/itlixudong/Image/main/img/20210302221154.png)

2. 代码执行会先执行到HttpServlet类中的service()方法，在service()方法中去判断我么的请求类型，然后调用doGet()方法执行到FrameworkServlet类中的doGet()方法

3. 在doGet()中调用processRequest(request, response)方法

4. processRequest()方法中调用到DispatcherServlet类中的doService(request, response)方法

5. 在doService(request, response)方法中又调用到我们的