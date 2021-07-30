# Spring Validation

# 개요

Spring-validation에 대해서 학습하고 정리한 글입니다.

**해당 글은 spring-boot 2.5.2 에서 진행되었습니다**.

Spring Validation은 어노테이션으로 간편하게 특정 값을 validation할 수 있도록 도와줍니다.

# Gradle

spring validation 사용을 위해서 그래이들 의존성을 추가합니다.

```groovy
dependencies {
		...
    implementation 'org.springframework.boot:spring-boot-starter-validation'
		...
}
```

# 예시

spring-validation은 다음과 같이 각각에 맞는 **Annotation**을 붙여줌으로써 값을 검증할 수 있습니다.

먼저 아래 예시 코드를 보고 어떻게 사용하는지 감을 잡아보세요.
작명이 잘 되어있어서 어느정도 유추할 수 있을 겁니다!

이후 내용에서 관련 **Annotation**에 대해 다룹니다.

```java
// Api.java

@GetMapping
public User get(@RequestParam @NotBlank String name,
                @RequestParam @Min(value = 1, message = "age는 1이상이어야 합니다.") Integer age) {
    User user = new User();
    user.setName(name);
    user.setAge(age);

    return user;
}

@PostMapping
public User post(@RequestBody @Valid UserDto user) {
    System.out.println(user);

    return user;
}

// UserDto.java

public class UserDto {

    @Size(min = 1, max = 10) // 길이
    private String name;

    @NotNull // null 불가능
    @Min(1) // 1 이상
    private Integer age;

		public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

}
```

# 관련 Annotation

- `@Valid`

해당 object에 validation을 하겠다는 표시

- `@NotNull`

null 불가능

- `@NotEmpty`

null, ""(공백) 불가능

- `@NotBlank`

null, "", " " 불가능

- `@Pattern`

정규식을 이용해서 검증 가능

- `@Size`

문자 길이 검증, 숫자 type은 불가능

**ex) @Size(min = 0, max = 11)**

- `@Max`

최대값 검증

- `@Min`

최소값 검증

- `@AssertTrue` / `@AssertFalse`

별도의 로직으로 검증 가능 → 조금 아래에서 살펴봅니다.

- `@Past`

과거 날짜 (오늘 이전)

- `@PastOrPresent`

오늘이거나 과거

- `@Future`

미래 날짜 (오늘 이후)

- `@FutureOfPresent`

오늘이거나 미래

# 검증 실패 시 Message

Validation에 실패하면 message를 통해서 검증 실패 메시지를 출력할 수 있습니다.

검증 실패 시 아래와 같이 server error가 발생하고 message가 출력되는 것을 알 수 있습니다.

![Spring%20Validation/Untitled.png](Spring%20Validation/Untitled.png)

## Message 변경하기

Validation 관련 Annotation을 살펴보면

아래와 같이 message라는 멤버를 가진 걸 알 수 있습니다.

이를 한 번 커스텀 해볼게요

```java
@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
@Repeatable(List.class)
@Documented
@Constraint(validatedBy = { })
public @interface Min {

	String message() default "{javax.validation.constraints.Min.message}";

	Class<?>[] groups() default { };

	Class<? extends Payload>[] payload() default { };

	/**
	 * @return value the element must be higher or equal to
	 */
	long value();
```

아래와 같이 간단한 api를 만들고 `@Min` 을 이용해서 age가 19 미만일 때를 검증하고 error 발생 시 `message` 를 다음과 같이 설정해 보겠습니다.

```java
@GetMapping
public User get(@RequestParam @NotBlank String name,
                @RequestParam @Min(value = 19, message = "해당 컨턴츠는 19세 이상 접근 가능합니다.")
												Integer age) {
    User user = new User();
    user.setName(name);
    user.setAge(age);

    return user;
}
```

이제 해당 uri로 접근하면?

다음과 같이 message가 변경된 것을 확인할 수 있습니다

![Spring%20Validation/Untitled%201.png](Spring%20Validation/Untitled%201.png)

# 검증 로직 커스텀하기

원하는 검증 로직을 설정할 수도 있습니다.

그 방법은 다음과 같습니다.

- `@AssertTrue`와 `@AssertFalse` 이용하기

  `@AsserFalse`는 여기선 다루지 않습니다.
  간단하게 @AssertTrue와 반대로 return값을 주면 됩니다.
  return값에 대한 자세한 내용은 아래 **@AssertTrue**를 확인해주세요

- `Annotation` + `Validator` 정의하기

## @AssertTrue

`@AssertTrue`를 사용하면 다음과 같이 메서드를 새로 정의해서 검증에 사용할 수 있습니다.

**주의점 : 앞에 boolean값을 return 해야하며, 메서드 명에 접두사로 is를 붙여야합니다.
안 붙이면 인식을 못 하더라구요..**

![Spring%20Validation/Untitled%202.png](Spring%20Validation/Untitled%202.png)

Day라는 dto를 생성하고 이를 테스트해보겠습니다.

```java
public class Day {

    String localDate;

    @AssertTrue(message = "yyyy-MM-dd 형식에 맞지 않습니다.")
    public boolean isValidLocalDate() {

        try {
            LocalDate.parse(getLocalDate(), DateTimeFormatter.ofPattern("yyyy-MM-dd"));
        } catch (Exception e) {
            return false;
        }

        return true;
    }

    public String getLocalDate() {
        return localDate;
    }

    public void setLocalDate(String localDate) {
        this.localDate = localDate;
    }
}
```

