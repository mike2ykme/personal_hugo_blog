---
title: "Spring Certification: Container, Dependency and IOC"
sub_title: "Reviewing the Study Guide"
date: 2020-12-24T15:54:48-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/spring.jpg"
alt_text: "Getting Spring Certified 1 screenshot"
year: "2020"
month: "2020/12"
draft: false
categories:
  - career
tags:
  - certification
  - Java
  - Spring
series:
Summary: "let's start with the basics of the Spring Container"
---


resources:

* [Spring Professional Study guide](https://pivotalcontent.s3.amazonaws.com/academy/Spring-Professional-Certification-Study-Guide.pdf)

* [Spring Professional Documentation - Web Servlet](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#spring-web)
    * Much of the content from Servlet and the react WebFlux cross over, and both of these documents contain references to each other; however, because of some fundamental differences of the underlying architecture there are also considerable differences.

--- 

The first section of the Study Guide is 'Container, Dependency and IOC' and I'm going to try and follow with that guided overview of material. 

## Container, Dependency and IOC

#### What is dependency injection and what are the advantages of using it?

To begin, let's look at the ultimate source of human knowledge, Wikipedia!

{{< sand-quote 
"In software engineering, dependency injection is a technique in which an object receives other objects that it depends on. These other objects are called dependencies. In the typical \"using\" relationship the receiving object is called a client and the passed (that is, \"injected\") object is called a service." 
"https://en.wikipedia.org/wiki/Dependency_injection" 
"Wikipedia: Dependency_injection"
>}}

Basically here, instead of us calling the 'new' keyword in our code, we tell Spring that we need something and it will give it to us. This has numerous benefits that relate to better OO & SOLID principles:

* Improves:
    * Testability because external dependencies can be more easily mocked or stubbed.
    * Clarity of the code because it's responsibilities have been narrowed.
    * Cohesion of class's interacting with each other as this design promotes coding by contract [Java Interfaces]
    * Maintenance as there is a clearer seperation of concerns the code is handling

---
---
---

#### What is an interface and what are the advantages of making use of them in Java?

{{< sand-quote
"...objects define their interaction with the outside world through the methods that they expose. Methods form the object's interface with the outside world;..."
"https://docs.oracle.com/javase/tutorial/java/concepts/interface.html"
"Oracle Java Documentation"
>}}

A Java interface let's you expose just the functionality you'd like to expose to it's users while hiding the more complicated implementation details. This also allows for different implementations to be used as well, preventing a lock-in of one implementation. So why does this matter? Again you can provide easier testing through mocks, as well as provide different levels of functionality by different implementations.

---
---
---

#### What is an ApplicationContext?


{{<raw>}}
<div class="w3-panel w3-leftbar w3-sand">
  <span style="font-size: 150px; line-height: 0.6em; opacity: 0.2">❝</span>
  <div class="w3-large w3-serif" style="margin-top: -40px">


<div class="block">Central interface to provide configuration for an application.
This is read-only while the application is running, but may be
reloaded if the implementation supports this.
<p>An ApplicationContext provides:
</p><ul>
<li>Bean factory methods for accessing application components.
Inherited from <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/ListableBeanFactory.html"><code>ListableBeanFactory</code></a>.
</li><li>The ability to load file resources in a generic fashion.
Inherited from the <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ResourceLoader.html" title="interface in org.springframework.core.io"><code>ResourceLoader</code></a> interface.
</li><li>The ability to publish events to registered listeners.
Inherited from the <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEventPublisher.html" title="interface in org.springframework.context"><code>ApplicationEventPublisher</code></a> interface.
</li><li>The ability to resolve messages, supporting internationalization.
Inherited from the <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/MessageSource.html" title="interface in org.springframework.context"><code>MessageSource</code></a> interface.
</li><li>Inheritance from a parent context. Definitions in a descendant context
will always take priority. This means, for example, that a single parent
context can be used by an entire web application, while each servlet has
its own child context that is independent of that of any other servlet.
</li></ul>
<p>In addition to standard <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/BeanFactory.html" title="interface in org.springframework.beans.factory"><code>BeanFactory</code></a>
lifecycle capabilities, ApplicationContext implementations detect and invoke
<a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContextAware.html" title="interface in org.springframework.context"><code>ApplicationContextAware</code></a> beans as well as <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ResourceLoaderAware.html" title="interface in org.springframework.context"><code>ResourceLoaderAware</code></a>,
<a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEventPublisherAware.html" title="interface in org.springframework.context"><code>ApplicationEventPublisherAware</code></a> and <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/MessageSourceAware.html" title="interface in org.springframework.context"><code>MessageSourceAware</code></a> beans.</p></div>
</div>
  <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html" style="text-decoration: none">
    <p>Spring JavaDoc - Application Context <img src="/icons/link.svg" alt="link icon"></p>
  </a>
</div>

{{</raw>}}

OK! So we are finally getting to some Spring specific elements. At it's core Spring is an __Inversion of Control__ framework and it handles creating and instantiating the resources needed for your application. Spring will take in your Java as well as configuration metadata, this can be through @annotations that are applied throughout your java codebase, and provide you with fully instantiated objects ready to use throughout your application. 

 Source: [Spring Core Doc](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-basics)

---
---
---

#### How are you going to create a new instance of an ApplicationContext?

In the below example you would provide Spring a set of classes that have Spring configuration metadata and from this you can pull out fully instantiated objects. 
```Java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(MyServiceImpl.class, Dependency1.class, Dependency2.class);
    MyService myService = ctx.getBean(MyService.class);
    myService.doStuff();
}
```
[Simple Construction Example](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-instantiating-container-constructor)

It should also be noted that you can instantiate a container and then provide metadata configuration after initial instantiation:

```Java
public static void main(String[] args) {
    AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
    ctx.register(AppConfig.class, OtherConfig.class);
    ctx.register(AdditionalConfig.class);
    ctx.refresh();
    MyService myService = ctx.getBean(MyService.class);
    myService.doStuff();
}
```
[Source](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-instantiating-container-register)


This also ignores the creating of any Servlet Application Context containers! 

[Servlet Configuration](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-container-config)

With Spring you can use the [*web.xml* configuration](https://docs.spring.io/spring-framework/docs/5.0.3.RELEASE/spring-framework-reference/core.html#beans-java-instantiating-container-web) 

```xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/app-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>

</web-app>

```






or the newer [3.0+ Servlet configuration](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-servlet)

```Java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { App1Config.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/app1/*" };
    }
}
```

Also, while I've note mentioned any of the actual configuration metadata process, you have the option to have a package be scanned for Java classes that can be used; however, it's important to note this is *NOT A ZERO COST* operation as scanning through large code bases will take time every time the application starts. Therefore, you should use scanning appropriately on code that would be picked up at least in a majority sense, and provide scoping or ignore rules to prevent unnecessary work.

---
---
---

#### Can you describe the lifecycle of a Spring Bean in an ApplicationContext?

Alright, this is a large and slightly tricky question, but it's only tricky because there's a lot of little steps that a bean will take in order to be fully prepared.

On a way zoomed back view, you can think of a bean being created like this:
    1. Spring Container reviews configuration metadata and creates bean 'recipes' that will be instantiated later.
    2. Spring processes additional configuration metadata that will affect how beans are created and additional capabilities, thereby altering the 'recipe' for beans.
    3. Spring creates the beans providing resources of already created beans in order to fulfill dependencies.
    4. Spring provides the ready made beans to be used
    5. SHUTDOWN hooks inside the container object trigger shutdown operations that cascade custom 

Now this is a way oversimplification of the process that actually occurs. I really recommend reviewing the actual documentation as there's quite a few steps involved, and each step has a lot of detail.[Spring core - beans-definition](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-definition)


---
---
You should also be aware of the differences in making changes to bean instances after they're created and modifying the bean metadata *'recipe'* 
{{<raw>}}

<div class="w3-panel w3-leftbar w3-sand">
  <span style="font-size: 150px; line-height: 0.6em; opacity: 0.2">❝</span>
  <div class="w3-large w3-serif" style="margin-top: -40px">

  
    <div class="admonitionblock note">
    <table>
    <tbody><tr>
    <td class="icon">
    <i class="fa icon-note" title="Note"></i>
    </td>
    <td class="content">
    <div class="paragraph">
    <p><code>BeanPostProcessor</code> instances operate on bean (or object) instances. That is,
    the Spring IoC container instantiates a bean instance and then <code>BeanPostProcessor</code>
    instances do their work.</p>
    </div>
    <div class="paragraph">
    <p><code>BeanPostProcessor</code> instances are scoped per-container. This is relevant only if you
    use container hierarchies. If you define a <code>BeanPostProcessor</code> in one container,
    it post-processes only the beans in that container. In other words, beans that are
    defined in one container are not post-processed by a <code>BeanPostProcessor</code> defined in
    another container, even if both containers are part of the same hierarchy.</p>
    </div>
    <div class="paragraph">
    <p>To change the actual bean definition (that is, the blueprint that defines the bean),
    you instead need to use a <code>BeanFactoryPostProcessor</code>, as described in
    <a href="https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-extension-factory-postprocessors">Customizing Configuration Metadata with a <code>BeanFactoryPostProcessor</code></a>.</p>
    </div>
    </td>
    </tr>
    </tbody></table>
    </div>

</div>
  <a href="https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-extension-bpp" style="text-decoration: none">
    <p>Bean Factory (i) info section<img src="/icons/link.svg" alt="link icon"></p>
  </a>
</div>
{{</raw>}}


---
---
While this step is a very fundamental building block of the bean instantiation process, in a general sense it's hidden from you in most cases except through interacting with either definition 


https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-lifecycle


https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-extension-bpp

---
---
Here are some additional resources to review on this as it's a rather spread out topic in the framework documentation:

* https://docs.spring.io/spring-framework/docs/5.0.3.RELEASE/spring-framework-reference/core.html#beans-factory-lifecycle

* https://docs.spring.io/spring-framework/docs/5.0.3.RELEASE/spring-framework-reference/core.html#beans-postconstruct-and-predestroy-annotations

* https://docs.spring.io/spring-framework/docs/5.0.3.RELEASE/spring-framework-reference/core.html#context-load-time-weaver


* [dZone article](https://dzone.com/articles/spring-bean-lifecycle) --  this article has a good picture of what the process looks like.

