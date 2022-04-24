# go

- [go](#go)
  - [개요](#개요)
  - [변수, 상수 선언](#변수-상수-선언)
    - [축약형 선언](#축약형-선언)
  - [타입](#타입)
  - [함수 선언](#함수-선언)
    - [ignore value(_)](#ignore-value_)
    - [defer](#defer)
    - [export function](#export-function)
  - [조건문(if, switch)](#조건문if-switch)
    - [switch, fallthrough](#switch-fallthrough)
  - [반복문(for)](#반복문for)
  - [pointer](#pointer)
  - [배열(slice, array)](#배열slice-array)
    - [slice 선언 with make()](#slice-선언-with-make)
    - [slice capacity](#slice-capacity)
  - [map](#map)
  - [구조체(struct)](#구조체struct)
  - [receiver와 method](#receiver와-method)
  - [struct String](#struct-string)
  - [타입(type)](#타입type)
  - [고루틴(goroutine)](#고루틴goroutine)
  - [channels](#channels)
    - [단방향 channel](#단방향-channel)
    - [buffered channel](#buffered-channel)

## 개요

- go 언어 사용법에 대해서 알아보자
- 다른 언어로 프로그래밍을 경험해본 사람 대상으로 작성한다

## 변수, 상수 선언

- 기본적으로 상수는 `const`, 변수는 `var` 키워드를 이용해 선언한다
- 여러 방법으로 변수, 상수 선언도 가능한데, 이는 아래 코드로 살펴보자

``` go
// 기본 선언
var age = 23

var name string
name = "mokhs"

// 변수 축약형 선언 : 아래서 자세히 알아보자
role := "Backend Engineer"

// 상수 선언
const minAge = 0

fmt.Println(age, name, role, minAge)
```

### 축약형 선언

- go 에서는 `name := "mokhs00"`와 같이 축약형 선언이 가능하다
- 대입하는 값으로 타입이 추론되어 자동으로 적용된다.
- 이는 함수 내부에서만 사용할 수 있고, 함수 외부에서는 사용이 불가능하니 주의하자

```go

// error
count := 1

func main() {
  count := 1

  fmt.Println(count)
}

```

## 타입

- go에는 타입이 존재하고 [공식문서](https://pkg.go.dev/go/types#pkg-variables)에서도 확인 가능하다

``` go

var Typ = []*Basic{
  Invalid: {Invalid, 0, "invalid type"},

  Bool:          {Bool, IsBoolean, "bool"},
  Int:           {Int, IsInteger, "int"},
  Int8:          {Int8, IsInteger, "int8"},
  Int16:         {Int16, IsInteger, "int16"},
  Int32:         {Int32, IsInteger, "int32"},
  Int64:         {Int64, IsInteger, "int64"},
  Uint:          {Uint, IsInteger | IsUnsigned, "uint"},
  Uint8:         {Uint8, IsInteger | IsUnsigned, "uint8"},
  Uint16:        {Uint16, IsInteger | IsUnsigned, "uint16"},
  Uint32:        {Uint32, IsInteger | IsUnsigned, "uint32"},
  Uint64:        {Uint64, IsInteger | IsUnsigned, "uint64"},
  Uintptr:       {Uintptr, IsInteger | IsUnsigned, "uintptr"},
  Float32:       {Float32, IsFloat, "float32"},
  Float64:       {Float64, IsFloat, "float64"},
  Complex64:     {Complex64, IsComplex, "complex64"},
  Complex128:    {Complex128, IsComplex, "complex128"},
  String:        {String, IsString, "string"},
  UnsafePointer: {UnsafePointer, 0, "Pointer"},

  UntypedBool:    {UntypedBool, IsBoolean | IsUntyped, "untyped bool"},
  UntypedInt:     {UntypedInt, IsInteger | IsUntyped, "untyped int"},
  UntypedRune:    {UntypedRune, IsInteger | IsUntyped, "untyped rune"},
  UntypedFloat:   {UntypedFloat, IsFloat | IsUntyped, "untyped float"},
  UntypedComplex: {UntypedComplex, IsComplex | IsUntyped, "untyped complex"},
  UntypedString:  {UntypedString, IsString | IsUntyped, "untyped string"},
  UntypedNil:     {UntypedNil, IsUntyped, "untyped nil"},
}


```

## 함수 선언

- 함수 선언은 `func` 키워드를 사용하여 선언한다
- 값을 여러개 return 하거나 여러개 타입을 한 번에 선언하는 등의 방식도 가능하다 이는 아래서 알아보자

``` go

// 함수 선언기본 형식

/*
func 함수명(인자) 반환타입 {
  return 반환타입과 일치하는 리턴값
}
*/

func foo(value int) int {
  return value
}

// 인자 여러개 받기
func foo(value ...int) []int {
  return value
}

// 여러 값 return
func foo(value int) (int, string) {
  return value, "SUCCESS"
}

// naked return : return 값을 미리 선언할 수 있음
func foo() (value int, message string) {
  value = 1
  message = "SUCCESS"
  return
}


```

### ignore value(_)

- 반환값을 변수로 선언해야 하지만, `_`를 이용 해당 반환값을 무시할 수 있다
- 참고로 `_`는 키워드로 취급되어 변수처럼 사용하는 것은 불가능하다
- 다음 예시를 통해 알아보자

``` go
func main() {
  _, b := foo()
}

func foo() (int, int) {
  return 1, 2
}
```

### defer

- `defer`를 이용해 함수 종료 시에 실행할 구문을 추가할 수 있다
- `defer`로 여러 개의 구문을 추가 시에 위에서부터 아래로 후입선출(LIFO)방식으로 실행된다

``` go
func foo() (int, int) {
  defer fmt.Println("isFinished")
  return 1, 2
}
```

### export function

- 함수를 export 하고 싶다면 함수의 첫 글자는 대문자로 작성해야한다
- go의 built-in package의 함수들에서 첫 글자가 대문자인 것을 발견할 수 있는데 이는 같은 이유이다

## 조건문(if, switch)

- `if`, `switch` 키워드를 이용한 조건문 작성이 가능하다
- 다른 언어들과 유사하며, 차이점이 있다면 조건문 선언과 동시에 해당 조건문 안에서만 사용할 수 있는 변수를 선언할 수 있다
- 아래 예시를 통해 알아보자

``` go
// if
func canIView(age int) bool {
  // if문 안에서 사용할 변수(koreanAge) 선언
  if koreanAge := age + 2; koreanAge < 18 {
    return false
  }
  return true
}

// switch
func canIView2(age int) bool {
  switch koreanAge := age + 2; {
  case koreanAge < 18:
    return false
  default:
    return true
  }
}
```

### switch, fallthrough

- go의 switch는 case마다 break문의 여부에 따라 연산이 종료되는 다른 언어와는 다르게 break문이 없어도 case의 조건과 일치한다면 switch 연산을 빠져나오게 된다
- 이는 go 컴파일러가 자동으로 case마다 break문을 추가해주기 때문이다
- break하고 싶지 않다면 다음과 같이 fallthrough 키워드를 이용해 break하지 않고 아래 case로 내려줄 수 있다
- 이 때 주의할 점은 아래 case에 일치하는지 여부와 상관없이 case 안의 연산을 모두 수행한다는 점이다

``` go
func main() {
  v := 1
  switch v {
  case 1:
    fmt.Println("1")
    fallthrough
  case 2: // 2와 일치하지 않지만 fallthrough에 의해서 case 와 일치할 때와 동일한 연산 수행
    fmt.Println("2")
    fallthrough
  default:
    fmt.Println("default")
  }
  
  /* 출력
  1
  2
  default
  */
}
```

## 반복문(for)

- 다른 언어에서 반복문을 사용할 때 `for`, `while`의 키워드가 존재하지만
- go에서 반복문은 `for`만 존재하고 `for`만으로 `while`의 기능도 가능하다
- 또한 조건문에서와 동일하게 반복문 안에서만 사용할 변수를 선언할 수 있다
- `range`를 이용하면 연속적인 값을 가지는 변수를 순회할 수 있다
- 자세한 건 아래 예시를 통해 알아보자

``` go
// 기본 for문
func main() {
  for i:=0; i < 10; i++ {
    // ...
  }
}

// range를 이용한 반복문
func addAll(numbers ...int) int {
  total := 0

  // range를 이용한 numbers 순회 numbers의 타입은 []int
  for _, v := range numbers {
    total += v
  }

  return total
}
```

## pointer

- C와 동일하게 포인터(pointer) 사용이 가능하다
- 포인터란 `해당 변수의 주소값을 가리키는 값`을 의미하고 주소값을 기반으로 참조를 이용하여 필요에 따라 다양한 로직을 적용할 수 있다
- 주소값을 의미하는 `&`, 해당 주소에 있는 value를 의미하는 `*`키워드를 이용한다
- 자세한 건 아래 예시를 통해서 알아보자

``` go
func main() {
  a := 2
  // b에 a의 주소값을 저장
  b := &a
  a = 10
  // 0xc00006ef60 0xc00006ef60
  println(&a, b)
  // *키워드로 주소에 저장된 value 조회
  // 10 10
  println(a, *b)
}
```

## 배열(slice, array)

- go는 배열을 나타내는 built-in 자료형으로 `slice`, `array`를 제공한다
- `slice`와 `array`의 차이점은 용량(배열의 크기)를 지정하는 것인데, array는 고정 크기, slice는 유동적인 크기를 가지며, 선언방법도 다르다
(+ slice는 내부적으로 배열 pointer,length, capacity 값을 가진다)
- 슬라이싱, 요소 추가(append), 복사(copy)길이 측정(length), 용량 측정(capacity) 등이 가능하다

- 아래 예시를 통해서 자세히 알아보자

``` go
func main() {
  slice := []int{1, 2, 3}
  array := [5]int{1, 2, 3}

  // [1 2 3] [1 2 3 0 0]
  fmt.Println(slice, array)

  // built-in 함수인 append()를 이용한 요소 추가, slice에만 가능하다
  // [1 2 3 3 4 5]
  fmt.Println(append(slice, 3, 4, 5))
  // slice에 다른 slice를 append하는 것도 가능
  // [1 2 3 5 6 7]
  fmt.Println(append(slice, []int{5, 6, 7}...))

  target := []int{0, 0, 0}
  // target := make([]int, len(slice), cap(slice))
  // target 과 slice의 length가 일치하지 않으면 복사되지 않으니 주의!!
  // 3 [1 2 3] [1 2 3]
  fmt.Println(copy(target, slice), target, slice)

  // 기존 slice나 array를 슬라이싱 (파이썬 문법과 유사하다)
  sliceOfArray := array[:2]

  // len : length 길이, cap : capacity 용량
  // [1 2] 2 5
  fmt.Println(sliceOfArray, len(sliceOfArray), cap(sliceOfArray))
}

```

### slice 선언 with make()

- slice에 대해서 좀 더 알아보자
- slice는 go의 built-in function인 make()로도 선언이 가능하다
- make()를 이용하면 slice의 `초기화 길이`(length), `용량`(capacity)를 지정할 수 있다
- `length`를 0아닌 값으로 지정하면 length 값 만큼 기본 값이 저장되므로 주의! (아래 예시 확인)

``` go
func main() {
  // make(type, length, capacity)
  s := make([]int, 5, 10)
  // 5 10 [0 0 0 0 0]
  fmt.Println(len(s), cap(s), s)
}
```

### slice capacity

- 보통 대부분의 언어에서 동적 배열(e.g.slice)은 요소를 추가할 때마다 용량을 동적으로 증가시킨다
- go에서는 지정한 capacity를 초과했을 때 기존 capacity를 2배로 늘린 새로운 배열을 생성해 기존 배열의 요소들을 복사한다
- 즉 위 과정으로 인해서 오버헤드가 발생할 수 있기 때문에 slice의 용량을 적절히 선언해야한다

## map

- key, value 형식으로 데이터를 저장할 수 있는 map을 제공한다
- 선언 방법은 아래와 같다

``` go
func main() {
  // map[key type][value type]
  fooMap := map[string]string{"key": "value"}

  fmt.Println(fooMap)
}

```

## 구조체(struct)

- go에서는 c의 구조체와 유사한 형태를 지원한다
- 구조체를 선언함으로써 새로운 자료형을 선언할 수 있고, type-safe에도 효과가 있다
- 아래 예시를 통해 알아보자

``` go

type user struct {
  name string
  age  int
}

func main() {
  user := user{name: "mokhs00", age: 23}

  fmt.Println(user)
}

```

## receiver와 method

- go에서는 receiver를 이용해 메서드 기능을 이용할 수 있다
- `func (t T) Foo() {}`형식이며 기존 함수에서 앞에 `(t *T)`가 추가된 것이다 (T는 타입이다)
- T에 대한 receiver가 존재하는 함수는 T를 이용해 해당 함수를 사용할 수 있다 ex) `T.Foo()`
- 경우에 따라 참조값을 사용해야 하므로 포인터를 이해하고 사용하는 것에 주의하자
- 추가로 함수나 구조체에 대한 주석은 해당 주체와 동일한 이름으로 시작해야한다
- 주석을 통해 사용하는 사람들에게 힌트를 줄 수도 있다
- 다음 예시를 통해 알아보자

``` go
// Account struct
type Account struct {
  owner   string
  balance int
}

// NewAccount create Account
func NewAccount(owner string) *Account {
  account := Account{owner: owner, balance: 0}
  return &account
}

// Deposit x amount on the account
func (account *Account) Deposit(amount int) {
  account.balance += amount
}

func main() {
  a := account.NewAccount("mokhs")

  a.Deposit(100)
}
```

## struct String

- 구조체를 출력해보면 나오는 문자를 변경할 수도 있다
- 출력 시에 기본적으로 String 메서드를 실행하게 되는데, String 메서드를 receiver를 이용해 재정의하면 이를 변경할 수 있다
- 다음 예시를 통해 알아보자

``` go
func (account Account) String() string {
  return fmt.Sprint(account.owner, " has: ", account.balance)
}

func main() {

  a := account.NewAccount("mokhs")
  a.Deposit(100)

  // mokhs has:100
  fmt.Println(a)
}


```

## 타입(type)

- 구조체가 아닌 타입을 정의할 수 있다
- 이는 type-safe한 프로그램을 위해서 유용하고, 네이밍을 통해 가독성을 증가시킬 수 있다
- 또한 타입도 구조체와 동일하게 메서드를 추가할 수 있다
- 아래 예시를 통해 알아보자

``` go
// dict.go
// Dictionary type
type Dictionary map[string]string

var errNotFoundElement = errors.New("not found element")

func (dictionary Dictionary) Search(word string) (string, error) {
  value, exists := dictionary[word]

  if exists {
    return value, nil
  }

  return "", errNotFoundElement
}

// main.go
func main() {
  dict := dict.Dictionary{}
  dict["a"] = "b"

  dict.Search("a")

  fmt.Println(dict)
}
```

## 고루틴(goroutine)

- 고루틴은 Go런타임에서 비동기 처리를 이용한 동시성을 지원하는 경량 가상 쓰레드이다.
- 가상 쓰레드라는 것은 실제로 OS레벨에서 쓰레드가 생성되는 것이 아니라 애플리케이션(Go)레벨에서 가상의 쓰레드가 생성되는 것을 의미하니 주의하자
- 런타임에서만 동작하기 때문에 main 런타임이 종료되면 모든 고루틴이 종료되니 주의하자
- `go` 키워드를 이용해 고루틴을 선언한다
- 다음 예시를 통해서 고루틴의 사용법을 알아보자

``` go
import (
  "fmt"
  "time"
)

func main() {
  // go 키워드를 이용해 고루틴 생성 -> 동시에 실행됨. 전체 출력을 보면서 이해해보자
  go bar("foo")
  bar("bar")

  /* 전체 출력
  bar 0
  foo 0
  foo 1
  bar 1
  bar 2
  foo 2  
  */
}

func bar(name string) {
  for i := 0; i < 10; i++ {
    fmt.Println(name, i)
    time.Sleep(time.Second)
  }
}
```

## channels

- 고루틴 간에 channel을 이용해 데이터를 주고 받을 수 있다
- `chan` 타입을 이용하며 보통 고루틴으로 사용할 함수에 매개변수로 채널을 전달하는 식으로 사용한다
- 채널에 데이터를 전달할 때는 `channel <- true`와 같은 형식으로 작성한다
- channel에 전송된 데이터는 `<-channel`을 이용해 받을 수 있다
- 다음 예시를 통해 알아보자
- *중요 : main 런타임이 종료 시에 고루틴이 모두 종료되므로 이 점을 항상 주의하자

``` go
func foo(person string, channel chan bool) {
  time.Sleep(time.Second * 3)
  fmt.Println(person)
  channel <- true
}

func main() {
  channel := make(chan bool)

  people := [2]string{"a", "b"}
  for _, person := range people {
    go foo(person, channel)
  }

  for range people {
    fmt.Println(<-channel)
  }
}
```

### 단방향 channel

- 기본적으로 channel은 양방향이지만 `<-`를 이용해 channel의 방향을 단 방향으로 한정할 수 있다

``` go
// 수신만 가능한 channel
reciveOnlyChannel := make(<-chan string)
// 전송만 가능한 channel
sendOnlyChannel := make(chan<- string)
```

### buffered channel