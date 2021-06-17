# JWT

`J`SON `W`EB `T`OKEN

# JWT란?

- 토큰 자체에 정보를 담고 있음
- 무겁지 않고
- 간편하고
- 쉽게 적용 가능
- Base64 URL Safe Encoding을 이용해서 **URL, Cooke, Header 등에 사용 가능**
- **중앙의 인증 서버, 데이터 스토어에 대한 의존성이 없어서 시스템 수평 확장에 유리함**

    **→ 기존에 인증 정보를 세션에 저장하는 방식은 해당 서버에서만 해당 인증 정보를 사용할 수 있어서 따로 세션 서버를 둬야했지만, 토큰은 클라이언트에 저장되기 때문에 세션 서버가 필요 없어짐.**

JWT는 다음과 같은 3개의 부분으로 구성되어 있음

```jsx
aaaaaa.bbbbbb.cccccc
// 앞에서부터 .을 기준으로 다음과 같음

// 헤더(header).내용(payload).서명(signature)
```

## 헤더(Header)

- Signature를 해싱하기 위한 알고리즘 정보

```jsx
const header = {
  "typ": "JWT",
  "alg": "HS256"
}
```

### Header는 두가지 정보를 가지고 있음

`typ` : 토큰의 타입을 지정 → `JWT`

`alg` : 해싱 알고리즘을 지정

alg에 기술한 알고리즘은 토큰을 **검증**할 때 `signature`부분에서 사용됨!

→ 암호화해서 쓸 것 같은 느낌이 팍팍!

## 정보(Payload)

- 서버와 클라이언트가 주고받는, **시스템에서 실제로 사용될 정보**에 대한 내용들을 담고 있음.

```jsx
{
	"sub" : "mokhs00",
	"auth" : "ROLE_USER"
	"exp" : 1480849147370
}
```

### 클레임(claim)

Payload 부분에 담는 정보의 한 **조각**을 `클레임`(**claim**)이라고 부름

**key / value** 한 쌍으로 이루어짐

클레임도 크게 세 분류로 나뉘어짐

- **등록된(registered) 클레임**
- **공개(public) 클레임**
- **비공개(private) 클레임**

### 1. 등록된(registered) 클레임

- 등록된 클레임들은 서비스에서 필요한 정보들이 아닌, **토큰에 대한 정보들을 담기 위해**서 **이름이 이미 정해져있는 클레임**! 

→ 그냥 스펙으로 느껴짐. 이미 등록되어 있다는 의미에서 명칭이 registered인듯

- 등록된 클레임의 사용은 모두 선택적임 

→ 쓰든 안 쓰든 자유라는 말

### 등록된 클레임 종류

밑줄 친 것들은 중요하다고 생각함.

- `iss` : 토큰 발급자 (issuer)
- `sub` : 토큰 제목 (subject)
- `aud` : 토큰 대상자 (audience)
- `exp` : **토큰 만료 시간 (expiration) 시간은 NumericDate 형식을 되어있어야 함.
당연한 이야기지만, 언제나 현재 시간보다 이후로 설정해야함.**
- `nbf` : Not Before을 의미, 토큰의 활성 날짜와 비슷한 개념. `exp`와 동일하게 NumericDate 형식으로 날짜를 지정. 해당 날짜가 지나기 전까지 토큰이 처리되지 않음
- `iat` : 토큰이 발급된 시간 (issued at). 이 값을 사용하여 토큰의 `age` 가 얼마나 되었는지 판단할 수 있음.
- `jti`  :JWT의 **고유 식별자**, 주로 중복적인 처리를 방지하기 위해서 사용됨. **일회용 토큰에 사용하면 유용함!**

### 2. 공개(public) 클레임

공개 클레임들은 **충돌이 방지된 (collision-resistant)** 이름을 가지고 있어야함.

충돌을 방지하기 위해서, 클레임 이름을 URI 형식으로 지음

```jsx
{
	"https://www.naver.com/jwt_claims/is_admin" : true
}
```

### 3. 비공개(private) 클레임

토큰을 사용하는 **양 측간(클라이언트 <-> 서버) 협의 하에 사용되는 클레임 이름들**

**이름이 중복되어 충돌이 생길 수 있으니 주의**

```jsx
{
  "username" : "mokhs00"
}
```

### ex) PayLoad

```jsx
{
	"iss" : "mokhs.com",
  "exp" : "1623857327070",
	"userId" : "5190",
	"username" : "mokhs"
}
```

## **❗주의 - Base64 encoding의 padding 문자와 url-safe**

`base64` 인코딩 시에 `=` 문자가 한 두개 붙을 수 있음.
이는 `base64` 인코딩의 `padding` 문자라고 불림

그런데 `=`은 파라미터로 인식될 수 있기에 **url-safe**하지 않음

**따라서 패딩 문자를 모두 지워줘야함!**

- **패밍 문자를 제거해도 decoding 시 전혀 문제가 되지 않음**

## 서명(Signature)

- JWT의 마지막 부분!
- **토큰의 유효성 검증을 위한 문자열**
    - 이 문자열을 통해 서버에서 이 토큰이 유효한 토큰인지 검증 가능
- **헤더(header)의 인코딩값, 정보(payload)의 인코딩값을 합친 후 주어진 비밀키로 해싱(hash)하여 생성**
- 서명 부분을 만드는 슈도코드(pseudocode)구조는 다음과 같음

```jsx
HMACSHA256(
	base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

- **그리고 이렇게 만든 hash를 base64인코딩을 하면 마지막 signature부분이 완성됨!**
- **여기서 핵심은 secret임 서버에 고유한 secret 값을 두면 해당 서버만 인증이 가능함**
    - **secret이 있기에 토큰의 위변조를 막을 수 있음**
    - **만약 토큰 내용이 위변조되어도 secret을 넣고 hash했을 때 기존 서명(signature)와 일치하지 않으면, 서버에선 인증 거부를 하면 됨!**

## header.payload.signature 합치기

- 각 부분의 중간 부분에 문자열 "."을 붙여서 합치면 토큰 완성!

# 마무리

## JWT 장점

- 중앙의 인증 서버, 데이터 스토어에 대한 의존성이 없음.

→ 시스템 수평 확장에 유리

- Base64 URL Safe Encoding 사용

→ URL, Cooke, Header 모두 사용 가능

- { token : "**header.payload.signature"** }

## JWT 단점

- Payload의 정보가 많아지면 네트워크 사용량 증가, 데이터 설계 고려 필요
- 토큰이 클라이언트에 저장됨, 서버에서 클라이언트의 토큰을 조작할 수 없음.


### 참고

[https://velopert.com/2389](https://velopert.com/2389)