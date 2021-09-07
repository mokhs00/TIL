- [Spring Security](#spring-security)
- [인증(Authentication)](#인증authentication)
- [인가 혹은 권한(Authorization)](#인가-혹은-권한authorization)

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
