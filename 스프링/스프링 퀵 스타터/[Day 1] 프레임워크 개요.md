# [Day 1] 프레임워크 개요

# 1. 프레임워크 개념

- Framework 를 직역하면 `뼈대` 를 의미한다.
- 이것을 소프트웨어 관점에서 보면 아키텍처에 해당하는 `골격 코드`이다.
- 애플리케이션을 개발할 때, 가장 중요한 것이 전체 애플리케이션의 구조를 결정하는 `아키텍처`.
- **프레임워크는 이 아키텍처에 해당하는 골격 코드를 제공한다.**

골격 코드를 제공해주기에 이에 맞춰 개발하면 누가 개발을 하더라도 같은 성능을 보이는 애플리케이션를 만들 수 있게 된다. 그리고 이는 각각 다른 스타일로 개발해오던 사람들에게 번역기 역할도 된다고 본다.

## 1-1. 프레임워크의 장점

프레임워크로부터 골격 코드를 제공받음으로써 얻는 이득은 쉬운 관리(`유지보수`), `개발 속도 향상`, `재사용성 증가` 등 다양하다. 

`빠른 구현 시간`

- 프레임워크가 제공하는 골격 코드가 존재하기에 개발자는 비즈니스 로직만 구현하면 되고, 제한된 시간에 많은 기능을 구현할 수 있게 됩니다.
- 이는 비즈니스 로직에 집중할 수 있는 시간을 벌어주기도 합니다.

`유지보수 비용과 시간 절약`

- 같은 프레임워크가 적용된 애플리케이션은 아키텍처가 같으므로 관리하기 쉽습니다.
- 결론적으로 유지보수 비용과 시간을 절약할 수 있습니다.

`개발자들의 역량 획일화`

- 숙련된 개발자와 초급 개발자는 지식과 경험이 다르므로 두 개발자가 만든 코드의 품질은 당연히 다를 수 밖에 없습니다.
- 하지만 프레임워크를 사용하면 골격이 존재하기에 두 개발자의 코드가 비슷해집니다.
- 그리고 초급 개발자도 세련되고 효율적인 코드를 생성해낼 수 있게 됩니다.
- 결과적으로 관리자 입장에서 개발 인력을 더 효율적으로 구성할 수 있습니다.

`검증된 아키텍처의 재사용과 일관성 유지`

- 검증된 프레임워크를 사용하게 되면 프레임워크가 제공하는 검증된 아키텍처를 이용합니다.
- 따라서 아키텍처에 관한 별다른 고민이나 검증없이 소프트웨어를 개발할 수 있습니다.
- 그리고 이렇게 개발한 시스템은 골격에 맞춰져 있기 떄문에 시간이 지나도 유지보수 과정에서 아키텍처가 왜곡되거나 변형되지 않게 됩니다.


# 2. 스프링 프레임워크

대부분의 기술들은 기존 상황을 개선하기 위해서 등장합니다. 따라서 기술의 탄생 배경을 알면 기술의 의도를 파악하는데 도움이 됩니다.

## 2-1. 스프링 탄생 배경

- `스프링 프레임워크`는 로드 존슨(Rod Johnson)이 2004년에 만든 `오픈소스` 프레임워크
- 스프링 프레임워크가 등장하기 이전에는 자바 기반의 엔터프라이즈 애플리케이션은 대부분 `EJB`(Enterprise Java Beans)로 개발되었습니다.
    - EJB 기술은 EJB 컨테이너가 제공하는 많은 기능과 나름의 장점에도 여러 가지 문제점이 있었고 개발자들로부터 외면받을 수 밖에 없었습니다.
    - EJB는 스펙이 너무 복잡해서 학습에 많은 시간이 필요하고, 유지보수 역시 복잡하고 힘들었습니다.
    - 어렵게 EJB 컴포넌트를 만들었다고 해도 이를 배치하고 실행하기 위해 WAS(Web Application Server)가 필요했고 대부분 고가의 장비들이었습니다.
    - 결국 EJB를 기반으로 비즈니스 컴포넌트를 구현하는 것은 매우 복잡하고 비싸며, 많은 시간과 노력이 필요하게 됩니다.
    - 또한 EJB를 제대로 사용하려면 성능을 유지해주고, 유지보수의 편의성을 향상해주는 다양한 디자인 패턴을 이해하고 적용할 수 있어야합니다.
    - 따라서 이런 이해없이 EJB를 사용한다면 문제가 발생합니다. 이런 디자인 패턴을 반드시 사용해야하는 것도 EJB의 문제로 볼 수 있습니다.
- 스프링 프레임워크는 평범한 `POJO`(Plain Old Java Object)를 사용하면서도 EJB에서만 가능했던 많은 일들을 가능하도록 지원합니다.
- 그리고 스프링 프레임워크는 이미 많은 디자인 패턴이 적용되어 배포되므로 프레임워크를 사용하는 것 자체가 디자인 패턴을 사용하는 것이 됩니다.
- 따라서 여러 디자인 패턴을 숙지하고 있어야 직접 적용했어야했던 EJB보다 매우 간단합니다.
- 이렇게 많은 시간이 단축되고 편리해졌으니 개발자들이 열광할 수 밖에 없었겠죠.

