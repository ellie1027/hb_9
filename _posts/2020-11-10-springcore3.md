---
layout: post
title: "Spring Core - IoC Container (3)"
tags: [spring]
comments: true
---

**references; Inflearn - 스프링 프레임워크 핵심 기술 (백기선)**

<br>
<br>

## 학습 목표
* 스프링 프레임워크의 핵심 기술 IoC, AOP, PSA를 이해한다.
* 스프링 프레임워크 IoC 컨테이너의 다양한 기능을 사용할 수 있다.
* 다양한 방법으로 빈을 정의하고 의존 관계를 주입할 수 있다.
* 스프링 AOP를 사용하여 Aspect를 모듈화 할 수 있다.
* 그밖에 다양한 스프링 핵심 기술을 이해하고 활용할 수 있다.
<br>
<br>

## 1. Spring IoC 컨테이너와 빈 (3) : @Component와 컴포넌트 스캔

### 컴포넌트 스캔
> 스프링 3.1부터 도입됨<br>
> 빈을 등록하기 위하여 스캔 위치를 설정하는 것<br>

다음과 같은 설정 방법이 있다.

* **basePackage(String)** : 해당 패키지를 기준으로 하위에 있는 클래스 파일들을 스캔한다. 
 그런데 String이 type-safe 하지 않으므로 basePackageClasses(Class)를 쓰는 것을 좀 더 추천한다. 해당 클래스 기준으로 그 다음에 있는 클래스들을 전부 스캔할 수 있다.<br>
* **excludeFilters / includeFilters** : 빈으로 등록하지 않고 걸러내도록 설정하는 것. 어떤 애노테이션을 스캔 할지 안 할지를 결정하는 것이다.<br>

<br>
<br>

### @Component
이 애노테이션을 들고 있으면 빈으로 등록이 된다. 아래 애노테이션은 전부 @Component의 애노테이션이다.
* @Repository
* @Service
* @Controller
* @Configuration
<br>
<br>
다만 싱글톤 스코프기 때문에 애플리케이션 컨텍스트에 초기에 모두 생성이 되므로, 
등록해야할 빈이 많을 경우에는 초기 구동 시간이 오래 걸릴 수 있다.
<br>
<br>
그런데 처음 구동할 때 딱 한번 생성되는 것이므로 큰 문제는 없으나, 시간이 걸리는 것 자체에 예민한 사람이라면
스프링 5부터 들어간 function을 사용하여 빈을 등록하는 방법을 고려해 볼 수 있다. 
<br>
<br>
리플렉션과 프록시를 이용한 방법은 성능에 영향을 주는데, functional 한 방법을 이용하여 빈을 등록하면 그런 기술을 
전혀 사용하지 않아도 되기 때문에 성능상의 이점(애플리케이션 초기 구동 타임)에 조금은 도움이 될 수 있다.

인스턴스를 만들어 사용하는 functional 한 방법의 configuration 예제를 보자.

```java
 
public class DemoSpringApplication{

    @Autowired
    MyService myService;
    
    public static void main(String[] args){
        new SpringApplicationBuilder()
            .sources(DemoSpringApplication.class)
            .initializers((ApplicationContextInitializer<GenericApplicationContext>)
            applicationContext -> {
                applicationContext.registerBean(MyService.class);
        }) 
        .run(args);     
    }    

}

```
## 동작 원리
* @ComponentScan은 스캔할 패키지와 애노테이션에 대한 정보
* 실제 스캐닝은 ConfigurationClassPostProcessor라는 BeanFactoryPostProcessor에 의해 처리됨.

>BeanFactoryPostProcessor은 다른 모든 빈들 (개발자가 만든 빈 혹은 @Bean과 같은 애노테이션으로 등록한 빈) 을 만들기 이전에 컴포넌트 스캔을 하여 적용된다. 
>빈 팩토리 프로세서와 유사하지만 실행 시점이 다르다. 








