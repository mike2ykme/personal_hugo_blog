---
title: "OLD_FIRST_IOC_CONTAINER"
sub_title: "OLD_FIRST_IOC_CONTAINER"
date: 2020-12-24T16:01:52-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/woods.jpg"
alt_text: "OLD_FIRST_IOC_CONTAINER screenshot"
year: "2020"
month: "2020/12"
draft: true
categories:
- Personal
- Thoughts
tags:
- software
- html
series:
Summary:
---

Lorem ipsum dolor sit amet, consectetur adipisicing elit. Impedit natus sint debitis corporis nobis perspiciatis tempore a dolorum animi cum, modi voluptatibus. Earum, explicabo velit?

<!--more-->

# Intro to Spring IoC Container #



### What is Spring & the IoC Container? ###
---

Spring is a framework that manages dependencies, reduces boilerplate code, and works to reduce effort of many common tasks by providing common patterns and features to remove errors.

While this is a lot to say, Spring let's you build better apps because you don't have to build out every piece of code that you're using for common tasks like database connectivity or network traffic; and everything starts with the core of Spring the Spring Inversion of Control Container.
- The IoC Container will manage all of your application's objects and their depdendencies
- so when you need something for your objects you can simply designate your needs through metadata [either xml or java] and Spring will read, parse, and evaluate this data to provide a fully constructed object.

So now it may be time to ask why, why do we need or want a layer of abstraction in our development process. Can't we just have all the other stuff without having something manage what we're requesting and handling?

In order to answer this let's consider the true benefit of object oriented programming: `Interfaces` [You could more generally say any system that allows for an inverted resonsibility of an object's design, but I think an Interface in the Java language sense adequately covers this. Realy it's just allowing components to be plugged in without having to restructure an entire application to take advantage of these differences] 
    - `Interfaces` allow programming via design contract, and by using these contracts we're able to develop objects that can be custom tailored for usages while still allowing existing code to call it.
 - Spring's IoC can free you from dealing with object creation, resource acquisition for objects, and object lifetimes, & object destruction / resource freeing.
   - Note this _can_ help you but since this is still a framework & not magic you'll need to play within the rules & provide Spring the necessary resources it needs.
   - So you'll need to understand how Spring builds your business objects, the general flow and usage of the Spring container, as well how to tie into these features.

So let's first talk about how Spring brings everything together inside an application, and Spring uses a central interface called [ApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html).

### From the JavaDoc documentation: ###

    Central interface to provide configuration for an application. This is read-only while the application is running, but may be reloaded if the implementation supports this.
     An ApplicationContext provides:

    * Bean factory methods for accessing application components. Inherited from ListableBeanFactory.
    The ability to load file resources in a generic fashion. Inherited from the ResourceLoader interface.
    * The ability to publish events to registered listeners. Inherited from the ApplicationEventPublisher interface.
    * The ability to resolve messages, supporting internationalization. Inherited from the MessageSource interface.
    * Inheritance from a parent context. Definitions in a descendant context will always take priority. This means, for example, that a single parent context can be used by an entire web application, while each servlet has its own child context that is independent of that of any other servlet.
    * In addition to standard BeanFactory lifecycle capabilities, ApplicationContext implementations detect and invoke ApplicationContextAware beans as well as ResourceLoaderAware, ApplicationEventPublisherAware and MessageSourceAware beans.

So from this documentation we can see a couple things
1. This is a central interface, but different implementations can provide additional functionality
2. We can tell this interface already extends other interfaces that provide a more specific chunk of functionality. 
    - The idea of core interfaces that are meant to be extended is a core tenant of the Spring Framework & we'll see this idea of basic --> advanced functionality in other Spring projects.
3. An ApplicationContext can be a child of another ApplicationContext, so we can have a hierarchy of contexts within an application.
4. We have the ability to listen & send messages within our application, a pretty nice feature that allows some advanced actions.
5. There are LIFECYCLE capabilities that we can connect to and work with.

Alright, so we now know that Spring has a centralized interface that let's us do stuff, but where do we go from here? Well from here we'll need to create an ApplicationContext from one of it's concrete class implementations

