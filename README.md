# Spring MVC

## What is the Spring MVC Framework?
Spring Web MVC is an original web framework built on the Servlet API.

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