---
layout: post
title: "restapi + ajax + jwt + spring security (1) java configuration"
tags: [security]
comments: true
---

## 0. 기본 설명

> ### 프로젝트 환경
> version : spring 5.2.7<br>
> jdk : java 1.8<br>
> ide : intellij<br>
> was : tomcat 8.5<br>
> build : maven<br>
<br>

### 목차
1) <mark>java configuration</mark>
2) spring security란?  
3) spring security configuration
4) spring security - basic authentication, form based authentication
5) spring security - csrf
6) spring security - jwt toekn 
<br><br>

### java configuration란?
기존 xml기반 설정 파일을 java 설정으로 바꾸는 것이다.<br>
스프링 3.2 버전부터 제공하며 확장성, 활용성 등이 기존 xml 기반 설정보다 뛰어나기 때문에 개발자들이 선호한다.<br>
xml 기반으로 설정 파일을 작성하는 것과 크게 다를 것은 없다. 그냥 똑같은 설정인데 언어가 java로 바뀌었을 뿐이다.<br>
<br><br>
### spring framework 구동 순서
![중간 이미지](/images/spring_security_1_00.png)
<br>
![중간 이미지](/images/spring_security_1_01.png)
<br><br>

### 기본 java configuration
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







