**分享概要：**

1. **springboot介绍**
2. **快速构建及部署**
3. **简单web项目**
4. **自动配置原理**
5. **第三方插件引入**
6. **启动流程解析**
7. **自定义启动器starter**

## **一、springboot介绍**
---
### 微服务：
>微服务架构是一种架构模式（用于服务管理微服务的），它把一组小的服务互相协调、互相配合，并且
 完成功能。每个服务运行在其独立的进程中，服务与服务间采用轻量级的通信机制互相协作（通常是基
 于HTTP 协议的RESTfulAPI ）。每个服务都围绕着具体业务进行构建，并且能够被独立的部署到生产环
 境、类生产环境等。另外，应当尽量避免统一的、集中式的服务管理机制，对具体的一个服务而言，应
 根据业务上下文，选择合适的语言、工具对其进行构建。

>目前而已，对于微服务业界没有一个统一的标准定义,但是通常而言提倡把一个单一的应用程序划分为一组小
  的服务，每个小的服务都会运行在自己的进程中，服务之间通过轻量级的通信机制（http的rest api）进行通信,那么
  一个个的小服务就是微服务。
[具体请看martin fowler的文章](https://martinfowler.com/articles/microservices.html#MicroservicesAndSoa)

单体架构到微服务架构的转变如下： 
 ![图片](https://github.com/Andachui/img-storage/blob/master/%E5%8D%95%E4%BD%93%E6%9E%B6%E6%9E%84%E5%88%B0%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E7%9A%84%E8%BD%AC%E5%8F%98.PNG?raw=true)

微服务的优势:
1. 优点每个服务足够内聚，足够小，代码容易理解这样能聚焦一个指定的业务功能或业务需求(职责单一)
2. 开发简单、开发效率提高，一个服务可能就是专一的只干一件事,微服务能够被小团队单独开发，这
   个小团队是 2 到 5 人的开发人员组成。
3. 微服务能使用不同的语言开发。
4. 易于和第三方集成，微服务允许容易且灵活的方式集成自动部署，通过持续集成工具，如Jenkins,Hudson,bamboo
5. 微服务只是业务逻辑的代码，不会和 HTML,CSS或其他界面组件混合
6. 每个微服务都有自己的存储能力，可以有自己的数据库。也可以有统一数据库。

### 微服务开发利器-springboot：
>Spring Boot来简化Spring应用开发，约定大于配置，去繁从简，just run就能创建一个独立的，产品级别的应用

 ![图片](https://github.com/Andachui/img-storage/blob/master/springboot%E4%BB%8B%E7%BB%8D.PNG?raw=true)
 
“Spring全家桶”时代。Spring Boot-J2EE一站式解决方案。Spring Cloud-分布式整体解决方案

优势:
1. 快速创建独立运行的Spring项目以及与主流框架集成
2. 使用嵌入式的Servlet容器，应用无需打成WAR包
3. starters自动依赖与版本控制
4. 大量的自动配置，简化开发，也可修改默认值
5. 无需配置XML，无代码生成，开箱即用
6. 准生产环境的运行时应用监控
7. 与云计算的天然集成

![图片](https://github.com/Andachui/img-storage/blob/master/spring%E5%85%A8%E5%AE%B6%E6%A1%B6.PNG?raw=true)
## **二、快速构建及部署**
### **1.基于maven版本构建**

1.先把maven的配置文件设置为如下配置
```
<profile>
    <id>jdk‐1.8</id>
    <activation>
    <activeByDefault>true</activeByDefault>
    <jdk>1.8</jdk>
    </activation>
    <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```
2.配置IDE的环境（maven配置）

![图片](https://github.com/Andachui/img-storage/blob/master/%E8%AE%BE%E7%BD%AEmaven%E8%B7%AF%E5%BE%84.PNG?raw=true)

3.创建一个空的maven工程，然后导入springboot相关的jar包

```
<!--父工程依赖 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.8.RELEASE</version>
    </parent>
    spring mvc-web的依赖
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    <!-- 引入一个spring boot插件，可以支持我们将web应用程序打成可运行jar包 -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build> 
```

4.编写主入口程序
```
/**
* Created by anqi 
*/
@SpringBootApplication
public class MavenStartMain {
    public static void main(String[] args) {
        SpringApplication.run(MavenStartMain.class,args);
    }
}
```

5.业务代码（必须写在主程序所在包及其子包下!）
```
/**
* Created by anqi 
*/
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String sayHello() {
        return "hello";
    }
}

```

6.启动程序，测试

  默认端口为8080，或者执行mvn package将项目打成jar包，用java -jar XXX.jar直接运行

### **2.idea创建springboot项目**
  new → module → spring initializer → 选取自己使用的组件（一般选取spring web） → 业务代码（规则同maven项目） → 启动

### **3.项目简单快速入门**

pom解析:

版本仲裁中心管理Spring Boot应用里面的所有依赖版本，它已经包括我们使用的大多数组件版本版本号。如果没有引入，我们可以自己手动引入依赖。
spring-boot-starter：spring-boot场景启动器；帮我们导入了web模块正常运行所依赖的组件
```
    <!-- 版本仲裁中心 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <!-- 引入一个spring boot插件，可以支持我们将web应用程序打成可运行jar包 -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```
主程序类:

  @SpringBootApplication 来标注一个主程序类，SpringBoot就根据个类的main方法来启动SpringBoot应用

resources文件夹中目录结构:
```
static：保存所有的静态资源； js css images；
templates：保存所有的模板页面；（Spring Boot默认jar包使用嵌入式的Tomcat，默认不支持JSP页面）；可以使用模板引擎（freemarker、thymeleaf）；
application.properties/application.yml：Spring Boot应用的配置文件；可以修改一些默认设置
```

环境配置文件切换（以.yml配置举例）:

application.yml (用于激活不同环境的配置文件)
```
spring:
    profiles:
        active: dev
```
application-dev.yml
```
server:
    port: 8081
```
application-prod.yml
```
server:
    port: 8082
```
或者以上配置文件都写在application.yml中，每种环境都是一个代码块，然后指定激活某个环境，但并不常有。
```
spring:
    profiles:
        active: prod  #指定属于哪个环境
‐‐‐
server:
    port: 8081
spring:
    profiles: dev
‐‐‐
server:
    port: 8082
spring:
    profiles: prod 

```

### **4.部署及启动参数设置**
#### **jar包部署**
指定打包名称
```
<build>
    <!--指定打包的文件名称-->
    <finalName>quickStartSpringboot</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```
当有多个主启动类时指定
```
<build>
     <plugins>
          <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.dachui.quickstart.QuickStartApplication</mainClass>
                </configuration>
                <goals>
                    <goal>repackage</goal>
                </goals>
          </plugin>
     </plugins>
</build>
```
#### **war包部署**
第一步:创建一个war项目（利用idea创建好目录结构）

目录结构web项目，引入springboot需要的的依赖

第二步:将嵌入式的Tomcat指定为provided

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring‐boot‐starter‐tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```

第三步:必须编写一个SpringBootServletInitializer的子类，并调用configure方法

![图片](https://github.com/Andachui/img-storage/blob/master/war%E5%8C%85%E5%90%AF%E5%8A%A8%E4%B8%BB%E7%A8%8B%E5%BA%8F.PNG?raw=true)

第四步:打成war包,放在tomcat上运行

#### **启动时参数配置**

application.yml中的配置参数可以在启动时指定，且优先级高于配置文件中的

```
java -jar quickStartSpringboot.jar
```

![图片](https://github.com/Andachui/img-storage/blob/master/jar%E5%8C%85%E5%90%AF%E5%8A%A8.PNG?raw=true)

直接在application.yml的配置文件中使用 spring.profiles.active=dev|prod|test


```
java -jar quickStartSpringboot.jar --spring.profiles.active=dev
```

覆盖配置端口

```
java -jar quickStartSpringboot.jar --server.port=8888
```

指定jvm参数

```
java -Xms128m -Xmx128m -jar quickStartSpringboot.jar
jps   //查看进行
jinfo 进程号    //查看jvm启动参数
```

## **三、简单web项目**
### **1.静态资源的映射规则**

```
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
    CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
    //wenjars映射
    if (!registry.hasMappingForPattern("/webjars/**")) {
        customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/")
                .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
    }
    String staticPathPattern = this.mvcProperties.getStaticPathPattern();
    //静态资源文件夹映射
    if (!registry.hasMappingForPattern(staticPathPattern)) {
        customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
                .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
                .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
    }
}        
```

"/**" 访问当前项目的任何资源，都去（静态资源的文件夹）找映射。

![图片](https://github.com/Andachui/img-storage/blob/master/char3/%E9%9D%99%E6%80%81%E8%B5%84%E6%BA%90%E6%98%A0%E5%B0%84%E8%B7%AF%E5%BE%84.PNG?raw=true)

所有 /webjars/** ，都去 classpath:/META-INF/resources/webjars/ 找资源，依赖请去[webjars官网](http://www.webjars.org/)
找。
![图片](https://github.com/Andachui/img-storage/blob/master/char3/webjar.PNG?raw=true)

欢迎页； 静态资源文件夹下的所有index.html页面；被"/**"映射
```
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext) {
    WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
            new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
            this.mvcProperties.getStaticPathPattern());
    welcomePageHandlerMapping.setInterceptors(getInterceptors());
    return welcomePageHandlerMapping;
}

private Optional<Resource> getWelcomePage() {
    //找静态资源路径
    String[] locations = getResourceLocations(this.resourceProperties.getStaticLocations());
    return Arrays.stream(locations).map(this::getIndexHtml).filter(this::isReadable).findFirst();
}
```

所有的 **/favicon.ico 都是在静态资源文件下找；
```
@Bean
public SimpleUrlHandlerMapping faviconHandlerMapping() {
    SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
    mapping.setOrder(Ordered.HIGHEST_PRECEDENCE + 1);
    mapping.setUrlMap(Collections.singletonMap("**/favicon.ico", faviconRequestHandler()));
    return mapping;
}

@Bean
public ResourceHttpRequestHandler faviconRequestHandler() {
    ResourceHttpRequestHandler requestHandler = new ResourceHttpRequestHandler();
    //静态资源映射
    requestHandler.setLocations(resolveFaviconLocations());
    return requestHandler;
}
```


### **2.thymeleaf**

Thymeleaf是一款用于渲染XML/XHTML/HTML5内容的模板引擎。类似JSP，Velocity，FreeMaker等（内置tomcat容器不支持Jsp），它也可以轻易的与Spring MVC等Web框架进行集成
作为Web应用的模板引擎。与其它模板引擎相比，Thymeleaf最大的特点是能够直接在浏览器中打开并正确显示模板页面，而不需要启动整个Web应用。默认文件夹为classpath:/templates/。
具体使用请参考[官网文档](https://www.thymeleaf.org/documentation.html)，本文不做详解。

### **3.SpringMVC自动配置及修改**

[springboot官方文](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/htmlsingle/#boot-features-developing-web-applications)档描述了默认的web配置
```
Spring Boot provides auto-configuration for Spring MVC that works well with most applications.
The auto-configuration adds the following features on top of Spring’s defaults:
    Inclusion of ContentNegotiatingViewResolver and BeanNameViewResolver beans.
    //自动配置了ViewResolver（视图解析器：根据方法的返回值得到视图对象（View），视图对象决定如何渲染（转发及重定向））
    Support for serving static resources, including support for WebJars (covered later in this document)).
    Automatic registration of Converter, GenericConverter, and Formatter beans.
    //转换器和格式化器
    Support for HttpMessageConverters (covered later in this document).
    Automatic registration of MessageCodesResolver (covered later in this document).
    //定义错误代码生成规则
    Static index.html support.
    Custom Favicon support (covered later in this document).
    Automatic use of a ConfigurableWebBindingInitializer bean (covered later in this document)
```
以及如何定制web配置
```
If you want to keep Spring Boot MVC features and you want to add additional 
MVC configuration (interceptors, formatters, view controllers, and other features),
you can add your own @Configuration class of type WebMvcConfigurer but without @EnableWebMvc.
If you wish to provide custom instances of RequestMappingHandlerMapping, RequestMappingHandlerAdapter, 
or ExceptionHandlerExceptionResolver, you can declare a WebMvcRegistrationsAdapter instance to provide such components.
```
编写一个配置类（@Configuration），是WebMvcConfigurerAdapter类型；注意不能标注@EnableWebMvc。这样既保留了所有的自动配置，也能用我们扩展的配置

原理:

WebMvcAutoConfiguration是SpringMVC的自动配置类,在做其他自动配置时会导入；@Import(EnableWebMvcConfiguration.class)
```
@Configuration
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {

	private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();

	@Autowired(required = false)
	public void setConfigurers(List<WebMvcConfigurer> configurers) {
         //保存所有配置类，所有配置类都会生效
		if (!CollectionUtils.isEmpty(configurers)) {
			this.configurers.addWebMvcConfigurers(configurers);
		}
	}
}
```
容器中所有的WebMvcConfigurer都会一起起作用，我们的配置类也会在spring加载时被调用。


```
If you want to take complete control of Spring MVC, you can add your own @Configuration annotated with @EnableWebMvc.
```
springBoot对SpringMVC的自动配置不需要了，所有都是我们自己配置；所有的SpringMVC的自动配置都失效了
我们需要在配置类中添加@EnableWebMvc即可
```
//使用EnableWebMvc全面接管web配置
@EnableWebMvc
@Configuration
public class MyMvcConfig extends WebMvcConfigurerAdapter {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //浏览器发送 /sayHello 请求来到 hello
        registry.addViewController("/sayHello").setViewName("hello");
    }
}

