---
layout: post
title: "restapi + ajax + jwt + spring security (1)"
tags: [security]
comments: true
---

## 1. Java Configuration

> 프로젝트 환경
> version : spring 5.2.7<br>
> jdk : java 1.8<br>
> ide : intellij<br>
> was : tomcat 8.5<br>
> build : maven<br>
<br>

### 시리즈 목차
1) <mark><strong>java configuration</strong></mark><br>
2) spring security란? 
3) spring security configuration<br>
4) spring security - basic authentication, form based authentication<br>
5) spring security - csrf<br>
6) spring security - jwt toekn<br> 
<br><br>

### java configuration란?
스프링 프레임워크의 기존 xml기반 설정 파일(ex; *-context.xml, web.xml...)을 java 설정으로 바꾸는 것이다. 
스프링 3.1 버전부터 제공하며 확장성, 활용성 등이 뛰어나기 때문에 개발자들이 선호한다.
<br><br>
### xml / java configuration 비교
![중간 이미지](/images/spring_security_1_00.png)
<br><br>
spring framework 구동 순서를 따라서 xml configuration과 java configuration을 비교한다.
<br>
>웹 어플리케이션이 실행되면 WAS에 의해 web.xml이 로딩되고, web.xml에 등록되어 있는 ContextLoaderListener가 메모리에 생성된다.
>ContextLoaderListener 클래스는 ApplicationContext를 생성한다. 

<p class="notice--info">
<strong>Watch out!</strong><br> 
ContextLoaderListener? 
Servlet의 생명주기를 관리해준다.
Servlet을 사용하는 시점에 Servlet Context에 ApplicationContext 등록, Servlet이 종료되는 시점에 ApplicationContext 삭제
</p>

java configuration의 경우, Spring은 애플리케이션을 구축할 수 있는 두 개의 컴포넌트를 제공한다.
* WebApplicationInitializer.class :  DispatcherServlet과 ContextLoaderListener 등록을 모두 구현해주어야 함.
<mark>* AbstractAnnotationConfigDispatcherServletInitializer.class : 내부적으로 서블릿 컨텍스트 초기화 작업이 이미 구현되어 있음.</mark>
                               
>ContextLoaderListener는 root config 관련 파일을 로딩한다. (주로 db, log 등의 common beans)
>최초의 웹 어플리케이션 요청이 오면 DispatcherServlet 가 생성
>DispatcherServlet 은 servlet config 관련 파일을 로딩한다.
<p class="notice--info">
<strong>Watch out!</strong><br> 
DispatcherServlet? 
FrontController의 역할을 수행. 어플리케이션으로 들어오는 요청을 받아 알맞은 Controller에게 전달 후 응답을 받아, 요청에 따른 응답을 어떻게 할지 결정함.
</p>

AbstractAnnotationConfigDispatcherServletInitializer.class 를 상속받으면 다음과 같은 메서드를 사용할 수 있다.
<br>
* getRootConfigClasses() : root application context 설정파일을 등록한다.
* getServletConfigClasses() : DispatcherServlet application context 설정파일을 등록한다.
* getServletMappings() : 브라우저에서 요청한 주소 패턴을 보고 스프링에서 처리할지 말지를 결정하는 메서드. 배열 형식이므로 요청 주소를 여러개 등록할 수 있다.
<br>
<br>

1. WebConfig.java

```java
//sample code
@Configuration 
public class WebConfig 
        extends AbstractAnnotationConfigDispatcherServletInitializer {
    
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{ RootConfig.class, DatabaseConfig.class };
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

2. Root Config.java

```java
//sample code
@Configuration
@ComponentScan(basePackages= {"your packages"})
public class RootConfig {

}
```

3. ServletConfig.java

```java
//sample code
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







