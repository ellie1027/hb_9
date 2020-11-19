---
title: '[스프링 코어] Inversion of Control 컨테이너 #4'
date: 2020-11-19 16:21:13
category: 'spring'
draft: false
---

- **REFERENCES** : [인프런 <스프링 프레임워크 핵심 기술> (백기선)](https://www.inflearn.com/course/spring-framework_core/)
<br>

## 학습 목표
* 스프링 프레임워크의 핵심 기술 IoC, AOP, PSA를 이해한다.
* 스프링 프레임워크 IoC 컨테이너의 다양한 기능을 사용할 수 있다.
* 다양한 방법으로 빈을 정의하고 의존 관계를 주입할 수 있다.
* 스프링 AOP를 사용하여 Aspect를 모듈화 할 수 있다.
* 그밖에 다양한 스프링 핵심 기술을 이해하고 활용할 수 있다.
<br>
    
## [1] Environment - 프로파일 / 프로퍼티

### applicationContext가 가지고 있는 기능 중 하나

```java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
//...
}

public class AppRunner implements ApplicationRunner {
    
    @Autowired
    ApplicationContext ctx;
    
    @Override
    public void run(ApplicationArguments args) throws Exception {
        Enviroment enviroment = ctx.getEnviroment();
        System.out.println(Arrays.toString(enviroment.getDefaultActiveProfiles())); 
        //이 default 프로파일은 따로 설정하지 않아도 항상 적용이 되는 프로파일이다. 지금까지 우리가 만들고 등록했던 빈들도 전부 이 환경설정에 속한다.
    }
}
```
프로파일과 프로퍼티를 다루는 인터페이스

### 프로파일
1) 빈들의 그룹
2) '환경 설정'을 의미한다 - 각각의 환경에 따라서 다른 빈들을 써야 하는 경우, 특정한 환경에서만 어떠한 빈을 등록해야 하는 경우

## 프로파일 유즈케이스
1) 테스트 환경에서는 a라는 빈을 사용하고, 배포 환경에서는 b라는 빈을 쓰고 싶을 때
2) 이 빈은 모니터링 용도이므로 테스트할 때는 필요가 없고 배포할 때만 등록이 되면 좋을 때

```java
//test라는 프로파일로 애플리케이션을 실행할 때만 사용되는 빈 설정 파일이 됨.
public class TestBookRepository implements BookRepository {
    
}

@Configuration
@Profile("test")
public class TestConfiguration  {

    @Autowired
    TestBookRepository testBookRepository;
    
    @Bean
    public BookRepository bookRepository(){
        return new TestBookRepository();    
    }

}
```

**프로파일을 설정하는 방법(Intellij Ultimate 기준)**
<br>
1) Run/Debug Configuration -> active profiles 에 "프로파일 명"
<br>
2) 1번 항목이 없을 경우 Run/Debug Configuration의 VM options에 -Dspring.profiles.active="프로파일 명" 
<br>
3) 빈 에다가 바로 설정하는 방법
    * 클래스에 정의 
        - @Configuration @Profile("test") / @Component @Profile("test")
    * 메소드에 정의
        - @Bean @Profile("test")
4) @ComponentScan 대상인 애노테이션과도 같이 쓸 수 있다

```java
@Repository
@Profile("test")
public class TestBookRepository implements BookRepository {
    
}

@Configuration
public class TestConfiguration  {

    @Autowired
    TestBookRepository testBookRepository;
    
    @Bean
    public BookRepository bookRepository(){
        return new TestBookRepository();    
    }

}
```

5) 테스트 용 @activeprofiles

> **빈 설정 시 표현식을 활용하여 이런 방법으로도 쓸 수 있다.**<br>
> @Repository @Profile("!prod") <br>
> @Repository @Profile("test&prod") <br>
> @Repository @Profile("test|prod") <br>

## 프로퍼티
1) 다양한 방법으로 정의할 수 있는 설정 값
2) Environment의 역할은 프로퍼티 소스 설정 및 프로퍼티 값 가져오기
<br>

* application에 등록된 여러 key/value 쌍으로 제공되는 프로퍼티에 접근할수 있는 기능
* 계층형으로 접근

```java
public interface ApplicationContext extends EnvironmentCapable, 
        ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
//...
}

public class AppRunner implements ApplicationRunner {
    
    @Autowired
    ApplicationContext ctx;
    
    @Override
    public void run(ApplicationArguments args) throws Exception {
        Enviroment enviroment = ctx.getEnviroment();
        System.out.println(Arrays.toString(enviroment.getProperty("app.name"))); 
        //이 default 프로파일은 따로 설정하지 않아도 항상 적용이 되는 프로파일이다. 지금까지 우리가 만들고 등록했던 빈들도 전부 이 환경설정에 속한다.
    }
}
```


## 프로퍼티는 우선순위가 있음

* StandardServletEnvironment의 우선순위
 - ServletConfig 매개변수
 - ServletContext 매개변수
 - JNDI (java:comp/env/)
 - JVM 시스템 프로퍼티(-Dkey="value")
 - JVM 시스템 환경 변수(운영체제 환경 변수)

@PropertySource
 - Environment를 통해 프로퍼티 추가하는 방법
 
```java
@SpringBootApplication
@PropertySource("classpath:/app.properties")
public class DemoSpringApplication {
    
    public static void main(String[] args){
        SpringSpplication.run(DemoSpringSpplication.class, args);
    }
}
```
 
## 스프링 부트의 외부 설정 참고
* 기본 프로퍼티 소스 지원(application.properties)
* 프로파일까지 고려한 계층형 프로퍼티 우선 순위 제공














