---
title: "Spring Security"
date: 2020-11-05
categories: security
---

...작성중!!!


<kbd>keyword ; spring security, jwt, java configuration</kbd>


현재 진행중인 토이 프로젝트에서 로그인/회원가입을 위하여 스프링 시큐리티를 사용하기로 하였다. 구글링을 통하여 블로그 포스팅, 책 등을 뒤져봤지만 전체적인 흐름이 눈에 
들어오지 않아서 일단 유투브에서 강의를 찾아 듣고 프로젝트에 적용해보았다.<br>
그런데 내가 하고 있는 프로젝트는 스프링 5.2.7 버전이었는데, 참고 할 만한 좋은 글들이 전부 스프링 부트를 기준으로 쓰여져서, 레퍼런스 찾기가 쉽지 않았다.<br>
(다음 프로젝트는 무조건 부트로...)<br>
또, 기존 프로젝트는 xml configuration 기반으로 진행하고 있었는데, spring security 설정을 java로 하면서 java configuration 기반으로 아예 바꿨다.<br>


## 1) 의존성 추가하기 

먼저 현재 사용하고 있는 스프링 버전에 맞춰 의존성을 추가해준다. 만약 스프링 부트를 사용하고 있다면 버전 관리의 필요성이 없지만 그렇지 않다면 버전 관리는 매우 중요하다.<br>
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

