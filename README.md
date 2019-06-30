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