From the JavaDoc of [ApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html)
> **All Known Implementing Classes**:
AbstractApplicationContext, AbstractRefreshableApplicationContext, AbstractRefreshableConfigApplicationContext, AbstractRefreshableWebApplicationContext, AbstractXmlApplicationContext, AnnotationConfigApplicationContext, AnnotationConfigWebApplicationContext, ClassPathXmlApplicationContext, FileSystemXmlApplicationContext, GenericApplicationContext, GenericGroovyApplicationContext, GenericWebApplicationContext, GenericXmlApplicationContext, GroovyWebApplicationContext, ResourceAdapterApplicationContext, StaticApplicationContext, StaticWebApplicationContext, XmlWebApplicationContext
- Note that in the examples I'm going to use 
[ConfigurableApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ConfigurableApplicationContext.html) instead of the ApplicationContext interface because this interface allows you to close the Application context and thereby freeing resources.
```
You may have heard of Spring Boot, which is an opinionated starter to the Spring Framework, and a lot of configuration that you would normally have to do has been eliminated inside of Spring Boot. It will look at the packages that are on your class path & from there conditionally enable or disable the creation of certain beans. Additionally, it takes a convention over configuration approach for many elements so you may feel it a bit "magical" at times where you can just have a depdenency you need created and managed for you. This is a great thing! But the Spring Cert mentions Spring Boot at a specific time so when looking through the documentation it's important to realize that just using Spring Boot without understanding how things work could cause some problems when working with older code and while taking the certification.

```

From the large list of implementation classes you can see there are a lot of options when it comes to creating a Spring AppliationContext. 
* I've created a small sample project with both XML & annotation ApplicationContext examples; inside of it I've used the ConfigurableApplicationContext & registered a shutdown hook as in 
[Shutting Down the Spring IoC Container Gracefully in Non-Web Applications](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-shutdown) mentions.

    > I found the example of the XML data for the spring Bean Definition here: [beans-factory-metadata](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-metadata)

    * Spring Boot automatically configures a shutdown hook for you, so you don't have to specify this.

    * Web Application Contexts will also have a shutdown hook registered since they'll be part of the Servlet lifecycle. This is not 100% accurate anymore since there is the reative web stack that doesn't use the Servlet API, but it's a nice way to think of it. You really just need to remember that web applications are tied to the lifecycle of something else and that something else's shutdown will cause the ApplicationContext to be stopped.
        * From the [Information 'i'](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-shutdown) section, you can see this 

            > This section applies only to non-web applications. Spring’s web-based ApplicationContext implementations already have code in place to gracefully shut down the Spring IoC container when the relevant web application is shut down.
    

* Additionally, through the example you can find a basic example of how to declare beans via [XML](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-constructor-injection) and through [Annotation](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-java-instantiating-container). You can also setup [Java Configuration](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-java-composing-configuration-classes). To me, using this kind of configuration is using Annotation configuration, but using it in a more XML type way. You'll specify the beans in a centralized [@Configuration](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-java-configuration-annotation) file that will provide beans for other parts of your app. 

    * Note you can [enable annotation configuration via XML](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-annotation-config) and this won't cause any problems. Spring provides you the ability to mix & match the types of configuration that you choose.
    * Letting Spring handle your depdendencies is known as Depdendency Injection, and this let's your code still be easily testable since you're not tied to a framework as well as letting your change out depdendencies through Spring Configurations or annotations.
* There is even support for [Web Applications](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-java-instantiating-container-web) with annotation configuration. I didn't provide a sample as this would still require setting up a full servlet setup.


OK, so reviewing the example code & the above there's some things we can determine

1. Multiple kinds of ApplicationContexts
    - We can specify XML or Annotation configuration or both
    - Each of these configuration methods have benefits / downsides; however, when working with them they are compatible with eachother 
2. There are some differences on the contexts based on where they're meant to be used
    - You can think of how in Web Based applications we will tap into the 'Servlet' lifecycle so we won't register a shutdown hook. _Yes I know about the reactive stack, see my comments above_
