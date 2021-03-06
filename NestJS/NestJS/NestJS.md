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
- [Providers](#providers)
  - [Services](#services)
  - [Dependency injection](#dependency-injection)
  - [Scopes](#scopes)
  - [Custom Providers](#custom-providers)
  - [Optional Providers](#optional-providers)
  - [Property-based injection](#property-based-injection)
  - [Provider registration](#provider-registration)
- [Modules](#modules)
  - [Shared modules](#shared-modules)
  - [Module re-exporting](#module-re-exporting)
  - [Module Dependency injection](#module-dependency-injection)
  - [Global modules](#global-modules)
  - [Dynamic modules](#dynamic-modules)
- [Middleware](#middleware)
  - [Middleware Dependency Injection](#middleware-dependency-injection)
  - [Applying middleware](#applying-middleware)
  - [Route wildcards](#route-wildcards)
  - [Middleware consumer](#middleware-consumer)
  - [Excluding routes](#excluding-routes)
  - [Functional middleware](#functional-middleware)
  - [Multiple middleware](#multiple-middleware)
  - [Global middleware](#global-middleware)
- [Exception filters](#exception-filters)
  - [Throwing standard exceptions](#throwing-standard-exceptions)
  - [Custom exceptions](#custom-exceptions)
  - [Built-in HTTP exceptions](#built-in-http-exceptions)
  - [Exception filters](#exception-filters-1)
  - [Arguments host](#arguments-host)
  - [Binging filters](#binging-filters)
  - [Catch everything](#catch-everything)

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
@Get('github')
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
@Get(':id')
findOne(@Param() params): string {
  return `This action returns a ${params.id}`
}
```

아래 방식도 가능

``` js
@Get(':id')
findOne(@Param('id') id): string {
  return `This action returns a ${id}`
}
```

### Sub-Domain Routing

- `@Controller` 데코레이터에 host option을 사용해 HTTP 호스트가 특정값과 일치하도록 할 수 있다.

``` ts
@Controller({ host: 'admin.example.com' })
export class AdminController {
  @Get()
  index(): string {
    return 'Admin page';
  }
}
```

- 아래와 같이 `@HostParam`을 이용해서 동적값을 캡처할 수도 있다.

``` ts
@Controller({ host: ':account.example.com' })
export class AccountController {
  @Get()
  getInfo(@HostParam('account') account: string) {
    return account;
  }
}
```

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

# Providers

- 프로바이더는 Nest의 기본 개념이며, 서비스, 리포지토리, 팩토리, 헬퍼 등 대부분의 기본 Nest 클래스는 프로바이더로 취급될 수 있다.
- 프로바이더의 주요 아이디어는 종속성을 주입할 수 있다는 것이고, Spring에서 Spring Bean을 주입하는 것과 유사하다는 느낌이 든다.
- `@Injectable` 데코레이터를 붙여서 `Provider`로 선언한다.
- 이는 Nest IoC 컨테이너가 관리할 수 있는 클래스임을 선언하는 메타데이터를 첨부한다.

## Services

- 다음은 `Service layer`의 코드를 `Controller`에서 생성자 주입 받는 코드이다.
- Controller에서 생성자에 private를 선언하여 주입 받는 것을 확인하자. 이는 약식(shorthand = 줄임말)으로 사용된다고 한다.

``` ts
// dogs.service.ts

import { Injectable } from '@nestjs/common';
import { Dog } from 'interfaces/dogs.interface';

@Injectable()
export class DogsService {
  private readonly dogs: Dog[] = [];

  create(dog: Dog) {
    this.dogs.push(dog);
  }

  findAll(): Dog[] {
    return this.dogs;
  }
}
```

``` ts
// dogs.controller.ts

import { Body, Controller, Get, Post } from '@nestjs/common';
import { Dog } from 'src/dogs/dogs.interface';
import { CreateDogDto } from './create-dog.dto';
import { DogsService } from './dogs.service';

@Controller('dogs')
export class DogsController {
  constructor(private readonly dogsService: DogsService) { }

  @Post()
  async create(@Body() createDogDto: CreateDogDto) {
    this.dogsService.create(createDogDto);
  }

  @Get()
  async findAll(): Promise<Dog[]> {
    return this.dogsService.findAll();
  }
}

```

## Dependency injection

- 위에서 살펴본 것처럼 다음과 같이 작성하면 Nest에서 생성자 Dependency Injection(의존성 주입)을 받을 수 있다.
- 물론 spring 과 유사하게 필드 주입도 가능하겠지만, 이는 이후에 다룬다.

``` ts
constructor(private dogsService: DogsService) {}
```

## Scopes

- Provider는 일반적으로 애플리케이션 수명주기와 동기화된 수명(범위=scope)를 갖는다.
- 애플리케이션이 `bootstrap`되면, 모든 종속성을 해결해야하므로 모든 `provider`를 인스턴스화 해야하고, 당연한 얘기지만, 애플리케이션이 종료되면 각 `provider`는 메모리에서 제거된다.
- 그러나 이러한 provider의 수명을 요청 범위로 만드는 방법도 있다고 한다. 이는 이후 `Injection Scope`에서 다룬다.

## Custom Providers

- 프로바이더를 정의하는 방법에는 여러가지가 있고, 일반 값, 클래스 및 비동기 또는 동기 팩토리를 사용할 수 있다. 이는 이후 `Custom Provider`에서 다룬다.

## Optional Providers

- 경우에 따라 반드시 필요하지 않은 종속성이 있을 수 있다.
- 예를 들어, 구성 객체에 종속될 순 있지만, 전달되는 것이 없으면 기본 값을 사용해야하는 경우를 들 수 있다.
- 프로바이더가 선택사항임을 나타내려면 생성자에 `@Optional()` 데코레이터를 사용하자.
- 아래 예시는 `HTTP_OPTIONS`라는 커스텀 토큰을 포함한다.
- 커스텀 프로바이더 및 관련 토큰에 대한 내용은 이후 `Custom Provider`에서 다룬다.

``` ts

import { Injectable, Optional, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  constructor(@Optional() @Inject('HTTP_OPTIONS') private httpClient: T) {}
}
```

## Property-based injection

- 속성 기반 의존성 주입도 가능하다.
- 클래스 상속의 경우에 최상위 클래스가 여러 프로바이더에 의존하는 경우 생성자의 하위 클래스에서 `super()`를 호출하여 모든 프로바이더를 전달하는 것은 매우 지루할 수 있다.
- 이를 방지하기 위해서 속성 수준에서 의존성 주입이 가능하도록 `@Inject` 데코레이터를 지원한다.
- **하지만 클래스가 다른 프로바이더를 확장하지 않는 경우 생성자 기반 주입을 선호해야함을 주의하자.**

``` ts
import { Injectable, Inject } from '@nestjs/common'

@Injectable()
export class HttpService<T> {
  @Inject('HTTP_OPTIONS')
  private readonly httpCliect: T;
}

```

## Provider registration

- Provider를 정의했으니 Nest의 컨테이너에서 이를 관리할 수 있도록 설정해주어야 한다.
- 다음과 같이 모듈 파일의 `@Module` 데코레이터의 `providers` 배열에 해당 Provider를 추가하자.
- 여기서는 DogsService를 Provider로 추가했다.

``` ts
// dogs.module.ts

import { Module } from '@nestjs/common';
import { DogsService } from './dogs.service';
import { DogsController } from './dogs.controller';

@Module({
  controllers: [DogsController],
  providers: [DogsService]
})
export class DogsModule {}
```

- 추가로 최상위 모듈에선 다음과 같이 하위 모듈을 추가한다.
- @Module의 imports에 DogsModule을 확인하자.

``` ts
// app.module.ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { DogsModule } from './dogs/dogs.module';

@Module({
  imports: [DogsModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

# Modules

- Nest에서 모듈이란 `@Module` 데코레이터로 주석이 달린 클래스를 말한다.
- `@Module()` 데코레이터는 Nest가 애플리케이션 구조를 구성하는데 사용하는 메타데이터를 제공한다(설정)

- 각 애플리케이션에는 `루트 모듈(root module)`이라는 하나 이상의 모듈이 존재한다.
- **루트 모듈은 Nest가 애플리케이션 그래프를 빌드하는데 사용하는 시작점이며**
- **Nest가 모듈과 공급자 관계 및 종속성을 해결하는데 사용하는 내부 데이터 구조이다.**
- Nest 공식 문서에선 모듈은 구성요소를 구성하는 효과적인 방법으로 적극 권장한다고. 강조되어있다.
- 아키텍처에 따라 모듈을 잘 캡슐화하여서 구성하는 것이 중요하다.

`@Module()` 데코레이터는 속성이 모듈을 설명하는 단일 객체를 매개변수로 받는다. 해당 매개변수는 다음과 같다.

| 매개변수      | 설명                                                                          |
| ------------- | ----------------------------------------------------------------------------- |
| `providers`   | Nest 인젝터에 의해 인스턴스화 되고 적어도 이 모듈에서 공유될 수 있는 provider |
| `controllers` | 인스턴스화 되어야하는 해당 모듈에 정의된 컨트롤러                             |
| `imports`     | 이 모듈에 필요한 프로바이더를 내보내는 다른 모듈 = import 해야하는 모듈 목록  | `exports` | 이 모듈에서 제공하고, 이 모듈을 import하는 다른 모듈에서 사용할 수 있어야하는 provider 집합 |

**모듈은 기본적으로 provider를 캡슐화하고, 현재 모듈에 직접 포함되거나 가져온 모듈에서 내보내지 않은 프로바이더를 삽입할 수 없다.
따라서 모듈에서 내보낸 프로바이더를 모듈의 공용 인터페이스 또는 API로 간주할 수 있다.**

## Shared modules

- Nest에서 모듈은 기본적으로 **싱글톤**이므로 여러 모듈간에 쉽게 프로바이더의 동일한 인스턴스를 공유할 수 있다.
- 모든 모듈은 자동으로 공유 모듈이며, 인스턴스화되면, 모든 모듈에서 재사용할 수 있다.
- `@Module()` 데코레이터의 옵션에 exports와 imports를 통해서 모듈을 공유할 수 있다.
- 만약에 DogsService를 다른 모듈에서 공유하고 싶다면 다음과 같이 하면 된다.
  
``` ts
// dogs.module.ts

import { Module } from '@nestjs/common';
import { DogsService } from './dogs.service';
import { DogsController } from './dogs.controller';

@Module({
  controllers: [DogsController],
  providers: [DogsService],
  exports: [DogsService]
})
export class DogsModule {}
```

## Module re-exporting

- 모듈은 exports 옵션을 통해 내부 프로바이더를 외부로 내보낼 수 있다.
- 또한, 가져온 모듈을 다시 내보낼 수도 있는데, 이 예시는 다음과 같다.

``` ts
@Module({
  imports: [CommonModule],
  exports: [CommonModule],
})
export class CoreModule {}

```

## Module Dependency injection

모듈 클래스에 다음과 같이 프로바이더를 주입할 수 있다.

하지만, `순환 종속성`으로 인해 모듈 클래스 자체는 프로바이더로 주입될 수 없다.

``` ts
// dogs.module.ts

import { Module } from '@nestjs/common';
import { DogsService } from './dogs.service';
import { DogsController } from './dogs.controller';

@Module({
  controllers: [DogsController],
  providers: [DogsService],
})
export class DogsModule {
  constructor(private dogsService: DogsService) {}
}
```

## Global modules

- 모든 곳에서 동일한 모듈을 가져와야한다면, 일일히 설정해주는 것은 힘들 수 있다.
- 이런 경우 해당 모듈을 다음과 같이 `@Global` 데코레이터를 이용해 전역 모듈로 설정해줄 수 있다.
- **단, 주의해야할 점은 전역 모듈은 일반적으로 루트 또는 코어 모듈에서 한번만 등록해야하고**
- **모든 모듈을 전역 모듈로 설정하는 것은 결코 좋은 디자인이 아니라는 것을 명심하자.**

``` ts
// dogs.module.ts

import { Module, Global } from '@nestjs/common';
import { DogsService } from './dogs.service';
import { DogsController } from './dogs.controller';

@Global()
@Module({
  controllers: [DogsController],
  providers: [DogsService],
  exports: [DogsService]
})
export class DogsModule {}

```

## Dynamic modules

- Nest의 모듈 시스템은 `동적 모듈(Dynamic module)`이라는 강력한 기능을 제공한다.
- 이 기능을 사용하면, 프로바이더를 동적으로 등록하고, 구성할 수 있는 커스텀 가능한 모듈을 쉽게 만들 수 있다. 이후에 동적 모듈에 대해 자세히 다룰 예정이다.
- 다음은 동적 모듈의 간단한 예시이다.
- 아래 코드를 보면 `DatabaseModule.forRoot()`를 이용해 entity에 대한 `DatabsaseProvider`를 동적으로 로딩하는 걸 볼 수 있다.

``` ts
import { Module, DynamicModule } from '@nestjs/common';
import { createDatabaseProviders } from './database.providers';
import { Connection } from './connection.provider';

@Module({
  providers: [Connection],
})
export class DatabaseModule {
  static forRoot(entities = [], options?): DynamicModule {
    const providers = createDatabaseProviders(options, entities);
    return {
      module: DatabaseModule,
      providers: providers,
      exports: providers,
    };
  }
}
```

``` ts
import { Module } from '@nestjs/common';
import { DatabaseModule } from './database/database.module';
import { User } from './users/entities/user.entity';

@Module({
  imports: [DatabaseModule.forRoot([User])],
})
export class AppModule {}
```

# Middleware

- 미들웨어는 라우트 핸들러 이전에 호출되는 함수를 말하며, Nest의 미들웨어는 기본적으로 express 미들웨어와 동일하다.
- 미들웨어는 다음과 같은 작업을 수행할 수 있다.
  - **추가 코드를 실행**
  - **요청 및 응답 객체를 변경**
  - **요청-응답 사이클을 종료**
  - **스택의 next 미들웨어를 호출 (다음 순서의 미들웨어. 미들웨어를 여러개 두는 경우.)**
  - **현재 미들웨어 함수가 요청-응답주기를 종료하지 않으면 next()를 호출하여 next 미들웨어 기능에 제어를 전달해야만 하고, 그렇지 않으면 해당 요청은 보류 상태가 되어서 종료되지 않을 수 있다.**

커스텀 Nest 미들웨어는 함수 또는 `@Injectable()` 데코레이터가 있는 클래스로 구현할 수 있다.
클래스의 경우 `NestMiddleware` 인터페이스를 구현해야하지만, 함수는 특별한 요구사항이 없다.
다음은 클래스 방식으로 간단한 커스텀 미들웨어를 구현한 것이다.

``` ts
import { NestMiddleware } from '@nestjs/common';
import { NextFunction } from 'express';

export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log('[Request] LOG TEST');
    next();
  }
}
```

## Middleware Dependency Injection

Middleware 또한 DI(Dependency Injection)가 가능하다.
Nest에서는 생성자 주입을 추천한다.

## Applying middleware

- `@Module()` 데코레이터 옵션에는 미들웨어를 설정할 수 없다.
- 대신에 모듈 클래스의 configure 메서드를 사용하여 설정한다.
- 미들웨어를 포함하는 모듈은 NestModule 인터페이스를 상속받아 구현해야한다.
- 다음 예시 코드를 확인하자.

``` ts
import { MiddlewareConsumer, Module, NestModule } from '@nestjs/common';
import { LoggerMiddleware } from 'middleware/logger.middleware';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { DogsModule } from './dogs/dogs.module';

@Module({
  imports: [DogsModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware) // (1)
      .forRoutes('dogs'); // (2)
  }
}
```

- (1) middleware 설정
- (2) 설정한 middleware를 적용할 route 경로 설정

또한, forRoutes() 메서드의 매개변수로 객체를 전달할 수 있는데, `path`, `method`를 다음과 같이 사용해서 특정 path와 특정 http method에 미들웨어를 적용할 수 있다.

``` ts
import {
  MiddlewareConsumer,
  Module,
  NestModule,
  RequestMethod,
} from '@nestjs/common';
import { LoggerMiddleware } from 'middleware/logger.middleware';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { DogsModule } from './dogs/dogs.module';

@Module({
  imports: [DogsModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes({ path: 'dogs', method: RequestMethod.GET });
  }
}
```

## Route wildcards

nest는 정규표현식 패턴 기반 라우팅도 지원한다. 다음과 같이 `*`를 사용해 모든 문자 조합을 허용할 수 있다.

`forRoutes({ path: "a*b", method: RequestMethod.ALL})`

`"a*b"`는 `a_b`, `acb` 등과 일치하고, `?`, `+`, `*`, `()`문자는 정규표현식 대응 부분이며, 라우트 경로에 사용할 수 있다. 단, `-`과 `.`은 문자열 기반 경로로 문자 그대로 해석된다.

## Middleware consumer

다음 코드에서 `NestModule`의 구현체인 `AppModule`의 생성자 매개변수에 `consumer`의 타입이 `MiddlewareConsumer`인 걸 볼 수 있다.

- `MiddlewareConsumer`는 미들웨어를 관리하기 위한 헬퍼 클래스이다.
- 미들웨어를 관리하기 위한 몇가지 내장 메서드를 제공한다.
- `forRutes()` 메서드는 `단일 문자열`, `여러 문자열`, `RouteInfo` 객체, `컨트롤러 클래스` 및 `여러 컨트롤러 클래스`를 매개변수로 받을 수 있다.
- 쉼표`','`로 구분되어 컨트롤러 목록을 매개변수로 전달해서 사용하면 된다.
- 아래는 단일 컨트롤러의 예시이다.
- 모든 미들웨어는 [fluent style](https://ko.wikipedia.org/wiki/%ED%94%8C%EB%A3%A8%EC%96%B8%ED%8A%B8_%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)로 간단하게 메서드 체이닝 될 수 있다.

``` ts
import { MiddlewareConsumer, Module, NestModule } from '@nestjs/common';
import { LoggerMiddleware } from 'middleware/logger.middleware';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { DogsController } from './dogs/dogs.controller';
import { DogsModule } from './dogs/dogs.module';

@Module({
  imports: [DogsModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes(DogsController);
  }
}
```

## Excluding routes

특정 API 경로는 미들웨어 적용을 제외할 수도 있다. `exclude()` 메서드를 이용해 특정 API 경로를 쉽게 제외할 수 있다.

- 다음은 `exlcude()` 메서드 사용 예시이다.
- 매개변수로는 `단일 문자열`, `여러 문자열`, 또는 `RouteInfo` 객체를 사용할 수 있다.
- `exclude()` 메서드는 [path-to-regexp](https://github.com/pillarjs/path-to-regexp#parameters) 패키지를 사용하여 아래 예시의 `dogs/(.*)`와 같이 와일드 카드를 매개변수로 지원한다.

``` ts
import {
  MiddlewareConsumer,
  Module,
  NestModule,
  RequestMethod,
} from '@nestjs/common';
import { LoggerMiddleware } from 'middleware/logger.middleware';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { DogsController } from './dogs/dogs.controller';
import { DogsModule } from './dogs/dogs.module';

@Module({
  imports: [DogsModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .exclude(
        { path: 'dogs', method: RequestMethod.GET }, 
        'dogs/(.*)'
      )
      .forRoutes(DogsController);
  }
}

```

## Functional middleware

- 미들웨어에 의존성이 필요하지 않다면, 간단하게 함수형 미들웨어를 사용하자.
- 클래스형이 아닌 Functional middleware는 다음과 같이 정의한다.

``` ts
// functionalLogger.middleware.ts
import { NextFunction, Request, Response } from 'express';

export function logger(req: Request, res: Response, next: NextFunction) {
  console.log('[functional logger]Request. . .');
  next();
}

```

- Module에 설정하는 코드는 다음과 같다

``` ts
// app.module.ts
import {
  MiddlewareConsumer,
  Module,
  NestModule,
} from '@nestjs/common';
import { functionalLogger } from 'middleware/functionalLogger.middleware';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { DogsController } from './dogs/dogs.controller';
import { DogsModule } from './dogs/dogs.module';

@Module({
  imports: [DogsModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(functionalLogger)
      .forRoutes(DogsController);
  }
}
```

## Multiple middleware

- `consumer.apply()` 메서드의 매개변수로 미들웨어를 여러개 넘겨줄 수 있다.
- 미들웨어는 순차적으로 실행되며 먼저 실행하고 싶은 걸 매개변수 배치 시에 우선 순위로 두면 된다.
- 다음 코드는 미들웨어를 여러개 설정하는 코드이다.

``` ts
consumer
      .apply(cors(), helmet(), functionalLogger)
```

## Global middleware

- 전역 범위에 미들웨어를 설정할 수도 있다.
- `INestApplication` 인스턴스에서 제공하는 `use()` 메서드에 매개변수로 미들웨어를 전달하면 된다.
- 글로벌 미들웨어에서는 DI 컨테이너에 접근할 수 없다. 즉 DI가 안된다. 대신에 함수형 미들웨어를 사용할 수 있다.
- 또는 클래스 미들웨어를 `AppModule`내에서 `.forRoutes('*')`를 사용할 수도 있다.

``` ts
import { NestFactory } from '@nestjs/core';
import { functionalLogger } from 'middleware/functionalLogger.middleware';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.use(functionalLogger);
  await app.listen(3000);
}
bootstrap();


```

# Exception filters

- Nest에는 `exceptions layer`가 내장되어 있어 있고 `exceptions layer`는 애플리케이션 전체에서 처리되지 않은 모든 exception을 처리한다.

![./NestJS/2.png](./NestJS/2.png)

- 기본적으로 `HttpException`을 핸들링하는 `global exception filter`에 의해 해당 작업이 수행되고, `인식되지 않은 예외`**(HttpException이 아니거나, 상속받지 않은 예외)** 발생 시에 다음과 같은 JSON response를 기본값으로 내보낸다.
  
``` json
{
  "statusCode": 500,
  "message": "Internal server error"
}
```

> global exception filter는 [http-errors](https://www.npmjs.com/package/http-errors) 라이브러리를 부분적으로 지원한다.
> `http-errors`로 발생시킨 error는 `InternalServerErrorException`으로 처리하지 않고,
> 기본적으로 statusCode, message 속성을 포함하는 error를 위에서 봤던 Nest 기본 error response에 맞게 내보낸다.

## Throwing standard exceptions

- Nest는 `@nestjs/common` 패키지에서 `HttpException`을 제공한다.
- HTTP REST/GraphQL API 기반 애플리케이션의 경우 표준 HTTP 응답 객체를 내보내는 것이 가장 좋다.
- 다음은 Nest에서 표준 HTTP 응답 객체를 이용해 403 코드를 내보내는 코드의 예시이다.
  
``` ts
@Get()
async findAll() {
  throw new HttpException('Forbidden', HttpStatus.FORBIDDEN);
}

```

- 이에 대한 json response는 다음과 같다.

``` json
{
  "statusCode": 403,
  "message": "Forbidden"
}
```

- `HttpException` 생성자는 다음 필수 매개변수를 사용한다.

| 매개변수(argument) | 설명                                      |
| ------------------ | ----------------------------------------- |
| `response`         | json 응답 본문을 정의 string이거나 object |
| `status`           | HTTP 상태 코드 정의                       |

- 그리고 json 응답 본문에는 다음 두가지 속성을 기본값으로 가진다.

| 속성(property) | 설명                                         |
| -------------- | -------------------------------------------- |
| `statusCode`   | `status` 매개변수로 제공된 HTTP 상태코드     |
| `message`      | `status`에 따른 HTTP 오류에 대한 간단한 설명 |

- 만약, json 응답 본문의 `message`부분을 재정의 하고 싶다면, `response` 매개변수에 원하는 문자열을 전달하면 된다.
- 전체 json response를 재정의 하고 싶다면, object를 매개변수로 전달하면 된다. Nest가 해당 객체를 직렬화핳고, json response로 반환한다.
- **단, 두번째 생성자 매개변수인 `status`는 유효한 HTTP 상태 코드여야하며, 모범사례로는 `@nestjs/common`의 `HttpStatus`을 사용하는 것이다.**
- 다음은 전체 json response를 재정의하는 예시이다.
  
``` ts
@Get()
async findAll() {
  throw new HttpException({
    status: HttpStatus.FORBIDDEN,
    error: 'This is a custom message',
  }, HttpStatus.FORBIDDEN);
}

```

- 다음은 해당 코드에 대한 json response이다.

``` json
{
  "status": 403,
  "error": "This is a custom message"
}
```

## Custom exceptions

- Custom Exception을 만들고 싶다면 다음과 같이 `HttpException` 클래스를 상속하는 고유한 예외 클래스를 만드는 것이 좋다.
- 이 방식을 이용하면, Nest가 예외를 인식하고 자동으로 error 응답 처리를 해준다.
- 다음은 `HttpException` 클래스를 상속받아 구현한 custom exception 클래스의 예시이다.

``` ts
// api.exception.ts
import { HttpException, HttpStatus } from '@nestjs/common';

export class ApiException extends HttpException {
  constructor(message: string, status: HttpStatus) {
    super(message, status);
  }
}
```

``` ts
// xxx.controller.ts
@Get()
async findAll() {
  throw new ApiException('error response test', HttpStatus.NOT_FOUND);
}
```

## Built-in HTTP exceptions

- 다음은 Nest가 제공하는 HTTP exceptions이다.
- `@nestjs/common` 패키지에 정의 되어 있으며, `HttpException`을 상속받은 구현체이다.
- `BadRequestException`
- `UnauthorizedException`
- `NotFoundException`
- `ForbiddenException`
- `NotAcceptableException`
- `RequestTimeoutException`
- `ConflictException`
- `GoneException`
- `HttpVersionNotSupportedException`
- `PayloadTooLargeException`
- `UnsupportedMediaTypeException`
- `UnprocessableEntityException`
- `InternalServerErrorException`
- `NotImplementedException`
- `ImATeapotException`
- `MethodNotAllowedException`
- `BadGatewayException`
- `ServiceUnavailableException`
- `GatewayTimeoutException`
- `PreconditionFailedException`

## Exception filters

- 기본 예외 필터가 자동으로 처리해주는 것들이 많지만, exceptions layer에 대해서 full control하기를 원할 수 있다.
- 예를 들어, `로깅을 추가`하거나 일부 동적 요인을 기반으로 각 상황에 맞는 json 스키마를 사용할 수 있다.
- exception filter는 정확히 위 목적을 위해서 설계되었고, 이는 개발자가 제어의 정확한 흐름과 사용자에게 내보낼 response를 제어할 수 있게 해준다.

- 모든 exception filter는 일반 `ExceptionFilter<T>` 인터페이스를 구현해야하며, `catch(exception: T, host: ArgumentsHost)` 메서드를 정의해야한다.
- 다음은 `HttpException`을 Catch 하여 응답 로직을 express 모듈의 Request와 Response를 이용해서 커스텀하는 예시 코드이다.

``` ts
import {
  ArgumentsHost,
  Catch,
  ExceptionFilter,
  HttpException,
} from '@nestjs/common';
import { Request, Response } from 'express';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getRequest<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString,
      path: request.url,
    });
  }
}
```

- `@Catch(HttpException)` 데코레이터는 필요한 메타데이터를 예외 필터에 바인딩하여, 해당 필터가 `HttpException` type의 예외를 Catch한다는 정보를 Nest에게 전달한다.
- 이를 이용해 한 번에 여러 타입의 예외에 대한 필터를 설정할 수도 있다.


## Arguments host

- 위에서 `catch()` 메서드에 `ArgumentsHost` type의 매개변수를 보았을 것이다. 
- 먼저 `catch()` 메서드의 매개변수로 exeption은 현재 처리중인 예외를 의미하고, host 매개변수의 type인 `ArgumentsHost`는 강력한 유틸리티 객체인데, `Execution context`장에서 자세하게 살펴보고 정리하자.

## Binging filters

- `@nest/common`패키지의 `@UseFilters()`를 이용해서 controller에 필터를 설정해보자.
- 다음은 메서드 범위에 filter를 설정하는 방법이다. 컨트롤러 범위, 전역 범위에 설정하는 것을 차례로 알아보자.

``` ts
// dogs.controller.ts
@Get()
@UseFilters(HttpExceptionFilter)
async findAll(): Promise<Dog[]> {
  throw new ApiException('error response test', HttpStatus.NOT_FOUND);
  // return this.dogsService.findAll();
}
```

- 컨트롤러 범위 필터 설정

``` ts
// dogs.controller.ts
@Controller('dogs')
@UseFilters(HttpExceptionFilter)
export class DogsController {
  constructor(private readonly dogsService: DogsService) { }
  //...
}
```

- 전역 범위 필터 설정

``` ts
// main.ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new HttpExceptionFilter());
  await app.listen(3000);
}
bootstrap();
```   



- 전역 범위 필터는 모듈 외부에서 등록하는 것이기 때문에 DI가 되지 않는다. 
- 이 문제를 해결하기 위해서는 다음과 같이 설정하면, 모든 모듈에서 직접 전역범위 필터를 등록할 수 있다.

``` ts
// app.module.ts
import { Module } from '@nestjs/common';
import { APP_FILTER } from '@nestjs/core';

@Module({
  providers: [
    AppService,
    { provide: APP_FILTER, useClass: HttpExceptionFilter },
  ],
})
export class AppModule {}
```


## Catch everything

- 모든 예외를 Catch 하고싶다면 `@Catch()` 데코레이터의 매개변수 목록을 비워두면 된다.

``` ts
// all-exception.filter.ts
import {
  ArgumentsHost,
  ExceptionFilter,
  HttpException,
  HttpStatus,
} from '@nestjs/common';

export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();
    const request = ctx.getRequest();

    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : HttpStatus.INTERNAL_SERVER_ERROR;

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString,
      path: request.url,
    });
  }
}

```