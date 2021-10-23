# 개요

TEST, TDD 관련된 내용을 정리합니다.

- [개요](#개요)
- [단위 테스트](#단위-테스트)
  - [mocha](#mocha)
    - [Example](#example)
  - [should](#should)
- [통합 테스트](#통합-테스트)
  - [supertest](#supertest)

# 단위 테스트

## mocha

- 테스트 코드를 돌려주는 테스트 러너
- 테스트 수트(test suite) : 테스트 환경으로 모카에서는 describe()으로 구현
- 테스트 케이스(test) : 실제 테스트를 말하며, 모카에서는 it()으로 구현한다.

### Example

``` js
const utils = require('./utils')
const assert = require('assert')

describe('utils.js capitialize() 함수는', () => {
  it('문자열의 첫번째 문자를 대문자로 변환한다.', () => {
    const result = utils.capitialize('hello')    
    assert.equal(result, 'Hello')
  })
})
```

## should

- node 공식 문서에 assert를 test에 활용하지 말고 서드파티 라이브러리를 사용할 것을 권장함
- 검증(assertion) 라이브러리
- 코드가 영어 문장을 읽는 듯한 구성이기에 assert보다 가독성이 높아진다

assert를 사용한 코드

``` js
const utils = require('./utils')
const assert = require('assert')

describe('utils.js capitialize() 함수는', () => {
  it('문자열의 첫번째 문자를 대문자로 변환한다.', () => {
    const result = utils.capitialize('hello')    
    assert.equal(result, 'Hello')
  })
})

```

should를 사용한 코드

``` js
const utils = require("./utils");
const should = require("should");

describe("utils.js capitialize() 함수는", () => {
  it("문자열의 첫번째 문자를 대문자로 변환한다.", () => {
    const result = utils.capitialize("hello");
    result.should.be.equal("Hello");
  });
});

```

# 통합 테스트

## supertest

- api 기능 test module
- 예시 코드는 다음과 같음
  
``` js
const request = require("supertest");
const should = require("should");
const { app } = require("./app");

describe("GET /users는", () => {
  describe("성공 시", () => {
    // get
    it("get users response", (done) => {
      request(app)
        .get("/users")
        .end((err, res) => {
          res.body.should.be.instanceOf(Array);
          done();
        });
    });

    // post
    it("user 추가", (done) => {
      request(app)
        .post("/users")
        .send({ name: "mokhs" })
        .expect(201)
        .end((err, res) => {
          res.body.should.have.property('id');
          done();
        });
    });
  });
  
});


```
