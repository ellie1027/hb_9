---
title: '[계획] 2020년 11월 3주'
date: 2020-11-17 16:21:13
category: 'plan'
draft: true
---

### 이주의 계획
[⭐] 스프링 시큐리티 정리 완료<br>
[⭐] 스프링 코어 인강 듣고 정리<br>
[⭐] 토이 프로젝트 jenkins 빌드 문제 해결<br>




>**SecurityContextHolder** : 쓰레드 로컬에 SecurityContext를 보관한다.<br>
>**SecurityContext** : 인증 정보(Authentication)를 보관하는 일종의 저장소.<br>
>**Authentication** : principal(인증 주체)를 확인하고 정보를 담는다. 인증 요청 시, 요청 정보를 담는다.<br>

>**AuthenticationManager** : 인증 처리 후, 인증 정보를 담고 있는 Authentication 객체를 리턴한다.<br>
>**AuthenticationProvider** : 유저가 입력한 username과 password를 DB에서 가져온 사용자의 정보(UserDetailsService)를 비교해주는 인터페이스.<br>

>**UserDetails** : 데이터베이스에서 가져온 데이터로 인증을 구축하는데 중요한 정보를 제공.<br>
>**UserDetailsService** : String 타입의 usrename(또는 인증된 ID 등)으로 정보를 가져오는 UserDetails를 생성.<br>

<br>









### [2] UserDetails

스프링 시큐리티에서의 실제로 데이터베이스에 저장된 유저 정보를 검색할 때 이용하는 인터페이스다.<br>

> username(must be unique)<br>
> password(must be encoded)<br>
> roles(ROLE_NAME)<br>
> authorities(permissions)<br>
> ...

<br>
UserDetails는 스프링 시큐리티의 핵심 인터페이스이다.
<br><br>
스프링 시큐리티는 UserDetails 인터페이스를 사용해 데이터베이스에서 가져온 유저 정보로 SecurityContextHolder를 구축한다.
기본적으로 유저이름, 패스워드, 롤, 권한 등의 필드가 있다.(부가적인 유저 정보를 담으려면 User 클래스를 상속받아 새로운 클래스를 만들자.)
<br><br>
UserDetailsService 인터페이스에는 단 하나의 메소드가 있는데, (대개)username을 매개변수로 받아서 유저 정보를 가져오는 메소드이다.
이 메소드는 스프링 시큐리티 내에서 사용자의 정보를 가져오는 가장 일반적인 방법이며, 사용자 정보가 필요할 때마다 프레임워크 전체에서 사용될 수 있다.

```java
package org.springframework.security.core.userdetails;

public interface UserDetailsService {
    UserDetails loadUserByUsername(String var1) throws UsernameNotFoundException;
}
```
<br>




<br>