## POJO(Plain Old Java Object)란?

- POJO란 말 그대로 평벙함 자바 객체를 의미
- POJO를 좀 더 쉽게 이해하기 위해서는 반대로 POJO가 아닌 클래스가 무엇인지 이해하면 된다.
- 대표적인 Not POJO 클래스는 Servlet 클래스이다.
- Servlet 클래스는 우리 마음대로 만들 수 없으며, 반드시 Servlet에서 요구하는 규칙에 맞게 클래스를 만들어야 실행할 수 있다.
- 다음은 Servlet 클래스 작성 규칙입니다.
    - servlet 관련 패키지를 import해야한다.
    - public 클래스로 선언되어야 한다.
    - Servlet, GenericServlet, HttpServlet 중 하나를 상속해야 한다.
    - 기본 생성자가 있어야 한다.
    - 생명주기에 해당하는 메소드를 재정의(Overriding) 해야한다.

## 2-2. 스프링 프레임워크의 특징

스프링의 특징을 한 줄로 하면 "IoC와 AOP를 지원하는 경량의 컨테이너 프레임워크" 이다

`경량`**(Lightweight)**

- 스프링은 크기 측면에서 가볍다. **물론 이는 상대적인 것이다.**
- 스프링은 여러 개의 모듈로 구성되어 있으며 각 모듈은 하나 이상의 JAR 파일로 구성된다.
- 그리고 이 몇 개의 JAR 파일만 있으면 개발과 실행이 모두 가능해서 스프링을 이용해 만든 애플리케이션 배포 역시 매우 빠르고 쉽다.
- 또한 스프링 프레임워크가 POJO 형태의 객체를 관리하기 때문에 기존 EJB 객체를 관리하는 것보다 훨씬 가볍고 빠를 수 밖에 없다.

`제어의 역행`**(IoC : Inversion of Controll)**

- 비즈니스 컴포넌트를 개발할 때, 항상 신경 쓰는 것은 `낮은 결합도`와 `높은 응집도`이다.
- 스프링은 `IoC`를 통해 애플리케이션을 구성하는 `객체 간의 느슨한 결합`, 즉 낮은 결합도를 유지한다.
- 쉽게 말해서 IoC를 적용되기 전에는 개발자가 애플리케이션 수행에 필요한 객체의 생성이나 객체 간의 의존관계를 **직접** 코드로 처리해야 한다.
- 하지만 IoC를 적용한다면 이를 `컨테이너`가 대신 처리하게 된다.
- 결과적으로 **의존관계가 명시되지 않으므로 결합도가 떨어져서 유지보수가 편리해진다.**
- **즉, IoC는 기존에 개발자가 하던 애플리케이션 흐름 제어를 컨테이너에게 넘겨주는 것이다.**

`관점지향 프로그래밍`**(AOP : Aspect Oriented Programming)**

- 관점지향 프로그래밍(`AOP`)은 비즈니스 메소드를 개발할 때, 핵심 비즈니스 로직과 각 비즈니스 메소드마다 반복해서 등장하는 `공통 로직을 분리`함으로써 **응집도가 높게 개발**할 수 있도록 지원한다.
- **공통으로 사용하는 기능들을 외부의 독립된 클래스로 분리하고, 해당 기능을 프로그램 코드에 직접 명시하지 않고 선언적으로 처리하여 적용한다**는 것이 **관점지향 프로그래밍의 기본 개념.**
- 공통 기능을 따로 분리해 관리하니 **응집도가 높은** 비즈니스 컴포넌트를 만들 수 있을 뿐만 아니라 **유지보수도 혁신적으로 향상된다.**

`컨테이너`**(Container)**

- `컨테이너`는 특정 `객체의 생성과 관리`를 담당하며 `객체 운용`에 필요한 다양한 기능을 제공한다.
- 일반적으로 서버 안에 포함되어 배포 및 구동된다.
- **대표적인 컨테이너**로는 Servlet 객체를 생성하고 관리하는 `Servlet 컨테이너`와 EJB 객체를 생성하고 관리하는 `EJB 컨테이너`가 있다.
- **우리가 사용하는 톰캣 서버에도 Servlet 컨테이너가 포함되어 있다.**
- **애플리케이션의 운용에 필요한 객체를 생성하고 객체 간의 의존관계를 관리한다는 점에서 스프링도 일종의 컨테이너라고 할 수 있다.**

## IoC(Inversion of Control) 컨테이너

기존에 자바 기반으로 애플리케이션을 개발할때는 애플리케이션이 구동하는 동안 어떤 객체를 생성할지 판단하고 객체 간의 의존관계 역시 개발자가 코드로 표현해야 했다.

즉, 제어 권한이 개발자에게 있었던 것이다. 이 제어 권한을 `컨테이너`에게 넘기는 것이 `제어의 역행`(IoC)이다

`톰캣 서버`에도 `Servlet 컨테이너`가 포함되어 있는데 이를 이용해 실습해볼 것이다.

스프링 컨테이너 역시 이와 유사하게 동작하고 비슷한 요소들이 존재한다.

아래와 같은 코드를 준비한다. package는 개인마다 다를 수 있으니 주의한다.

