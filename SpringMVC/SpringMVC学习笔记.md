# SpringMVC学习笔记

## 第一章  请求与参数

### 1.1  @RequestMapping 请求映射

* @RequestMapping：用于建立请求URL和处理请求方法之间的对应关系

* 在控制器的**类定义**及**方法定义**处都可以标注

  * 类定义处：提供初步的请求映射信息。相当于WEB应用的根目录

  * 方发处：提供进一步的细分映射信息。相当于类定义出的URL。若类定义处未标注@RequestMapping，  

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

* @RequestMapping除了可以使用请求URL映射请求外，还可以使用请求方法、请求参数及请求头映射请求

* @RequestMapping的**value**、**method**、**params**及**heads**分别表示请求URL、请求方法、请求参数及请求头  

  的映射条件，他们之间是与的关系，联合使用多个条件可让请求映射更加精确化

* params和headers支持简单的表达式：

  * param1：表示请求必须包含名为param1的请求参数

  * !param1：表示请求不能包含名为param1的请求参数

  * param1 != value1：表示请求包含名为param1的请求参数，但其值不能为value1

  * {"param1=value1", "param2"}：请求必须包含名为param1和param2的两个请求参数，且param1参数  

    的值必须为value1

* @RequestMapping支持Ant通配符映射

  * ?：问号匹配文件名中的一个字符
  * *：匹配文件名中的任意字符
  * **：匹配多层路径

### 1.2  获取Servlet原生API

* **直接在controller层的方法形参上加上下面的参数即可**

  * **HttpServletRequest**

  * **HttpServletResponse**

  * **HttpSession**

  * **java.security.Principal**

  * **Locale**

  * **InpusStream**

  * **OutputStream**

  * **Reader**

  * **Writer**

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

* **@RequestBody**：用于获取请求体内容，直接使用得到的是k-v&k-v...结构的数据，不能用于get方法

  * required：是否必须有请求体，默认值true

    ```java
    @RequestMapping("/helloworld")
    public String helloWorld(@RequestBody Employee employee){
        System.out.println(employee);
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

* **@RequestHeader**：用于获取请求头信息

  ```java
  @RequestMapping("/hello2")
  public String hello(@RequestHeader(value="Accept-Language")String language){
      System.out.println(language)
  	return "";
  }
  ```

* **@CookieValue**：获得Cookie的值

  ```java
  @RequestMapping("/hello3")
  public String hello(@CookieValue("JSESSIONID")String jsessionId){
      System.out.println(jsessionId)
  	return "";
  }
  ```

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

### 1.4  REST风格

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

## 第二章  响应数据和结果返回值

### 2.1  返回值是字符串

* 返回字符串可以指定逻辑视图名，通过视图解析器解析为物理视图地址

  ```java
  //指定逻辑视图名，经过视图解析器解析为jsp，物理路径为resources下的success.jsp
  @RequestMapping("/helloworld")
  public String helloWorld(Model model){
      System.out.println(model);
      return "success";
  }
  ```

### 2.2  返回值为void

* 一般需要自己跳转页面

  ```java
  @RequestMapping("/helloworld")
  public void helloWorld(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      //跳转
      request.getRequestDispatcher("/success.jsp").forward(request, response);
      //重定向
      response.sendRedirect("/success.jsp");
  }
  ```

### 2.3  返回值是ModelAndView

* ModelAndView对象是SpringMVC提供的一个对象，可以用来调整具体的JSP视图

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

### 2.4  使用forward和redirect进行页面跳转

* 使用关键字的方法进行转发

  ```java
  @RequestMapping("/helloworld")
  public String helloWorld(){
      //请求的转发
      return "forward:/success.jsp";
  }
  ```

* 使用关键字的方法进行重定向

  ```java
  @RequestMapping("/helloworld")
  public String helloWorld(){
      //重定向
      return "redirect:/success.jsp";
  }
  ```

### 2.5  @ResponseBody响应json数据

* 加入@ResponseBody或者@RestController注解后，会自动把返回值转为json字符串，并且不再使用视图解  

  析器，单纯的返回数据

### 2.6  SpringMVC实现文件上传

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

  * SpringMVC框架提供了MultipartFile对象，该对象表示上传的文件，要求变量名称必须和表单file标签  

     的name属性名称相同

    ```java
    @RequestMapping("/upload")
    public String upload(@RequestParam("file") MultipartFile file){
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

## 第三章  常用注解对比

|                     注解                     |                             备注                             |
| :------------------------------------------: | :----------------------------------------------------------: |
|                   @Service                   |                           bean注册                           |
|                  @Component                  |                           bean注册                           |
| @Autowired、@Resource、@Autowired+@Qualifier | @Resource=@Autowired+@Qualifier。如果接口实现只有一个，那么用@Autowired就可以了，也不需要指定名字。如果接口有多个实现，那么用@Resource并指定name（建议）。或者使用@Autowired+@Qualifier的value值 |
|             @Configuration+@Bean             |                           ben注册                            |
|                   @Values                    |                      从配置文件中取参数                      |