```
具体原理请看web自动配置类
```
@Configuration
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
//当不存在WebMvcConfigurationSupport时自动配置类才会生效
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
		ValidationAutoConfiguration.class })
public class WebMvcAutoConfiguration {
```
而@EnableWebMvc会注入WebMvcConfigurationSupport的子类DelegatingWebMvcConfiguration
```
@Import(DelegatingWebMvcConfiguration.class)
public @interface EnableWebMvc {
```
而导入的WebMvcConfigurationSupport只是SpringMVC最基本的功能，自动配置类会失效

总结:

1）、SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean、@Component）如
果有就用用户配置的，如果没有，才自动配置；如果有些组件可以有多个（ViewResolver）将用户配置的和自己默
认的组合起来；

2）、在SpringBoot中会有非常多的xxxConfigurer帮助我们进行扩展配置

3）、在SpringBoot中会有很多的xxxCustomizer帮助我们进行定制配置



### **4.拦截器，过滤器，servlet扩展**

1）、拦截器
第一步创建拦截器
```
@Component
public class MyInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)throws Exception {
        System.out.println("我是MyInterceptor的preHandle方法");
        return true;
    }
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,@Nullable ModelAndView modelAndView) throws Exception {
        System.out.println("我是MyInterceptor的postHandle方法");
    }
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,@Nullable Exception ex) throws Exception {
        System.out.println("我是MyInterceptor的afterCompletion方法");
    }
}
```
第二步注册拦截器
```
@Configuration
public class MyConfig extends WebMvcConfigurerAdapter {
    @Autowired
    private MyInterceptor MygInterceptor;
    /**
     * 注册拦截器
     * @param registry
     */
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(MyInterceptor).addPathPatterns("/**").excludePathPatterns("/index.html","/");
    }
}
```

2）、过滤器
第一步创建过滤器
```
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("MyFilter的doFilter方法");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
    }
}
```
第二步注册过滤器
```
/**
 * 注册一个filter
 * @return
 */
