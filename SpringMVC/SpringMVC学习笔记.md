# SpringMVC学习笔记

## 第一章  请求与参数

### 1.1  @RequestMapping 请求映射

* @RequestMapping：用于建立请求URL和处理请求方法之间的对应关系

* 在控制器的**类定义**及**方法定义**处都可以标注

  * 类定义处：提供初步的请求映射信息。相当于WEB应用的根目录

  * 方法处：提供进一步的细分映射信息。相当于类定义出的URL。若类定义处未标注@RequestMapping，  

    则方发处标记的URL相对于WEB应用的根目录

  ```java
  @Target({ElementType.TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Controller
  @ResponseBody
  public @interface RestController {
      @AliasFor(
          annotation = Controller.class
      )
      String value() default "";
  }
  ```

* @RequestMapping的其他属性

  * **value**：限定映射路径

  * **method**：限定请求方式：常用GET、POST

  * **params**：限定请求参数

    * param1：表示请求必须包含名为param1的请求参数

    * !param1：表示请求不能包含名为param1的请求参数

    * param1 != value1：表示请求包含名为param1的请求参数，但其值不能为value1

    * {"param1=value1", "param2"}：请求必须包含名为param1和param2的两个请求参数，且param1  

      参数的值必须为value1

      ```Java
      @RequestMapping(value = "/helloworld", params = {"!param1"})
      public String helloWorld(){
          return "";
      }
      ```

  * **heads**：限定请求头

  * **consumes**：只接受内容类型是哪种的请求，规定请求头中的Content-Type

  * **produces**：告诉浏览器返回的内容类型是什么，如响应头中加上Content-Type:text/html;charset=utf-8

* @RequestMapping支持Ant通配符映射

  * ?：问号匹配文件名中的一个字符
  * *：匹配文件名中的多个字符、或者匹配一层路径
  * **：匹配多层路径

### 1.2  获取Servlet原生API

* **直接在controller层的方法形参上加上下面的参数即可**

  * **HttpServletRequest**

  * **HttpServletResponse**

  * **HttpSession**

  * **java.security.Principal**：安全协议相关的

  * **Locale**：国际化有关的区域信息对象

  * **InpusStream**：httpServletRequest.getInputStream()

  * **OutputStream**：httpServletResponse.getOutputStream()

  * **Reader**：httpServletRequest.getReader()

  * **Writer**：httpServletResponse.getWriter()

    ```java
    @RequestMapping("/helloworld")
    public String getServletAPI(HttpServletRequest request, HttpServletResponse 	response){
        return "";
    }
    ```

### 1.3  绑定参数常用注解

* **@RequestParam**：将请求参数中的某个参数注入到指定属性中

  * value：请求参数中的名称

  * required：请求参数中是否必须提供此参数，默认true，不提供将报错

    ```java
    @RequestMapping("/hello")
    public String hello(@RequestParam(value="userName", required=false)String userName,
    @RequestParam("age")int age){
        System.out.println(userName + "---" + age)
    	return "";
    }
    ```

* **@PathVariable**：可以将URL中占位符参数绑定到控制器处理方法的入参中：URL中的{xxx}占位符可以通过  

  @PathVariable("xxx")绑定到操作方法的入参中

  ```java
  @RequestMapping(/delete/{id})
  public String delete(@PathVariable("id")Integer id){
      userService.delete(id);
      return "";
  }
  ```

* **@RequestBody**：用于获取请求体内容，可以接受json数据。直接使用得到的是k-v&k-v...结构的数据，不能  

  用于get方法

  * required：是否必须有请求体，默认值true

    ```java
    @RequestMapping("/helloworld")
    public String helloWorld(@RequestBody Employee employee){
        System.out.println(employee);
        return "";
    }
    ```

* **@RequestHeader**：获取请求头中某个key的值，如果请求头中没有这个值就报错

  ```java
  @RequestMapping("/hello2")
  public String hello(@RequestHeader(value="Accept-Language")String language){
      System.out.println(language);
  	return "";
  }
  ```

* **@CookieValue**：获得Cookie的值

  ```java
  @RequestMapping("/hello3")
  public String hello(@CookieValue("JSESSIONID")String jsessionId){
      System.out.println(jsessionId);
  	return "";
  }
  ```

* **HttpEntity**<T>：获取请求的所有信息，包括请求头和请求体等

  ```java
  @RequestMapping("/hello")
  public String hello(HttpEntity<String> str){
      System.out.println(str);
  	return "";
  }
  ```

### 1.4  乱码解决方法

* **请求乱码**

  * **GET请求**：修改tomcat的server.xml，在8080端口处加入  URIEncoding=UTF-8

  * **POST请求**：原理是设置过滤器解决

    * **xml配置文件方式**

      ```xml
      <filter>
          <filter-name>CharacterEncodingFilter</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter
          </filter-class>
          <init-param>
          	<param-name>encoding</param-name>
              <param-value>UTF-8</param-value>
          </init-param>
          <init-param>
              <param-name>forceRequestEncoding</param-name>
              <param-value>true</param-value>
          </init-param>
          <init-param>
              <param-name>forceResponseEncoding</param-name>
              <param-value>true</param-value>
          </init-param>
      </filter>
      <filter-mapping>
      	<filter-name>CharacterEncodingFilter</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
      ```

    * **springboot配置文件方式**

      ```properties
      spring.http.encoding.force=true
      spring.http.encoding.charset=UTF-8
      spring.http.encoding.enabled=true
      server.tomcat.uri-encoding=UTF-8
      ```

* **响应乱码**

  ```java
  response.setContentType("text/html;charset=utf-8");
  ```

### 1.5  REST风格

* **REST**：即Representaional  State  Transfer。（资源）表现层状态转化，目前最流行的一种互联网软件架  

  构。它结构清晰、符合标准、易于理解、扩展方便。

* **资源**（Resources）：网络上的一个实体，或者说是网络上的一个具体信息。它可以使一段文本、一张图片、  

  一首歌曲、一种服务，总之就是一个具体的存在。可以用一个URI（统一资源定位符）指向它，每种资源对应  

  一个特定的URI。要获取这个资源，访问它的URI就可以，因此URO即为每一个资源的独一无二的标识符

* **表现层**（Representation）：把资源具体呈现出来的形式，叫做它的表现层。比如，文本可以使用txt格式表  

  现，也可以用HTML格式、XML格式、JSON格式，甚至可以采用二进制格式

* **状态转化**（State Transfer）：每发出一个请求，就代表了客户端和服务器的一次交互过程。HTTP协议，是  

  一个无状态协议，即所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，  

  让服务器端发生“状态转化”。而这种转化是建立在表现层之上的，所以就是“表现层状态转化”。具体说，就是  

  HTTP协议里面，4个代表操作方式的动词：GET、POST、PUT、DELETE。他们分别对应四种基本操作

## 第二章  数据输出

### 2.1 传入Map、Model、ModelMap

* **传入Map**：对应的值只存在request域中

  ```java
  @RequestMapping("/helloworld")
  public String helloworld(Map<String, Object> map){
      map.put("msg", "你好");
      return "success";
  }
  ```

* **传入Model**：对应的值只存在request域中

  ```java
  @RequestMapping("/helloworld")
  public String helloworld(Model model){
      model.addAttribute("msg", "你好坏");
      return "success";
  }
  ```

