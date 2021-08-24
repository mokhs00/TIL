- [개요](#개요)
- [시작](#시작)
  - [프로젝트 구조 살펴보기](#프로젝트-구조-살펴보기)
  - [여러 플랫폼 호환](#여러-플랫폼-호환)
  - [애플리케이션 실행](#애플리케이션-실행)
- [컨트롤러(Controllers)](#컨트롤러controllers)
  - [기본 정의](#기본-정의)
  - [Controller 관련 데코레이터](#controller-관련-데코레이터)
    - [express 관련](#express-관련)
    - [HTTP method 관련](#http-method-관련)
    - [Status Code](#status-code)
    - [Response Header](#response-header)
    - [Redirection](#redirection)
    - [Route parameters](#route-parameters)
    - [Sub-Domain Routing](#sub-domain-routing)
  - [Nest CLI로 Controller 빠르게 생성하기](#nest-cli로-controller-빠르게-생성하기)

# 개요
NestJS에 대해서 학습합니다.

# 시작

NestJS에서는 Nest CLI를 제공한다. 이를 이용하면 새 프로젝트를 빠르게 시작할 수 있다.

``` bash
npm i -g @nestjs/cli
nest new project-name

```

## 프로젝트 구조 살펴보기

Nest CLI를 이용해서 프로젝트를 생성하면 다음과 같은 구조를 볼 수 있다.

```
src
├─app.controller.spec.ts
├─app.controller.ts
├─app.module.ts
├─app.service.ts
└─main.ts
```

각 파일은 다음을 의미한다.

- `app.controller.ts` : 단일 경로가 있는 기본 컨트롤러
- `app.controller.spec.ts` : 컨트롤러에 대한 단위 테스트
- `app.module.ts` : 애플리케이션 루트 모듈
- `app.service.ts` : service layer
- `main.ts` : core function인 NestFactory를 사용하여 Nest 애플리케이션 인스턴스를 생성하는 파일

**main.ts**를 살펴보면 다음과 같이 `AppModule`을 매개변수로 넘겨서 `NestFactory`를 생성하는 함수를 정의하고 해당 함수 **bootstrap()** 을 사용한다.

**이는 컨테이너(NestFactory)에 모듈로 정의한 것들을 미리 띄워놓는 듯하다.**

``` js
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();

```

## 여러 플랫폼 호환

Nest는 플랫폼에 독립적인 것을 목표로 한다. 기술적으로 Nest Adapter가 생성되면 모든 Node Http 프레임워크에서 동작할 수 있다.
기본적으로 `express`와 `fastify`를 지원한다.

## 애플리케이션 실행

아래 명령어로 프로젝트를 실행한다.

``` bash
npm run start

```

package.json에서 start와 관련된 명령어만 가져와봤다.
참고로 `--watch`는 핫 리로드 옵션이다.

이외에 명령어들도 살펴보면 Nest가 자동으로 많은 것들을 지원하고 있다는 걸 알 수 있다.

``` json
{
    // ...
    "start": "nest start",
    "start:dev": "nest start --watch",
    "start:debug": "nest start --debug --watch",
    "start:prod": "node dist/main",
}
```

# 컨트롤러(Controllers)

컨트롤러는 기본적으로 클라이언트로부터 오는 HTTP 요청을 처리하고 응답을 반환하는 역할을 한다.
애플리케이션에서 컨트롤러는 기본적으로 라우팅에 의해서 특정 요청을 수신하고 응답을 처리하는데,
Nest에서는 이를 **데코레이터(@)** 로 손쉽게 설정할 수 있도록 지원한다.

## 기본 정의

nestjs에서는 `@Controller` 데코레이터를 사용해서 기본 컨트롤러를 정의한다.

기본적으로 다음과 같이 정의한다.
데코레이터를 사용하는 걸 보면 spring이 연상된다.

``` js
import {Controller, Get, HttpCode} from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  @HttpCode(HttpStatus.OK)
  findAll(): string {
    return 'This action returns all cats';
  }
} 
```

## Controller 관련 데코레이터

### express 관련

다음은 express에서 사용하던 방식이 호환되는 **데코레이터(@)** 이다.

express에 typing을 하고 싶다면 `@types/express` 패키지를 설치하자.

기본적인 import는 다음과 같다.

``` js
import { Controller, Post, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('home')
export class HomeController {
  @Post()
  create(@Req() req: Request): string {
    return 'create';
  }
}

```

이제 여러가지 express관련 데코레이터를 정리해보자

| 데코레이터(@)             | express                             |
| ------------------------- | ----------------------------------- |
| `@Request(), @Req()`      | `req`                               |
| `@Response(), @Res()`     | `res`                               |
| `@Next()`                 | `next`                              |
| `@Session()`              | `req.session`                       |
| `@Param(key?: string)`    | `req.params` / `req.params[key]`    |
| `@Body(key?: string)`     | `req.body` / `req.body[key]`        |
| `@Query(key?: string)`    | `req.query` / `req.query[key]`      |
| `@Headers(name?: string)` | `req.headers` / `req.headers[name]` |
| `@Ip()`                   | `req.ip`                            |
| `@HostParam()`            | `req.hosts`                         |

### HTTP method 관련

All은 모든 요청을 처리함
`@Get()`, `@Post()`, `@Put()`, `@Delete()`, `@Patch()`, `@Options()`, `@Head()`, `@All()`

### Status Code

response status code를 다음과 같이 간단하게 설정 가능하다.

`@HttpCode(200)`
`@HttpCode(HttpStatus.OK)`
``` js
import { Controller, HttpCode, Post, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('home')
export class HomeController {
  @Post()
  @HttpCode(201)
  create(@Req() req: Request): string {
    return 'create';
  }
}
```

### Response Header

`@nestjs/common` package의 `@Header()` 를 사용해서 response에 header를 넘겨줄 수 있다.

``` js
@Post()
@Header('Cache-Control', 'none')
create() {
  return 'This action adds a new cat';
}

```

### Redirection

`@Redirect()`를 이용해 redirect 가능
매개변수로 url, statusCode를 받음 statusCode는 default 값으로 302(Found)

``` js
@Redirect('https://github.com/mokhs00', 301)
```

return 값을 통해서 매개변수(url, statusCode)를 변경할 수 있다.

return 값 형식은 다음과 같다

``` js
{
  "url": string,
  "statusCode": number
}
```


``` js
@Get(github)
@Redirect('https://github.com/mokhs00', 302)
toGithub(@Query('name') name){
  if(name){
    return { url: `https://github.com/${name}` }
  }
}
```

### Route parameters

`@Param()`을 이용해서 path parameter 가져오기 가능

``` js
@Get(:id)
findOne(@Param() params): string {
  return `This action returns a ${params.id}`
}
```

아래 방식도 가능

``` js
@Get(:id)
findOne(@Param('id') id): string {
  return `This action returns a ${id}`
}
```


### Sub-Domain Routing

## Nest CLI로 Controller 빠르게 생성하기

Nest CLI를 이용해서 다음과 같이 입력하면 controller를 빠르게 생성해준다.

``` bash
nest g controller home
```

해당 명령어를 입력하면 src 아래에 다음과 같은 구조로 코드들이 자동으로 생성된다

심지어 app.module.ts에도 자동으로 등록해준다.

![./NestJS/1.png](./NestJS/1.png)

``` js
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { HomeController } from './home/home.controller';

@Module({
  imports: [],
  controllers: [AppController, HomeController],
  providers: [AppService],
})
export class AppModule {}

```