@Bean
public FilterRegistrationBean MyFilter(){
    FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
    filterRegistrationBean.setFilter(new MyFilter());
    filterRegistrationBean.addUrlPatterns("/*");
    return filterRegistrationBean;
}
```

3）、servlet
第一步创建servlet
```
public class MyServlet extends HttpServlet {
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("hello......");
    }
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req,resp);
    }
}
```
第二步注册servlet
```
public class MyServlet extends HttpServlet {
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("hello......");
    }
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req,resp);
    }
}
```

### **5.错误页面定制**
浏览器请求服务报错页面
![图片](https://github.com/Andachui/img-storage/blob/master/char3/%E6%B5%8F%E8%A7%88%E5%99%A8%E6%8A%A5%E9%94%99.PNG?raw=true)

postman请求服务报错页面
![图片](https://github.com/Andachui/img-storage/blob/master/char3/postman%E6%8A%A5%E9%94%99.PNG?raw=true)

我们可以看出，不同的终端发送的请求 会返回不同的错误异常类容，springboot是根据不同客户端发送的请求的请求头来区分是 返回页面还是json数据
![图片](https://github.com/Andachui/img-storage/blob/master/char3/%E8%AF%B7%E6%B1%82%E5%A4%B4.PNG?raw=true)

原理:

第一步、启动阶段ErrorMvcAutoConfiguration自动注入了4个相关错误组件
```
@Bean
@ConditionalOnMissingBean(value = ErrorAttributes.class, search = SearchStrategy.CURRENT)
public DefaultErrorAttributes errorAttributes() {
    return new DefaultErrorAttributes(this.serverProperties.getError().isIncludeException());
}

@Bean
@ConditionalOnMissingBean(value = ErrorController.class, search = SearchStrategy.CURRENT)
public BasicErrorController basicErrorController(ErrorAttributes errorAttributes) {
    return new BasicErrorController(errorAttributes, this.serverProperties.getError(), this.errorViewResolvers);
}

@Bean
public ErrorPageCustomizer errorPageCustomizer() {
    return new ErrorPageCustomizer(this.serverProperties, this.dispatcherServletPath);
}

@Bean
public static PreserveErrorControllerTargetClassPostProcessor preserveErrorControllerTargetClassPostProcessor() {
    return new PreserveErrorControllerTargetClassPostProcessor();
}
```

第二步、发生错误时，ErrorPageCustomizer进行错误转发
```
@Override
public void registerErrorPages(ErrorPageRegistry errorPageRegistry) {
    //根据配置找转发的url
    ErrorPage errorPage = new ErrorPage(
            this.dispatcherServletPath.getRelativePath(this.properties.getError().getPath()));
    errorPageRegistry.addErrorPages(errorPage);
}
```
第三步、BasicErrorController接受错误，对html和postman这种错误分别处理
```
浏览器发送的请求来到这个方法处理
@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
    HttpStatus status = getStatus(request);
    Map<String, Object> model = Collections
            .unmodifiableMap(getErrorAttributes(request, isIncludeStackTrace(request, MediaType.TEXT_HTML)));
    response.setStatus(status.value());
    //去哪个页面作为错误页面；包含页面地址和页面内容
    ModelAndView modelAndView = resolveErrorView(request, response, status, model);
    return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
}

