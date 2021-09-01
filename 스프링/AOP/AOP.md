# AOP (Aspect Oriented Programming)

## 주요 Annotation

`@Aspect` : 자바에서 널리 사용하는 프레임워크에 포함됨, AOP를 정의하는 클래스에 할당

`@Pointcut` : 기능을 어디에 적용시킬지 설정, 메서드, Annotation 등 AOP를 적용시킬 지점 설정

`@Before` : 메서드 실행 이전

`@After` : 메서드가 성공적으로 실행 후, 예외가 발생되더라도 실행

`@AfterReturning` : 메서드 호출 성공 실행 시 (Not Throws)

`@AfterThorowing` : 메서드 호출 실패 예외 발생 (Throws)

`@Around` : Before / After 모두 제어