```java
package com.example.spring_mvc.servlet;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class HelloServlet extends HttpServlet {

	public HelloServlet() {
		super();
		System.out.println("LOG : HelloServlet 객체 생성");
	}

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		super.doGet(req, resp);
		System.out.println("LOG : doGet() 메소드 호출");
	}

}
```

이후에 톰캣 설정 파일인 `web.xml`에 servlet을 등록해줄 것이다.

필자는 STS로 생성한 Spring MVC 패키지 내부의 web.xml에 등록했다.

프로젝트 구조는 아래와 같다.

![day-1-i/Untitled%201.png](day-1-image/Untitled%201.png)

```xml
<!-- web.xml -->

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee https://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
	<servlet>
		<servlet-name>hello</servlet-name>
		<servlet-class>com.example.spring_mvc.servlet.HelloServlet</servlet-class>	
	</servlet>
	
	<servlet-mapping>
		<servlet-name>hello</servlet-name>
		<url-pattern>/hello.do</url-pattern>
	</servlet-mapping>
</web-app>
```

위와 같이 기술해놓으면 동작 과정은 다음과 같다.

- '/hello.do' 라는 URL 패턴으로 GET 요청을 했을 때
- 컨테이너가 hello라는 이름의 Servlet 객체. 즉, HelloServlet를 생성하고
- GET요청을 했으니 doGet()메소드가 실행된다.

이제 본인이 설정한 톰캣 포트(보통 기본값은 8080, 필자는 8081)에 /hello.do로 접속해보면 console에 로그가 찍히는 것을 볼 수 있다.

![day-1-image/Untitled%202.png](day-1-image/Untitled%202.png)

![day-1-image/Untitled%203.png](day-1-image/Untitled%203.png)

전체 과정을 요약하자면

- xml등 설정을 통해서 servlet을 컨테이너에 등록해주고
- 컨테이너가 등록된 url-pattern과 일치하는 요청이 오면
- 해당 servlet 객체를 생성한다
- 즉 등록만 해주면 개발자가 servlet 객체 생성에 관여할 일 없이 컨테이너가 이를 관리해준다.

**정리하자면, IoC를 통해 얻는 이점은 다음과 같다.**

- 개발자가 기술해야 했던 객체 생성과 의존관계에 대한 코드가 사라지고
- 결과적으로 **낮은** `결합도`**의 컴포넌트를 구현할 수 있게된다.**

## 결합도

**어떤 모듈이 다른 모듈에 의존하는 정도**. 보통 `응집도`(**cohesion**)와 대비된다.

## **결합도가 높으면 생기는 문제점**

예시를 들어보자면, A라는 클래스(`Main`이라 하자)를 실행하는데 B라는 `Task`가 필요하다고 하자. 즉, 이는 A를 실행하는데 B가 필요하기 때문에 A가 B에 `의존`하는 상태이다.

이 상황에서 A에 사용하는 B를 C라는 Task로 변경해야하는 일이 생겼다. 

그럼 다음과 같은 시나리오가 진행될 것이다.

- A의 내부 코드에 B를 모두 C에 연관된 코드로 바꿔야한다.
- 그런데 만약 C의 내부 메소드와 B의 내부 메소드 명이 다르다면?
- A에서 B의 메소드를 호출해서 사용했던 코드들을 C에 맞게 모두 수정해주는 작업을 해야한다.
- 만약 이런 **의존관계가 많고 복잡하다면**? 모든 코드를 수정해야 할 수도 있다.
- 따라서 **결합도가 높으면 자연스럽게 유지보수가 힘들어진다.**

**아래 결합도와 관련된 모든 내용은 이 시나리오를 기반으로 한다.**

## 결합도를 낮추는 방법

결합도가 유지보수에 문제가 된다면 이를 **해결하는 방법**을 알아야한다.

1. **다형성 이용하기**
    - 객체지향 언어의 핵심 개념인 `다형성`(Polymorphism)을 이용한다
    - 만약 위 시나리오에서 B와 C가 Task라는 클래스를 상속해서 만들어지고 메소드 명이 같다면,
    - A에 결합하는 객체를 Task로 하고 B나 C를 이에 맞게 끼워주면 된다.
    - 이렇게되면 코드를 적게 수정하고 모듈을 변경할 수 있다.
    - **하지만 이 또한 코드를 수정해야한다. → 더 좋은 방법이 있다.**