//产生json数据，其他客户端来到这个方法处理
@RequestMapping
public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
    Map<String, Object> body = getErrorAttributes(request, isIncludeStackTrace(request, MediaType.ALL));
    HttpStatus status = getStatus(request);
    return new ResponseEntity<>(body, status);
}
```
第四步、DefaultErrorAttributes定制错误信息
```
@Override
public Map<String, Object> getErrorAttributes(WebRequest webRequest, boolean includeStackTrace) {
    Map<String, Object> errorAttributes = new LinkedHashMap<>();
    errorAttributes.put("timestamp", new Date());
    addStatus(errorAttributes, webRequest);
    addErrorDetails(errorAttributes, webRequest, includeStackTrace);
    addPath(errorAttributes, webRequest);
    return errorAttributes;
}
```
第五步、DefaultErrorViewResolver解析错误码，找对应的错误页
```
@Override
public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
    //解析视图
    ModelAndView modelAndView = resolve(String.valueOf(status.value()), model);
    //没有对应的解析精确匹配的状态码 使用模糊匹配比如4XX 5XX
    if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
        //返回4XX 5XX的页面
        modelAndView = resolve(SERIES_VIEWS.get(status.series()), model);
    }
    return modelAndView;
}

private ModelAndView resolve(String viewName, Map<String, Object> model) {
    //相对路径  如error/404
    String errorViewName = "error/" + viewName;
    //模板引擎可以解析这个页面地址就用模板引擎解析
    TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName,
            this.applicationContext);
    if (provider != null) {
        //模板引擎可用的情况下返回到errorViewName指定的视图地址
        return new ModelAndView(errorViewName, model);
    }
    //模板引擎不可用，就在静态资源文件夹下找errorViewName对应的页面 error/404.html
    return resolveResource(errorViewName, model);
}
```
综上所述springboot错误自动配置生效的流程，我们可以自己定义错误页面和内容。首先将错误页面命名为“错误状态码.html”放在模板引擎文件夹里面的
error文件夹下，或者放在静态文件路径下，发生此状态码的错误就会来到对应的页面。如果想错误码通用匹配，我们可以使用4xx和5xx作为错误页面的文件名来匹配这种类型的所有错误，精确优先（优先寻找精确的状态码.html）

如果想定制错误信息可以继承DefaultErrorAttributes，对上述流程中的getErrorAttributes()方法进行重写
```
//给容器中加入我们自己定义的ErrorAttributes
@Component
public class MyErrorAttributes extends DefaultErrorAttributes {
    @Override
    public Map<String, Object> getErrorAttributes(RequestAttributes requestAttributes, boolean includeStackTrace) {
        Map<String, Object> map = super.getErrorAttributes(requestAttributes,includeStackTrace);
        map.put("name","andachui");
        return map;
    }
}
```

## **四、自动配置原理**

配置文件默认到底能写什么？怎么写？
[springboot官方文](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/htmlsingle/#common-application-properties)


### **1.Springboot常用注解**
| 常用注解        | 功能    | 
| --------   |: -----  |
| @SpringBootApplication        | Spring Boot应用标注在某个类上说明这个类是SpringBoot的主配置类      | 
| @Configuration       | 配置类上来标注这个注解，相当于一个xml配置文件，配置类也是容器中的一个组件（@Component）      |
| @SpringBootConfiguration        | 继承自@Configuration。SpringBoot的配置类，标注在某个类上，表示这是一个Spring Boot的配置类      |  
| @EnableAutoConfiguration        | 开启自动配置功能      |
| @AutoConfigurationPackage        | 自动配置包      |
| @Import        | Spring的底层注解@Import，给容器中导入一个组件|
| @Bean        | 价于XML中配置的bean      |
| @Conditional      | 判断是否满足当前指定条件      |
### **2.SpringBootApplication注解实现自动配置**

![图片](https://github.com/Andachui/img-storage/blob/master/char4/@SpringBootApplication.png?raw=true)

1)、主启动类注解@SpringBootApplication，开启了自动配置功能 @EnableAutoConfiguration

2)、@EnableAutoConfiguration作用

利用@Import(AutoConfigurationImportSelector.class)导入自动配置组件 
    
具体导入内容在AutoConfigurationImportSelector的selectImports()  
```
if (!isEnabled(annotationMetadata)) {
			return NO_IMPORTS;
		}
		AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader
				.loadMetadata(this.beanClassLoader);
		AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(autoConfigurationMetadata,
				annotationMetadata);
		return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
```
getAutoConfigurationEntry 
```
protected AutoConfigurationEntry getAutoConfigurationEntry(AutoConfigurationMetadata autoConfigurationMetadata,
			AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return EMPTY_ENTRY;
    }
    AnnotationAttributes attributes = getAttributes(annotationMetadata);
    //获取候选的配置类
    List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
    configurations = removeDuplicates(configurations);
    Set<String> exclusions = getExclusions(annotationMetadata, attributes);
    checkExcludedClasses(configurations, exclusions);
    //移除重复的
    configurations.removeAll(exclusions);
    configurations = filter(configurations, autoConfigurationMetadata);
    fireAutoConfigurationImportEvents(configurations, exclusions);
    return new AutoConfigurationEntry(configurations, exclusions);
}
```
getCandidateConfigurations
```
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
            getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
            + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```
loadFactoryNames
```
//加载配置类
public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
    String factoryClassName = factoryClass.getName();
    return loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
}
```
loadSpringFactories   
```
private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
    MultiValueMap<String, String> result = cache.get(classLoader);
    if (result != null) {
        return result;
    }

    try {
        //"META-INF/spring.factories" 去类路径下该文件中加载 EnableAutoConfiguration.class
        Enumeration<URL> urls = (classLoader != null ?
                classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
                ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
        result = new LinkedMultiValueMap<>();
        //遍历解析出来的集合
        while (urls.hasMoreElements()) {
            URL url = urls.nextElement();
            UrlResource resource = new UrlResource(url);
            //放在Properties中
            Properties properties = PropertiesLoaderUtils.loadProperties(resource);
            for (Map.Entry<?, ?> entry : properties.entrySet()) {
                String factoryClassName = ((String) entry.getKey()).trim();
                for (String factoryName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
                    result.add(factoryClassName, factoryName.trim());
                }
            }
        }
        cache.put(classLoader, result);
        return result;
    }
    catch (IOException ex) {
        throw new IllegalArgumentException("Unable to load factories from location [" +
                FACTORIES_RESOURCE_LOCATION + "]", ex);
    }
}
```

```
//自动配置加载路径
public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories"
```
每一个这样的xxxAutoConfiguration类都是容器中的一个组件，都加入到容器中，用他们来做自动配置。
![图片](https://github.com/Andachui/img-storage/blob/master/char4/%E8%87%AA%E5%8A%A8%E9%85%8D%E7%BD%AE.PNG?raw=true)

以HttpEncodingAutoConfiguration（Http编码自动配置）为例解释自动配置原理
```
@Configuration  //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件
@EnableConfigurationProperties(HttpProperties.class)
//启动HttpProperties的ConfigurationProperties功能；将配置文件中对应的值和HttpProperties绑定起来；并把HttpProperties加入到ioc容器中
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
//Spring底层@Conditional注解（Spring注解版），根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效； 判断当前应用是否是web应用，如果是，当前配置类生效
@ConditionalOnClass(CharacterEncodingFilter.class)
//判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)
//判断配置文件中是否存在某个配置 spring.http.encoding.enabled；如果不存在，判断也是成立的
//即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；
public class HttpEncodingAutoConfiguration {

