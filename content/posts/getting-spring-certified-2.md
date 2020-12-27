---
title: "Spring Certification: Container, Dependency and IOC 2"
sub_title: "Reviewing the Study guide"
date: 2020-12-25T22:11:27-06:00
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
* [Spring Professional Documentation - Testing](https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#testing)

{{<br >}}

<!-- let's continue with the 'Container, Dependency and IOC' section of the study guide, -->

A lot of the study guide questions seem simple at the start but when you begin looking at the actual functionality you soon discover there's lots of extra material that you can review.

Take the below question for example, there's literally a single annotation needed to perform this task *@ContextConfiguration*; however, because of the additional testing framework required to be integrated you must also consider how the Spring Framework integrates with other technology. The good news is that Spring is notorious for bringing together different technologies into a comprehensive and sensible way.

{{<br>}}

### How are you going to create an ApplicationContext in an integration test? 

{{<br>}}

It's relatively simple to create an ApplicationContext, Spring provides the annotation [@ContextConfiguration](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/ContextConfiguration.html) that allows a test to use configuration metadata in order to load up an ApplicationContext.[Example in the documentation](https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-contextconfiguration), but THIS DOES NOT INCLUDE THE TESTING FRAMEWORK SETUP CODE.


{{<br>}}

[SpringRunner source](https://github.com/spring-projects/spring-framework/blob/master/spring-test/src/main/java/org/springframework/test/context/junit4/SpringRunner.java)

{{<br>}}

--  [JUnit 4 -- Testing Doc](https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#testcontext-junit4-runner) --
```Java
                                         // This indicates that we're using JUnit 4, but is just an Alias for 
@RunWith(SpringRunner.class)             // org.springframework.test.context.junit4.SpringJUnit4ClassRunner
@ContextConfiguration(classes = {AppConfig.class})      // Here we're loading up the configuration metadata from AppConfig.class
@ActiveProfiles(profiles = "test")      // This isn't required, but will ensure that Spring only loads up the beans under the "test" profile
public class ContextTesting {

    @Autowired      // Annotation provides autowiring of UserRepo bean
    UserRepo userRepo;

    @Autowired      // Annotation provides autowiring of Spring Environment bean
    Environment environment;

    @Test           // This will run the test and load up the Spring Application Context 
    public void contextLoads() {

    }
    ...
}
```
{{<br>}}

--  [JUnit 5 | JUnit Jupiter -- Testing Doc](https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#testcontext-junit-jupiter-extension) --
```Java
@ExtendWith(SpringExtension.class)             // This indicates that we're using JUnit 5 / Jupiter with the SpringRunner.class
@ContextConfiguration(classes = {AppConfig.class})      // Here we're loading up the configuration metadata from AppConfig.class
@ActiveProfiles(profiles = "test")      // This isn't required, but will ensure that Spring only loads up the beans under the "test" profile
public class ContextTesting {

    @Autowired      // Annotation provides autowiring of UserRepo bean
    UserRepo userRepo;

    @Autowired      // Annotation provides autowiring of Spring Environment bean
    Environment environment;

    @Test           // This will run the test and load up the Spring Application Context 
    public void contextLoads() {

    }
    ...
}
```
{{<br>}}

{{<raw >}}

<div class="w3-panel w3-leftbar w3-sand">
  <span style="font-size: 150px; line-height: 0.6em; opacity: 0.2">❝</span>
  <div class="w3-large w3-serif" style="margin-top: -40px">

<!--  -->

<div class="paragraph">
<p><code>@WebAppConfiguration</code> is a class-level annotation that you can use to declare that the
<code>ApplicationContext</code> loaded for an integration test should be a <code>WebApplicationContext</code>.
The mere presence of <code>@WebAppConfiguration</code> on a test class ensures that a
<code>WebApplicationContext</code> is loaded for the test, using the default value of
<code>"file:src/main/webapp"</code> for the path to the root of the web application (that is, the
resource base path). The resource base path is used behind the scenes to create a
<code>MockServletContext</code>, which serves as the <code>ServletContext</code> for the test’s
<code>WebApplicationContext</code>.</p>
</div>

<!--  -->

  </div>
  <a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-webappconfiguration" style="text-decoration: none">
    <p>Spring Testing Documentation: @WebAppConfiguration <img src="/icons/link.svg" alt="link icon"></p>
  </a>
</div>

{{</raw>}}
* This is a highly powerful annotation that helps mock out a web context so you can ensure proper testing is performed.
* [Baeldung](https://www.baeldung.com/spring-webappconfiguration) has an article on it's general usage as well. 

<!--  -->

* [3.4.1. Spring Testing Annotations](https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#integration-testing-annotations-spring)
  * You can find  list of information on the documentation page.

{{<br>}}

Spring's testing framework annotations provide support for the framework as well as for use cases. Here's a short list of examples:

* @ActiveProfiles does just what it says; you let Spring know which profile you'd like to use and you can use the beans for that profile. This is a great example of how Spring encourages you to test the logic of your code, because you wouldn't want to hit a production database while you're still working on a feature or a bug.

* @DirtiesContext, sometimes when you're doing test things in the context will become changed, and when that happens you don't want it to affect your other tests so Spring provides an easy to use annotation, along with different 'classMode' setup to allow you to control this for yourself.

* Additionally, there are several annotations related to sql and databse operations like commit and rollback.
  * With these you can help to control what you're actually testing to ensure the results you're expecting are what is true.


{{<br 2>}}

{{<raw>}}
<div class="w3-panel w3-leftbar w3-sand">
  <span style="font-size: 150px; line-height: 0.6em; opacity: 0.2">❝</span>
  <div class="w3-large w3-serif" style="margin-top: -40px">
  <p>Spring’s testing annotations include the following:</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-bootstrapwith"><code>@BootstrapWith</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-contextconfiguration"><code>@ContextConfiguration</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-webappconfiguration"><code>@WebAppConfiguration</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-contexthierarchy"><code>@ContextHierarchy</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-activeprofiles"><code>@ActiveProfiles</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-testpropertysource"><code>@TestPropertySource</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-dynamicpropertysource"><code>@DynamicPropertySource</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-dirtiescontext"><code>@DirtiesContext</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-testexecutionlisteners"><code>@TestExecutionListeners</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-commit"><code>@Commit</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-rollback"><code>@Rollback</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-beforetransaction"><code>@BeforeTransaction</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-aftertransaction"><code>@AfterTransaction</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-sql"><code>@Sql</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-sqlconfig"><code>@SqlConfig</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-sqlmergemode"><code>@SqlMergeMode</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#spring-testing-annotation-sqlgroup"><code>@SqlGroup</code></a></p>
</li>
</ul>
</div>
  <a href="https://docs.spring.io/spring-framework/docs/current/reference/html/testing.html#integration-testing-annotations-spring" style="text-decoration: none">
    <p>Spring Testing Documentation: 3.4.1. Spring Testing Annotations  <img src="/icons/link.svg" alt="link icon"></p>
  </a>
</div>
{{</raw>}}

{{<br 1>}}