**BindingResult를 Controller 매개변수에 추가하면 validation 실패 시에 error 정보를 받아올 수 있습니다.**

아래 코드는 이를 이용해서 error 메시지를 response로 전달할 수 있게 구성했습니다.

```java
@RestController
@RequestMapping("/v1")
public class Api {

    @PostMapping("/day")
    public ResponseEntity day(@RequestBody @Valid Day dto, BindingResult bindingResult) {

        if (bindingResult.hasErrors()) {
            StringBuilder sb = new StringBuilder();

            List<ObjectError> errors = bindingResult.getAllErrors();

            errors.forEach(objectError -> {
                FieldError fieldError = (FieldError) objectError;
                String field = fieldError.getField();
                String message = fieldError.getDefaultMessage();
                sb.append("field : " + field + "\n");
                sb.append("message : " + message + "\n");
            });

            return ResponseEntity.badRequest()
                    .body(sb.toString());
        }

        return ResponseEntity.ok(dto);
    }
}
```

postman을 이용해서 테스트를 해보면 validation이 잘 적용되는 것을 확인할 수 있네요!

![Spring%20Validation/Untitled%203.png](Spring%20Validation/Untitled%203.png)

## Annotation + Validator 정의하기

사실 위 **@AssertTrue** 방식은 매번 DTO마다 메서드를 정의해줘야 하기에 코드가 중복될 가능성이 있습니다.

이를 방지하기 위해서 `Annotation`과 `Validator`를 정의해서 **프로젝트 전역에서 Annotation만으로 Validation을 수행할 수 있습니다!**

**먼저 DateTime 이라는 Annotation을 정의해보겠습니다.**

**기본적으로 yyyy-MM-dd 형식으로 검증을 진행하고, pattern을 커스텀할 수도 있게 구성했습니다.**

참고로 validation message에는 EL(표현언어)를 사용할 수 있습니다.

message의 ${validatedValue}는 Request로 사용자가 입력한 값이고, {pattern}은 DateTime 필드인 pattern값 입니다.

```java
// DateTime.java

@Target({METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {DateTimeValidator.class}) // (1)
public @interface DateTime {
		// (2)
    String message() default "${validatedValue}는 {pattern}형식과 일치하지 않습니다.";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};

		// (3)
    String pattern() default "yyyy-MM-dd";

}
```

이제 `Validator`를 정의해봅시다. `ConstraintValidator` interface를 상속받아 구현합니다.

아래 보이는 DateTimeValidator는 **@DateTime**이 있는 필드 혹은 매개변수를 검증합니다.

```java
public class DateTimeValidator implements ConstraintValidator<DateTime, String> {

    private String pattern;

    @Override
    public void initialize(DateTime constraintAnnotation) {
        this.pattern = constraintAnnotation.pattern(); // (1)
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {

				// (2)
        try {
            LocalDate.parse(value, DateTimeFormatter.ofPattern(this.pattern));
        } catch (Exception e) {
            return false;
        }

        return true;

    }
}
```

- (1) @DateTime의 parttern을 가져옵니다.
- (2) LocalDate로 parsing이 가능한지 검증합니다. parsing 불가능 시 error가 발생하여 try catch 문을 사용합니다.

**이제 Validation을 위한 준비를 마쳤습니다**

이번에는 QueryString을 Validation 해보려고 합니다. 그러기 위해선 몇가지 작업이 더 필요합니다.

QueryString을 Validation하기 위해서는 해당 컨트롤러 클래스에 `@Valdated`를 추가하고, 테스트에 사용할 api를 정의합니다.

`@Validated`를 사용하면 service같이 controller가 아닌 다른 class에서도 validation을 수행할 수 있습니다.

```java
@Validated // (1)
@RestController
@RequestMapping("/v1")
public class Api {

    @GetMapping("/items") // (2)
    public ResponseEntity items(@RequestParam("createdDate") @DateTime String createdDate) {
        return ResponseEntity.ok(createdDate);
    }

}
```

- (1) **@Validated**를 추가해줍니다.
- (2) QueryString으로 **createdDate** 값을 받아서 검증할 api입니다. **@DateTime**를 확인해주세요

이후에 postman으로 테스트 해보면 다음과 같이 검증 로직이 잘 적용되는 것을 확인할 수 있습니다!

![Spring%20Validation/Untitled%204.png](Spring%20Validation/Untitled%204.png)

**주의!** 눈치채신 분도 있겠지만 이번 **items() GET API**에서 BindingResult를 매개변수로 사용하지 못 했습니다. `BindingResult`는 `@RequestBody` 혹은 `@RequestPart`사용 시에만 매개변수로 사용할 수 있으니 이 점 주의해주세요!

# 마무리

여기까지 Spring Validation에 대해서 알아봤습니다.

다음에는 Spring Validation과 exceptionHandler를 조합해서 API 사용자가 어떤 값을 보냈는지에 대해서 알맞는 Response를 보내줄 수 있게 하는 과정에 대해서 글을 작성해보겠습니다!

# 참고

Validation에 대해서 더 많은 정보를 알고 싶다면 아래 글들을 참고하는 걸 추천합니다!

[https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/](https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/)

[https://meetup.toast.com/posts/223](https://meetup.toast.com/posts/223)