	private final HttpProperties.Encoding properties;

    //只有一个有参构造器的情况下，参数的值就会从容器中拿
	public HttpEncodingAutoConfiguration(HttpProperties properties) {
		this.properties = properties.getEncoding();
	}

	@Bean   //给容器中添加一个组件，这个组件的某些值需要从properties中获取
	@ConditionalOnMissingBean  //判断容器没有这个组件
	public CharacterEncodingFilter characterEncodingFilter() {
		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
		filter.setEncoding(this.properties.getCharset().name());
		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
		return filter;
	}
```
一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties类中获取
的，这些类里面的每一个属性又是和配置文件绑定的.

所有在配置文件中能配置的属性都是在xxxxProperties类中封装者‘；配置文件能配置什么就可以参照某个功
能对应的这个属性类
```
@ConfigurationProperties(prefix = "spring.http") //获取用户的配置信息，进行赋值，没有的话走默认
public class HttpProperties {
```
总结：
1）、SpringBoot启动会加载大量的自动配置类

2）、我们看我们需要的功能有没有SpringBoot默认写好的自动配置类

3）、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）

4）、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这
些属性的值

## **五、第三方插件引入**
### **1.druid及监控**
1）、加入druid的依赖
```
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
//自定义属性绑定配置
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```
2）、配置druid的数据源属性

自动配置数据源DataSourceProperties属性不全，无法完全注入我们的配置，因此我们需要自己手动注入一个数据源。

```
@ConfigurationProperties(prefix = "spring.datasource.druid")
public class DruidDataSourceProperties {

    private String username;

    private String password;

    private String jdbcUrl;

    private String driverClassName;

    private Integer initialSize;

    private Integer maxActive;

    private Integer minIdle;

    private long maxWait;

    private boolean poolPreparedStatements;

    public String filters;
    //此处省略get，set方法
``` 
3）、注入配置
``` 
@Configuration
@EnableConfigurationProperties(value = DruidDataSourceProperties.class)
public class DruidDataSourceConfig {

    @Autowired
    private DruidDataSourceProperties druidDataSourceProperties;

    @Bean(initMethod = "init")
    public DruidDataSource dataSource() throws SQLException {
        System.out.println(druidDataSourceProperties);
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUsername(druidDataSourceProperties.getUsername());
        druidDataSource.setPassword(druidDataSourceProperties.getPassword());
        druidDataSource.setUrl(druidDataSourceProperties.getJdbcUrl());
        druidDataSource.setDriverClassName(druidDataSourceProperties.getDriverClassName());
        druidDataSource.setInitialSize(druidDataSourceProperties.getInitialSize());
        druidDataSource.setMinIdle(druidDataSourceProperties.getMinIdle());
        druidDataSource.setMaxActive(druidDataSourceProperties.getMaxActive());
        druidDataSource.setMaxWait(druidDataSourceProperties.getMaxWait());
        druidDataSource.setFilters(druidDataSourceProperties.getFilters());
        druidDataSource.setPoolPreparedStatements(druidDataSourceProperties.isPoolPreparedStatements());
        return druidDataSource;
    }
``` 
至此，我们完成了注入druid数据源，接下来是配置监控
3）、配置数据源监控，配置一个statViewServlet(后端管理)和WebStatFilter的sql监控
```
 /**
 * 配置druid管理后台的servlet
 * @return
 */
@Bean
public ServletRegistrationBean statViewSerlvet() {
    ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(),"/druid/*");
    Map<String,Object> initParameters = new HashMap<>();
    initParameters.put("loginUsername","admin");
    initParameters.put("loginPassword","123456");
    bean.setInitParameters(initParameters);
    return bean;
}

@Bean
public FilterRegistrationBean filterRegistrationBean() {
    FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(new WebStatFilter());
    filterRegistrationBean.setUrlPatterns(Arrays.asList("/*"));

    Map<String,Object> initParams = new HashMap<>();
    initParams.put("exclusions","*.js,*.css,/druid/*");
    filterRegistrationBean.setInitParameters(initParams);
    return  filterRegistrationBean;
}
```
打开http://localhost:8080/druid/查看监控情况
![图片](https://github.com/Andachui/img-storage/blob/master/char5/druid%E7%9B%91%E6%8E%A7%E6%88%AA%E5%9B%BE.PNG?raw=true)

### **2.mybatis**
1）、导入依赖，注入数据源（同上节）
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.0.0</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.8</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
    <version>5.1.46</version>
</dependency>
```

2）、指定配置文件位置
```
#配置mybaits
mybatis:
  configuration:
    map-underscore-to-camel-case: true
  mapper-locations: classpath:/mybatis/mapper/*.xml
```

3）、开启包扫描
```
@MapperScan(value = "com.dachui.componentmybatis.dao")
@SpringBootApplication
public class ComponentMybatisApplication {

    public static void main(String[] args) {
        SpringApplication.run(ComponentMybatisApplication.class, args);
    }

}
```
如果需要自定义配置可以自己注入ConfigurationCustomizer，来修改配置。具体使用可参照[官网](http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)

### **3.rabbitmq**
1）、导入依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```
2）、查看自动配置以及对应的自动配置属性类，发现已经自动配置了rabbitTemplate。查看RabbitProperties需要配置的属性
```
@ConfigurationProperties(prefix = "spring.rabbitmq")
public class RabbitProperties {

	/**
	 * RabbitMQ host.
	 */
	private String host = "localhost";

	/**
	 * RabbitMQ port.
	 */
	private int port = 5672;

	/**
	 * Login user to authenticate to the broker.
	 */
	private String username = "guest";

	/**
	 * Login to authenticate against the broker.
	 */
	private String password = "guest";

