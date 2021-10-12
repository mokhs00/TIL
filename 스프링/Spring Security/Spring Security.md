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
- [정적 리소스 허용 편하게 하기](#정적-리소스-허용-편하게-하기)
- [H2-console path 허용 간편하게 하기 (PathRequest)](#h2-console-path-허용-간편하게-하기-pathrequest)
- [AuthenticationDetailsSource 커스텀하기](#authenticationdetailssource-커스텀하기)
- [RoleHierarchy : 상위 하위 권한 설정](#rolehierarchy--상위-하위-권한-설정)
- [Authentication 메커니즘](#authentication-메커니즘)
  - [인증 제공자 (AuthenticationProvider)](#인증-제공자-authenticationprovider)
  - [인증 관리자 (AuthenticationManager)](#인증-관리자-authenticationmanager)
  - [Authentication 메커니즘 커스텀하기 (AutehnticationProvider, AuthenticationManager)](#authentication-메커니즘-커스텀하기-autehnticationprovider-authenticationmanager)
- [Basic 토큰인증 (with SPA, etc..)](#basic-토큰인증-with-spa-etc)
  - [DaoAuthenticationProvider, UserDetailsService](#daoauthenticationprovider-userdetailsservice)
  - [UserDetailsService, UserDetails 구현](#userdetailsservice-userdetails-구현)
- [로그인을 지원하기 위한 필터들](#로그인을-지원하기-위한-필터들)
  - [스프링이 지원하는 로그인 방식](#스프링이-지원하는-로그인-방식)
  - [세션 이용하기 In Spring Security](#세션-이용하기-in-spring-security)
  - [`SecurityContextPersistenceFilter`](#securitycontextpersistencefilter)
  - [`RememberMeAuthenticationFilter`](#remembermeauthenticationfilter)
  - [PersistenceTokenBasedRememberMeServices 적용](#persistencetokenbasedremembermeservices-적용)
  - [`AnonymousAuthentcationFilter`](#anonymousauthentcationfilter)
  - [HttpSessionEventPublisher](#httpsessioneventpublisher)
- [세션 관리](#세션-관리)
  - [ConcurrentSessionFilter](#concurrentsessionfilter)
  - [SessionManagementFilter](#sessionmanagementfilter)
- [세션 인증 vs 토큰 인증](#세션-인증-vs-토큰-인증)
  - [JWT](#jwt)
    - [JWT 스펙에서 지정한 claim](#jwt-스펙에서-지정한-claim)
    - [토큰에는 어떤 데이터를 담아야 하는가?](#토큰에는-어떤-데이터를-담아야-하는가)
    - [토큰 관리](#토큰-관리)
    - [Spring Boot에서 JWT 이용하기 (with Spring Security)](#spring-boot에서-jwt-이용하기-with-spring-security)

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
- SecurityContextHolderAwareRequestFilter : 보안 관련해서 Servlet 3 스펙을 지원하기 위한 필터
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

| 필드                                | 개요                                                                                 |
| ----------------------------------- | ------------------------------------------------------------------------------------ |
| `Set<GrantedAuthority> authorities` | 인증 권한 정보                                                                       |
| `principal`                         | 인증 대상에 관한 정보, 주로 UserDetails 객체가 옴                                    |
| `credentials`                       | 인증 확인을 위한 정보. 주로 비밀번호가 오지만, 보안을 위해 인증 후 삭제함            |
| `details`                           | 그 밖에 필요한 정보. IP, 세션, 기타 인증 요청에 사용했던 정보                        |
| `boolean authenticated`             | 인증여부 체크 (Anonymous로 통과 시에도 true임 위에 로그인 인증을 처리하는 기준 참고) |

# 정적 리소스 허용 편하게 하기

SecurityConfig에서 다음과 같이 설정하면 정적 리소스 허용을 한 번에 해줄 수 있다.

``` java
// SecurityConfig.java
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    // '''
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring()
                .requestMatchers(
                        PathRequest.toStaticResources().atCommonLocations()
                )
        ;

    }
    // '''
}

```

# H2-console path 허용 간편하게 하기 (PathRequest)

PathRequest에서 지원해준다. `WebSecurityConfigurerAdapter` 구현체에서 다음과 같이 설정하자

``` java
// SecurityConfig.java
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    // '''

    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring()
                .requestMatchers(
                        PathRequest.toStaticResources().atCommonLocations(), // (1)
                        PathRequest.toH2Console() // (2)
                )
        ;
    }
    // '''

}
```

- (1) 정적 리소스 허용
- (2) h2-console 허용

참고로 `PathRequest.toStaticResources().atCommonLocations()` 가 반환하는 값을 찾아가보면 enum type인 `StaticResourceLocation`의 모든 필드를 enumSet으로 반환하는 걸 찾아볼 수 있다.

``` java
// StaticResourceLocation.java
package org.springframework.boot.autoconfigure.security;

public enum StaticResourceLocation {

 /**
  * Resources under {@code "/css"}.
  */
 CSS("/css/**"),

 /**
  * Resources under {@code "/js"}.
  */
 JAVA_SCRIPT("/js/**"),

 /**
  * Resources under {@code "/images"}.
  */
 IMAGES("/images/**"),

 /**
  * Resources under {@code "/webjars"}.
  */
 WEB_JARS("/webjars/**"),

 /**
  * The {@code "favicon.ico"} resource.
  */
 FAVICON("/favicon.*", "/*/icon-*");

 private final String[] patterns;

 StaticResourceLocation(String... patterns) {
  this.patterns = patterns;
 }

 public Stream<String> getPatterns() {
  return Arrays.stream(this.patterns);
 }

}
```

``` java
// StaticResourceRequest.java
public final class StaticResourceRequest {
  // '''
  public StaticResourceRequestMatcher atCommonLocations() {
  return at(EnumSet.allOf(StaticResourceLocation.class));
 }
  // '''
}
```

# AuthenticationDetailsSource 커스텀하기

formLogin시 `SecurityContextHolder`에 올라가는 `Authentication`의 details 멤버를 커스텀해보자.
과정은 다음 단계로 진행된다.

- CustomAuthDetails 정의 : HttpServletRequest를 받아올 미들웨어
- RequestInfo(details) 정의 : 실제 details 멤버에 들어갈 정보
- SecurityConfig에 적용  (`login.authenticationDetailsSource(customAuthDetails`))

``` java
// CustomAuthDetails
@Component
public class CustomAuthDetails implements AuthenticationDetailsSource<HttpServletRequest, RequestInfo> {

    @Override
    public RequestInfo buildDetails(HttpServletRequest request) {
        return RequestInfo.builder()
                .remoteIp(request.getRemoteAddr())
                .sessionId(request.getSession().getId())
                .loginTime(LocalDateTime.now())
                .build();
    }
}
```

``` java
// RequestInfo
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class RequestInfo {

    private String remoteIp;
    private String sessionId;
    private LocalDateTime loginTime;
}
```

``` java
// SecurityConfig.java

@EnableWebSecurity(debug = true)
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final CustomAuthDetails customAuthDetails; // (1)

    public SecurityConfig(CustomAuthDetails customAuthDetails) {
        this.customAuthDetails = customAuthDetails;
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .formLogin(
                        login -> login.loginPage("/login")
                                .permitAll()
                                .defaultSuccessUrl("/", false)
                                .failureUrl("/login-error")
                                .authenticationDetailsSource(customAuthDetails) // (2)
                )
        ;

    }
```

- (1) : CustomAuthDetails 생성자 주입
- (2) : 주입받은 customAuthDetails를 authenticationDetailsSource로 설정

# RoleHierarchy : 상위 하위 권한 설정

Spring Security에서 Admin 권한이 있으면 User 권한이 필요한 페이지에도 접근할 수 있게 하는 방법으로 `RoleHierarchy`를 빈으로 등록하여 적용할 수 있다.

``` java
@Bean
public RoleHierarchy roleHierarchy() {
    RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
    roleHierarchy.setHierarchy("ROLE_ADMIN > ROLE_USER");
    return roleHierarchy;
}
```

# Authentication 메커니즘

Authentication은 인증된 결과뿐만 아니라, **인증을 하기 위한 정보와 인증을 받기 위한 정보가 하나의 객체에 동시에 들어있다.**

이는 인증을 제공해줄 `AuthenticationProvier`가 어떤 인증에 대해서 허가를 내줄 것인지 판단하기 위해서 **직접 입력된 인증을 보고 허가된 인증을 내어주는 방식**이기 때문이다.
그래서 `AuthenticationProvider`는 처리가능한 **Authentication에 대해서 알려주는** `supports(Class<?> authentication)` 메서드를 지원하고, `authenticate(Authentication authentication)`에서 Authentication을 입력값과 동시에 출력값으로도 사용한다.

- Authentication 구현체는 보통 Token이라는 이름의 객체로 구현되며 이를 인증 토큰이라고 부르기도 한다.
- Authentication 객체는 SecurityContextHolder를 통해 세션과 무관하게 언제든 접근할 수 있도록 필터체인에서 보장해준다.

## 인증 제공자 (AuthenticationProvider)

- `AuthenticationProvider`는 기본적으로 Authentication을 받아서 해당 객체를 인증하고 인증된 결과를 다시 Authentication 객체로 전달한다.
- 이 때 해당 **인증 제공자(AuthenticationProvider)**가 어떤 인증을 진행할지 `AuthenticationManager`에게 알려줘야 하기 때문에 `supports()`라는 메서드로 인증 대상에 대한 정보를 제공해야한다.
- 또한, 인증 대상과 방식이 다양할 수 있으므로, 인증 제공자(AuthenticationProvider) 또한 여러개가 있을 수 있다.

다음은 supports() 메서드를 구현한 예시이다.

``` java
@Override
public boolean supports(Class<?> authentication) {
    return authentication == UsernamePasswordAuthenticationToken.class;
}
```

## 인증 관리자 (AuthenticationManager)

- 인증 제공자들을 관리한다. Spring Security에서는 `AuthenticationManager` 인터페이스가 제공되고 이를 구현한 객체가 `ProviderManager`이다.
- ProviderManager 또한 AuthenticationProvider와 동일하게 여러개 존재할 수 있다.
- 개발자가 직접 `AuthenticationManager`를 정의해서 제공하지 않는다면, `AuthenticationManagerFactoryBean`에서 `DaoAuthenticationProvider`를 기본 인증 제공자로 등록한 `AuthenticationManger`를 만든다.
- DaoAuthenticationProvider는 반드시 1개의 UserDetailsService를 발견할 수 있어야하고 만약 없다면, `InmemoryUserDetailsManager`에 `[username=user, password={서버가 생성한 비밀번호}]`로 사용자가 등록되어 제공된다.

## Authentication 메커니즘 커스텀하기 (AutehnticationProvider, AuthenticationManager)

Authentication 메커니즘을 커스텀 해보자.

실제로는 주로 `AuthenticationManagerFactoryBean`이 기본으로 제공하는 `DaoAuthenticationProvider`을 이용해, `UserDetailsService`와 `UserDetails`를 구현해서 사용하지만, 학습을 위해서 커스텀 해보자.

구현 과정은 다음과 같다.

- Authentication 구현체에 넣을 principal 객체 구현 -> `Student`
- Authentication 구현체 생성 -> `StudentAuthenticationToken`
- AuthenticationProvider 구현 -> `StudentManager`
- AutehnticationManager 등록

``` java
// Student.java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class Student {
    private String id;
    private String username;
    private Set<GrantedAuthority> role;
}
```

``` java
// StudentAuthenticationToken.java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class StudentAuthenticationToken implements Authentication {
    private Student principal;
    private String credentials;
    private String details;
    private boolean authenticated;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return principal == null ? new HashSet<>() : principal.getRole();
    }

    @Override
    public String getName() {
        return principal == null ? "" :  principal.getUsername();
    }

}


```

``` java
// StudentManager.java
@Component
@Slf4j
public class StudentManager implements AuthenticationProvider, InitializingBean {
    private HashMap<String, Student> studentDB = new HashMap<>(); // (1)

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        UsernamePasswordAuthenticationToken token = (UsernamePasswordAuthenticationToken) authentication;
        if (studentDB.containsKey(token.getName())) {
            Student student = studentDB.get(token.getName());
            return StudentAuthenticationToken.builder()
                    .principal(student)
                    .details(student.getUsername())
                    .authenticated(true)
                    .build();
        }
        return null;
    }

    @Override
    public boolean supports(Class<?> authentication) {
        return authentication == UsernamePasswordAuthenticationToken.class;
    }

    @Override
    public void afterPropertiesSet() throws Exception { // (1)
        Set.of(
                new Student("hong", "홍홍", Set.of(new SimpleGrantedAuthority("ROLE_STUDENT"))),
                new Student("kim", "김김", Set.of(new SimpleGrantedAuthority("ROLE_STUDENT"))),
                new Student("woo", "우우", Set.of(new SimpleGrantedAuthority("ROLE_STUDENT")))
        ).forEach(s ->
                studentDB.put(s.getId(), s)
        );
    }
}


```

- (1) : 스프링 빈으로 등록 후에 실행되는 메서드 현재 DB를 사용하지 않아 HashMap 자료형에 임의로 값을 넣어줌

``` java
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final StudentManager studentManager;

    public SecurityConfig(StudentManager studentManager) {
        this.studentManager = studentManager;
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.authenticationProvider(studentManager);
    }
}


```

# Basic 토큰인증 (with SPA, etc..)

- Basic 토큰 인증 방식은 `username:password`를 base64 encoding하고 `Authorization` header로 넘겨서 사용자를 인증한다.
- `username:password` 값이 header에 같이 전송되기 때문에 보안에 취악하므로 https 프로토콜 사용을 권장한다.
- 최초 로그인 시에만 인증을 처리하고, 이후에는 세션에 의존.
- `RememberMe`를 설정한 경우 세션이 만료되더라도 remember-me 쿠키를 확인하여 인증처리한다.

- 다음과 같이 SSR(Server Side Rendering)로 로그인 페이지를 사용할 수 없는 상황에 사용한다.

  - SPA (react, angular, vue ...)
  - 브라우저 기반의 모바일 앱(ex : ionic)

## DaoAuthenticationProvider, UserDetailsService

실제 개발 환경에서는 대부분 DB를 사용해 사용자를 관리한다.

실제로 Spring Security를 사용해서 서비스를 만들라고 하면 대부분 User 객체와 UserDetailsService를 구현하여 만든다.

그 이유는 UserDetailsService와 UserDetails 구현체(User)만 구현하면 나머지는 Spring Security가 쉽게 사용할 수 있도록 지원해주기 때문!

추가로 Spring Security는 default값으로 다음과 같은 인증 처리 흐름을 제공한다

1. `UsernamePasswordAuthenticationFilter` 입장 ->
2. UsernamePasswordAuthenticationToken을  `ProviderManager(AuthenticationManager)`에 넘김 ->
3. `DaoAuthenticationProvider(AuthenticationProvider)`가 해당 통행증(Token)을 처리 ->
4. `UserService(UserDetailsService)`이 존재한다면, 처리  ->
5. UserService에서 가져온 `User(UserDetails)`를 principal로 설정 ->
6. `UsernamePasswordAuthenticationToken` 발급

## UserDetailsService, UserDetails 구현

Spring Data Jpa를 곁들여서 UserDetailsService, UserDetails를 구현해보자. 다음의 순서로 진행된다.

- `UserDetails`(SpUser), `GrantedAuthority`(SpAuthority) 구현
- `JpaRepository`(SpUserRepository), `UserDetailsService`(SpUserService) 구현
- `UserDetailsService`(SpUserService) 등록

``` java
// SpUser.java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@Entity
@Table(name = "sp_user")
public class SpUser implements UserDetails {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long userId;

    @OneToMany(fetch = FetchType.EAGER, cascade = CascadeType.ALL, orphanRemoval = true)
    @JoinColumn(name = "user_id", foreignKey = @ForeignKey(name = "user_id"))
    private Set<SpAuthority> authorities;

    private String email;

    private String password;

    private boolean enabled;

    @Override
    public Set<SpAuthority> getAuthorities() {
        return this.authorities;
    }

    @Override
    public String getPassword() {
        return this.password;
    }

    @Override
    public String getUsername() {
        return this.email;
    }

    @Override
    public boolean isAccountNonExpired() {
        return this.enabled;
    }

    @Override
    public boolean isAccountNonLocked() {
        return this.enabled;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return this.enabled;
    }

    @Override
    public boolean isEnabled() {
        return this.enabled;
    }
}
```

``` java
// SpUserRepository.java
public interface SpUserRepository extends JpaRepository<SpUser, Long> {
    Optional<SpUser> findSpUserByEmail(String email);
}
```

``` java
// SpUserService.java
@Service
@Transactional
public class SpUserService implements UserDetailsService {

    private final SpUserRepository spUserRepository;

    public SpUserService(SpUserRepository spUserRepository) {
        this.spUserRepository = spUserRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        return spUserRepository.findSpUserByEmail(username)
                .orElseThrow(() -> new UsernameNotFoundException(username));
    }

}
```

``` java
// SecurityConfig.java
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final SpUserService userService;

    public SecurityConfig(SpUserService userService) {
        this.userService = userService;
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth
                .userDetailsService(userService); // (1) 
    }

    // ```
}
```

- (1) userDetailsService 등록

# 로그인을 지원하기 위한 필터들

로그인 인증은 보통 세션 or SessionLess 방식 등을 이용한다.

스프링 시큐리티의 인증 처리는 세션과 별도로 동작하도록 설계되어있고, session의 유무에 상관없이 `Authentication`과 `AuthenticationProvider`를 사용할 수 있다.

## 스프링이 지원하는 로그인 방식

먼저 스프링이 지원하는 로그인 방식들을 알아보자.
이는 다음과 같다.

- UsernamePasswordAuthentication
  - 폼 로그인
  - 세션 기반
- Basic Authentication
  - 클라이언트 로그인 (header에 username:password)
  - 세션 혹은 SessionLess
- OAuth2 로그인
- JWT 토큰 (Bearer Token)
  - 클라이언트 로그인
  - 주로 SessionLess 혹은 세션

## 세션 이용하기 In Spring Security

WAS의 세션 정책과 스프링의 인증 체계를 조합해서 사용하려면 `SecurityContextPersistenceFilter`와 `RememberMeAuthenticationFilter`, `AnonymousAuthenticationFilter` 등과 같이 인증을 보조해주는 필터의 도움을 받아야한다.

- 기본적으로 인증에 대한 것은 필터 권한에 대한 것은 인터셉터에서 관리한다.
- 권한은 API마다 다를 수 있어 인터셉터에서 검증하는 것이 적합하고, 인증에 관한 것은 해당 애플리케이션 서버에 접속할 수 있는 것 자체에 대해서 검증하는 것이므로 애플리케이션에 들어오기 전 필터에서 하는 것이 적합하다.

이제부터 이 필터들에 대해서 알아보자

## `SecurityContextPersistenceFilter`

- `SecurityContextPersistenceFilter`는 저장된 `SecurityContext`를 `Request`의 `LocalThread`에 넣어주었다가 뺏는 역할을 한다.
- SecurityContextPersistenceFilter는 SecurityContext를 저장하는 `SecurityContextRepository`를 사용하는데, SecurityContextRepository의 여러 구현체 중에 http 세션에 `SecurityContext`를 저장하는 `HttpSessionSecurityContextRepository`를 default로 사용한다.

-

## `RememberMeAuthenticationFilter`

- 세션이 만료되어도, `remember-me`쿠키를 이용해서 로그인을 기억했다가 자동으로 로그인을 처리할 수 있다.

- RememberMeAuthenticationFilter는 `RememberMeServices`를 갖는데 이를 구현한 `AbstractRememberMeServices`가 존재하고 또 이를 상속받아 구현되는 `TokenBasedRemeberMeServices`와 `PersistenceTokenBasedRememberMeServieces`가 있는데, 이에 대한 설명은 다음과 같다.

- `TokenBasedRemeberMeServices`
  - 별도 설정이 없을 시 default값
  - 토큰 기반으로 **브라우저에만!** 토큰을 저장한다.
  - `아이디:만료시간:Md5Hex(아이디:만료시간:비밀번호:인증키)` 의 형식을 갖는다.
  - 토큰 탈취 시 비밀번호가 변경되지 않고, 만료시간이 지나지 않는 한 접근을 막을 방법이 없다.
  - 또한 토큰이 탈취되었는지 판별도 쉽지 않다.
  - 기본 유효기간은 14일이고 설정이 가능하다.

- `PersistenceTokenBasedRememberMeServieces`
  - 서버(DB)에도 토큰을 저장한다.
  - `series:token` 의 형식을 가져서, 토큰에 username, 만료시간이 노출되지 않는다.
  - PersistentTokenRepository에 토큰 값이 저장되며, 토큰의 필드값은 다음과 같다.
    - `username : String`
    - `series : String`
    - `token : String`
    - `last_used : Date`
  - series는 브라우저마다 다른 값을 가지는데, series가 달라질 시 토큰의 유효성은 사라진다. 즉 같은 브라우저에서만 **연속적 인증**을 허용하여 토큰 탈취 시 위험성을 어느정도 줄일 수 있다.
  - 재로그인될 때 마다 token 값을 갱신해주며, 토큰이 탈취되어 다른 사용자가 다른 장소에서 로그인을 했다면 정상 사용자가 다시 로그인할 때, `CookieTheftException`이 발생하고, 서버는 해당 사용자로 발급된 모든 remember-me 쿠키값들을 삭제하고 재로그인을 요청하게 된다.
  - `InmemoryTokenRepository`는 서버가 재시작되면 등록된 토큰들이 사라지므로, `JdbcTokenRepository`를 사용하거나 이와 유사한 방법으로 토큰을 관리해야한다.
  - 로그아웃하게되면 다른 곳에서 만들어놓은 remember-me 쿠키값이 쓸모없어지므로 만약, 다른 곳의 remember-me 쿠키를 살리고 싶다면, series로 구분하여 삭제하도록 logout을 수정해야한다.

## PersistenceTokenBasedRememberMeServices 적용

- PersistenceTokenBasedRememberMeServices는 DB에 remember-me 정보를 저장하므로 스케일 아웃 시에도 유리하다고 볼 수 있으니 알아두자.
- 다음은 `PersistenceTokenBasedRememberMeServices`를 적용한 예시 코드이다.

``` java
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    private final UserDetailsService spUserService;
    private final DataSource dataSource; // (1)

    // '''
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .csrf().disable()
                .authorizeRequests(request ->
                        request.antMatchers("/").permitAll()
                                .anyRequest().authenticated()
                )
                .formLogin(login ->
                        login.loginPage("/login")
                                .loginProcessingUrl("/loginprocess")
                                .permitAll()
                                .defaultSuccessUrl("/", false)
                                .failureUrl("/login-error")
                )
                .logout(logout ->
                        logout.logoutSuccessUrl("/"))
                .exceptionHandling(error ->
                        error.accessDeniedPage("/access-denied")
                )
                .rememberMe(r -> r.rememberMeServices(rememberMeServices())) // (2)
        ;
    }

    @Bean
    PersistentTokenRepository persistentTokenRepository() {
        JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
        jdbcTokenRepository.setDataSource(this.dataSource); // (3) 

        // table 존재 여부를 파악하기 위한 코드 (꼼수)
        try {
            jdbcTokenRepository.removeUserTokens("1");
        } catch (Exception e) {
            jdbcTokenRepository.setCreateTableOnStartup(true);
        }

        return jdbcTokenRepository;
    }

    @Bean
    PersistentTokenBasedRememberMeServices rememberMeServices() {
        PersistentTokenBasedRememberMeServices service =
                new PersistentTokenBasedRememberMeServices("secret-key", // (4)
                        spUserService, // (5) 
                        persistentTokenRepository()
                );
        return service;
    }
    // '''

}
```

- (1) JdbcTokenRepositoryImpl에 DataSource 주입을 위해서 생성자 주입을 받았다.
- (2) remembermeServices를 설정해준다.
- (3) JdbcTokenRepositoryImpl은 DataSource 주입이 필요하다.
- (4) seceret key이다. 기본적으로 secret-key를 설정하지 않으면 애플리케이션 재시작 시마다 key 값이 변경되어 기존 rememeber-me 토큰은 무쓸모가 된다.
- (5) UserDetailsService를 상속받아 구현한 CustomUserDetailsService이다.

## `AnonymousAuthentcationFilter`

- 로그인 하지 않은 사용자 혹은 로그인이 검증되지 않은 사용자는 기본적으로 AnonymousAuthenticationToken을 발급해주고, ROLE_ANONYMOUS가 허용되는 리소스에만 접근할 수있다.
- 익명 사용자의 권한을 커스텀할 수도 있고, 익명 사용자의  principal 객체도 커스텀할 수 있다.

## HttpSessionEventPublisher

`ServletListenerRegistrationBean<HttpSessionEventPublisher>`를 스프링 빈으로 다음과 같이 등록해서 Session의 생명주기에 대해서 모니터링할 수 있다.

``` java

@Bean
public ServletListenerRegistrationBean<HttpSessionEventPublisher> httpSessionEventPublisherServletListenerRegistrationBean() {

    return new ServletListenerRegistrationBean<>(new HttpSessionEventPublisher() {
        @Override
        public void sessionCreated(HttpSessionEvent event) {
            super.sessionCreated(event);
            log.info("===>> [{}] SESSION CREATED {} \n", LocalDateTime.now(), event.getSession().getId());
        }
        @Override
        public void sessionDestroyed(HttpSessionEvent event) {
            super.sessionDestroyed(event);
            log.info("===>> [{}] SESSION DESTROYED {} \n", LocalDateTime.now(), event.getSession().getId());
        }
        @Override
        public void sessionIdChanged(HttpSessionEvent event, String oldSessionId) {
            super.sessionIdChanged(event, oldSessionId);
            log.info("===>> [{}] SESSION CHANGED {} to {} \n", LocalDateTime.now(), oldSessionId, event.getSession().getId());
        }
    });
}
```

# 세션 관리

서버는 보통 사용자를 판단할 때 토큰을 발급하고 해당 토큰을 세션에 넣어서 세션 여부에 따라 인증 여부를 결정한다.
그리고 해커들은 세션이 인증역할을 한다는 것을 알고 있기때문에 세션 탈취를 시도한다.
그래서 세션 관리에 헛점이 없도록 기본 구성 내용을 잘 아는 것은 중요하다.

다음은 세션 관리에 관여되는 필터들이다.

## ConcurrentSessionFilter

- Session은 서블릿 컨테이너(톰켓)에서 제공하는 것이므로 스프링이 제어할 수는 없지만 서블릿 컨테이너가 넘겨주는 세션을 스프링의 SessionRegistry에서 SessionInformation이라는 래퍼 클래스로 관리하고,
- SessionInformation를 살펴봤을 때 expired 되었다면 해당 세션이 애플리케이션으로 들어오지 못하게 한다.
- 어떤 SessionInformation을 expire할 것인가에 대한 정책은 SessionManagementFilter에서 관리한다.

## SessionManagementFilter

- SessionAuthenticationStrategy에서 다음과 같은 설정으로 여러가지 세션 인증 정책을 관리할 수 있다.

``` java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .sessionManagement(s -> s
//                                .sessionCreationPolicy(p-> SessionCreationPolicy.STATELESS) // (1)
                                .sessionFixation(sessionFixationConfigurer -> sessionFixationConfigurer.changeSessionId()) // (2)
                                .maximumSessions(2) // (3)
                                .maxSessionsPreventsLogin(true) // (4)
                                .expiredUrl("/session-expired") // (5)
                )
        ;
    }
```

- (1) `세션 생성 정책`을 설정할 수 있음. 보통 ALWAYS나 STATELESS를 사용하며, 거의 건들 일이 없지만, JWT 사용 시 STATELESS(세션을 사용하지 않음) 정도를 사용할 수 있다.
- (2) `세션 고정 정책`을 설정할 수 있다. changeSessionId()가 기본 값이며, 세션 고정 시 해커가 임의로 자신의 세션을 대상자에게 심어서 자동 로그인에 사용할 수 있으므로 주의해야한다.
- (3) `세션 최대 수 정책`을 설정할 수 있다.
- (4) `세션 초과 시 로그인 방지 정책`을 설정할 수 있다.
- (5) `세션 만료 시 이동할 페이지`를 설정할 수 있다.

# 세션 인증 vs 토큰 인증

- 서버에서 인증을 유지해주는 방법으로 보통 세션을 이용하지만, 여러 대의 서버를 둘 경우 세션 관리에 대한 비용이 매우 커지게 된다.
- 또한 같은 사용자가 서로 다른 도메인의 데이테를 요청할 경우 SSO(Single Sign-On)을 위한 세션 관리 비용도 매우 커지게 된다.

- 위와 같은 이유에서 인증 정보를 담은 토큰을 이용해서 이를 매 요청마다 검사하여 인증용도로 사용하는 방식이 유용하다.
- 그리고 대표적인 토큰으로 JWT(Json Web Token)가 있다.

## JWT

- header, body, signature로 구성되어 있다.
- JWT 자바 라이브러리는 보통 다음 두 가지를 이용한다.
  - auth0.com 에서 만든 java-jwt
  - okta 에서 만든 jjwt 라이브러리
- 두 라이브러리는 signingKey를 사용하는 방식에 차이가 있으니 주의해야한다.
  - java-jwt에서는 key를 그대로 사용하고 JWT는 key를 해싱해서 사용한다.
- 다음 코드는 각각 jjwt와 java-jwt를 이용해서 JWT를 만들어본 코드이다.

``` java
void test_jjwt() {
    String token = Jwts.builder().addClaims(
                    Map.of("user_id", 1, "price", 3000)
            ).signWith(SignatureAlgorithm.HS256, "mokhs")
            .compact();
    printToken(token);

    Jws<Claims> tokenInfo = Jwts.parser().setSigningKey("mokhs").parseClaimsJws(token);
    System.out.println(tokenInfo);
}

```

``` java
void test_java_jwt() {
    byte[] SEC_KEY = DatatypeConverter.parseBase64Binary("mokhs"); // (1)
    String token = JWT.create()
            .withClaim("user_id", "mokhs")
            .withClaim("price", 3000)
            .sign(Algorithm.HMAC256(SEC_KEY));

    printToken(token);

    DecodedJWT decodedJWT = JWT.require(Algorithm.HMAC256(SEC_KEY)).build().verify(token);

    System.out.println(decodedJWT.getClaims());

    Jws<Claims> tokenInfo = Jwts.parser().setSigningKey(SEC_KEY).parseClaimsJws(token);

    System.out.println(tokenInfo);
}
```

- (1) DatatypeConverter를 이용하면 같은 서로 라이브러리가 다르더라도 같은 키를 이용할 수 있기는 하다.

### JWT 스펙에서 지정한 claim

- `iss` : `Issuer` 토큰을 발행한 사람(단체,사이트)이 누구인지
- `sub` : `Subject` 무엇에 관한 토큰인지
- `aud` : `Audience` 누구를 대상으로 한 토큰인지
- `exp` : `Expiration` 토큰의 만료 시간
- `nbf` : `Not Before` 토큰이 언제부터 유효한지
- `iat` : `Issued At` 토큰 발행 시간
- `jti` : `JWT ID` : 토큰 자체의 아이디(식별자) -> 변경 감지 가능
- 그 밖에 인증에 필요하거나 대상서버에서 필요로 하는 데이터

### 토큰에는 어떤 데이터를 담아야 하는가?

- 일반적으로 인증에 필요한 최소한의 데이터
- 다른 사람에게 노출되어도 상관없는 데이터
- 비밀번호 X, 전화번호 X

### 토큰 관리

- 이론적으로 토큰은 클라이언트에서 관리하게 한다.
- 하지만 실제로 서버에서 사용자 정보 캐싱, 토큰 유효성 검사 그리고 refresh 토큰 정책을 위해서 서버에서 토큰을 관리하기도 한다.
- 이 경우 토큰과 사용자 정보를 관리하는 방법으로 다음과 같은 방법들을 사용하기도 한다.
  - 캐시 (redis, hazelcast)
  - DB 저장


### Spring Boot에서 JWT 이용하기 (with Spring Security)
  
- 먼저 `WebSecurityConfigurerAdapter`에서 session을 사용하지 않을 것이기 때문에 session생성 정책을 STATELESS로 하고
- `UsernamePasswordAuthenticationFilter`와 `BasicAuthenticationFilter`의 자리에 각각 loginFilter, jwtCheckFilter를 설정합니다.
  
``` java
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class AdvancedSecurityConfig extends WebSecurityConfigurerAdapter {

    private final UserService userService;

    public AdvancedSecurityConfig(SpUserService userService) {
        this.userService = userService;
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        JWTLoginFilter loginFilter = new JWTLoginFilter(authenticationManager()); 
        JWTCheckFilter jwtCheckFilter = new JWTCheckFilter(authenticationManager(), userService);
        http
                .csrf().disable()
                .sessionManagement(session ->
                        session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                )
                .addFilterAt(loginFilter, UsernamePasswordAuthenticationFilter.class)
                .addFilterAt(jwtCheckFilter, BasicAuthenticationFilter.class);
    }
}
```
