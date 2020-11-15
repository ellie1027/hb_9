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
 
@SpringBootApplication
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

<br>
<br>

## Bean의 Scope

우리가 지금까지 등록한 Bean들에게는 전부 스코프가 있다. 그것도, 전부 싱글 스코프인 빈들만 사용해왔다. 
왜냐하면 기본 스코프가 싱글톤 스코프이기 때문이다.

<br>
<br>

싱글톤 스코프
: 애플리케이션 전반에 걸쳐서, 해당 빈의 인스턴스가 오직 한 개뿐인 것.

<br>

```java

@SpringBootApplication
public class DemoSpringApplication{
    
    public static void main(String[] args){
        SpringApplication.run(DemoSpringApplication.class, args);   
    }    

}

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    Single single;
    
    @Autowired
    Proto proto;

    @Override
    public void run(ApplicationArguments args) throws Exception{
        System.out.println(proto); //이 클래스에 주입된 proto
        System.out.println(single.proto); //single 클래스가 참조하고 있는 proto
        
    }
}

@Component
public class Single{
    
    @Autowired
    Proto proto;
    
    public Proto getProto(){
        return proto;
    }   
}

@Component
public class Proto{
}

//결과; 같은 인스턴스를 씀.
me.whiteship.demospring.Proto@6b34931
me.whiteship.demospring.Proto@6b34931

```

<br>

웬만한 경우에는 거의 싱글톤 스코프를 쓰게 된다.

프로토타입 스코프 
: 매번 새로운 인스턴스를 만들어서 써야하는 스코프
<br>
* Request Scope
* Session Scope
* WebSocket Scope
...

<br>

```java

@SpringBootApplication
public class DemoSpringApplication{
    
    public static void main(String[] args){
        SpringApplication.run(DemoSpringApplication.class, args);   
    }    

}

@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception{
        System.out.println(ctx.getBean(Proto.class)); 
        System.out.println(ctx.getBean(Proto.class)); 
        System.out.println(ctx.getBean(Proto.class)); 

        System.out.println(ctx.getBean(Single.class)); 
        System.out.println(ctx.getBean(Single.class)); 
        System.out.println(ctx.getBean(Single.class)); 
        
        //싱글톤 타입이 참조하는 프로토 타입
        System.out.println(ctx.getBean(Single.class).getProto()); 
        System.out.println(ctx.getBean(Single.class).getProto()); 
        System.out.println(ctx.getBean(Single.class).getProto()); 
        
    }
}

@Component
public class Single{
    
    @Autowired
    Proto proto;
    
    public Proto getProto(){
        return proto;
    }   
}

@Component @Scope("prototype") //프로토 클래스에 스코프 애노테이션을 추가한다.
public class Proto{
}

//결과; 프로토 타입은 빈을 받아 올때마다 새로운 인스턴스를 생성한다.
me.whiteship.demospring.Proto@57435801
me.whiteship.demospring.Proto@2da66a44
me.whiteship.demospring.Proto@527fc8e

me.whiteship.demospring.Single@6b34931
me.whiteship.demospring.Single@6b34931
me.whiteship.demospring.Single@6b34931

//싱글톤 타입이 프로토 타입을 참조할 경우 프로토 타입의 특성상 서로 다른 인스턴스를 뱉어야
하는데 똑같은 인스턴스를 뱉음
me.whiteship.demospring.Proto@2da66a44
me.whiteship.demospring.Proto@2da66a44
me.whiteship.demospring.Proto@2da66a44

```

<br>

> 프로토타입 빈이 싱글톤 빈을 참조하면? 아무 문제 없음.
> 싱글톤 빈은 인스턴스가 하나이기 때문에 개발자가 의도한 대로 항상 같은 인스턴스가 들어옴.
> 프로토타입은 매번 꺼낼 때마다 새로운 인스턴스가 나가는데, 프로토타입 빈이 참조한 싱글톤타입 빈은
> 항상 같은 인스턴스가 나간다. 
> 때문에 전혀 문제되지 않는다.

<br>

싱글톤 빈이 프로토 타입 빈을 참조하면?
* 계속 새로운 인스턴스가 생성되기 때문에 싱글톤 빈이 참조하는 프로토타입 빈이 업데이트 되지 않음.

해결하려면?
* scoped-proxy
* Object-Provider
* Provider (표준)

## scoped-proxy

```java
//scoped-proxy
@Component @Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS) //프로토 클래스에 스코프 애노테이션을 추가한다.
public class Proto{
}

```

프록시를 쓴다. 라는 것은 이 빈을, 다른 빈들이 참조할 때 프록시로 감싸라는 것이다.
왜 프록시를 써야하는가? 싱글 스코프의 빈이 프로토 타입 빈을 참조할때 직접적으로 참조하면 안되고
프록시를 거쳐서 참조해야 하기 때문에 프록시를 써야 한다. 이럴 경우 빈 호출시마다 프록시의 인스턴스가 
만들어지고 해당 인스턴스가 호출된다.
<br>
* 자바 jdk 1.8에 있는 프록시는 인터페이스 기반의 프록시밖에 못 만드므로 proxyMode를 
알려주어야 한다.

<br>

## Object-Provider

```java
//Object-Provider
@Component @Scope("prototype")
public class Proto{
}

@Component
public class Single{
    
    @Autowired
    private ObjectProvider<Proto> proto;
    
    public Proto getProto(){
        return proto.getIfAvailable();
    }   
}

```

<br>

코드 자체에 대한 변경이 일어나는 방법.

<br>
<br>

# 싱글톤 객체 사용시 주의할 점
* 프로퍼티가 공유된다.<br>
: 따라서 멀티 스레드 환경에서 프로퍼티 값(예를 들자면 int value = 0; 같이 선언되는 값들)이 여러군데서 변경되면서 의도하지 않은 값이 출력될 수도 있다.<br>
따라서 반드시 스레드 세이프한 방법으로 코딩해야한다.<br>
* ApplicationContext 초기 구동 시 인스턴스 생성.<br>
: 따라서 애플리케이션 구동할 때 시간이 많이 걸릴 수도 있음.

