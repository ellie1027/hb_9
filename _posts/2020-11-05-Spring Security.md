---
layout: post
title: "Spring Framework + RestApi + Ajax + Jwt Token + Spring Security (1)"
tags: [security]
comments: true
---

##이 포스팅에서는...
현재 진행중인 토이 프로젝트에서 로그인/회원가입을 위하여 스프링 시큐리티를 사용하기로 하였다. 
<br>
그런데 내가 하고 있는 프로젝트는 스프링 5.2.7 버전이었는데, 참고 할 만한 좋은 글들이 전부 스프링 부트를 기준으로 쓰여져서, 레퍼런스 찾기가 쉽지 않았다.<br>
(다음 프로젝트는 무조건 부트로...)<br>
<br>
또, 기존 프로젝트는 xml configuration 기반으로 진행하고 있었는데, spring security 설정을 java로 하면서 java configuration 기반으로 아예 바꿨다.<br>
확실히 java configuration 이 개발적인 면에서 좀더 활용도가 높고, 확장 가능성이 큰 것 같다.<br>(spring 3.0 이상부터 java 설정을 제공함)
<br>
<br>
따라서 이 포스팅 시리즈에서는 
1) java configuration + spring security configuration
2) spring security란?  
3) spring security를 이용한 여러 방법
4) csrf 방지
4) jwt를 이용한 토큰 기반 인증
등을 다룰 것이다.
<br><br>

## 1) 의존성 추가하기 
<br>
먼저 현재 사용하고 있는 스프링 버전에 맞춰 의존성을 추가해준다. <br>
만약 스프링 부트를 사용하고 있다면 버전 관리의 필요성이 없지만 그렇지 않다면 버전 관리는 매우 중요하다.<br>

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
## 2) 기본 java configuration

![중간 이미지](/images/spring_security_1_00.png)
>ss
<br>
![중간 이미지](/images/spring_security_1_01.png)
>java configuration 패키지 형태

1. WebConfig.java
- DispatcherServlet 등록

```java
@Configuration //스프링 설정 파일 등록
public class WebConfig 
        extends AbstractAnnotationConfigDispatcherServletInitializer {
    
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
<br>
자바 기반 스프링 설정을 사용할 수 있도록 DispatcherServlet 을 등록하는 클래스이다. 
간단하게 AbstractAnnotationConfigDispatcherServletInitializer 클래스를 상속받아서 만든다.(스프링 3.2 부터 사용 가능한 클래스!)
WebApplicationInitializer 인터페이스의 onStartup 메서드를 구현하는 방법도 있지만 나는 해당 클래스를 상속받는 편이 좀 더 간단한 것 같다.
<br>
<br>
해당 클래스를 상속받으면 다음과 같은 메서드를 사용할 수 있다.
<br>
* getRootConfigClasses() : root application context 설정파일을 등록한다.
* getServletConfigClasses() : DispatcherServlet application context 설정파일을 등록한다.
* getServletMappings() : 브라우저에서 요청한 주소 패턴을 보고 스프링에서 처리할지 말지를 결정하는 메서드. 배열 형식이므로 요청 주소를 여러개 등록할 수 있다.
<br>
<br>

2. Root Config.java

```java
@Configuration
@ComponentScan(basePackages= {"your packages"})
public class RootConfig {

}
```

3. ServletConfig.java

```java
@EnableWebMvc
@ComponentScan(basePackages= {"your packages"})
public class ServletConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/resources/**").addResourceLocations("/resources/");
    }

    @Bean
    public UrlBasedViewResolver tilesViewResolver() {
        UrlBasedViewResolver urlBasedViewResolver = new UrlBasedViewResolver();
        urlBasedViewResolver.setViewClass(TilesView.class);
        urlBasedViewResolver.setOrder(1);
        return urlBasedViewResolver;
    }

    @Bean
    public TilesConfigurer tilesConfigurer() {
        TilesConfigurer tilesConfigurer = new TilesConfigurer();
        tilesConfigurer.setDefinitions("/WEB-INF/views/layout/tiles-layout.xml");
        return tilesConfigurer;
    }

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        InternalResourceViewResolver bean = new InternalResourceViewResolver();
        bean.setViewClass(JstlView.class);
        bean.setPrefix("/WEB-INF/views/");
        bean.setSuffix(".jsp");
        registry.viewResolver(bean);
    }

    @Bean
    public CommonsMultipartResolver multipartResolver() {
        CommonsMultipartResolver resolver = new CommonsMultipartResolver();
        resolver.setDefaultEncoding("utf-8");
        return resolver;
    }
}
```
<br>
<br>
WebMvcConfigurer 클래스를 상속받아 resourceHandler, ViewResolver 등 여러 빈들을 등록해주는 클래스이다.
지금 프로젝트에서 기본 ViewResolver 대신 Tiles를 사용하고 있으므로, Tiles ViewResolver 를 먼저 등록해주었다.







