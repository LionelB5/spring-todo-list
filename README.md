# Spring MVC

## What is the Spring MVC Framework?
Spring Web MVC is a web framework built on the Servlet API.

Spring Web MVC has shipped in the Spring Framework from the very beginning and is **more commonly known as Spring MVC**.

Spring MVC, like many other web frameworks, is designed around the front controller pattern.

MVC refers to the popular Model View Controller design pattern:
- **Model**: Responsible for managing the application's data, business logic and business rules.
- **View**: The presentation layer of the application, and is an output representation of information.
- **Controller**: Responsible for invoking methods on the model to perform business logic, and then updating the view 
based on the Models output.

In Spring MVC we have a central Servlet, known as the `DispatchServlet`, it provides a shared algorithm for request 
processing.
- The actual work is performed by configurable, delegated components, in other words, we have controllers that handle 
requests.
- This design has the advantage of being flexible, and supporting different workflows.

The aforementioned `DispatcherServlet` expects a `WebApplicationContext`, which is an extension of plain 
`ApplicationContext`, for its own configuration.
- The `DispatcherServlet` delegates to special beans to process requests and render the appropriate responses.
- With the `WebApplicationContext` there are some beans that are registered for us automatically.


## The Maven WAR (Web Application Archive) plugin

**The WAR Plugin is responsible for collecting all artifact dependencies, classes and resources of the web application 
and packaging them into a web application archive.**

A project using the WAR plugin will typically have a structure similar to below:
```
 |-- pom.xml
 `-- src
     `-- main
         |-- java
         |   `-- com
         |       `-- example
         |           `-- projects
         |               `-- SampleAction.java
         |-- resources
         |   `-- images
         |       `-- sampleimage.jpg
         `-- webapp
             |-- WEB-INF
             |   `-- web.xml
             |-- index.jsp
             `-- jsp
                 `-- websource.jsp
```

`/webapp`
- This directory contains web application sources such as HTML, CSS and JavaScript files.
- The name of this folder is defined by the Maven Standard Directory Layout (we could use a different directory name, 
but this would not follow standard convention)

`/webapp/WEB-INF`
- This is a directory that contains all things related to the application that are'nt in the document root of the 
application.
- This directory **is not** part of the public document tree of the application (files in this directory cannot be 
directly served to the client by the Spring container).
- Despite this, the contents of this directory are visible to the servlet code, so we can use the servlet to process 
its files and display them to the user.
- Generally, this directory will contain jsp files, Thymeleaf templates, configuration files etc.

`/webapp/WEB-INF/web.xml`
- This file is a deployment descriptor for a servlet based java web application.
- It declares which servlets exist and which URLs they handle
- **This declaration and handling can also be achieved through the use of annotations**

## The Maven Cargo plugin
With the Maven Cargo plugin, we can use goals to manipulate WAR projects within the Apache Tomcat servlet container.
- One of the best things about this is that we can run Apache Tomcat in embedded mode, meaning there is no need to 
install anything on your computer (Spring Boot is somewhat similar in this regard).
- An example Cargo goal is the `cargo:run` goal, which will start an embedded Apache Tomcat servlet container, and will 
deploy our web application to this container. With the default settings, once this goal is executed, we can access the 
root of our web application by `http://localhost:8080/<application-name>/index.html`.

## The Spring MVC Dispatcher Servlet

**The Dispatcher Servlet is the front facing controller of Spring MVC and is used to dispatch HTTP requests to other 
controllers.**

There are two ways to register a servlet in an application:
- One way is to use a `web.xml` file (XML based configuration)
- A Dispatcher Servlet may also be registered programmatically (Java code based configuration)

To programmatically register a servlet, we must implement the `WebApplicationInitializer` interface.
- Implementations of this interface are automatically detected by Spring on startup of the application.
- Implementors of this interface are expected to implement the `onStartup` method that is invoked during Servlet container startup.
- Within the `onStartup` method we are able to programmatically create and configure our Dispatcher Servlet.

The following is an example of a class that implements this interface. *Note the `WebConfig` class would usually be in a 
separate file.*

```
public class WebAppInitializer implements WebApplicationInitializer {

    private static final String DISPATCHER_SERVLET_NAME = "dispatcher";
    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {
        // create the spring application context
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.register(WebConfig.class);

        // create the dispatcher servlet
        DispatcherServlet dispatcherServlet = new DispatcherServlet(context);

        // register and configure the dispatcher servlet
        ServletRegistration.Dynamic registration =
                servletContext.addServlet(DISPATCHER_SERVLET_NAME, dispatcherServlet);
                
        // configure startup loading of the servlet so that the container will instantiate and initialize the servlet
        registration.setLoadOnStartup(1);
        
        // a simple URL mapping that does not point to a controller
        registration.addMapping("/");
    }
}

@EnableWebMvc
@Configuration
@ComponentScan(basePackages = "lionel.learnspring")
public class WebConfig  {
}
```

**The `@EnableWebMvc` annotation**
- This annotation is used with the configuration class to import the Spring MVC configuration.
- By default this annotation registers some beans that are specific to Spring MVC (for example, the view resolver, 
request mapper etc.)

## The `@Controller` annotation
Spring MVC is designed around the Dispatcher Servlet which plays the role of Front Controller.

We can map requests to methods in classes annotated with `@Controller` (a specialized type of the `@Component` 
annotation). These classes are known as annotated controllers or controller classes.

Spring MVC provides an annotation-based programming model where `@Controller` and `@RestController` components
use annotations to express request mappings, request input, exception handling, and more.
- Annotated controllers have flexible method signatures and do not have to extend base classes or implement specific 
interfaces.

