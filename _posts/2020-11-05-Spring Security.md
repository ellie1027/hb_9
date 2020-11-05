---
layout: post
title: "Spring Security (1) 의존성 추가 및 기본 설정"
tags: [security]
comments: true
---

## 0) 스프링 시큐리티를 배운 이유

현재 진행중인 토이 프로젝트에서 로그인/회원가입을 위하여 스프링 시큐리티를 사용하기로 하였다. 구글링을 통하여 블로그 포스팅, 책 등을 뒤져봤지만 전체적인 흐름이 눈에 
들어오지 않아서 일단 유투브에서 강의를 찾아 듣고 프로젝트에 적용해보았다.<br>
<br>
그런데 내가 하고 있는 프로젝트는 스프링 5.2.7 버전이었는데, 참고 할 만한 좋은 글들이 전부 스프링 부트를 기준으로 쓰여져서, 레퍼런스 찾기가 쉽지 않았다.<br>
(다음 프로젝트는 무조건 부트로...)<br>
<br>
또, 기존 프로젝트는 xml configuration 기반으로 진행하고 있었는데, spring security 설정을 java로 하면서 java configuration 기반으로 아예 바꿨다.<br>
확실히 java configuration 이 개발적인 면에서 좀더 활용도가 높고, 확장 가능성이 큰 것 같다.<br>(spring 3.0 이상부터 java 설정을 제공함)
<br>
<br>
따라서 이 포스팅 시리즈에서는 기본적인 java configuration 부터 시작하여 스프링 시큐리티의 기본 설정, jwt를 이용한 토큰 기반 인증을 구현하는 법을 다룰 것이다.<br>
<br>
## 1) 의존성 추가하기 
<br>
먼저 현재 사용하고 있는 스프링 버전에 맞춰 의존성을 추가해준다. <br>
만약 스프링 부트를 사용하고 있다면 버전 관리의 필요성이 없지만 그렇지 않다면 버전 관리는 매우 중요하다.<br>

<br>
```xml
<!-- spring security -->
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-web</artifactId>
        <version>5.2.7.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-config</artifactId>
        <version>5.2.7.RELEASE</version>
    </dependency>
    <!--<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency> -->
```

## 2) java configuration

![중간 이미지](/images/spring_security_1_01.png)
- java configuration 패키지 형태

1. WebConfig.java

```java
@Configuration
@ComponentScan(basePackages="your packages")
public class WebConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{ RootConfig.class, AppSecurityConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{ ServletConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```

자바 기반 스프링 설정을 사용할 수 있도록 DispatcherServlet 을 등록하는 클래스이다. 
간단하게 AbstractAnnotationConfigDispatcherServletInitializer 클래스를 상속받아서 만든다.(스프링 3.2 부터 사용 가능한 클래스!)
WebApplicationInitializer 인터페이스의 onStartup 메서드를 구현하는 방법도 있지만 나는 해당 클래스를 상속받는 편이 좀 더 간단한 것 같다.
<br>
<br>
해당 클래스를 상속받으면 다음과 같은 메서드를 사용할 수 있다.
<br>
* getRootConfigClasses()
    - root application context 설정파일을 등록한다.
* getServletConfigClasses()
    - DispatcherServlet application context 설정파일을 등록한다.
<br>






