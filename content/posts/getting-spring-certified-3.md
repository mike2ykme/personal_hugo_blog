---
title: "Getting Spring Certified 3"
sub_title: "Reviewing the Study guide"
date: 2020-12-26T14:16:17-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/spring.jpg"
alt_text: "a mechanical spring"
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
Summary: "let's continue with the basics of the Spring Container"
---


resources:

* [Spring Professional Study guide](https://pivotalcontent.s3.amazonaws.com/academy/Spring-Professional-Certification-Study-Guide.pdf)
* [Spring Boot documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)


{{<br 3>}}

### What is the preferred way to close an application context?  Does Spring Boot do this for you?

{{<br>}}

#### NON-WEB CONTEXT 

```Java
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public final class Boot {

    public static void main(final String[] args) throws Exception {
        ConfigurableApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");

        // add a shutdown hook for the above context...
        ctx.registerShutdownHook();

        // app runs here...

        // main method exits, hook is called prior to the app shutting down...
    }
}
```
* [Source](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-shutdown)

{{<br>}}

It's important to note here the 'Boot' class here has nothing to do with Spring Boot. This will link the application to the JVM and upon JVM shutdown the ApplicationContext to close.
* [registerShutdownHook API](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/support/AbstractApplicationContext.html#registerShutdownHook--)

{{<br>}}

Additionally, there is a close() function that will close the application context and destroy al of it's beans as well. Additionally this will remove a JVM linked shutdown hook if one is registered. 
* [close API](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/support/AbstractApplicationContext.html#close--)


If you're using Spring to create a web based application it will link itself with the container in order to shutdown properly; otherwise you'll need to register a shutdown hook yourself.

{{<br>}}

#### Spring-Boot shutdown Hook

Spring boot's [SpringApplication](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/SpringApplication.html) itself registers a shutdown hook with the JVM. There's also the capability for an application to create a bean that implements the [ExitCodeGenerator Interface](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/ExitCodeGenerators.java). In the below example from Spring's Boot documentation you can see 

```Java
@SpringBootApplication
public class ExitCodeApplication {

    @Bean
    public ExitCodeGenerator exitCodeGenerator() {
        return () -> 42;
    }

    public static void main(String[] args) {
        System.exit(SpringApplication.exit(SpringApplication.run(ExitCodeApplication.class, args)));
    }

}
```


* [Source](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-application-exit)

{{<br>}}

#### WEB Hooks
{{<br>}}

Spring integrates the ApplicationContext lifecycle with the web application itself so no manual steps are needed for the web application context.
{{<br>}}

{{<sand-quote 
"This section applies only to non-web applications. Spring’s web-based ApplicationContext implementations already have code in place to gracefully shut down the Spring IoC container when the relevant web application is shut down."
"https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-shutdown"
"Spring Core Documentation" 

>}}

{{<br 3>}}

### Next part of the study guide has multiple sections:
#### Can you describe:
    * Dependency injection using Java configuration?
    * Dependency injection using annotations (@Autowired)?
    * Component scanning, Stereotypes?–Scopes for Spring beans? 
    * What is the default scope?

{{<br>}}


I want to take a second and mention how I don't like the question above. It mentions using "Java configuration" and then mentions "using annotations"; this to me is a bit confusing as annotations are Java code. Regardless, I keep this in my head as thinking of the distinction of using Java class(es) as a central place to create Spring beans. I also will be splitting up some of the above questions based upon how large these posts become.

{{<br>}}
#### Dependency injection using Java configuration?
{{<tab>}} Java configuration would look like below. You can see that we're explicitly instantiating the beans inside of the methods. 


```Java
@Configuration
@PropertySource("classpath:application.properties")
public class AppConfig {

    @Bean
    public static PropertySourcesPlaceholderConfigurer
    propertySourcesPlaceholderConfigurer() {
        return new PropertySourcesPlaceholderConfigurer();
    }
    @Bean
    @Profile("test")
    UserRepo userRepoTest() {
        return new HashMapUserRepo();
    }

    @Bean
    @Profile("test")
    Greeter greeterTest(@Value("${greeting.test}") String greeting) {
        return new GreeterImpl(greeting);
    }

    @Bean
    @Profile("prod")
    Greeter greeterProd(@Value("${greeting.prod}") String greeting) {
        return new GreeterImpl(greeting);
    }

    @Bean
    @Profile("prod")
    UserRepo prodRepo() {
        return new HashMapUserRepo();
    }
}
```
{{<br>}}

While the above code is calling methods like normal, it's important to understand that by using the @Configuration annotation you're explicitly telling Spring to intercept method calls using Spring's AOP methods. This means beans are still created in a prototype like fashion regardless of the actual usage of the method being called. So if you call any method you'll only really be given the same object each time. Additionally, if you don't use the @Configuration annotation on a class then you'll have different effects when creating @Bean methods especially on intr-bean calls.

{{<br>}}

{{<raw>}}

<div class="w3-panel w3-leftbar w3-sand">
  <span style="font-size: 150px; line-height: 0.6em; opacity: 0.2">❝</span>
  <div class="w3-large w3-serif" style="margin-top: -40px">

<!--  -->
<div class="content">
<div class="title">Full @Configuration vs “lite” @Bean mode?</div>
<div class="paragraph">
<p>When <code>@Bean</code> methods are declared within classes that are not annotated with
<code>@Configuration</code>, they are referred to as being processed in a “lite” mode. Bean methods
declared in a <code>@Component</code> or even in a plain old class are considered to be “lite”,
with a different primary purpose of the containing class and a <code>@Bean</code> method
being a sort of bonus there. For example, service components may expose management views
to the container through an additional <code>@Bean</code> method on each applicable component class.
In such scenarios, <code>@Bean</code> methods are a general-purpose factory method mechanism.</p>
</div>
<div class="paragraph">
<p>Unlike full <code>@Configuration</code>, lite <code>@Bean</code> methods cannot declare inter-bean dependencies.
Instead, they operate on their containing component’s internal state and, optionally, on
arguments that they may declare. Such a <code>@Bean</code> method should therefore not invoke other
<code>@Bean</code> methods. Each such method is literally only a factory method for a particular
bean reference, without any special runtime semantics. The positive side-effect here is
that no CGLIB subclassing has to be applied at runtime, so there are no limitations in
terms of class design (that is, the containing class may be <code>final</code> and so forth).</p>
</div>
<div class="paragraph">
<p>In common scenarios, <code>@Bean</code> methods are to be declared within <code>@Configuration</code> classes,
ensuring that “full” mode is always used and that cross-method references therefore
get redirected to the container’s lifecycle management. This prevents the same
<code>@Bean</code> method from accidentally being invoked through a regular Java call, which helps
to reduce subtle bugs that can be hard to track down when operating in “lite” mode.</p>
</div>
</div>

<!--  -->

  </div>
  <a href="https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-basic-concepts" style="text-decoration: none">
    <p>Spring Core Documentation: Full @Configuration vs “lite” @Bean mode? <img src="/icons/link.svg" alt="link icon"></p>
  </a>
</div>

{{</raw>}}
{{<br>}}

I've also included the below example from the documentation where they have a simple configuration class:

```Java

@Configuration
public class AppConfig {

    @Bean
    public BeanOne beanOne() {
        return new BeanOne(beanTwo());
    }

    @Bean
    public BeanTwo beanTwo() {
        return new BeanTwo();
    }
}
```
* [Source: 1.12.4. Using the @Configuration annotation](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-configuration-annotation)

{{<br>}}

{{<tab>}} It's also important to think of Java annotation like XML configuration when it comes to integrating multiple disparate sources into a unified area. This means that you can have one Java configuration that will pull in XML files, as well as other @Configuration annotated classes


```Java
@Configuration
public class ServiceConfig {

    @Bean
    public TransferService transferService(AccountRepository accountRepository) {
        return new TransferServiceImpl(accountRepository);
    }
}

@Configuration
public class RepositoryConfig {

    @Bean
    public AccountRepository accountRepository(DataSource dataSource) {
        return new JdbcAccountRepository(dataSource);
    }
}

@Configuration
@Import({ServiceConfig.class, RepositoryConfig.class})
public class SystemTestConfig {

    @Bean
    public DataSource dataSource() {
        // return new DataSource
    }
}

public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(SystemTestConfig.class);
    // everything wires up across configuration classes...
    TransferService transferService = ctx.getBean(TransferService.class);
    transferService.transfer(100.00, "A123", "C456");
}
```
* [Injecting Dependencies on Imported @Bean Definitions](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-injecting-imported-beans)
* You can see here that we're pulling in configuration metadata from other classes, but this could just as easily have been XML documents
* Really one of the major points to understand, is that with this you're able to create a central repository where you instantiate and setup objects to be used throughout your application.

{{<br>}}


Alright, this has gone on long enough and I'll begin with this next:  *Dependency injection using annotations (@Autowired)*