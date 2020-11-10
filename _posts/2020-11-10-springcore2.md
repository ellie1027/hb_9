---
layout: post
title: "Spring Core - IoC Container (2)"
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

## 1. Spring IoC 컨테이너와 빈 (3) : @Autowired

* BookService와 BookRepository가 있다고 가정해보자. BookService에 BookRepository의 의존성 주입을 하려면 세 가지 방법이 있다.
<br>
**사용할 수 있는 위치** 
    * 생성자(스프링 4.3부터는 생략 가능) - 빈을 만들때도 관여하기 때문에 optional한 설정을 못함
    * 세터
    * 필드
<br>    

```java
@Repository
public class BookRepository{
    
}

@Service
public class BookService{
    
    //1. 생성자를 이용하여 의존성 주입 - 빈을 만들때도 관여하기 때문에 optional한 설정을 못함
    BookRepository bookRepository;
    
    @Autowired
    public BookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }
    
    //2. 세터를 이용한 의존성 주입
    @Autowired(requried = false) //해당하는 빈의 타입을 못찾거나, 의존성 주입을 할 수 없는 경우에는 애플리케이션 구동이 제대로 되지 않음. 그럴때 optional로 설정을 해줌.
    public void setBookRepository(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }

    //3. 필드를 이용한 의존성 주입 
    @Autowired(requried = false)
    BookRepository bookRepository;
    
}
```

<br>
<br>
지금까지 해당 타입의 빈이 한 개 있거나 혹은 없을 때 의존성 생성을 optional하게 해주는 법을 배웠다. 그런데 같은 타입의 빈이 여러 개 이면 어떻게 의존성을 주입해주어야 할까? 
<br>
<br>

**경우의 수**
* 해당 타입의 빈이 없는 경우
* 해당 타입의 빈이 한 개인 경우
* 해당 타입의 빈이 여러 개인 경우
    * 빈 이름으로 시도
    * 같은 이름의 빈 찾으면 해당 빈 사용
    * 같은 이름 못 찾으면 실패
    
```java
@Repository
public class BookRepository{
    
}
@Repository @Primary //1. @Primary로 무조건 이 빈을 주입하게 하기
public class MyBookRepository implements BookRepository{
    
}

@Service
public class BookService{
    
    @Autowired
    BookRepository bookRepository;
    
}
```
<br>

```java
@Repository
public class BookRepository{
    
}
@Repository
public class MyBookRepository implements BookRepository{
    
}

@Service
public class BookService{
    
    @Autowired @Qualifier("myBookRepository") //2. @Qualifier로 지정해주기
    BookRepository bookRepository;
    
}
```    

```java
@Repository
public class BookRepository{
    
}
@Repository
public class MyBookRepository implements BookRepository{
    
}

@Service
public class BookService{
    
    @Autowired //3. 해당 타입의 빈 모두 주입 받기
    List<BookRepository> bookRepository;
    
}


```

**같은 타입의 빈이 여러 개 일때**
    * @Primary - 무조건 이 Bean을 주입해라! 좀 더 type safe한 방법.
    * 해당 타입의 빈 모두 주입 받기
    * Qualifier(빈 이름)



빈 라이프사이클
- 빈 포스트 프로세서라는 라이프사이클 인터페이스의 구현체에 의해 동작
- 빈 포스트 프로세서 ? 
	- 빈 이니셜라이즈 라이프사이클 이전 혹은 이후에 작업을 할수있는 또다른 라이프사이클 콜백

@PostConstruct

빈 이니셜라이즈 전에 
AutoWiredSnnotationBeanPostProcessor
라는 클래스가 
빈을 찾아서 주입해준다!!  