* **传入ModelMap**：对应的值只存在request域中

  ```java
  @RequestMapping("/helloworld")
  public String helloworld(ModelMap modelMap){
      modelMap.addAttribute("msg", "你好棒");
      return "success";
  }
  ```

* **三者关系**：无论是Map、Model还是ModelMap，最终都是BindingAwareModelMap在工作，相当于给  

  BindingAwareModelMap中保存的东西都会被保存在请求域中

  ​					Map(interface(jdk))											Model(interface(spring))

  ​								||														            //							

  ​								||																 //

  ​							     \/															   //	

  ​						ModelMap(class)										  //

  ​									\\\\													   //

  ​										\\\\												//	

  ​													ExtendedModelMap

  ​																	||

  ​																	 \/

  ​													BindingAwareModelMap

### 2.2 返回值是ModelAndView

* 既包含**视图信息**（页面地址）也包含**模型数据**（页面数据），数据也放在请求域中

  ```java
  @RequestMapping("/helloworld")
  public ModelAndView helloWorld(){
      ModelAndView mv = new ModelAndView();
      Employee employee = new Employee(1, "xianCan", 1, "xianCan@qq.com", 1);
      mv.addObject("employee", employee);
      mv.setViewName("success");
      return mv;
  }
  ```

### 2.3 将数据存到Session中

* **@SessionAttributes**：若希望在多个请求之间共用某个模型属性数据，将模型中的某个属性暂存  

  到HttpSession中，一边多个请求之间可以共享这个属性

  ```java
  @RestController
  //这个注解只能放在类上面，可通过指定属性名放到会话中的属性外，也可以通过指定对象类型
  @SessionAttributes(value = {"employee"}, types = {String.class})
  public class SpringMVCTest {
  
      @RequestMapping("/helloworld")
      public String helloWorld(Map<String, Object> map){
          Employee employee = new Employee(1, "xianCan", 1, "xianCan@qq.com", 1);
          map.put("employee", employee);
          map.put("name", "caizw");
          return "";
      }
  }
  ```

### 2.4 @ModelAttribute

* **@ModelAttribute**：方法入参标注该注解后，入参的对象就会放到数据模型中，有点像拦截器。如果某  

  个方法被@ModelAttribute注解修饰了，会先执行被@ModelAttribute注解修饰的方法，再执行被  

  @RequestMapping注解修饰的方法，而且@ModelAttribute注解修饰的方法的参数值可以传递到  

  @RequestMapping注解修饰的方法

  ```java
  @RestController
  public class SpringMVCTest {
  
      @ModelAttribute
      public void getModelAttribute(@RequestParam(value = "id", required = false)Integer id, Map<String, Object> map){
          //模拟从数据库拿数据
          Employee employee = new Employee(1, "xianCan", 1, "xianCan@qq.com", 1);
          map.put("employee", employee);
  
      }
  
      @RequestMapping("/helloworld")
      //上述方法的employee可以传递到本方法的employee形参
      public String helloWorld(Employee employee){
          System.out.println(employee);
          return "";
      }
  }
  ```

### 2.5  返回值是字符串

* **返回字符串可以指定逻辑视图名，通过视图解析器解析为物理视图地址**

  ```java
  //指定逻辑视图名，经过视图解析器解析为jsp，物理路径为resources下的success.jsp
  @RequestMapping("/helloworld")
  public String helloWorld(Model model){
      System.out.println(model);
      return "success";
  }
  ```

### 2.6  返回值为void

* **一般需要自己跳转页面**

  ```java
  @RequestMapping("/helloworld")
  public void helloWorld(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      //跳转
      request.getRequestDispatcher("/success.jsp").forward(request, response);
      //重定向
      response.sendRedirect("/success.jsp");
  }
  ```

### 2.7  @ResponseBody响应json数据

* 加入**@ResponseBody**或者**@RestController**注解后，会自动把返回值转为json字符串，并且不再使用视  

  图解析器，单纯的返回数据

### 2.8  ResponseEntity用于自定义响应

* **可用于自定义返回响应体，响应头和响应状态码等**

  ```java
  @RequestMapping("/handle03")
  public ResponseEntity<Employee> handle05(){
      //状态码
      HttpStatus statusCode = HttpStatus.OK;
      //响应头
      MultiValueMap<String, String> headers = new HttpHeaders();
      headers.add("Set-Cookie", "username=hahaha");
      //响应体
      Employee employee = new Employee();
      ResponseEntity<Employee> entity = new ResponseEntity<>(employee, headers, statusCode);
      return entity;
  }
  ```

### 2.9  SpringMVC实现文件下载

* **主要定制响应头**

  * 1、指定响应头MIME类型：ContentType
  * 2、指定接收程序处理的方式（打开方式）：Content-Disposition

  ```java
  //指定响应头类型：ContentType
  //如果知道下载的类型，可以写死，也可以动态获取
  //response.setContentType("application/x-msdownload;charset=utf8");
  //response.setContentType("application/force-download;charset=utf8");
  response.setContentType("application/octet-stream;charset=utf8");
  
  //指定响应头打开方式：Content-Disposition
  //告诉浏览器以附件的形式打开
  response.setHeader( "Content-Disposition","attachment;filename=" + fileName);
  ```

* **动态获取文件的MIME类型，利用jdk1.7的nio包**

  ```java
  public static String getContentType(String filename){
      String type = null;
      Path path = Paths.get(filename);
      try {
          type = Files.probeContentType(path);
      } catch (IOException e){
          e.printStackTrace();
      }
      return type;
  }
  ```

### 2.10  SpringMVC实现文件上传

* **文件上传的必要前提**

  * form表单的enctype取值必须是**multipart/form-data**，enctype是表单请求时正文的类型，enctype的  

    默认值是**application/x-www-form-urlencoded**

  * method属性取值必须是POST

  * 提供一个文件选择域<input type="file" />

* **文件上传的原理分析**

  * 当form表单的enctype不是默认值后，request.getParameter()将失效，enctype的取值是默认值时，表  

    单的正文内容是：key=value&key=value&key=value...

  * 当form表单的enctype取值是Multipart/form-data时，请求正文内容将变成每一部分都是MIME类型描述  

    的正文

    --------------------------------------------------------7de1a433602ac				分隔符

    Content-Disposition:form-data;name="userName"						协议头

    aaa																											协议的正文

    --------------------------------------------------------7de1a433602ac

    Content-Disposition:form-data;name=file;filename="C:\a.txt"

    Content-Type:text/plain																		协议的类型（MIME类型）

    bbbbbbbbbbbbbbbbbbbbbbbbb

    --------------------------------------------------------7de1a433602ac--

* **引入第三方组件实现文件上传**

  ```xml
  <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.1</version>
  </dependency>
  <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.4</version>
  </dependency>
  ```

* **SpringMVC上传文件**

  * SpringMVC框架提供了MultipartFile对象，该对象表示上传的文件，要求变量名称必须和表单file标签的  

     name属性名称相同

    ```java
    @RequestMapping("/upload")
    public String upload(@RequestParam("file") MultipartFile file){
        //文件项的name，就是前端指定的那个name
        //String name = file.getName();
        //获取原始文件名
        String fileName = file.getOriginalFilename();
        //获取文件后缀
        String suffixName = fileName.substring(fileName.lastIndexOf("."));
        //文件保存路径
        String filePath = "H:/upload/";
        //文件重命名，防止重复
        fileName = filePath + UUID.randomUUID() + fileName;
        //文件对象
        File dest = new File(fileName);
        //判断路径是否存在，如果不存在则创建
        if (!dest.getParentFile().exists()){
            dest.getParentFile().mkdirs();
        }
    	file.transferTo(dest);
        return "fail";
    }
    ```
  