	/**
	 * SSL configuration.
	 */
	private final Ssl ssl = new Ssl();
    .....
```
3）、配置属性
#可配置常用属性
```
spring:
    rabbitmq:
        host: 127.0.0.1
        port: 5672
        virtual-host: tulingvip-springboot-rabbitmq
        username: guest
        password: guest
        connection-timeout: 5000
        template:
            mandatory: true
```
### **4.redis**
1）、导入依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2）、配置参数
```
spring:
  redis:
    host: 47.104.128.12
    port: 6379
    password: 123456
    jedis:
      pool:
        max-active: 20
        max-idle: 10
        min-idle: 5
```
3）、修改序列化
```
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)  {
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        //redis默认使用jdk的默认序列化，我们修改为json
        template.setDefaultSerializer(new Jackson2JsonRedisSerializer<Object>(Object.class));
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }
}
```
4）、使用
```
@SpringBootTest
class ComponentRedisApplicationTests {

    //redis原生api
    @Autowired
    private RedisTemplate redisTemplate;
    
    //继承自RedisTemplate，方便存储string类型数据，序列化非jdk
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
```

## **六、启动流程解析**
上面我们分析了自动配置结构和一些常用第三方组件的集成，接下来我们来看一下springboot的启动过程
### **1.jar包启动**
1）、tomcat相关组件的自动配置
我们知道SpringBoot的自动装配的秘密在org.springframework.boot.autoconfigure包下的spring.factories文件中，而嵌入Tomcat的原理就在这个文件中加载的一个配置类：org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration

```
@Configuration
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)  //这个注解是决定配置类的加载顺序的，当注解里的值越小越先加载
@ConditionalOnClass(ServletRequest.class)
@ConditionalOnWebApplication(type = Type.SERVLET)
@EnableConfigurationProperties(ServerProperties.class)  //开启ServerProperties的属性配置
@Import({ ServletWebServerFactoryAutoConfiguration.BeanPostProcessorsRegistrar.class,
        //这个类注册了两个bean.WebServerFactoryCustomizerBeanPostProcessor和ErrorPageRegistrarBeanPostProcessor
		ServletWebServerFactoryConfiguration.EmbeddedTomcat.class,
        //这个类会在存在Tomcat相关jar包时添加一个TomcatServletWebServerFactorybean,以下两个类没有导入jar包不生效
		ServletWebServerFactoryConfiguration.EmbeddedJetty.class,
		ServletWebServerFactoryConfiguration.EmbeddedUndertow.class })
public class ServletWebServerFactoryAutoConfiguration {

	@Bean
	public ServletWebServerFactoryCustomizer servletWebServerFactoryCustomizer(
			ServerProperties serverProperties) {
		return new ServletWebServerFactoryCustomizer(serverProperties);
	}

	@Bean
	@ConditionalOnClass(name = "org.apache.catalina.startup.Tomcat")
	public TomcatServletWebServerFactoryCustomizer tomcatServletWebServerFactoryCustomizer(
			ServerProperties serverProperties) {
		return new TomcatServletWebServerFactoryCustomizer(serverProperties);
	}
```
这个类的关键在于BeanPostProcessorsRegistrar.class导入两个后置处理器（bean初始化前后（创建完对象，还没赋值赋值）执行初始化工作），我们接下来说作用
```
@Configuration
class ServletWebServerFactoryConfiguration {

	@Configuration
	@ConditionalOnClass({ Servlet.class, Tomcat.class, UpgradeProtocol.class })
	@ConditionalOnMissingBean(value = ServletWebServerFactory.class, search = SearchStrategy.CURRENT)
	public static class EmbeddedTomcat {

		@Bean
		public TomcatServletWebServerFactory tomcatServletWebServerFactory() {
			return new TomcatServletWebServerFactory();
		}

	}
```
并且注入了TomcatServletWebServerFactory工厂类
2）、 springboot启动

运行main方法
```
@SpringBootApplication
public class ComponentApplication {

