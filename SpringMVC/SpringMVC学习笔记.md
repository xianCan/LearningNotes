# SpringMVC学习笔记

## 第一章  请求

### 1.1  @RequestMapping

* @RequestMapping：用于简历请求URL和处理请求方法之间的对应关系

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

### 1.2  @PathVariable 映射URL绑定的占位符

* 带占位符的URL是Spring3.0新增的功能，该功能在SpringMVC向REST目标挺进发展过程中具有里程碑的意义  

* 通过@PathVariable可以将URL中占位符参数绑定到控制器处理方法的入参中：URL中的{xxx}占位符可以通过

  @PathVariable("xxx")绑定到操作方法的入参中

  ```java
  @RequestMapping(/delete/{id})
  public String delete(@PathVariable("id")Integer id){
      userService.delete(id);
      return "";
  }
  ```

### 1.3  REST

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

### 1.4  请求处理参数类

* SpringMVC通过分析处理方法的签名，将HTTP请求信息绑定到处理方法的相应入参中

* SpringMVC对控制器处理方法签名的限制是很宽松，几乎可以按喜欢的任何方式对方法进行签名

* 必要时可以对方法及方法入参标注相应的注解、  SpringMVC框架会将HTTP请求的信息绑定到相应的方法入  

  参中，并根据方法的返回值类型作出相应的后续

* **@PathVariable**：从请求路径获取参数

* **@RequestParam**：将请求参数中的某个参数注入到指定属性中，可以使用required指定是否必须的

  ```java
  @RequestMapping("/hello")
  public String hello(@RequestParam(value="userName", required=false)String userName,
  @RequestParam("age")int age){
      System.out.println(userName + "---" + age)
  	return "";
  }
  ```

* **@RequestHeader**：将请求头中的某个属性值注入到指定属性中

  ```java
  @RequestMapping("/hello2")
  public String hello(@RequestHeader(value="Accept-Language")String language){
      System.out.println(language)
  	return "";
  }
  ```

* **@CookieValue**：可让处理方式入参绑定某个Cookie值

  ```java
  @RequestMapping("/hello3")
  public String hello(@CookieValue("JSESSIONID")String jsessionId){
      System.out.println(jsessionId)
  	return "";
  }
  ```

* **使用Servlet原生API**

  * **HttpServletRequest**
  * **HttpServletResponse**
  * **HttpSession**
  * **java.security.Principal**
  * **Locale**
  * **InpusStream**
  * **OutputStream**
  * **Reader**
  * **Writer**

### 1.5  处理模型数据

* SpringMVC提供了以上几种途径输出模型数据

  * **ModelAndView**：处理方法返回值类型为ModelAndView时，方法体即可通过该对象添加模型数据；  

    其中可以包含视图和模型信息，SpringMVC会把ModelAndView的model中数据放入到request域中

  * **Map 及 Model**：入参为org.springframework.ui.Model、org.springframework.ui.ModelMap或  

    java.util.Map时，处理方法返回时，Map中的数据会自动添加到模型中

  * **@SessionAttributes**：将模型中的某个属性暂存到HttpSession中，一边多个请求之间可以共享这  

    个属性

  * **@ModelAttribute**：方法入参标注该注解后，入参的对象就会放到数据模型中

