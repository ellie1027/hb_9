---
title: "Spring Core"
date: 2020-10-14 
categories: spring
tags: spring, spring core
---

**Spring Core**

Spring FrameWork Documentation
Core - IoC container, Events, Resources, i18n, Validation, Data Binding, Type Conversion, SpEL, AOP


##### Spring IoC 컨테이너와 빈 #####

Inversion Of Countrol 
- 의존 관계 주입(Dependency Injection)이라고도 하며, 어떤 객체가 사용하는 의존 객체를 **직접 만들어 사용하는게 아니라, 주입 받아** 사용하는 방법을 말함

BookRepository bookRepository = new BookRepository();
BookService bookService = new BookService(bookRepository);

이런식으로 의존 객체를 직접 만드는 대신

대신 

@Service, @Repository, @Autowired 등 IoC 컨테이너에 있는 어노테이션을 사용하여 POJO 객체를 Bean으로 등록을 하고 주입 한다.

스프링 IoC 컨테이너
- BeanFactory 
  * IoC컨테이너 중 가장 최상위에 있는 인터페이스, 가장 핵심적인 클래스
  * 다양한 빈 팩토리 라이프사이클 인터페이스로 여러 기능 제공
  * 애플리케이션 컴포넌트의 중앙 저장소
  * 빈 설정 소스로부터 빈 정의를 읽어들이고, 빈을 구성하고 제공.
- 애플리케이션 컴포넌트의 중앙 저장소
- 빈 설정 소스로부터 빈 정의를 읽어들이고, 빈을 구성하고 제공한다.

빈
- 스프링 IoC 컨테이너가 관리하는 객체.
- 장점
  * 의존성 관리(의존성 관리를 하려면 빈이여야 함) -->(단점? 의존성 관리때문에 단위테스트 만들기 힘듬)
  * 스코프
    * 싱글톤 : 하나
    * 빈을 만들때 아무런 애노테이션을 붙이지 않았다면, 그 빈들은 모두 싱글톤 스코프로 등록됨. 
      애플리케이션 전반에서 컨테이너로부터 서비스,레파지토리를 받아서 사용한다면 그 인스턴스들은 항상 '같은 객체'일 것이다. 메모리, 런타임 시 성능최적화에도 유리하다.
    * 프로포토타입 : 매번 다른 객체
    * 라이프사이클 인터페이스를 지원함(스프링 IoC컨테이너에 등록된 빈에 국한)
    
    
- Interface ApplicationContext (BeanFactory를 상속받음)
IoC의 기능을 가지고 있으면서도, EventPublisher, EnviromentCapable, HierarchicalBeanFactory, ListableBeanFactory, MessageSource(메세지 다국화),
ResourceLoader(classpath에 있는 특정한 파일, 파일시스템에 있는 파일, 웹에 있는 파일 등을 리소스라고 하는데 이것들을 읽어오는 기능), ResourcePatternResolver 기능도 가지고 있다.
빈팩토리에 비해 다양한 기능을 추가로 더 가지고 있는 인터페이스.

 
작성중...
  