    public static void main(String[] args) {
        SpringApplication.run(ComponentApplication.class, args);
    }

}
```
创建SpringApplication对象
```
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
    this.resourceLoader = resourceLoader;
    Assert.notNull(primarySources, "PrimarySources must not be null");
    //保存主配置类
    this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
    //保存web应用的类型
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    //保存 容器初始化器(ApplicationContextInitializer类型的)
    setInitializers((Collection) getSpringFactoriesInstances(
            ApplicationContextInitializer.class));
    //把监听器保存到 SpringApplication中[ApplicationListener]
    setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
    //保存主配置类
    this.mainApplicationClass = deduceMainApplicationClass();
}

//还是去META-INFO/spring.factories 中获取ApplicationContextInitializer 类型，用于初始化容器
private <T> Collection<T> getSpringFactoriesInstances(Class<T> type,
			Class<?>[] parameterTypes, Object... args) {
    ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
    // Use names and ensure unique to protect against duplicates
    Set<String> names = new LinkedHashSet<>(
            SpringFactoriesLoader.loadFactoryNames(type, classLoader));
    List<T> instances = createSpringFactoriesInstances(type, parameterTypes,
            classLoader, args, names);
    AnnotationAwareOrderComparator.sort(instances);
    return instances;
}

//查找主配置类 查询的依据就是看哪个方法是否有main方法
private Class<?> deduceMainApplicationClass() {
    try {
        StackTraceElement[] stackTrace = new RuntimeException().getStackTrace();
        for (StackTraceElement stackTraceElement : stackTrace) {
            if ("main".equals(stackTraceElement.getMethodName())) {
                return Class.forName(stackTraceElement.getClassName());
            }
        }
    }
    catch (ClassNotFoundException ex) {
        // Swallow and continue
    }
    return null;
}
```
运行run方法
```
public ConfigurableApplicationContext run(String... args) {
    StopWatch stopWatch = new StopWatch();
    stopWatch.start();
    //创建一个 容器对象
    ConfigurableApplicationContext context = null;
    Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
    configureHeadlessProperty();
    //去meta-info/spring.factories中获取SpringApplicationRunListener 监听器(事件发布监听器)
    SpringApplicationRunListeners listeners = getRunListeners(args);
    //回调所有的获取SpringApplicationRunListener.starting()方法
    listeners.starting();
    try {
        //封装命令行参数
        ApplicationArguments applicationArguments = new DefaultApplicationArguments(
                args);
        //准备容器环境
        1:获取或者创建环境
        2：把命令行参数设置到环境中
        3：通过监听器发布环境准备事件
        ConfigurableEnvironment environment = prepareEnvironment(listeners,
                applicationArguments);
        configureIgnoreBeanInfo(environment);
        //打印springboot的图标
        Banner printedBanner = printBanner(environment);
        //创建容器 根据webApplicationType 来创建容器 通过反射创建
        context = createApplicationContext();
        //去meta-info类中 获取异常报告
        exceptionReporters = getSpringFactoriesInstances(
                SpringBootExceptionReporter.class,
                new Class[] { ConfigurableApplicationContext.class }, context);
        //准备环境
        1：把环境设置到容器中
        2: 循环调用AppplicationInitnazlier 进行容器初始化工作
        3:发布容器上下文准备完成事件
        4:注册关于springboot特性的相关单例Bean
        5:发布容器上下文加载完毕事件
        prepareContext(context, environment, listeners, applicationArguments,
                printedBanner);
        //刷新容器；ioc容器初始化（如果是web应用还会创建嵌入式的Tomcat
        //扫描，创建，加载所有组件的地方；（配置类，组件，自动配置）
        refreshContext(context);
        //运行 ApplicationRunner 和CommandLineRunner
        afterRefresh(context, applicationArguments);
        stopWatch.stop();
        if (this.logStartupInfo) {
            new StartupInfoLogger(this.mainApplicationClass)
                    .logStarted(getApplicationLog(), stopWatch);
        }
        //发布容器启动事件
        listeners.started(context);
        //运行 ApplicationRunner 和CommandLineRunner
        callRunners(context, applicationArguments);
    }
    catch (Throwable ex) {
        //出现异常；调用异常分析保护类进行分析
        handleRunFailure(context, ex, exceptionReporters, listeners);
        throw new IllegalStateException(ex);
    }

    try {
        listeners.running(context);
    }
    catch (Throwable ex) {
        handleRunFailure(context, ex, exceptionReporters, null);
        throw new IllegalStateException(ex);
    }
    return context;
}
```
3）、 嵌入式tomcat容器创建及启动

在之前run方法的refreshContext.refresh.refresh.onRefresh的ServletWebServerApplicationContext这个实现类中
```
protected void onRefresh() {
    super.onRefresh();
    try {
        createWebServer();
    }
    catch (Throwable ex) {
        throw new ApplicationContextException("Unable to start web server", ex);
    }
}

private void createWebServer() {
    WebServer webServer = this.webServer;
    ServletContext servletContext = getServletContext();
    //第一次访问的时候两个对象都为空
    if (webServer == null && servletContext == null) {
        ServletWebServerFactory factory = getWebServerFactory();
        this.webServer = factory.getWebServer(getSelfInitializer());
    }
    else if (servletContext != null) {
        try {
            getSelfInitializer().onStartup(servletContext);
        }
        catch (ServletException ex) {
            throw new ApplicationContextException("Cannot initialize servlet context", ex);
        }
    }
    initPropertySources();
}
protected ServletWebServerFactory getWebServerFactory() {
		// Use bean names so that we don't consider the hierarchy
		String[] beanNames = getBeanFactory().getBeanNamesForType(ServletWebServerFactory.class);
		if (beanNames.length == 0) {
			throw new ApplicationContextException("Unable to start ServletWebServerApplicationContext due to missing "
					+ "ServletWebServerFactory bean.");
		}
		if (beanNames.length > 1) {
			throw new ApplicationContextException("Unable to start ServletWebServerApplicationContext due to multiple "
					+ "ServletWebServerFactory beans : " + StringUtils.arrayToCommaDelimitedString(beanNames));
		}
		return getBeanFactory().getBean(beanNames[0], ServletWebServerFactory.class);
	}
```
Tomcat的启动就在createWebServer方法里面了，首先看一下getWebServerFactory
```
protected ServletWebServerFactory getWebServerFactory() {
    // 这里获取的beanname就是上方注册的tomcatServletWebServerFactory了
    String[] beanNames = getBeanFactory().getBeanNamesForType(ServletWebServerFactory.class);
    if (beanNames.length == 0) {
        throw new ApplicationContextException("Unable to start ServletWebServerApplicationContext due to missing "
                + "ServletWebServerFactory bean.");
    }
    if (beanNames.length > 1) {
        throw new ApplicationContextException("Unable to start ServletWebServerApplicationContext due to multiple "
                + "ServletWebServerFactory beans : " + StringUtils.arrayToCommaDelimitedString(beanNames));
    }
    return getBeanFactory().getBean(beanNames[0], ServletWebServerFactory.class);
}
```
创建了tomcat容器工厂。准备环境里注册的bean现在出来一个了。注意，上方还注册了一个后置处理器EmbeddedServletContainerCustomizerBeanPostProcessor，获取beantomcatServletWebServerFactory的时候就会执行后置处理器的postProcessBeforeInitialization方法
```
public Object postProcessBeforeInitialization(Object bean, String beanName)
			throws BeansException {
    if (bean instanceof WebServerFactory) {
        postProcessBeforeInitialization((WebServerFactory) bean);
    }
    return bean;
}

private void postProcessBeforeInitialization(WebServerFactory webServerFactory) {
    LambdaSafe
            .callbacks(WebServerFactoryCustomizer.class, getCustomizers(),
                    webServerFactory)
            .withLogger(WebServerFactoryCustomizerBeanPostProcessor.class)
            .invoke((customizer) -> customizer.customize(webServerFactory));
}

