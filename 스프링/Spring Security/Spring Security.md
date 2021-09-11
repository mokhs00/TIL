- [Spring Security](#spring-security)
- [인증(Authentication)](#인증authentication)
- [인가 혹은 권한(Authorization)](#인가-혹은-권한authorization)
- [Spring Security의 큰 그림](#spring-security의-큰-그림)
  - [필터와 서블릿](#필터와-서블릿)
  - [Security Filter](#security-filter)
  - [Security Filter 종류](#security-filter-종류)
- [Spring Security 로그인](#spring-security-로그인)
  - [인증 토큰(Authentication)을 제공하는 필터들](#인증-토큰authentication을-제공하는-필터들)
- [Authentication 인터페이스](#authentication-인터페이스)

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


## Security Filter 종류

각 필터는 역할에 의해서 정의되어 있다. 
- HeaderWriterFilter : HttpHeader 검사, 부족하거나 필요한 헤더에 대한 검사
- CorsFilter : Cors 관련 -> 허가된 HOST로 부터의 요청인지 검사
- CsrfFilter : 동일 출처로 부터 온 요청인지 검사
- LogoutFilter : 해당 request가 로그아웃 시도인지 검사
- UsernamePasswordAuthenticationFilter : username / password 로 로그인하는 요청인지 검사, 맞다면 처리 후 이동할 페이지를 지정
- ConcurrentSessionFilter : Session 동시성 관련 검사
- BearerTokenAuthenticationFilter : `Authorization` 헤더의 Bearer 토큰에 대한 검사 -> **spring boot 2.5.3에서 찾을 수 없음..**
- BasicAuthenticationFilter : `Authrization` 헤더에 Basic 토큰에 대한 검사
- RequestCacheAwareFilter : 캐시처리 관련 
- SecurityContextHolderAwareRequestFilter : 보완 관련해서 Servlet 3 스펙을 지우너하기 위한 필터
- RememberMeAuthenticationFilter : 인증이 되지 않은 경우 RememberMe 쿠키를 검사하여 인증처리
- AnonymousAuthenticationFilter : 인증되지 않은 경우 Anonymous 사용자로 filter 통과 
- SessionManagemetFilter : 서버에서 지정한 세션 정책을 검사
- ExcpetionTranslationFilter : 이 필터 이후에 인증 혹은 권한 예외가 발생하면, 예외 처리
- FilterSecurityInterceptor : 이 필터까지 왔다면, 인증이 있는 걸로 판단하고, 요청에 해당하는 리소스에 접근할 대한 자격이 있는지 검사
- 이 외에도 OAuth2, Saml2, Cas, X509...



# Spring Security 로그인
**Spring Security**에서 `로그인한다` 라는 것은 **authenticated가 true**인 `Authentication` 객체를 `SecurityContext`에 갖고있는 상태를 의미!

**단, Authentication이 AnonymousAuthenticationToken만 아니면 됨.**

## 인증 토큰(Authentication)을 제공하는 필터들

- UsernamePasswordAuthenticationFilter : 폼 로그인 -> UsernamePasswordAuthenticationToken
- RememberMeAuthenticationFilter : remember-me 쿠키 로그인 -> RememberMeAuthenticationToken
- AnonymousAuthenticationFilter : 로그인하지 않은 것을 인증(ROLE_ANONYMOUS) -> AnonymousAuthenticationToken
- SecurityContextPersistenceFilter : 로그인 유지 (기본적으로 Session 이용)
- BearerTokenAuthenticationFilter : JWT 로그인 -> **spring boot 2.5.3에서 찾을 수 없음..**
- BasicAuthenticationFilter : ajax 로그인 -> UsernamePasswordAuthenticationToken
- OAuth2LoginAuthenticationFilter : 소셜 로그인 -> OAuth2LoginAuthenticationToken, OAuth2AuthenticationToken
- OpenIDAuthenticationFilter : OpenID 로그인

등등...

- `Authentication` 을 제공하는 `AuthenticationProvider`는 여러개가 동시에 존재할 수 있음.
- 또한, 인증 방식에 따라 `ProviderManager`도 여러개 존재할 수 있음.
# Authentication 인터페이스
| 필드                              | 개요           |
| --------------------------------- | -------------- |
| Set<GrantedAuthority> authorities | 인증 권한 정보 |
| principal                         | 인증 대상에 관한 정보, 주로 UserDetails 객체가 옴               |
|credentials| 인증 확인을 위한 정보. 주로 비밀번호가 오지만, 보안을 위해 인증 후 삭제함 |
| details| 그 밖에 필요한 정보. IP, 세션, 기타 인증 요청에 사용했던 정보|
|boolean authenticated| 인증여부 체크 (Anonymous로 통과 시에도 true임 위에 로그인 인증을 처리하는 기준 참고)|


