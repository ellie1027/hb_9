---
title: "Spring Core - IoC Container"
date: 2020-10-14 
categories: spring
---

<kbd>references; Inflearn - 스프링 프레임워크 핵심 기술 (백기선)</kbd>

## 학습 목표
* 스프링 프레임워크의 핵심 기술 IoC, AOP, PSA를 이해한다.
* 스프링 프레임워크 IoC 컨테이너의 다양한 기능을 사용할 수 있다.
* 다양한 방법으로 빈을 정의하고 의존 관계를 주입할 수 있다.
* 스프링 AOP를 사용하여 Aspect를 모듈화 할 수 있다.
* 그밖에 다양한 스프링 핵심 기술을 이해하고 활용할 수 있다.
<br>
<br>
## 1. Spring IoC 컨테이너와 빈

Inversion of Control 
: 의존 관계 주입(Dependency Injection)이라고도 하며, 어떤 객체가 사용하는 의존 객체를 **직접 만들어 사용하는게 아니라, 주입 받아** 사용하는 방법을 말함

```java
class BookService {

    BookRepository bookRepository = new BookRepository();
    BookService bookService = new BookService(bookRepository);
    
    //이런식으로 의존 객체를 직접 만드는 대신 어떤 장치(ex. 생성자)를 사용하여 주입받아 사용하는 것
    //스프링이 없어도 저런 장치만 마련되어 있으면 만들 수 있다.
    
    private BookRepository bookRepository;
    
    public BookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }     
}
```
<br>
그런데 위와 같은 방법을 사용하지 않고, 제어역전에 IoC 컨테이너를 사용하는 이유는 스프링 초기부터 여러 개발자들의 논의와 노하우가 쌓인 결과로 이러한 방식이 더 효율적이라고
여겨져 만들어진 프레임워크이기 때문이다.
<br>

스프링 IoC 컨테이너
: 'Bean'들을 담고 있는 IoC 기능을 가지고 있는 컨테이너.

<br>

Bean
: 스프링 IoC 컨테이너가 관리하는 객체.<br>

<br>

BeanFactory 
: IoC컨테이너 중 가장 최상위에 있는 인터페이스, 가장 핵심적인 클래스<br>
  다양한 빈 팩토리 라이프사이클 인터페이스들이 여러 기능 제공<br>
  애플리케이션 컴포넌트의 중앙 저장소<br>
  빈 설정 소스로부터 빈 정의를 읽어들이고, 빈을 구성하고 제공.<br>

<br>
<br>

* 장점<br>
  * 의존성 관리 - 의존성 관리를 하려면 빈이여야 함. 빈이기 때문에 의존성을 알아서 관리해줌.<br>
  * 스코프<br>
    * 싱글톤타입 : '하나' 만 만들어서 그걸 계속 사용하는 것<br>
        * 빈을 만들때 아무런 애노테이션을 붙이지 않았다면, 그 빈들은 모두 싱글톤 스코프로 등록됨.<br> 
        * 애플리케이션 전반에서 컨테이너로부터 서비스,레파지토리 등를 받아서 사용한다면 그 인스턴스들은 항상 '같은 객체' 일 것이다. 메모리, 런타임 시 성능최적화에도 유리하다.<br>
    * 프로포토타입 : 매번 만들어서 계속 다른 객체를 사용하는 것<br>
    * 라이프사이클 인터페이스를 지원함 : 스프링 IoC 컨테이너에 등록된 빈에 국한함. <br>
<br>
<br>
* 단점<br>
  * 의존성 관리때문에 단위테스트 만들기 힘듬(예를 들면, bookService에 bookRepository를 의존성 주입했을 때, bookRepository의 메소드를 완성해야만 bookService를 테스트할 수 있음) 
<br>
<br>


    
ApplicationContext (BeanFactory를 상속받음)<br>
IoC의 기능을 가지고 있으면서도, EventPublisher, EnviromentCapable, HierarchicalBeanFactory, ListableBeanFactory, MessageSource(메세지 다국화),
ResourceLoader(classpath에 있는 특정한 파일, 파일시스템에 있는 파일, 웹에 있는 파일 등을 리소스라고 하는데 이것들을 읽어오는 기능), ResourcePatternResolver 기능도 가지고 있다.
빈팩토리에 비해 다양한 기능을 추가로 더 가지고 있는 인터페이스.

<br>
<br>

대표적인 ApplicationContext와 해당 ApplicationContext가 가지고 있는 Bean 설정파일
- ClassPathXmlApplicationContext(XML)
- AnnotationConfigApplicationContext(Java)

@Autowired

1. 생성자 - 빈을 만들때도 관여하기 때문에 optional한 설정을 못함
2. 세터
3. 필드

빈 라이프사이클
- 빈 포스트 프로세서라는 라이프사이클 인터페이스의 구현체에 의해 동작
- 빈 포스트 프로세서 ? 
	- 빈 이니셜라이즈 라이프사이클 이전 혹은 이후에 작업을 할수있는 또다른 라이프사이클 콜백

@PostConstruct

빈 이니셜라이즈 전에 
AutoWiredSnnotationBeanPostProcessor
라는 클래스가 
빈을 찾아서 주입해준다!!  