3. Spring will create and manage a bean and it's depdendencies.
    - If you tell Spring there is something required, then it will fail if it's not provided
    - Note that you can specify optional parameters through either annotations or by using the Java 8 Optional type.
    - Additionally, you're able to specify multiple types of dependencies & through the power of reflection, Spring can even set values into private fields.
        - Here though I feel like I should mention you really shouldn't mess around with private fields if possible. If you need to set something that's required think about a constructor argument. If something is optional then maybe a setter argument, but be careful of nulls as they'll cause a lot of headaches. For field assignements on private variables, if you have access to the source code then you should consider if you can make a change to your code to avoid this, as it's going to cause a lot of problems in testing.
4. Spring has annotations that cause class behavior to be changed. You can think of the @Configuration class where method calls to the `@Bean` methods would be intercepted by the Spring Container & a bean of an appropriate scope would be provided.

    - The scope of a bean designates it's lifetime. There are several different kinds & each has their own use. By default Spring uses the Singleton scope, and this means that no matter how many times you create/ask for a bean of that type it'll always give you the same object behind the scenes.
        - One major point to understand is that Spring will proxy the `@Configuration` class handle calls to public methods, even with [Injecting Inter-bean Dependencies](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-java-injecting-dependencies). This basicall means you can call a `@Bean` method within another `@Bean` method and Spring will intercept these calls. This only works on full configuration, as in non-`@Configuration` classes are operating in 'lite' mode and cannot provide the same level of functionality. see [Full @Configuration vs “lite” @Bean mode?](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-java-basic-concepts). This also moeans that because of using CGLIB, you cannot have final `@Bean` methods or final `@Configuration` classes with CGLIB.
        * this is an important point, as you can see where I updated the greeting you can change an object that has side effects that leak over to other objects.

            `2020-05-16 14:08:04,143 [main] [INFO ] annotationContext.Main - Results from javaConfigConsumer Hello Greeting Consumer from JavaConfig
            2020-05-16 14:08:04,143 [main] [INFO ] annotationContext.Main - Results from javaConfigConsumer Hello I've been changed!
            2020-05-16 14:08:04,143 [main] [INFO ] annotationContext.Main - Results from javaConfigConsumer Hello I've been changed!`

    - I try to avoid just giving a link and suggest reading all the documentation, but seriously, you should review the [Bean Scopes](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-scopes) documentation from Spring. It's not very long & will give you a more thorough understanding of what a scope is. Plus there's pictures!


5. There are multiple ways you can designate to Spring that an object should be part of the ApplicationContext.
    - GreeterSetterConsumer class uses the `@Component` annotation. This means the `AnnotationConfigApplicationContext` Context will be able to pick up this and register it as a bean.
        - There is also an [`@Autowired` annotation](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/annotation/Autowired.html) that tells Spring how we want our dependencies injected.
        - In this class we're telling Spring that we want the depdendency injected as a setter, but we could also have specified this behavior in a constructor.
            - Since Spring 4.3, if your component has only one constructor then Spring will use that by default. This can be further specified by annotation
    - It's important to note that @Component is part of the stereotype annotations that Spring has provided. These allow you to add additional semantic metadata to your code that helps to self document the intent of a class.

        > [1.10.1. @Component and Further Stereotype Annotations](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-stereotype-annotations)
        > 
        > Spring provides further stereotype annotations: @Component, @Service, and @Controller. @Component is a generic stereotype for any Spring-managed component. @Repository, @Service, and @Controller are specializations of @Component for more specific use cases (in the persistence, service, and presentation layers, respectively). Therefore, you can annotate your component classes with @Component, but, by annotating them with @Repository, @Service, or @Controller instead, your classes are more properly suited for processing by tools or associating with aspects. For example, these stereotype annotations make ideal targets for pointcuts. @Repository, @Service, and @Controller can also carry additional semantics in future releases of the Spring Framework. Thus, if you are choosing between using @Component or @Service for your service layer, @Service is clearly the better choice. Similarly, as stated earlier, @Repository is already supported as a marker for automatic exception translation in your persistence layer.


There's so many pieces of the Spring Container, but let's shift over to a basic understanding of testing



<!-- Next let's consider testing -->


NEED TO ENSURE WE'RE TALKING ABOUT COMPONENT SCANNING!!!
@Autowired
    > Field, method, constructor injection