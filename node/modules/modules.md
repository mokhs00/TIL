# modules

유용한 node modules에 대해서 정리합니다.

# http 관련

## bodyParser (json request body)

- express에서는 json request body를 req.body로 가져오기 위해선 몇 가지 세팅이 필요하다.

- express 4.x ~ 4.16 이전 버전인 경우 body-parser module을 이용해야 한다.

``` js
const express = require('express');
const app = express();
const bodyParser = require('body-parser');

app.use(bodyParser.json());
```

- express 4.16 이후 버전인 경우 express.json() 모듈을 이용하면 된다.

``` js
const express = require('express');
const app = express();

app.use(express.json());
```