2. **디자인 패턴 이용하기**

    결합도를 낮추기 위해서 디자인 패턴을 이용하는 방법도 있다.

    `Factory 패턴`

    - 클라이언트에서 사용할 객체를 `캡슐화`해서 결합 상태를 느슨하게 만들어준다
    - Factory 패턴을 사용하면 클라이언트 소스를 수정하지 않고 위 시나리오에서 Task를 변경할 수 있다.
    - 예시 코드는 아래와 같다. 아래 코드에 Factory 패턴을 적용하면서 main 메소드가 바뀌는 것에 주목하자.

    ```java
    // MainA.java

    public class MainA {
    	
    	public static void main(String[] args) {		
    		Task task = new TaskB();
    //		Task task = new TaskC();
    		
    		task.service();
    	}

    }

    // Task.java

    public interface Task {
    	
    	public void service();

    }

    // TaskB.java

    public class TaskB implements Task{

    	@Override
    	public void service() {
    		System.out.println("LOG : Task B를 실행합니다.");
    	}

    }

    //TaskC.java

    public class TaskC implements Task{

    	@Override
    	public void service() {
    		System.out.println("LOG : Task C를 실행합니다.");
    		
    	}	

    }
    ```

    이후에 Task를 관리할 BeanFactory를 만들고 그에 맞게 main 메소드도 수정해줍니다.

    ```java
    // BeanFactory.java

    import java.util.HashMap;

    public class BeanFactory {
    	
    	HashMap<String, Object> beanMap = new HashMap<String, Object>();
    	
    	public BeanFactory() {
    		beanMap.put("taskB", new TaskB());
    		beanMap.put("taskC", new TaskC());
    	}
    	
    	public Object getBean(String beanName) {		
    		return beanMap.get(beanName);		
    	}

    }

    // MainA.java

    public class MainA {

    	public static void main(String[] args) {		
    		BeanFactory beanFactory = new BeanFactory();
    		// Task task = new TaskB();
        // Task task = new TaskC();
    		Task task = (Task) beanFactory.getBean("taskB");
    		task.service();	
    	}

    }
    ```

    이렇게하면 MainA에서 관리하던 Task 객체를 생성하는 행위를 BeanFactory에게 위임하므로써 기존보다 느슨하게 결합하게 됩니다.

    또한 이 BeanFactory를 다른 클래스들에도 재사용 가능할 것입니다.

    결론적으론 유지보수 측면에서 많은 이점을 가져오겠네요

# 3. 스프링 컨테이너

대부분 IoC 컨테이너는 각 컨테이너에서 관리할 객체들을 위한 별도의 `설정 파일`이 있습니다.

톰캣에서는 web.xml을 이용하죠.

스프링 컨테이너도 이러한 설정 파일이 필요합니다.

여기서는 xml파일을 이용해 설정합니다.

기존 코드를 수정해보겠습니다.

`스프링이 import되어 있는 프로젝트에서 진행해야합니다.`

필자는 STS에서 스프링 레거시 프로젝트를 생성하여 진행했습니다.

아래와 같이 src/main/resources 폴더 아래에 applicationContext.xml을 생성하고 `<bean>` 태그를 이용해서 bean을 등록하도록 설정합니다.

![day-1-image/Untitled%204.png](day-1-image/Untitled%204.png)

```xml
<!-- applicationContext.xml -->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- class="class위치" ex) class="com.example.spring_mvc.TaskD"-->
	<!-- 필자는 default package에 각 클래스를 생성하여서 아래와 같이 기술합니다.-->
	<bean id="taskB" class="TaskB"></bean>
	<bean id="taskC" class="TaskC"></bean>

</beans>
```

 그리고 main메소드를 수정해보겠습니다.

```java
// MainA.java

import org.springframework.context.support.AbstractApplicationContext;
import org.springframework.context.support.GenericApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class MainA {

	public static void main(String[] args) {		
//		BeanFactory beanFactory = new BeanFactory();
		
		// 스프링 컨테이너 중 하나
		AbstractApplicationContext factory = 
             new GenericXmlApplicationContext("applicationContext.xml");
		
//		Task task = (Task) beanFactory.getBean("taskB");
//		Task task = (Task) factory.getBean("taskB");		
		
//		task.service();	
	}

}
```

`GenericXmlApplicationContext`는 스프링 컨테이너 중 하나입니다. 

설정 파일인 `applicationContext.xml` 을 읽어와서 xml에 기술된 `Bean`을 컨테이너에 등록하게 됩니다.

bean이 언제 생성되는지 알기 위해서 생성자를 넣어두었습니다.

main메소드를 실행해서 확인해보면 Console Log는 다음과 같습니다.

```prolog
INFO : org.springframework.beans.factory.xml.XmlBeanDefinitionReader - Loading XML bean definitions from class path resource [**applicationContext.xml**]
INFO : org.springframework.context.support.GenericXmlApplicationContext - Refreshing org.springframework.context.support.GenericXmlApplicationContext@b3d7190: startup date [Mon May 24 19:14:37 KST 2021]; root of context hierarchy
INFO : org.springframework.beans.factory.support.DefaultListableBeanFactory - Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@5b7a5baa: defining beans [**taskB,taskC**]; root of factory hierarchy
LOG : **Task B를 생성**합니다.
LOG : **Task C를 생성**합니다.
```

- applicationContext.xml을 읽어오고 그 설정으로 컨테이너인 GenericXmlApplicationContext를 생성합니다.
- 그리고 생성된 컨테이너는 설정에 기술된 Bean들을 등록합니다.
- 이에 따른 생성자가 호출되고 LOG가 찍히게 됩니다.

눈치챘을지 모르겠지만, main메소드에서 bean을 호출하거나 하지 않았는데 생성자가 호출되는 걸 봐서는 이미 생성되어 있음을 알 수 있습니다.

즉 컨테이너가 미리 객체를 생성해두고 다른 클래스에서 이를 가져오고 싶을 때 컨테이너에서 미리 생성해둔 객체를 return해줍니다.

이렇게되면 컨테이너가 가지고 있는 동일한 클래스의 객체가 하나만 존재하게 됩니다.

