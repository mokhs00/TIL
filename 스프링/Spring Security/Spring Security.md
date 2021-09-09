- [Spring Security](#spring-security)
- [인증(Authentication)](#인증authentication)
- [인가 혹은 권한(Authorization)](#인가-혹은-권한authorization)
- [Spring Security의 큰 그림](#spring-security의-큰-그림)
  - [필터와 서블릿](#필터와-서블릿)
  - [Security Filter](#security-filter)

# Spring Security

Spring Security에 대해 학습하고 정리합니다.

# 인증(Authentication)
유저가 누구인지 확인하는 절차.
ex) 회원가입, 로그인

최근에는 아이디는 까먹기 쉬워서 이메일로 로그인하도록 유도함.

- UsernamePassword 인증
  - Session 관리 -> **최근에는 scale out 하는 경우 Session 동기화 문제로 인해서 sessionless방식을 이용하는 추세**
  - **토큰 관리 (sessionless)**
- SNS 로그인 (소셜 로그인) : 인증 위임

# 인가 혹은 권한(Authorization)

Spring Security에서 권한을 검증하는 방법

- @Secured -> deprecated됨
- @PrePostAuthorize : controller method가 실행되기 직전에 정의된 표현식에 따라 권한을 검증함.
- AOP


# Spring Security의 큰 그림

## 필터와 서블릿
![spring security request life cycle](./Spring%20Security/image1.png)

- 기본적으로 톰캣과 같은 웹 애플리케이션은 다수의 서블릿을 동시에 관리하기 때문에 `서블릿 컨테이너`라고 한다.
- 그리고 이러한 웹 애플리케이션은 기본적으로 필터와 서블릿으로 구성되어 있다.
- 또한, 필터는 체인처럼 연속적으로 엮여있기 때문에 필터 체인이라고도 부르는데, 모든 요청(request)은 필터 체인을 반드시 거쳐야만 서블릿 서비스에 도착하게 된다.


## Security Filter

- Spring Security는 DelegatingFilterProxy라는 필터를 만들어 메인 필터체인에 끼워넣고
- 그 아래에 SecurityFilterChain 그룹을 등록하여 필터에서 Security 검증이 적용되도록 한다.
- 이 필터 체인은 반드시 한 개 이상이고 url 패턴에 따라 필터체인을 다르게 적용할 수 있다.
- 또한, 필터체인을 다르게 적용할 수 있는 것을 이용하여서 web resource같은 경우 필터를 무시하고 통과시킬 수도 있다.
- 이렇게 각 상황에 맞게 다른 필터체인을 적용할 수 있는 것은 필터체인 프록시인 DelegatingFilterProxy 덕분이다.