The `@RequestMapping` annotation is used to map requests to controller methods.
- It has various attributes to match by URL, HTTP method, requests parameters, headers and media types.
- Shortcut variants of `@RequestMapping` exist and map to different request methods, for example `@GetMapping`, 
`@PostMapping`, `@PutMapping` etc.


## The View Resolver and View
Spring MVC defines ViewResolver and View interfaces which enable us to render models in a browser without forcing the 
use of a specific view technology (JSP (Java Server Pages), Thymeleaf, Freemarker etc.).

`ViewResolver` provides mapping between view names and actual views.
- We can declare a bean that returns a `ViewResolver` within a class decorated with the `@Configuration` annotation.
- In this bean we can configure the view resolver, and set things like a prefix and suffix to use when resolving the 
location of a view to be rendered.

The following example demonstrates a few things:
- Declaring a `ViewResolver` bean and configuring a suffix and prefix for the `ViewResolver` instance.
- A Controller with a method decorated by `RequestMapping` that returns the name of the view file we wish to render
- This example relies on a JSP file being present: `/webapp/WEB-INF/view/welcome.jsp`
- Imports omitted for brevity, classes should reside in separate files

```
@EnableWebMvc
@Configuration
@ComponentScan(basePackages = "lionel.learnspring")
public class WebConfig  {

    public static final String RESOLVER_PREFIX = "/WEB-INF/view/";
    public static final String RESOLVER_SUFFIX = ".jsp";

    @Bean
    public ViewResolver viewResolver() {
        UrlBasedViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix(RESOLVER_PREFIX);
        viewResolver.setSuffix(RESOLVER_SUFFIX);
        return viewResolver;
    }
}

@Controller
public class HelloWorldController {

    // http://localhost:8080/todo-list/welcome
    // prefix + name + suffix
    // /WEB-INF/view/welcome.jsp
    @GetMapping("welcome")
    public String welcome() {
        return "welcome";
    }
}

```

`JSP` is a text document that contains two types of text:
- Static data, which can be expressed in any text-based format (such as HTML).
- JSP elements, which construct dynamic content.
- The JavaServer Pages Standard Tag Library (JSTL) is a useful library that extends the JSP specifications by adding 
a library of JSP tags for common tasks like loops etc.

## Spring MVC Request Processing

```
+-------------+                                                          +--------------+
|             |                                                          |              |
|   Request   |                                                          |   Response   |
|             |                                                          |              |
+-----+-------+                                                          +------+-------+
      |                                                                         ^
      |                                                                         |
      | 1                                                                       | 10
      |                                                                         |
      v                                                                         |
+-----+-------------------------------------------------------------------------+-------+
|                                                                                       |
|                                                                                       |
|                                   Dispatcher Servlet                                  |
|                                                                                       |
|                                                                                       |
+--+----------------+------+-------------------+------+---------------+--------+------+-+
   |                ^      |                   ^      |               ^        |      ^
   |                |      |                   |      |               |        |      |
   | 2              | 3    | 4                 | 5    | 6             | 7      | 8    | 9
   |                |      |                   |      |               |        |      |
   v                |      v                   |      v               |        v      |
+--+----------------+-+  +-+-------------------+-+  +-+---------------+-+    +-+------+-+
|                     |  |                       |  |                   |    |          |
|   Handler Mapping   |  |   Handler Controller  |  |   View Resolver   |    |   View   |
|                     |  |                       |  |                   |    |          |
+---------------------+  +-----------------------+  +-------------------+    +----------+

```
1. The browser creates a request to a specific url and the dispatcher servlet begins to handle the request from the 
browser.
2. The dispatcher servlet has to identify which controller should be used to handle the request. This is achieved via 
Handler Mapping to find the correct controller.
3. Handler mapping returns the specific handler method that should handle the request.
4. The dispatcher servlet calls this specific handler method.
5. The handler method returns the model and the view name.
6. The dispatcher servlet now has the logical view name, but it still needs to determine the view file to use. To 
achieve this, it finds the view resolver that we configured and calls using the logical view name.
7. The view resolver locates the target view file and returns it to the dispatcher servlet.
8. The dispatcher servlet executes the view and makes the model available to the view.
9. The view is now rendered and the view returns the content to the dispatcher servlet.
10. The dispatcher servlet sends the response back to the browser.

## Model and Model Attributes

The model interface defines a holder for model attributes and is primarily designed for adding attributes to the model.
The model is exposed to the view, meaning the view can access the attributes of the model.

There are two main ways we can add attributes to the model.

*Accessing the model object in the requests method within our controller*
- Request methods can accept many different parameters. One supported parameter is the `model` parameter of type 
`Model`.
- This parameter will be passed to this request method once it is called by the dispatcher servlet.
- The model can be seen as a key -> value store.
- Adding an attribute to a model is as simple as calling the `addAttribtue` method on the instance of the Model passed 
in by the dispatcher servlet.

*Declare methods on the controller and decorate them with the `@ModelAttribute` annotation*
- Methods decorated with this annotation are run each time the controller is invoked to handle a request.
- These methods are always executed first, before the appropriate request method is called.
- The return value of these functions determine the value to be set on the model object.
- The annotation can accept a single String argument which dictates what the key on the model for the value will be.

## Query Parameters
To read parameters from the query string we can use the `@RequestParam` annotation to decorate arguments that 
our controller function accepts.
- The annotation accepts optional arguments that allow you to configure whether the parameter is required, its default 
value etc.
- The name of the parameter decorated with this annotation will determine the key of the request parameter that is 
searched for in the query string. An exception to this is if one provides `name` as an argument to the `@RequestParam` 
constructor.