이를 객체를 하나만 생성해서 메모리적으로 이점을 가지는 `싱글톤 패턴`이라고 합니다. 

## 스프링 컨테이너의 종류

스프링에서는 `BeanFactory`와 이를 상속한 `ApplicationContext` 두 가지 유형의 컨테이너를 제공합니다.

`BeanFactory`

- 스프링 설정 파일에 등록된 bean 객체를 생성하고 관리하는 가장 기본적인 컨테이너 기눙만을 제공.
- 클라이언트의 `요청`(Lookup)에 의해서만 bean 객체가 생성되는 `지연 로딩`(Lazy Loading) 방식을 사용
- 따라서 일반적인 스프링 프로젝트에서는 BeanFactory를 사용할 일이 전혀 없다.

`ApplicationContext`

- BeanFactory의 기본 기능 외에도 `트랙잭션 관리`, `메시지 기반의 다국어 처리` 등 다양한 기능을 지원합니다.
- 컨테이너가 구동되는 시점에 bean이 등록된 객체들을 생성하는 `즉시 로딩`(pre-loading) 방식으로 동작합니다.
- 대부분 웹 애플리케이션 개발도 지원하므로 대부분 스프링 프로젝트는 `ApplicationContext` 유형의 컨테이너를 이용합니다.

AppicationContext의 구현 클래스는 매우 다양하지만 실제로 가장 많이 사용하는 클래스 두 개는 다음과 같습니다.

`GenericXmlApplicationContext`
- 파일 시스템이나 클래스 경로에 있는 xml 설정 파일을 로딩하여 구동하는 컨테이너

`XmlWebApplicationContext`
- 웹 기반의 스프링 애플리케이션을 개발할 때 사용하는 컨테이너



## 스프링 XML 설정

`<beans>` 루트 엘리먼트

- 스프링 컨테이너는 bean 저장소에 해당하는 xml 설정 파일을 참조하여 bean의 생명주기를 관리하고 여러 서비스를 제공하기에 beans 엘리먼트는 가장 중요한 역할을 담당합니다.
- 이 설정 파일을 정확하게 작성하고 관리하는 것이 무엇보다 중요합니다.

`<import>` 엘리먼트

- 스프링 설정이 길어지고 복잡해지면 여러 설정 파일로 나눠서 관리할 수 있다
- 이럴 때 `<import>` 를 이용해서 다른 설정 파일을 불러온다
- ex) `<import resource="context-datasource.xml"/>`


<bean> 엘리먼트

- bean을 등록하기 위한 엘리먼트
- id와 class 속성을 사용.
- class는 필수 id는 필수 X
    - 이 외 속성들

    `init-method` 속성 : 스프링 컨테이너가 해당 객체가 **생성**할 때 실행할 메소드를 등록할 수 있다.

    ```xml
    <bean id="~" class="~" init-method="이 빈 객체가 생성될 때 실행할 메소드명"/>
    ```

    `destroy-method` 속성 : 스프링 컨테이너가 해당 객체를 **삭제하기 직전**에 실행할 메소드를 등록

    ```xml
    <bean id="~" class="~" destroy-method="이 빈 객체가 삭제되기 직전에 실행할 메소드명"/>
    ```

    `lazy-init` 속성 : 지연 로딩 설정(객체가 사용되는 시점에 생성되도록 함) → 메모리 관리용

    ```xml
    <bean id="~" class="~" lazy-init="true / false"/>
    ```

    `scope` 속성 : 해당 객체를 공유하는 범위를 지정 

    **기본값**은 **singleton**이다

    ```xml
    <!-- singleton : 객체를 하나만 생성함 -->
    <bean id="~" class="~" scope="singleton"/>

    <!-- prototype : 객체를 매번 생성함  -->
    <bean id="~" class="~" scope="prototype"/>
    ```

# 4. 의존성 주입

## 스프링이 의존성을 관리하는 방법

스프링 프레임워크의 가장 중요한 특징은 객체의 생성과 의존관계를 컨테이너가 자동으로 관리한다는 점이다. 이것이 `IoC`의 핵심 원리이기도 하다.

스프링은 IoC를 다음 두가지 형태로 지원한다.

- `Dependency Lookup`
    - 컨테이너가 애플리케이션 운용에 필요한 객체를 생성하고 클라이언트는 컨테이너가 생성한 객체를 `검색`(**Lookup**)하여 사용하는 방식
    - 실제 개발 과정에서 사용하지 않음 대부분 `Dependency Injection`을 사용
- `Dependency Injection` (`Setter Injection`, `Constructor Injection`)
    - 컨테이너가 직접 객체들 사이의 의존관계를 처리하는 것
    - Setter 메소드 기반 **Setter Injection**과 생성자 기반 **Constructor Injection**으로 나뉨
    - 객체 사이의 의존관계를 스프링 설정 파일에 등록된 정보를 바탕으로 컨테이너가 자동으로 처리해줌.
    - 따라서 의존성 설정을 바꾸고 싶을 때 프로그램 코드를 수정하지 않고 설정 파일 수정만으로 변경사항을 적용할 수 있어 유지보수가 향상됨.

## 의존성 관계

`의존성`(Dependency) 관계란 객체와 객체의 결합 관계.