* **多文件上传**

  * 直接将MultipartFile改为数组即可

    ```java
    @RequestMapping("/upload")
    public String upload(@RequestParam("file") MultipartFile[] files){
        for(MultipartFile file : files){
            if(!file.isEmpty()){
                ...
            }
        }
        return "fail";
    }
    ```

## 第三章  DispatcherServlet结构与源码分析

### 3.1  DispatcherServlet结构分析

![](./01.png)

### 3.2  请求处理的大致流程

* 所有请求过来DispatcherServlet收到请求
* 调用doDispatch()方法进行处理
  * **getHandler()**：根据当前请求地址找到能处理这个请求的目标处理器类（处理器）
    * **根据当前请求在HandlerMapping中找到这个请求的映射信息，获取目标处理器类**
  * **getHandlerAdapter()**：根据当前处理器类获取到能执行这个处理器方法的适配器（适配器）
    * **根据当前处理器类，找到当前类的HandlerAdapter（适配器）**
  * 使用刚才获取到的适配器（AnnotationMethodHandlerAdapter）执行目标方法
  * 目标方法执行后会返回一个ModelAndView对象
  * 根据ModelAndView的信息转发到具体的页面，并可以再请求域中取出ModelAndView中的模型数据

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
        HttpServletRequest processedRequest = request;
        HandlerExecutionChain mappedHandler = null;
        boolean multipartRequestParsed = false;
        WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

        try {
            try {
                ModelAndView mv = null;
                Object dispatchException = null;

                try {
                    //1.检查是否文件上传请求
                    processedRequest = this.checkMultipart(request);
                    multipartRequestParsed = processedRequest != request;
                    //2.根据当前的请求地址找到哪个controller类来处理请求
                    mappedHandler = this.getHandler(processedRequest);
                    //3.如果没有找到对应的controller，则抛出404异常
                    if (mappedHandler == null) {
                        this.noHandlerFound(processedRequest, response);
                        return;
                    }
					//4.拿到能执行这个类的所有方法的适配器（反射工具）
                    HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
                    String method = request.getMethod();
                    boolean isGet = "GET".equals(method);
                    if (isGet || "HEAD".equals(method)) {
                        long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                        if ((new ServletWebRequest(request, response)).checkNotModified(lastModified) && isGet) {
                            return;
                        }
                    }
					//拦截器的preHandle执行位置，有一个拦截器返回false目标方法以后都不会执行；直接跳到afterCompletion
                    if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                        return;
                    }
                    //控制器（Controller），处理器（Handler）
					//5.适配器来执行目标方法，将目标方法执行完成后的返回值作为视图名，设置保存到
                    //ModelAndView中
                    //目标方法的返回值无论是什么，最终适配器执行完成后都会将结果封装到ModelAndView
                    mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
                    if (asyncManager.isConcurrentHandlingStarted()) {
                        return;
                    }

                    this.applyDefaultViewName(processedRequest, mv);
                    //目标方法只要正常就会执行postHandle
                    mappedHandler.applyPostHandle(processedRequest, response, mv);
                    //出现异常就会直接渲染视图
                } catch (Exception var20) {
                    dispatchException = var20;
                } catch (Throwable var21) {
                    dispatchException = new NestedServletException("Handler dispatch failed", var21);
                }
				//6.根据方法最终执行完成后封装的ModelAndView，转发到对应页面，并把ModelAndView中的数据可以从请求域中获取
                this.processDispatchResult(processedRequest, response, mappedHandler, mv, (Exception)dispatchException);
            } catch (Exception var22) {
                this.triggerAfterCompletion(processedRequest, response, mappedHandler, var22);
            } catch (Throwable var23) {
                this.triggerAfterCompletion(processedRequest, response, mappedHandler, new NestedServletException("Handler processing failed", var23));
            }

        } finally {
            if (asyncManager.isConcurrentHandlingStarted()) {
                if (mappedHandler != null) {
                    mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
                }
            } else if (multipartRequestParsed) {
                this.cleanupMultipart(processedRequest);
            }

        }
    }

//preHandle拦截器的方法，顺序执行
boolean applyPreHandle(HttpServletRequest request, HttpServletResponse response) throws Exception {
        HandlerInterceptor[] interceptors = this.getInterceptors();
        if (!ObjectUtils.isEmpty(interceptors)) {
            for(int i = 0; i < interceptors.length; this.interceptorIndex = i++) {
                HandlerInterceptor interceptor = interceptors[i];
                if (!interceptor.preHandle(request, response, this.handler)) {
                    this.triggerAfterCompletion(request, response, (Exception)null);
                    return false;
                }
            }
        }

        return true;
    }

//postHandle执行方法，倒序执行
void applyPostHandle(HttpServletRequest request, HttpServletResponse response, @Nullable ModelAndView mv) throws Exception {
        HandlerInterceptor[] interceptors = this.getInterceptors();
        if (!ObjectUtils.isEmpty(interceptors)) {
            for(int i = interceptors.length - 1; i >= 0; --i) {
                HandlerInterceptor interceptor = interceptors[i];
                interceptor.postHandle(request, response, this.handler, mv);
            }
        }

    }
