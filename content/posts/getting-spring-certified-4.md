---
title: "Getting Spring Certified"
sub_title: "Continuing with annotation based dependency injection"
date: 2021-01-05T21:37:21-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/spring.jpg"
alt_text: "a mechanical spring"
year: "2021"
month: "2021/01"
draft: false
categories:
  - career
tags:
  - certification
  - Java
  - Spring
series:
Summary: "Continuing reviewing the study guide with dependency injection using annotations"
---

resources:

* [Spring Professional Study guide](https://pivotalcontent.s3.amazonaws.com/academy/Spring-Professional-Certification-Study-Guide.pdf)
* [Spring Core Reference](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html)
    * [Spring Core Reference - annotation config](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-annotation-config)

{{<tab>}} While in Java based configuration you would still retain a distinct area for bean creation, or at least a distinct area where to define where beans are created, annotation based configuration uses metadata placed on the classes themselves to tell Spring the dependencies as well as specialized configuration metadata to tell Spring where the classes are. 

{{<tab>}} How does Spring know what class we want to have available for autowiring and what class should be ignored? After all, there will be some data classes that we wouldn't just want injected into properties. Also, what happens if we have multiple objects that could satisfy an interface where the usage needs to be determined based on the deployment area?

1. Spring makes use of [@Component](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Component.html) and it's specializations in order to determine what should be handled by Spring.

```Java

@Component  // Tells Spring to pick up this class as a bean candidate
public class MyClass{
    // Just a POJO
    public MyClass(Dependency dependency){
        ...
        // Spring will auto use the constructor if there's only 1
        // otherwise, Spring will look for a default constructor
        // specified with @Autowired or JSR330 equivalent.
    } 
}
```
{{<br>}}

 	
{{<sand-quote
"As of Spring Framework 4.3, an @Autowired annotation on such a constructor is no longer necessary if the target bean defines only one constructor to begin with. However, if several constructors are available and there is no primary/default constructor, at least one of the constructors must be annotated with @Autowired in order to instruct the container which one to use. See the discussion on [constructor resolution](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-autowired-annotation-constructor-resolution) for details."
"https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-autowired-annotation"
"1.9.2. Using @Autowired"
>}}

While this class has the @Component, Spring offers several meta-annotations from the @Component annotation. So you can use them in the places where @Component would be used, but they offer a sort of self documenting annotation to help you understand the use of a class's usage
* [@Repository API](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Repository.html)
* [@Service API](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Service.html)
* [@Controller API](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Controller.html)

{{<br>}}

Great! Spring can now understand we have a class that needs to be used as a bean candidate, how does Spring find it? 

You just need to tell Spring to look for them,
1. Using [@ComponentScan](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html) as a Java annotation
    * [Enabling Component Scan](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-instantiating-container-scan)
    * [Spring Boot default Package](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-using-the-default-package)
        * This is from the Spring boot documentation, but it mentions the dangers of component scanning without restrictions.
    ```Java
    @Configuration
    @ComponentScan(basePackages="org.example")
    public class MyConfiguration{
        ... 
    }
    ```
    * [Example from Spring documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-scanning-autodetection)

2. Spring Boot performs this by default with its [@SpringBootApplication](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/SpringBootApplication.html)
    ```Java
    package com.example.myapplication;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```

3. Using XML configuration with [<context:component-scan>](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-scanning-autodetection)
    * [XML configuration](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-annotation-config)
    ```XML
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            https://www.springframework.org/schema/context/spring-context.xsd">

        <context:component-scan base-package="org.example"/>

    </beans>
    ```
    * [Example from Spring documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-scanning-autodetection)

{{<br>}}
{{<sand-quote
"The use of <context:component-scan> implicitly enables the functionality of <context:annotation-config>. There is usually no need to include the <context:annotation-config> element when using <context:component-scan>."
"https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-scanning-autodetection"
"beans-scanning-autodetection"
>}}

{{<br>}}

It is important to understand that even with component scanning, there is still functionality to ensure only select elements are discovered and Spring provides the ability to customize how your class finds configuration metadata via component scanning:

```Java
@Configuration
@ComponentScan(basePackages = "org.example",
        includeFilters = @Filter(type = FilterType.REGEX, pattern = ".*Stub.*Repository"),
        excludeFilters = @Filter(Repository.class))
public class AppConfig {
    ...
}
```
* [1.10.4. Using Filters to Customize Scanning](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-scanning-filters)

{{<br>}}

{{<raw>}}

<div class="w3-panel w3-leftbar w3-sand">
  <span style="font-size: 150px; line-height: 0.6em; opacity: 0.2">❝</span>
  <div class="w3-large w3-serif" style="margin-top: -40px">

<table id="beans-scanning-filters-tbl" class="tableblock frame-all grid-all stretch">
<caption class="title">Table 5. Filter Types</caption>
<colgroup>
<col style="width: 33.3333%;">
<col style="width: 33.3333%;">
<col style="width: 33.3334%;">
</colgroup>
<thead>
<tr>
<th class="tableblock halign-left valign-top">Filter Type</th>
<th class="tableblock halign-left valign-top">Example Expression</th>
<th class="tableblock halign-left valign-top">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">annotation (default)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.example.SomeAnnotation</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">An annotation to be <em>present</em> or <em>meta-present</em> at the type level in target components.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">assignable</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.example.SomeClass</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">A class (or interface) that the target components are assignable to (extend or implement).</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">aspectj</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.example..*Service+</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">An AspectJ type expression to be matched by the target components.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">regex</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org\.example\.Default.*</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">A regex expression to be matched by the target components' class names.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">custom</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.example.MyTypeFilter</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">A custom implementation of the <code>org.springframework.core.type.TypeFilter</code> interface.</p></td>
</tr>
</tbody>
</table>

<!--  -->

  </div>
  <a href="https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-scanning-filters" style="text-decoration: none">
    <p>1.10.4. Using Filters to Customize Scanning<img src="/icons/link.svg" alt="link icon"></p>
  </a>
</div>

{{</raw>}}


{{<br>}}

A Spring application is not limited to a single type of configuration and you can mix and match them, the configuration can be a collection of elements that best suite the situation. So feel free to use the best tools for the job.

Now that we've gone though letting Spring know where to find the beans, let's continue with the next question:

{{<br>}}

#### Scopes for Spring beans? What is the default scope?

{{<tab>}} So what is a 'scope'? In this context, a scope is the lifetime of the bean object that is provided. The default bean scope is a singleton and this means there will only be at most one object created by Spring in the ApplicationContext. Spring reads the configuration metadata and creates the appropriate scoped bean; these scoped beans allow for additional functionality because they're proxied objects under the container's control except for the [Prototype scope](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-prototype), these are just created every time they're requested and not managed by Spring's container.

When beans are under Spring's control there is the ability for different scopes to be combined when necessary see [1.5.3. Singleton Beans with Prototype-bean Dependencies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-sing-prot-interaction)

{{<br>}}

{{<raw>}}

<div class="w3-panel w3-leftbar w3-sand">
  <span style="font-size: 150px; line-height: 0.6em; opacity: 0.2">❝</span>
  <div class="w3-large w3-serif" style="margin-top: -40px">

<!--  -->

<table id="beans-factory-scopes-tbl" class="tableblock frame-all grid-all stretch">
<caption class="title">Table 3. Bean scopes</caption>
<colgroup>
<col style="width: 20%;">
<col style="width: 80%;">
</colgroup>
<thead>
<tr>
<th class="tableblock halign-left valign-top">Scope</th>
<th class="tableblock halign-left valign-top">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-singleton">singleton</a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(Default) Scopes a single bean definition to a single object instance for each Spring IoC
container.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-prototype">prototype</a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Scopes a single bean definition to any number of object instances.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-request">request</a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Scopes a single bean definition to the lifecycle of a single HTTP request. That is,
each HTTP request has its own instance of a bean created off the back of a single bean
definition. Only valid in the context of a web-aware Spring <code>ApplicationContext</code>.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-session">session</a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Scopes a single bean definition to the lifecycle of an HTTP <code>Session</code>. Only valid in
the context of a web-aware Spring <code>ApplicationContext</code>.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-scopes-application">application</a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Scopes a single bean definition to the lifecycle of a <code>ServletContext</code>. Only valid in
the context of a web-aware Spring <code>ApplicationContext</code>.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="web.html#websocket-stomp-websocket-scope">websocket</a></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Scopes a single bean definition to the lifecycle of a <code>WebSocket</code>. Only valid in
the context of a web-aware Spring <code>ApplicationContext</code>.</p></td>
</tr>
</tbody>
</table>

<!--  -->

  </div>
  <a href="https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes" style="text-decoration: none">
    <p>1.5. Bean Scopes<img src="/icons/link.svg" alt="link icon"></p>
  </a>
</div>

{{</raw>}}