A 객체가 B 객체의 메소드를 사용해야 한다면, A는 B에 의존하는 상태

B객체의 생성 코드를 A 객체나 C 등 다른 객체에서 중복해서 사용하게 된다면 메모리적으로 손실.

스프링 컨테이너는 이를 `DI`(Dependency Injection) 를 이용해서 해결

## 생성자 인젝션

프로젝트 구조는 다음과 같습니다.

![day-1-image/Untitled%207.png](day-1-image/Untitled%207.png)

먼저 applicationContext.xml 설정을 다음과 같이 기술합니다.

이렇게 설정하면 다음과 같이 스프링 컨테이너가 의존성을 주입해줍니다.

- Process 클래스를 생성할 때 생성자 `arg`로 taskB(`bean id`)라는 이름의 빈을 주입해준다.
- **이름에 주의해주세요!**

```xml
<!-- applicationContext.xml -->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="process" class="DI.Process">
		<constructor-arg ref="taskB"></constructor-arg>
	</bean>
	<bean id="taskB" class="springContainer.TaskB"></bean>
	<bean id="taskC" class="springContainer.TaskC"></bean>

</beans>
```

이후에 아래 코드들을 위 프로젝트 구조에 맞게 준비합니다.

```java
// Task.java

package springContainer;

public interface Task {
	
	public void service();
}

// TaskB.java

package springContainer;

public class TaskB implements Task{
	
	public TaskB() {
		System.out.println("CREATE : Task B를 생성합니다.");
	}

	@Override
	public void service() {
		System.out.println("RUN : Task B를 실행합니다.");		
	}

}

// TaskC.java

package springContainer;

public class TaskC implements Task{

	public TaskC() {
		System.out.println("CREATE : Task C를 생성합니다.");
	}

	@Override
	public void service() {
		System.out.println("RUN : Task C를 실행합니다.");		
	}
	

}

// Process.java

package DI;

import springContainer.Task;

public class Process {
	
	private Task task;

	public Process(Task task) {		
		this.task = task;
		
		System.out.println("CREATE : Process를 생성합니다.");
	} 
	
	public void service() {
		this.task.service();
	}
	

}
```

마지막으로 메인 메소드를 실행할 DITest 클래스입니다.

```java
// DITest.java

package DI;

import org.springframework.context.support.AbstractApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class DITest {
	
	public static void main(String[] args) {
		// 스프링 컨테이너 중 하나
		AbstractApplicationContext factory = new GenericXmlApplicationContext("applicationContext.xml");
		
		Process process = (Process) factory.getBean("process");		
		
		// TaskB가 실행됩니다.
		process.service();		
	}

}
```

그리고 이를 실행하면 어떻게 될지 생각해보고 로그를 보겠습니다.

- Process 생성하기 위해서는 TaskB가 필요하니 TaskB를 먼저 생성하고 Process 객체가 생성될 것이다.

이제 로그를 확인해보면 실제로 위 과정이 일어나는 것을 알 수 있습니다.

```prolog
INFO : org.springframework.beans.factory.xml.XmlBeanDefinitionReader - Loading XML bean definitions from class path resource [applicationContext.xml]
INFO : org.springframework.context.support.GenericXmlApplicationContext - Refreshing org.springframework.context.support.GenericXmlApplicationContext@588df31b: startup date [Mon May 24 21:45:34 KST 2021]; root of context hierarchy
INFO : org.springframework.beans.factory.support.DefaultListableBeanFactory - Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@37271612: defining beans [process,taskB,taskC]; root of factory hierarchy
CREATE : Task B를 생성합니다.
CREATE : Process를 생성합니다.
CREATE : Task C를 생성합니다.
RUN : Task B를 실행합니다.
```

**스프링 컨테이너는 기본적으로 bean이 등록된 순서대로 객체를 생성하며 모든 객체는 기본 생성자 호출을 기본값으로 합니다.**

**이를 설정을 이용해서 순서를 조정하고 매개변수가 있는 생성자를 사용할 수 있게 된 것입니다.**

## 다중 변수 매핑

매개 변수를 여러개 받을 수도 있습니다.

코드를 조금 수정해보겠습니다.

```java
// Process.java

package DI;

import springContainer.Task;

public class Process {

	private Task task;
	private int price;

	public Process(Task task, int price) {
		this.task = task;
		this.price = price;

		System.out.println("CREATE : Process를 생성합니다.");
	}

	public void service() {
		this.task.service();
		System.out.println("RUN : 비용은 '" + price + "'원 입니다.");
	}

}
```

```xml
<!-- applicationContext.xml -->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="process" class="DI.Process">
		<constructor-arg ref="taskB"></constructor-arg>
		<constructor-arg value="1000"></constructor-arg>
	</bean>
	<bean id="taskB" class="springContainer.TaskB"></bean>
	<bean id="taskC" class="springContainer.TaskC"></bean>

</beans>
```

다시 DITest.java를 실행해보면 console log는 다음과 같습니다.

