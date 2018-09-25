# Micronaut Vs Spring

Hey, there we are!. Today we are going to talk about 'Micronaut' a JVM modular microframework. Micronaut is developed by Grails code team and they claim a minimal JAR size (around 8 Mb) and support Java, Groovy and Kotlin language. The syntax and annotation paradigm is very similar to Spring. Both, Spring and Micronaut, use reflexion in order to process java annotations, something that I don't like because break completely the object-oriented paradigm and encapsulation. The main different of Micronaut is that they use reflexion at compile time, instead of runtime, this will give you a better start time.

On the other hand, they also implement their own dependency injection, based on annotation and an application context that handler all beans initialization and tracking (pretty much as Spring).

So well, let move on to a real example in order to see, about what we are talking about. We are going to setup [this petstore](https://github.com/micronaut-projects/micronaut-examples/tree/master/petstore) example application and have a look how much memory they use at start time. 

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/petShop_example_micronaut_memory.png">

```
java -> petstore_pets_1 -> 162.5Mb
java -> petstore_mail_1 -> 58.52Mb
java -> petstore_offers_1 -> 149.6Mb
```

Mmm ... It's too far from this 8Mb that 'OCI', the Grails core team, claims. Why?. Maybe because they were talking about JAR size, however, this JAR will never work without a JVM. I am sure that if you use Java 11 and modules you will be able to reduce this JAR + JVM size. [We already did the effort](https://github.com/pjgg/javaMicroservicePOC), and we got a microservice of 40 Mb using vertx framework.

## Hello World Micronaut Vs Hello World Spring

In order to make a fair comparison between both framework, we are going to set up the "same" HelloWorld application.

- [Micronaut helloWorld app](https://github.com/micronaut-projects/micronaut-examples/tree/master/hello-world-java)
- [Spring helloWorld app](https://spring.io/guides/gs/rest-service/)

### Micronaut Memory/Thread usage

We are going to run a performance test with 100 concurrent users that make one request per second, in order to see how each framework behaves in terms of memory consumption and threads.

1. Micronaut Memory

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/helloWorld.png">

```
around ... 76.3Mb
```

2. Micronaut Threads

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/helloWorld-micro.png">

Threads details

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/helloWorld-micro-2.png">

The behavior's as expected. Micronaut is an asynchronous none blocking threads framework (reactor), so we expected this behavior a fixed thread pool (even one thread would be enough) that handler all request. This is quite handy when you have to design and develop a middleware that acts as a proxy of several pieces each one with their own latency.

### Spring Memory/Thread usage

1. Spring Memory

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/helloWorld-spring-memory.png">

```
around ... 233.7Mb
```

2. Spring Threads

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/helloWorld-spring-threads.png">

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/spring5.1.0.RELEASE-helloWorld-threads.png">

... 1 min later the test was stopped...

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/spring-helloWorld-threads3.png">

Threads details

<img align="center" src="https://github.com/pjgg/micronautVsSpring/blob/master/helloWorld-Spring-Threads2.png">

The behavior's also as bad as expected. Spring has a huge legacy and has a lot of libraries that break the main golden rule of all 'asynchronous none blocking' frameworks:

> Don't Block the Event Loop

This is very important to keep in mind because if you use Micronaut you should pay attention to all of these third party libraries that you use in order to don't finish up as Spring. Anyway, it's quite interesting for this test, because this is a hello world application (there are not third-party libraries), and Spring behaves like an old synchronous and blocking framework.

## Conclusion

Micronaut it's a fresh air in the Spring world, they keep the annotation paradigm live at the same time that they remove all legacy 'mess'. The result was a slim approach that handler in a smooth way all concurrency issues. I miss an eventbus as other JVM frameworks have, as Lagom, Akka, Vertx... that decouple the microservice (the business logic) with 'how to deliver' this logic. 