```

### 3.3  getHandler()获取对应的处理器

* getHandler()会返回目标处理器的执行链

* handlerMap：ioc容器启动创建Controller对象的时候扫描每个处理器都能处理什么请求，保存在  

  HandlerMapping的handlerMap属性中，下一次请求过来，就来看哪个HandlerMapping中有这个请求映射  

  信息就行了

  ```java
  mappedHandler = this.getHandler(processedRequest);
  
  @Nullable
  protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
      if (this.handlerMappings != null) {
          Iterator var2 = this.handlerMappings.iterator();
  
          while(var2.hasNext()) {
              HandlerMapping mapping = (HandlerMapping)var2.next();
              HandlerExecutionChain handler = mapping.getHandler(request);
              if (handler != null) {
                  return handler;
              }
          }
      }
  
      return null;
  }
  ```

### 3.4  getHandlerAdapter()获取对应的适配器

* 最终会返回一个RequestMappingHandlerAdapter

  ```java
  protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
      if (this.handlerAdapters != null) {
          Iterator var2 = this.handlerAdapters.iterator();
  
          while(var2.hasNext()) {
              HandlerAdapter adapter = (HandlerAdapter)var2.next();
              if (adapter.supports(handler)) {
                  return adapter;
              }
          }
      }
  
      throw new ServletException("No adapter for handler [" + handler + "]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
  }
  ```

### 3.5  handle()执行目标方法

* 1、首先，方法执行的第一个入口在DispatcherServlet类中doDispatch()方法中

  ```java
  HandlerAdapter ha;...
  mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
  ```

* 2、接着，HandlerAdapter的handle方法由其抽象子类AbstractHandlerMethodAdapter进行重写。然后调  

  类中方法handleInternal()方法

  ```java
  @Nullable
  public final ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
      return this.handleInternal(request, response, (HandlerMethod)handler);
  }
  
  @Nullable
  protected abstract ModelAndView handleInternal(HttpServletRequest var1, HttpServletResponse var2, HandlerMethod var3) throws Exception;
  ```

* 3、然后，抽象方法handleInternal由AbstractHandlerMethodAdapter的子类  

  RequestMappingHandlerAdapter进行重写，进行关键方法invokeHandlerMethod()

  ```java
  protected ModelAndView handleInternal(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {
      this.checkRequest(request);
      ModelAndView mav;
      //根据需要是否进行同步操作
      if (this.synchronizeOnSession) {
          HttpSession session = request.getSession(false);
          if (session != null) {
              Object mutex = WebUtils.getSessionMutex(session);
              synchronized(mutex) {
                  //核心重点：执行方法
                  mav = this.invokeHandlerMethod(request, response, handlerMethod);
              }
          } else {
              //核心重点：执行方法
              mav = this.invokeHandlerMethod(request, response, handlerMethod);
          }
      } else {
          //核心重点：执行方法
          mav = this.invokeHandlerMethod(request, response, handlerMethod);
      }
  
      if (!response.containsHeader("Cache-Control")) {
          if (this.getSessionAttributesHandler(handlerMethod).hasSessionAttributes()) {
              this.applyCacheSeconds(response, this.cacheSecondsForSessionAttributeHandlers);
          } else {
              this.prepareResponse(response);
          }
      }
  
      return mav;
  }
  ```

* 4、执行处理器方法：核心方法

  ```java
  @Nullable
  protected ModelAndView invokeHandlerMethod(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {
      ServletWebRequest webRequest = new ServletWebRequest(request, response);
  
      ModelAndView var15;
      try {
          //创建@InitBinder注解方法的工厂类，进行缓存
          WebDataBinderFactory binderFactory = this.getDataBinderFactory(handlerMethod);
          //创建@ModelAttribute @ControlAdvice注解方法工厂并缓存
          ModelFactory modelFactory = this.getModelFactory(handlerMethod, binderFactory);
          ServletInvocableHandlerMethod invocableMethod = this.createInvocableHandlerMethod(handlerMethod);
          if (this.argumentResolvers != null) {
              invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
          }
  
          if (this.returnValueHandlers != null) {
              invocableMethod.setHandlerMethodReturnValueHandlers(this.returnValueHandlers);
          }
  
          invocableMethod.setDataBinderFactory(binderFactory);
          invocableMethod.setParameterNameDiscoverer(this.parameterNameDiscoverer);
          //创建结果容器并初始化一些参数
          ModelAndViewContainer mavContainer = new ModelAndViewContainer();
          mavContainer.addAllAttributes(RequestContextUtils.getInputFlashMap(request));
          //执行@ModelAttribute注解的方法，将结果放到结果容器中
          modelFactory.initModel(webRequest, mavContainer, invocableMethod);
         mavContainer.setIgnoreDefaultModelOnRedirect(this.ignoreDefaultModelOnRedirect);
          //一些异步的操作
          AsyncWebRequest asyncWebRequest = WebAsyncUtils.createAsyncWebRequest(request, response);
          asyncWebRequest.setTimeout(this.asyncRequestTimeout);
          WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
          asyncManager.setTaskExecutor(this.taskExecutor);
          asyncManager.setAsyncWebRequest(asyncWebRequest);
          asyncManager.registerCallableInterceptors(this.callableInterceptors);
          asyncManager.registerDeferredResultInterceptors(this.deferredResultInterceptors);
          Object result;
          if (asyncManager.hasConcurrentResult()) {
              result = asyncManager.getConcurrentResult();
              mavContainer = (ModelAndViewContainer)asyncManager.getConcurrentResultContext()[0];
              asyncManager.clearConcurrentResult();
              LogFormatUtils.traceDebug(this.logger, (traceOn) -> {
                  String formatted = LogFormatUtils.formatValue(result, !traceOn.booleanValue());
                  return "Resume with async result [" + formatted + "]";
              });
              invocableMethod = invocableMethod.wrapConcurrentResult(result);
          }
  		//继续执行方法
          invocableMethod.invokeAndHandle(webRequest, mavContainer, new Object[0]);
          if (asyncManager.isConcurrentHandlingStarted()) {
              result = null;
              return (ModelAndView)result;
          }
  		//返回值
          var15 = this.getModelAndView(mavContainer, modelFactory, webRequest);
      } finally {
          webRequest.requestCompleted();
      }
  
      return var15;
  }
  ```

* 5、invokeAndHandle方法

  ```java
  public void invokeAndHandle(ServletWebRequest webRequest, ModelAndViewContainer mavContainer, Object... providedArgs) throws Exception {
      //执行方法，获取返回值
      Object returnValue = this.invokeForRequest(webRequest, mavContainer, providedArgs);
      this.setResponseStatus(webRequest);
      if (returnValue == null) {
          if (this.isRequestNotModified(webRequest) || this.getResponseStatus() != null || mavContainer.isRequestHandled()) {
              this.disableContentCachingIfNecessary(webRequest);
              mavContainer.setRequestHandled(true);
              return;
          }
      } else if (StringUtils.hasText(this.getResponseStatusReason())) {
          mavContainer.setRequestHandled(true);
          return;
      }
  
      mavContainer.setRequestHandled(false);
      Assert.state(this.returnValueHandlers != null, "No return value handlers");
  
      try {
          //处理返回值，封装结果集
          this.returnValueHandlers.handleReturnValue(returnValue, this.getReturnValueType(returnValue), mavContainer, webRequest);
      } catch (Exception var6) {
          if (this.logger.isTraceEnabled()) {
              this.logger.trace(this.formatErrorForReturnValue(returnValue), var6);
          }
  
          throw var6;
      }
  }
  ```

* 6、invokeForRequest方法

  ```java
  @Nullable
  public Object invokeForRequest(NativeWebRequest request, @Nullable ModelAndViewContainer mavContainer, Object... providedArgs) throws Exception {
      //处理参数
      Object[] args = this.getMethodArgumentValues(request, mavContainer, providedArgs);
      if (this.logger.isTraceEnabled()) {
          this.logger.trace("Arguments: " + Arrays.toString(args));
      }
  	//反射执行方法
      return this.doInvoke(args);
  }
  ```

* 7、getMethodArgumentValues方法处理参数

  ```java
  protected Object[] getMethodArgumentValues(NativeWebRequest request, @Nullable ModelAndViewContainer mavContainer, Object... providedArgs) throws Exception {
      MethodParameter[] parameters = this.getMethodParameters();
      if (ObjectUtils.isEmpty(parameters)) {
          return EMPTY_ARGS;
      } else {
          Object[] args = new Object[parameters.length];
  		//遍历方法的所有参数
          for(int i = 0; i < parameters.length; ++i) {
              MethodParameter parameter = parameters[i];
              parameter.initParameterNameDiscovery(this.parameterNameDiscoverer);
              //检查当前参数类型是否已经提供了参数值
              args[i] = findProvidedArgument(parameter, providedArgs);
              if (args[i] == null) {
                  //这块是比那里预置的参数解析器，就是前面说的责任链模式
                  if (!this.resolvers.supportsParameter(parameter)) {
                      throw new IllegalStateException(formatArgumentError(parameter, "No suitable resolver"));
                  }
  
                  try {
                      //由找到的参数解析器，来解析参数
                      args[i] = this.resolvers.resolveArgument(parameter, mavContainer, request, this.dataBinderFactory);
                  } catch (Exception var10) {
                      if (this.logger.isDebugEnabled()) {
                          String exMsg = var10.getMessage();
                          if (exMsg != null && !exMsg.contains(parameter.getExecutable().toGenericString())) {
                              this.logger.debug(formatArgumentError(parameter, exMsg));
                          }
                      }
  
                      throw var10;
                  }
              }
          }
  
          return args;
      }
  }
  ```

  ```java
  @Nullable
  protected static Object findProvidedArgument(MethodParameter parameter, @Nullable Object... providedArgs) {
      if (!ObjectUtils.isEmpty(providedArgs)) {
          Object[] var2 = providedArgs;
          int var3 = providedArgs.length;
  
          for(int var4 = 0; var4 < var3; ++var4) {
              Object providedArg = var2[var4];
              if (parameter.getParameterType().isInstance(providedArg)) {
                  return providedArg;
              }
          }
      }
  
      return null;
  }
  ```

* 8、resolvers.resolveArgument()找到对应的参数解析器来解析参数

  ```java
  @Nullable
  public Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer, NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
      //根据 MethodParameter获取不同的参数解析器
      HandlerMethodArgumentResolver resolver = this.getArgumentResolver(parameter);
      if (resolver == null) {
          throw new IllegalArgumentException("Unsupported parameter type [" + parameter.getParameterType().getName() + "]. supportsParameter should be called first.");
      } else {
          //数据绑定，这里就不再深入
          return resolver.resolveArgument(parameter, mavContainer, webRequest, binderFactory);
      }
  }
  ```

* 9、确定自定义类型参数的值，如自定义的POJO类。在ModelAttributeMethodProcessor类中处理

  ```java
  @Nullable
  public final Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer, NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {
      Assert.state(mavContainer != null, "ModelAttributeMethodProcessor requires ModelAndViewContainer");
      Assert.state(binderFactory != null, "ModelAttributeMethodProcessor requires WebDataBinderFactory");
      String name = ModelFactory.getNameForParameter(parameter);
      ModelAttribute ann = (ModelAttribute)parameter.getParameterAnnotation(ModelAttribute.class);
      if (ann != null) {
          mavContainer.setBinding(name, ann.binding());
      }
  
      Object attribute = null;
      BindingResult bindingResult = null;
      if (mavContainer.containsAttribute(name)) {
          attribute = mavContainer.getModel().get(name);
      } else {
          try {
              attribute = this.createAttribute(name, parameter, binderFactory, webRequest);
          } catch (BindException var10) {
              if (this.isBindExceptionRequired(parameter)) {
                  throw var10;
              }
  
              if (parameter.getParameterType() == Optional.class) {
                  attribute = Optional.empty();
              }
  
              bindingResult = var10.getBindingResult();
          }
      }
  
      if (bindingResult == null) {
          //获绑定器
          WebDataBinder binder = binderFactory.createBinder(webRequest, attribute, name);
          if (binder.getTarget() != null) {
              if (!mavContainer.isBindingDisabled(name)) {
                  this.bindRequestParameters(binder, webRequest);
              }
  
              this.validateIfApplicable(binder, parameter);
              if (binder.getBindingResult().hasErrors() && this.isBindExceptionRequired(binder, parameter)) {
                  throw new BindException(binder.getBindingResult());
              }
          }
  
          if (!parameter.getParameterType().isInstance(attribute)) {
              attribute = binder.convertIfNecessary(binder.getTarget(), parameter.getParameterType(), parameter);
          }
  
          bindingResult = binder.getBindingResult();
      }
  
      Map<String, Object> bindingResultModel = bindingResult.getModel();
      mavContainer.removeAttributes(bindingResultModel);
      mavContainer.addAllAttributes(bindingResultModel);
      return attribute;
  }
  ```
  
* 10、doInvoke()方法

  ```java
  @Nullable
  protected Object doInvoke(Object... args) throws Exception {
      ReflectionUtils.makeAccessible(this.getBridgedMethod());
  
      try {
          return this.getBridgedMethod().invoke(this.getBean(), args);
      } catch (IllegalArgumentException var4) {
          this.assertTargetBean(this.getBridgedMethod(), this.getBean(), args);
          String text = var4.getMessage() != null ? var4.getMessage() : "Illegal argument";
          throw new IllegalStateException(this.formatInvokeError(text, args), var4);
      } catch (InvocationTargetException var5) {
          Throwable targetException = var5.getTargetException();
          if (targetException instanceof RuntimeException) {
              throw (RuntimeException)targetException;
          } else if (targetException instanceof Error) {
              throw (Error)targetException;
          } else if (targetException instanceof Exception) {
              throw (Exception)targetException;
          } else {
              throw new IllegalStateException(this.formatInvokeError("Invocation failure", args), targetException);
          }
      }
  }
  ```

### 3.6  SpringMVC的九大组件

```java
/*文件上传解析器*/
private MultipartResolver multipartResolver;

/*区域信息解析器，和国际化有关*/
private LocaleResolver localeResolver;

/*主题解析器，强大的主题效果更换，少人用*/
private ThemeResolver themeResolver;

/*Handler映射信息*/
private List<HandlerMapping> handlerMappings;

/*Handler的适配器*/
private List<HandlerAdapter> handlerAdapters;

/*SpringMVC强大的异常解析功能：异常解析器*/
private List<HandlerExceptionResolver> handlerExceptionResolvers;

/*视图转换器*/
private RequestToViewNameTranslator viewNameTranslator;

/*SpringMVC中运行重定向携带数据的功能*/
private FlashMapManager flashMapManager;

/*视图解析器*/
private List<ViewResolver> viewResolvers;
```

### 3.7  九大组件初始化

* **在DispatcherServlet类中的onRefresh()方法中进行初始化**

  ```java
  protected void onRefresh(ApplicationContext context) {
      this.initStrategies(context);
  }
  
  protected void initStrategies(ApplicationContext context) {
      this.initMultipartResolver(context);
      this.initLocaleResolver(context);
      this.initThemeResolver(context);
      this.initHandlerMappings(context);
      this.initHandlerAdapters(context);
      this.initHandlerExceptionResolvers(context);
      this.initRequestToViewNameTranslator(context);
      this.initViewResolvers(context);
      this.initFlashMapManager(context);
  }
  ```

* **组件的初始化**

  * 有些组件在容器中是使用类型找的，有些组件是使用id找的
  * 去容器中找到这个组件，如果没有找到就是用默认的配置

* **以initHandlerMappings为例**

  ```java
  private void initHandlerMappings(ApplicationContext context) {
      this.handlerMappings = null;
      if (this.detectAllHandlerMappings) {
          Map<String, HandlerMapping> matchingBeans = BeanFactoryUtils.beansOfTypeIncludingAncestors(context, HandlerMapping.class, true, false);
          if (!matchingBeans.isEmpty()) {
              this.handlerMappings = new ArrayList(matchingBeans.values());
              AnnotationAwareOrderComparator.sort(this.handlerMappings);
          }
      } else {
          try {
              //根据id获取对应的组件
              HandlerMapping hm = (HandlerMapping)context.getBean("handlerMapping", HandlerMapping.class);
              this.handlerMappings = Collections.singletonList(hm);
          } catch (NoSuchBeanDefinitionException var3) {
              ;
          }
      }
  
      if (this.handlerMappings == null) {
          //如果获取不到，则使用默认的策略
          this.handlerMappings = this.getDefaultStrategies(context, HandlerMapping.class);
          if (this.logger.isTraceEnabled()) {
              this.logger.trace("No HandlerMappings declared for servlet '" + this.getServletName() + "': using default strategies from DispatcherServlet.properties");
          }
      }
  }
  ```

## 第四章  视图解析器

### 4.1  forward前缀指定一个转发操作

* forward转发一个页面：不经过视图解析器

  * /hello.jsp：转发当前项目下的hello
  * 前缀的转发，不会由我们配置的视图解析器拼串

  ```java
  @RequestMapping("/handle01")
  public String handle01(){
      return "forward:/hello.jsp";
  }
  
  @RequestMapping("/handle02")
  public String handle02(){
      //跳转到handle01
      return "forward:/handle01";
  }
  ```

### 4.2  redirect前缀指定重定向到页面

* redirect重定向：不经过视图解析器

  ```java
  @RequestMapping("/handle03")
  public String handle03(){
      return "redirect:/hello.jsp";
  }
  ```

### 4.3  视图解析流程

* 1、在ha.handle方法执行后，会得到ModelAndView对象，里面包含view视图对象

* 2、然后调用this.processDispatchResult(processedRequest, response, mappedHandler, mv,   

  (Exception)dispatchException)方法进行页面渲染

  ```java
  private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv, @Nullable Exception exception) throws Exception {
      boolean errorView = false;
      if (exception != null) {
          if (exception instanceof ModelAndViewDefiningException) {
              this.logger.debug("ModelAndViewDefiningException encountered", exception);
              mv = ((ModelAndViewDefiningException)exception).getModelAndView();
          } else {
              Object handler = mappedHandler != null ? mappedHandler.getHandler() : null;
              mv = this.processHandlerException(request, response, handler, exception);
              errorView = mv != null;
          }
      }
  
      if (mv != null && !mv.wasCleared()) {
          //渲染页面
          this.render(mv, request, response);
          if (errorView) {
              WebUtils.clearErrorRequestAttributes(request);
          }
      } else if (this.logger.isTraceEnabled()) {
          this.logger.trace("No view rendering, null ModelAndView returned.");
      }
  
      if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
          if (mappedHandler != null) {
              mappedHandler.triggerAfterCompletion(request, response, (Exception)null);
          }
  
      }
  }
  ```

* 3、调用render(mv, request, response)方法渲染页面

  ```java
  protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
          Locale locale = this.localeResolver != null ? this.localeResolver.resolveLocale(request) : request.getLocale();
          response.setLocale(locale);
          String viewName = mv.getViewName();
          View view;
          if (viewName != null) {
              //获取view对象
              view = this.resolveViewName(viewName, mv.getModelInternal(), locale, request);
              if (view == null) {
                  throw new ServletException("Could not resolve view with name '" + mv.getViewName() + "' in servlet with name '" + this.getServletName() + "'");
              }
          } else {
              view = mv.getView();
              if (view == null) {
                  throw new ServletException("ModelAndView [" + mv + "] neither contains a view name nor a View object in servlet with name '" + this.getServletName() + "'");
              }
          }
  
          if (this.logger.isTraceEnabled()) {
              this.logger.trace("Rendering view [" + view + "] ");
          }
  
          try {
              if (mv.getStatus() != null) {
                  response.setStatus(mv.getStatus().value());
              }
  			//9、调用view对象的render方法
              view.render(mv.getModelInternal(), request, response);
          } catch (Exception var8) {
              if (this.logger.isDebugEnabled()) {
                  this.logger.debug("Error rendering view [" + view + "]", var8);
              }
  
              throw var8;
          }
      }
  ```

* 4、调用resolveViewName获取View对象

  ```java
  @Nullable
  protected View resolveViewName(String viewName, @Nullable Map<String, Object> model, Locale locale, HttpServletRequest request) throws Exception {
      if (this.viewResolvers != null) {
          Iterator var5 = this.viewResolvers.iterator();
  
          while(var5.hasNext()) {
              //获取对应的view处理器
              ViewResolver viewResolver = (ViewResolver)var5.next();
              //根据对应的view处理器获取对应的view对象
              View view = viewResolver.resolveViewName(viewName, locale);
              if (view != null) {
                  return view;
              }
          }
      }
  
      return null;
  }
  ```

* 5、ViewResolver是个接口，有多个实现类，一般以AbstractCachingViewResolver类中的方法进行处理

  ```java
  @Nullable
  public View resolveViewName(String viewName, Locale locale) throws Exception {
      if (!this.isCache()) {
          return this.createView(viewName, locale);
      } else {
          //判断有没有缓存
          Object cacheKey = this.getCacheKey(viewName, locale);
          View view = (View)this.viewAccessCache.get(cacheKey);
          //没有缓存则重新创建
          if (view == null) {
              Map var5 = this.viewCreationCache;
              synchronized(this.viewCreationCache) {
                  view = (View)this.viewCreationCache.get(cacheKey);
                  if (view == null) {
                      //创建view对象
                      view = this.createView(viewName, locale);
                      if (view == null && this.cacheUnresolved) {
                          view = UNRESOLVED_VIEW;
                      }
  
                      if (view != null) {
                          this.viewAccessCache.put(cacheKey, view);
                          this.viewCreationCache.put(cacheKey, view);
                      }
                  }
              }
          } else if (this.logger.isTraceEnabled()) {
              this.logger.trace(formatKey(cacheKey) + "served from cache");
          }
  
          return view != UNRESOLVED_VIEW ? view : null;
      }
  }
  ```

* 6、创建View对象，以UrlBasedViewResolver为例

  ```java
  protected View createView(String viewName, Locale locale) throws Exception {
      if (!this.canHandle(viewName, locale)) {
          return null;
      } else {
          String forwardUrl;
          //redirect另外处理
          if (viewName.startsWith("redirect:")) {
              forwardUrl = viewName.substring("redirect:".length());
              RedirectView view = new RedirectView(forwardUrl, this.isRedirectContextRelative(), this.isRedirectHttp10Compatible());
              String[] hosts = this.getRedirectHosts();
              if (hosts != null) {
                  view.setHosts(hosts);
              }
  
              return this.applyLifecycleMethods("redirect:", view);
          //forward另外处理
          } else if (viewName.startsWith("forward:")) {
              forwardUrl = viewName.substring("forward:".length());
              InternalResourceView view = new InternalResourceView(forwardUrl);
              return this.applyLifecycleMethods("forward:", view);
          } else {
              //调用父类的createView方法
              return super.createView(viewName, locale);
          }
      }
  }
  ```

* 7、当子类没有实现或者调用父类AbstractCachingViewResolver方法时，统一走以下方法

  ```java
  @Nullable
  protected View createView(String viewName, Locale locale) throws Exception {
      return this.loadView(viewName, locale);
  }
  
  @Nullable
  protected abstract View loadView(String var1, Locale var2) throws Exception;
  ```

* 8、然后调用子类的loadView方法，以UrlBasedViewResolver为例

  ```java
  protected View loadView(String viewName, Locale locale) throws Exception {
      AbstractUrlBasedView view = this.buildView(viewName);
      View result = this.applyLifecycleMethods(viewName, view);
      return view.checkResource(locale) ? result : null;
  }
  
  protected AbstractUrlBasedView buildView(String viewName) throws Exception {
      Class<?> viewClass = this.getViewClass();
      Assert.state(viewClass != null, "No view class");
      AbstractUrlBasedView view = (AbstractUrlBasedView)BeanUtils.instantiateClass(viewClass);
      view.setUrl(this.getPrefix() + viewName + this.getSuffix());
      String contentType = this.getContentType();
      if (contentType != null) {
          view.setContentType(contentType);
      }
  
      view.setRequestContextAttribute(this.getRequestContextAttribute());
      view.setAttributesMap(this.getAttributesMap());
      Boolean exposePathVariables = this.getExposePathVariables();
      if (exposePathVariables != null) {
          view.setExposePathVariables(exposePathVariables.booleanValue());
      }
  
      Boolean exposeContextBeansAsAttributes = this.getExposeContextBeansAsAttributes();
      if (exposeContextBeansAsAttributes != null) {
          view.setExposeContextBeansAsAttributes(exposeContextBeansAsAttributes.booleanValue());
      }
  
      String[] exposedContextBeanNames = this.getExposedContextBeanNames();
      if (exposedContextBeanNames != null) {
          view.setExposedContextBeanNames(exposedContextBeanNames);
      }
  
      return view;
  }
  ```

* 9、经过第8步后能得到View对象，然后就执行第3步中提到的view.render()方法。View是一个接口，它的  

  render()方法由AbstractView实现

  ```java
  public void render(@Nullable Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
      if (this.logger.isDebugEnabled()) {
          this.logger.debug("View " + this.formatViewName() + ", model " + (model != null ? model : Collections.emptyMap()) + (this.staticAttributes.isEmpty() ? "" : ", static attributes " + this.staticAttributes));
      }
  
      Map<String, Object> mergedModel = this.createMergedOutputModel(model, request, response);
      this.prepareResponse(request, response);
      //render方法的核心，经过这步后，视图渲染工作就完成了
      this.renderMergedOutputModel(mergedModel, this.getRequestToExpose(request), response);
  }
  ```

* 10、renderMergedOutputModel是一个抽象方法，InternalResourceView对其进行了实现

  ```java
  protected void renderMergedOutputModel(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
      //将模型中的数据放在请求域中
      this.exposeModelAsRequestAttributes(model, request);
      this.exposeHelpers(request);
      //获取转发的路径
      String dispatcherPath = this.prepareForRendering(request, response);
      //获取对应的转发器
      RequestDispatcher rd = this.getRequestDispatcher(request, dispatcherPath);
      if (rd == null) {
          throw new ServletException("Could not get RequestDispatcher for [" + this.getUrl() + "]: Check that the corresponding file exists within your web application archive!");
      } else {
          if (this.useInclude(request, response)) {
              response.setContentType(this.getContentType());
              if (this.logger.isDebugEnabled()) {
                  this.logger.debug("Including [" + this.getUrl() + "]");
              }
  
              rd.include(request, response);
          } else {
              if (this.logger.isDebugEnabled()) {
                  this.logger.debug("Forwarding to [" + this.getUrl() + "]");
              }
  			//进行转发操作
              rd.forward(request, response);
          }
  
      }
  }
  ```

* **11、总结**

  * **视图解析器只是为了得到视图对象**

  * **视图对象才能真正地转发（将模型数据全部放在请求域中）或者重定向到页面。视图对象才能真正的**  

    **渲染视图**

### 4.4  时序图

![](./02.png)

### 4.5  常用的视图实现类

| 大类     | 视图类型                     | 说明                                                         |
| -------- | ---------------------------- | ------------------------------------------------------------ |
| URL视图  | InternalResourceView         | 将JSP或其他资源封装成一个视图，是InternalResourceViewResolver默认使用的视图实现类 |
|          | JstlView                     | 如果JSP文件中刚使用了JSTL国际化标签功能，则需要使用该视图类  |
| 文档视图 | AbstractExcelView            | Excel文档视图的抽象类。该视图基于POI构造Excel文档            |
|          | AbstractPdfView              | PDF文档视图的抽象类，该视图基于iText构造PDF文档              |
| 报表视图 | ConfigurableJsperReportsView | 几个使用JasperReports报表技术的视图                          |
|          | JsperReportsCsvView          |                                                              |
|          | JsperReportsMultiFormatView  |                                                              |
|          | JasperReportsHtmlView        |                                                              |
|          | JasperReportsPdfView         |                                                              |
|          | JasperReportsXlsView         |                                                              |
| JSON视图 | MappingJacksonJsonView       | 将模型数据通过Jackson开源框架的ObjectMapper以JSON方式输出    |

### 4.6  常用的视图解析器实现类

| 大类             | 视图类型                     | 说明                                                         |
| ---------------- | ---------------------------- | ------------------------------------------------------------ |
| 解析为Bean的名字 | BeanNameViewResolver         | 将逻辑视图名解析为一个Bean，Bean的id等于逻辑视图名           |
| 解析为URL文件    | InternalResourceViewResolver | 将视图名解析为一个URL文件，一般使用该解析器将视图名映射为一个保存在WEB-INF目录下的程序文件（如JSP） |
|                  | JasperReportsViewResolver    | JasperReports是一个基于Java的开源报表工具，该解析器将视图名解析为报表文件对应的URL |
| 模板文件视图     | FreeMarkerViewResolver       | 解析为基于FreeMarker模板技术的模板文件                       |
|                  | VeloityViewResolver          | 解析为基于Velocity模板技术的模板文件                         |
|                  | VelocityLayoutViewResolver   |                                                              |

## 第五章  数据绑定&数据格式化&数据校验

### 5.1  数据绑定的问题

* 1、数据绑定期间的数据类型转换？String-Integer String-Boolean
* 2、数据绑定期间的数据格式化问题？比如日期进行转换
* 3、数据校验？提交的数据必须是合法的

### 5.2  数据绑定流程

* 1、SpringMVC主框架将ServletRequest对象及目标方法的入参实例传递给WebDataBinderFactory实例，以  

  创建**DataBinder**实例对象

* 2、DataBinder调用装配在SpringMVC上下文的**ConversionService**组件进行**数据类型转换、数据格式化**工  

  作。将Servlet中的请求信息填充到入参对象中

* 3、调用**Validator**组件对已经绑定了请求信息的入参对象进行合法性校验，并最终生成数据绑定结果  

  **BindingData**对象

* 4、SpringMVC抽取**BindingResult**中的入参对象和校验错误对象，将它们赋给处理方法的响应入参

### 5.3  数据绑定流程图示

* SPringMVC通过反射机制对目标处理方法进行解析，将请求消息绑定到处理方法的入参中。数据绑定的核心  

  部件**DataBinder**，运行机制如下：

  ![](./03.png)

### 5.4  数据绑定-规定格式

* 可以在POJO中使用@DataTimeFormat进行绑定日期。使用@NumberFormat进行绑定浮点数

  ```java
  @DataTimeFormat(pattern="yyyy-MM-dd HH:mm:ss")
  private Date date;
  
  @NumberFormat(pattern="#，###，###.##")
  private Double salary;
  ```

### 5.5  数据校验

* SpringMVC为我们提供了JSR 303对JavaBean数据合法性校验提供标准框架，它包含在JavaEE 6.0中

* JSR 303通过在JavaBean属性上标志注解来进行规则校验

  | 注解                       | 功能说明                               |
  | -------------------------- | -------------------------------------- |
  | @Null                      | 元素必须为null                         |
  | @NotNull                   | 元素必须不为null                       |
  | @AssertTrue                | 元素必须为true                         |
  | @AssertFalse               | 元素必须为false                        |
  | @Min(value)                | 元素必须为数字，且大于等于指定的最小值 |
  | @Max(value)                | 元素必须为数字，且小于等于指定的最大值 |
  | @DecimalMin(value)         | 元素必须为数字，且大于等于指定的最小值 |
  | @DecimalMax(value)         | 元素必须为数字，且大于等于指定的最小值 |
  | @Size(max, min)            | 元素的大小必须在指定的范围内           |
  | @Digits(integer, fraction) | 必须是一个数字，其值必须在范围内       |
  | @Past                      | 元素必须是一个过去的日期               |
  | @Future                    | 元素必须是一个未来的日期               |
  | @Pattern(value)            | 元素必须是一个符合规定的正则表达式     |

* 示例

  ```java
  public class Employee{
      //不能为空，message为错误信息
      @NotNull(mseeage="不能为空")
  	private String lastName;
  }
  
  @RequestMapping("/helloworld")
  //@Valid注解启用JavaBean中的校验规则，后面紧跟BindingResult可以获取校验结果
  public String helloworld(@Valid Employee employee, BindingResult result){
      boolean hasErrors = result.hasErrors();
      if(hasErrors){
          List<FieldError> errors = result.getFieldErrors();
          ...
      }
      return "";
  }
  ```

## 第六章  拦截器

### 6.1  拦截器

* 允许运行目标方法之前进行一些拦截工作，或者目标方法之后进行一些其他处理
  * javaWeb：Filter
  * SpringMVC：HandlerInterceptor
* SpringMVC的拦截器都实现了**HandlerInterceptor**接口
  * **HandlerInterceptor**
    * preHandle：在目标方法运行之前调用。根据返回的boolean值确定要不要执行目标方法
    * postHandle：在目标方法运行之后调用
    * afterCompletion：在请求整个完成之后，来到目标页面之后调用

### 6.2  自定义拦截器

* 1、实现HandlerInterceptor接口

  ```java
  @Component
  public class MyInterceptor implements HandlerInterceptor {
  
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          System.out.println("自定义拦截器...");
          return true;
      }
  
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
  
      }
  
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
  
      }
  }
  ```

* 2.1、注册拦截器（java配置方式，推荐）

  ```java
  @Configuration
  public class MyMvcConfig extends WebMvcConfigurationSupport {
  
      @Autowired
      private MyInterceptor myInterceptor;
  
      @Override
      protected void addInterceptors(InterceptorRegistry registry) {
          //"/usr/login"  拦截/usr/login的请求
          //全拦截
          registry.addInterceptor(myInterceptor).addPathPatterns("/**");
      }
  }
  ```

* 2.2、注册拦截器（xml方式）

  ```xml
  <mvc:interceptors>
      <mvc:interceptor>
          <mvc:mapping path="/**"/>
      	<bean class="com.xianCan.springboot.config.MyInterceptor"></bean>
      </mvc:interceptor>
  </mvc:interceptors>
  ```

### 6.3  运行流程

* **单个拦截器正常运行流程**
  * 1、拦截器的preHandle
  * 2、目标方法
  * 3、拦截器postHandle
  * 4、页面
  * 5、拦截器的afterCompletion
* **单个拦截器异常流程**
  * 1、只要preHandle不放行就没有以后的流程
  * 2、只要放行了，都会执行afterCompletion
* **多个拦截器正常运行流程**
  * 和过滤器一样：先1pre，2pre，然后2post，1post
  * 拦截器的执行顺序按照注册的顺序
* **多个拦截器的异常流程**
  * postHandle：哪一个不放行，都没有postHandle
  * afterCompletion：哪一个不放行，它前面的**已经放行的拦截器的afterCompletion都会执行**

### 6.4  拦截器与过滤器

* 1、拦截器基于过滤器，但比过滤器强大。能进行prehandle、postHandle、afterCompletion
* 2、拦截器能交给Spring容器进行管理，能注入其它组件
* 3、简单功能用过滤器（如修改字符编码），其它复杂功能用拦截器

## 第七章  异常处理

### 7.1  三个异常处理器

* **ExceptionHandlerExceptionResolver**：处理@ExceptionHandler注解的异常
* **ResponseStatusExceptionResolver**：处理@ResponseStatus
* **DefaultHandlerExceptionResolver**：判断是否SpringMVC自带的异常
* **SimpleMappingExceptionResolver**：通过配置的方式进行异常处理

### 7.2  @ExceptionHandler

* 处理本类中的异常

  ```java
  /**
   * 告诉springMVC这个方法专门用来处理本类中所有的异常
   * 方法形参上随便写一个Exception，用来接收发生的异常
   * @return
   */
  @ExceptionHandler(value = Exception.class)
  public String handleException(Exception exception){
      
      return "myError";
  }
  ```

* 将方法抽取成通用类

  ```java
  /**
   * @author xianCan
   * @date 2020/4/8 23:32
   * 
   * 集中处理所有异常
   * 1、集中处理所有异常的类加入到ioc容器中
   * 2、@ControllerAdvice专门处理异常的类
   */
  @ControllerAdvice
  public class MyExceptionHandler {
  
      @ExceptionHandler(value = Exception.class)
      public ModelAndView handleException(Exception exception){
          ModelAndView mv = new ModelAndView("myError");
          mv.addObject(exception);
          return mv;
      }
  }
  ```

* **1、给方法上随便写一个Exception，用来接收发生的异常**

* **2、要携带异常信息不能给参数位置写Model，返回值写ModelAndView即可**

* **3、如果有多个@ExceptionHandler都能处理整个一场，精确优先**

* **4、全局异常处理与本类同时存在，本类优先**

### 7.3  @ResponseStatus

* 该注解可用于自定义异常

  ```java
  /**
   * @author xianCan
   * @date 2020/4/9 0:13
   */
  @ResponseStatus(reason = "自定义异常", value = HttpStatus.NOT_ACCEPTABLE)
  public class MyException extends RuntimeException {
  
  }
  ```

## 第八章  SpringMVC运行流程

### 8.1  文字描述

* **1、前端控制器（DispatcherServlet）收到请求，调用doDispatch进行处理**
* **2、根据HandlerMapping中保存的请求映射信息，找到处理当前请求的处理器执行链（包含拦截器）**
* **3、根据当前处理器找到对应HandlerAdapter（适配器）**
* **4、拦截器的preHandle先执行**
* **5、适配器执行目标方法**
  * 1）ModelAttribute注解标注的方法提前运行
  * 2）执行目标方法的时候（确定目标方法用到的参数）
* **6、拦截器的postHandle执行**
* **7、处理结果（页面渲染流程）**
  * 1）如果有异常使用异常解析器处理异常，处理完之后还是会返回ModelAndView
  * 2）调用render进行页面渲染
    * 1）视图解析器根据视图名得到视图对象
    * 2）视图对象调用render方法
* **8、执行拦截器的afterCompletion**

### 8.2  流程图

![](./04.png)

## 第九章  常用注解对比

|                     注解                     |                             备注                             |
| :------------------------------------------: | :----------------------------------------------------------: |
|                   @Service                   |                           bean注册                           |
|                  @Component                  |                           bean注册                           |
| @Autowired、@Resource、@Autowired+@Qualifier | @Resource=@Autowired+@Qualifier。如果接口实现只有一个，那么用@Autowired就可以了，也不需要指定名字。如果接口有多个实现，那么用@Resource并指定name（建议）。或者使用@Autowired+@Qualifier的value值 |
|             @Configuration+@Bean             |                           ben注册                            |
|                   @Values                    |                      从配置文件中取参数                      |

