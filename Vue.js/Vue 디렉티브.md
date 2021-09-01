# Vue 디렉티브(Directive)

# 디렉티브 (= 지시문)

- 요소에게 특정 지시를 하는데 사용되는 특수 속성
- Vue 디렉티브 속성 값은 단일 JavaScript 표현식! (v-for은 예외)

# 디렉티브의 역할

- 디렉티브의 역할은 **표현식의 값이 변경될 때 사이드이펙트를 반응적으로 DOM에 적용하는 것**.

# [+]
- Vue 디렉티브에는 Vue에서 기본 제공하는 디렉티브가 있고 커스텀 디렉티브를 사용할 수도 있다.
- Vue 디렉티브는 'v-'라는 접두사를 가진다.
- 'v-text', 'v-for', 'v-if' 이런 방식
- [Vue 디렉티브 API 문서](https://kr.vuejs.org/v2/api/index.html#%EB%94%94%EB%A0%89%ED%8B%B0%EB%B8%8C)

# 전달인자
- 일부 디렉티브는 콜론으로 표시되는 "전달인자"를 사용 가능

ex)
``` html
<p v-bind:title="title"> ... </p>

<a v-on:click="doSomething"> ... </a>
```

# 동적 전달인자
> 2.6.0버전부터 javascript 표현식을 대괄호```[]```로 묶어 디렉티브의 인자로 사용하는 것이 가능해짐
``` html
<a v-on:[eventName]="doSomething"> ... </a>
```
# 중요 디렉티브 

## v-if
- 표현식 값의 참 거짓을 기반으로 요소(element)를 조건부 렌더링. 요소 및 포함된 디렉티브 / 컴포넌트는 토글하는 동안 다시 작성됨
- 조건이 변경될 때 트랜지션이 호출됨


## v-bind
- 동적으로 하나 이상의 컴포넌트 속성 또는 표현식을 ```바인딩```함.
- ```class``` 또는 ```style``` 속성을 묶는데 사용될 때, Array나 



## v-for
- 

## v-model
-

## v-on
-