	private Collection<WebServerFactoryCustomizer<?>> getCustomizers() {
    if (this.customizers == null) {
        // Look up does not include the parent context
        this.customizers = new ArrayList<>(getWebServerFactoryCustomizerBeans());
        this.customizers.sort(AnnotationAwareOrderComparator.INSTANCE);
        this.customizers = Collections.unmodifiableList(this.customizers);
    }
    return this.customizers;
}

@SuppressWarnings({ "unchecked", "rawtypes" })
private Collection<WebServerFactoryCustomizer<?>> getWebServerFactoryCustomizerBeans() {
    return (Collection) this.beanFactory
            .getBeansOfType(WebServerFactoryCustomizer.class, false, false).values();
}
```
这个处理器的作用是获得所有定制器，然后执行定制器的方法.接下就是getWebServer剩余流程，根据定制器初始化tomcat容器，并启动。

springboot启动流程中的onRefresh方法加载我们自定的bean（如@Controller @Service，和包扫描），这部分属于spring的范围，故不作讲解。

总结：
内置tomcat容器依赖于ioc容器。
### **2.war包启动**
servlet3.0的规范规定
```
The ServletContainerInitializer class is looked up via the jar services API.

For each application, an instance of the ServletContainerInitializer is created by the container at application startup time.

The framework providing an implementation of the ServletContainerInitializer MUST bundle in the META-INF/services directory of the jar file a file called

javax.servlet.ServletContainerInitializer, as per the jar services API,that points to the implementation class of the ServletContainerInitializer.

In addition to the ServletContainerInitializer we also have an annotation -HandlesTypes.

The HandlesTypes annotation on the implementation of the ServletContainerInitializer is used to express interest in classes that may
have annotations (type, method or field level annotations) specified in the value of
the HandlesTypes or if it extends / implements one those classes anywhere in the
class’ super types.
The HandlesTypes annotation is applied irrespective of the
setting of metadata-complete.
```
第一，web应用启动，会创建当前Web应用导入jar包中的 ServletContainerInitializer类的实例

第二，ServletContainerInitializer 类必须放在jar包的 META-INF/services目录下,文件名称为 javax.servlet.ServletContainerInitializer

第三，文件的内容指向ServletContainerInitializer实现类的全路径

第四，使用@HandlesTypes 在我们应用启动的时候，加载我们感兴趣的类(本类及实现类)

1)、tomcat启动，然后去org\springframework\springweb\5.0.10.RELEASE\spring-web-5.0.10.RELEASE.jar!\METAINF\services\javax.servlet.ServletContainerInitializer 文件中
org.springframework.web.SpringServletContainerInitializer的实例
```
@HandlesTypes(WebApplicationInitializer.class)
public class SpringServletContainerInitializer implements ServletContainerInitializer {
```

2)、将@HandlesTypes标注的感兴趣的类（WebApplicationInitializer）都传入到 osStartup()的方法中Set<Class<?>>
参数中为这些感兴趣的类创建实例 ReflectionUtils.accessibleConstructor(waiClass).newInstance());
```
public void onStartup(@Nullable Set<Class<?>> webAppInitializerClasses, ServletContext servletContext)
			throws ServletException {

    List<WebApplicationInitializer> initializers = new LinkedList<>();

    if (webAppInitializerClasses != null) {
        for (Class<?> waiClass : webAppInitializerClasses) {
            // Be defensive: Some servlet containers provide us with invalid classes,
            // no matter what @HandlesTypes says...
            if (!waiClass.isInterface() && !Modifier.isAbstract(waiClass.getModifiers()) &&
                    WebApplicationInitializer.class.isAssignableFrom(waiClass)) {
                try {
                    initializers.add((WebApplicationInitializer)
                            ReflectionUtils.accessibleConstructor(waiClass).newInstance());
                }
                catch (Throwable ex) {
                    throw new ServletException("Failed to instantiate WebApplicationInitializer class", ex);
                }
            }
        }
    }

    if (initializers.isEmpty()) {
        servletContext.log("No Spring WebApplicationInitializer types detected on classpath");
        return;
    }

    servletContext.log(initializers.size() + " Spring WebApplicationInitializers detected on classpath");
    AnnotationAwareOrderComparator.sort(initializers);
    for (WebApplicationInitializer initializer : initializers) {
        initializer.onStartup(servletContext);
    }
}
```
2)、调用WebApplicationInitializer的onStartup方法

由于我们的类继承了SpringBootServletInitializer，所以执行该类的onStartup()
```
public void onStartup(ServletContext servletContext) throws ServletException {
    // Logger initialization is deferred in case an ordered
    // LogServletContextInitializer is being used
    this.logger = LogFactory.getLog(getClass());
    //创建web应用的上下文对象
    WebApplicationContext rootAppContext = createRootApplicationContext(servletContext);
    if (rootAppContext != null) {
        servletContext.addListener(new ContextLoaderListener(rootAppContext) {
            @Override
            public void contextInitialized(ServletContextEvent event) {
                // no-op because the application context is already initialized
            }
        });
    }
    else {
        this.logger.debug("No ContextLoaderListener registered, as " + "createRootApplicationContext() did not "
                + "return an application context");
    }
}

protected WebApplicationContext createRootApplicationContext(ServletContext servletContext) {
    //创建spring应用的构建器
    SpringApplicationBuilder builder = createSpringApplicationBuilder();
    builder.main(getClass());
    //设置环境
    ApplicationContext parent = getExistingRootWebApplicationContext(servletContext);
    if (parent != null) {
        this.logger.info("Root context already created (using as parent).");
        servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, null);
        builder.initializers(new ParentContextApplicationContextInitializer(parent));
    }
    builder.initializers(new ServletContextApplicationContextInitializer(servletContext));
    builder.contextClass(AnnotationConfigServletWebServerApplicationContext.class);
    //调用我们自己启动类上的confiure方法 传入我们自己的主启动类
    builder = configure(builder);
    builder.listeners(new WebEnvironmentPropertySourceInitializer(servletContext));
    SpringApplication application = builder.build();
    if (application.getAllSources().isEmpty()
            && AnnotationUtils.findAnnotation(getClass(), Configuration.class) != null) {
        application.addPrimarySources(Collections.singleton(getClass()));
    }
    Assert.state(!application.getAllSources().isEmpty(),
            "No SpringApplication sources have been defined. Either override the "
                    + "configure method or add an @Configuration annotation");
    // Ensure error pages are registered
    if (this.registerErrorPageFilter) {
        application.addPrimarySources(Collections.singleton(ErrorPageFilterConfiguration.class));
    }
    //调用我们类上的run方法
    return run(application);
}
```
从onStartup我们可以看到经过启动外置tomcat容器之后，依然执行run()来启动ioc容器，不同的是run()没有创建内置tomcat容器，只启动了ioc容器。
总结：
ioc容器依赖于外置tomcat容器。

## **七、自定义启动器starter**
### **1.starter结构**
从自动装配原理我们可以窥见starter的一般结构，接下就来总结一下。第三方启动器命名以xxx-spring-boot-starter，springboot官方的形式是spring-boot-starter-xxx

1）、自动配置模块
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.dachui</groupId>
    <artifactId>my-spring-boot-autoconfigure</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>my-spring-boot-autoconfigure</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
```
加载自动配置文件

![图片](https://github.com/Andachui/img-storage/blob/master/char7/springfac.PNG?raw=true)

2）、启动器模块

引入自定义的自动配置my-spring-boot-autoconfigure
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.dachui</groupId>
    <artifactId>my-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>my-spring-boot-starter</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.dachui</groupId>
            <artifactId>my-spring-boot-autoconfigure</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
```

3）、依赖自定义starter
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.dachui</groupId>
    <artifactId>my-test-autoconf</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>my-test-autoconf</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.dachui</groupId>
            <artifactId>my-spring-boot-starter</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
```
这样springboot启动时会扫描到自定义的spring.factories，加载我们洗的自动配置类。