```prolog
INFO : org.springframework.beans.factory.xml.XmlBeanDefinitionReader - Loading XML bean definitions from class path resource [applicationContext.xml]
INFO : org.springframework.context.support.GenericXmlApplicationContext - Refreshing org.springframework.context.support.GenericXmlApplicationContext@588df31b: startup date [Mon May 24 21:56:08 KST 2021]; root of context hierarchy
INFO : org.springframework.beans.factory.support.DefaultListableBeanFactory - Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@38102d01: defining beans [process,taskB,taskC]; root of factory hierarchy
CREATE : Task B를 생성합니다.
CREATE : Process를 생성합니다.
CREATE : Task C를 생성합니다.
RUN : Task B를 실행합니다.
RUN : 비용은 '1000'원 입니다.
```

눈치채신 분도 있겠지만, 위 코드에서 TaskB와 TaskC는 Task 인터페이스를 상속받은 구현체입니다.

기본에 Process Bean에 의존성을 주입하기 위해 기술했던 `ref="taskB"` 를 taskC로 변경해도 프로그램은 잘 돌아갈 것입니다. 하지만 **자바 코드는 변경하지 않았죠.** 이런 방식으로 다형성과 DI를 이용하면 유지보수 면에서 효율이 좋아지겠죠?

## Setter 인젝션

생성자 인젝션과 비슷한 과정이지만 `setter 메소드`를 이용해서 의존성을 주입한다는 차이가 있습니다.

둘 중 아무거나 사용해도 상관 없지만, 개발 팀, 코딩 스타일에 따라서 달라질 것입니다.

**예외로 setter 인젝션이 불가능한 클래스에 대해서만 생성자 인젝션을 사용합니다.**

기존 코드를 setter 인젝션으로 변경해보겠습니다.

```xml
<!-- applicationContext.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="process" class="DI.Process">
		<property name="price" value="2000"></property>
		<property name="task" ref="taskC"></property>
	</bean>
	<bean id="taskB" class="springContainer.TaskB"></bean>
	<bean id="taskC" class="springContainer.TaskC"></bean>

</beans>
```

```java
// Process.java

package DI;

import springContainer.Task;

public class Process {

	private Task task;
	private int price;

	public Process() {	
		System.out.println("CREATE : Process를 생성합니다.");
	}

	public void service() {
		this.task.service();
		System.out.println("RUN : 비용은 '" + price + "'원 입니다.");
	}

	public Task getTask() {
		return task;
	}

	public void setTask(Task task) {
		this.task = task;
		System.out.println("INJECTED : setTask() - task injected");
	}

	public int getPrice() {		
		return price;		
	}

	public void setPrice(int price) {		
		this.price = price;
		System.out.println("INJECTED : setPrice() - price injected");
	}
	
	
	

}
```

DITest.java는 기존 코드와 동일합니다.

```java
// DITest.java

package DI;

import org.springframework.context.support.AbstractApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class DITest {
	
	public static void main(String[] args) {
		// 스프링 컨테이너 중 하나
		AbstractApplicationContext factory = new GenericXmlApplicationContext("applicationContext.xml");
		
		Process process = (Process) factory.getBean("process");		
		
		// TaskB가 실행됩니다.
		process.service();		
	}

}
```

이제 DITest.java의 main 메소드를 실행하면 setter 메소드가 실행될 때 마다 로그가 찍히겠죠?

한 번 확인해보겠습니다.

```prolog
INFO : org.springframework.beans.factory.xml.XmlBeanDefinitionReader - Loading XML bean definitions from class path resource [applicationContext.xml]
INFO : org.springframework.context.support.GenericXmlApplicationContext - Refreshing org.springframework.context.support.GenericXmlApplicationContext@5579bb86: startup date [Mon May 24 22:09:34 KST 2021]; root of context hierarchy
INFO : org.springframework.beans.factory.support.DefaultListableBeanFactory - Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@7c7a06ec: defining beans [process,taskB,taskC]; root of factory hierarchy
CREATE : Process를 생성합니다.
CREATE : Task C를 생성합니다.
INJECTED : setPrice() - price injected
INJECTED : setTask() - task injected
CREATE : Task B를 생성합니다.
RUN : Task C를 실행합니다.
RUN : 비용은 '2000'원 입니다.
```

조금 달라진 점이 있다면, 

기존에 생성자 인젝션은 

- Process를 생성하기 전에 Task를 먼저 생성했지만,

setter 인젝션은

- Process를 미리 생성하고
- Task를 생성한 후에 setter 메소드를 통해 injection 해줍니다.

# 5. 어노테이션 기반 설정

대부분 프레임워크가 그렇듯이 스프링 프레임워크도 XML 설정이 매우 중요합니다. 

XML에 과도한 설정을 기술한다면 부담이 크고 관리하기 힘들겠죠.

이로 인해 프레임워크 사용을 꺼리기도 합니다. 사실 필자도 XML설정을 좋아하진 않습니다.

대신 대부분 프레임워크는 어노테이션 기반 설정을 지원하고 스프링도 이를 지원합니다.

## Context 네임스페이스 추가

기존 applicationContext.xml 파일에 `context` 네임스페이스를 추가해줍니다.

STS에서는 xml 파일을 오픈 했을때 하단 왼쪽 메뉴에서 Namespaces탭을 누르면 다음과 같이 쉽게 설정할 수 있습니다. 

![day-1-image/Untitled%208.png](day-1-image.png)

혹은 아래와 같이 상단에 context 네임스페이스를 직접 기술해줘도 됩니다.

```xml
<!-- applicationContext.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd">

	<bean id="process" class="DI.Process">
		<property name="price" value="2000"></property>
		<property name="task" ref="taskC"></property>
	</bean>
	<bean id="taskB" class="springContainer.TaskB"></bean>
	<bean id="taskC" class="springContainer.TaskC"></bean>

</beans>
```

## 컴포넌트 스캔(component-scan) 설정

스프링 설정 파일에 bean을 등록하지 않고 자동으로 생성하는 방법도 있습니다.

이는 특정 `@Component` `어노테이션`을 클래스에 붙여주면 스프링의 `컴포넌트 스캔`(component-scan)이 이를 스캔해서 bean을 등록해주는 방식입니다.

기존에 작성했던 예시에서 component-scan 방식으로 바꾼 뒤 Process에서 Task를 injection 받아 보겠습니다.

프로젝트 구조는 다음과 같습니다.

![day-1-image/Untitled%209.png](day-1-image/Untitled%209.png)

기존 스프링 xml 파일에 `<context:component-scan/>`이라는 엘리먼트를 정의합니다.

```xml
<!-- applicationContext.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd">

	<!-- 
	<bean id="process" class="DI.Process">
		<property name="price" value="2000"></property>
		<property name="task" ref="taskC"></property>
	</bean>
	<bean id="taskB" class="springContainer.TaskB"></bean>
	<bean id="taskC" class="springContainer.TaskC"></bean>
	 -->
	 
	 <context:component-scan base-package="com.example.spring_mvc"></context:component-scan>

</beans>
```

클래스 상단에 `@Component` 어노테이션을 붙이면 컴포넌트 스캔 시 해당 클래스가 대상이 됩니다.

`@Autowired`가 보일텐데 이는 의존성을 자동으로 주입해달라는 의미입니다.

```java
// Process.java

package com.example.spring_mvc.DI;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import com.example.spring_mvc.Container.Task;

@Component
public class Process {

	@Autowired
	private Task task;	
//	private int price;

	public Process() {	
		System.out.println("CREATE : Process를 생성합니다.");
	}

	public void service() {
		this.task.service();
//		System.out.println("RUN : 비용은 '" + price + "'원 입니다.");
	}

//	public Task getTask() {
//		return task;
//	}
//
//	public void setTask(Task task) {
//		this.task = task;
//		System.out.println("INJECTED : setTask() - task injected");
//	}

//	public int getPrice() {		
//		return price;		
//	}
//
//	public void setPrice(int price) {		
//		this.price = price;
//		System.out.println("INJECTED : setPrice() - price injected");
//	}

}

// TaskC.java 

package com.example.spring_mvc.Container;

import org.springframework.stereotype.Component;

@Component
public class TaskC implements Task{

	public TaskC() {
		System.out.println("CREATE : Task C를 생성합니다.");
	}

	@Override
	public void service() {
		System.out.println("RUN : Task C를 실행합니다.");		
	}
	

}
```

`@Component("id")` 과 같이 기존 xml에 썼던 id를 기술 가능합니다.

id를 기술하지 않으면 스프링이 알아서 클래스의 첫 글자를 소문자로 변경한 이름으로 id를 지정해줍니다.

눈치챘을지 모르겠지만, 현재 Process.java에 setter와 매개변수가 지정된 생성자가 없습니다.

하지만 놀랍게도 실행이 잘 됩니다.

로그를 보아서는 스프링 컨테이너가 생성자 인젝션을 한 것으로 보이고

![day-1-image/Untitled%2010.png](day-1-image/Untitled%2010.png)

## 의존성 주입 관련 어노테이션

보통 `@Autowired`를 많이 사용한다.

`@Autowired`

- 주로 변수 위에 설정하여 해당 `변수 타입`을 기준으로 객체를 찾는다

`@Qualifier`

- 특정 객체 이름(`id`)을 이용하여 의존성 주입
- 같은 클래스의 객체가 여러개일 경우 사용
- @Component("taskC")으로 주입할 빈 클래스 위에  bean id를 지정해주고
- @Qualifier("taskC")으로 bean id와 매칭되는 빈 클래스를 인젝션 받습니다.

`@Inject`

- @Autowired와 동일한 기능
- 객체 이름 기반으로 의존성 주입

`@Resource`

- @Autowired + @Qualifier
- ex) @Resource(name="taskC")

## 추가 어노테이션

아래 어노테이션은 모두 @Component가 포함되어 있다.

`@Service` 

비즈니스 로직을 처리할 Service 클래스에 사용

`@Repository`

DB 연동과 관련된 클래스(DAO, Repository)에 사용

`@Controller`

사용자 요청을 제어하는 Controller 클래스에 사용

# 정리

IoC

- IoC란 제어의 역행을 의미하며 기존에 개발자가 객체의 생성 주입을 관리하던 것을
- 컨테이너가 대신해주어 제어 권한을 위임하는 것이다.

스프링 컨테이너

- Bean들을 미리 생성해두고 의존관계를 편리하게 추가해주는 컨테이너

참고

[http://www.yes24.com/Product/Goods/29173715](http://www.yes24.com/Product/Goods/29173715)