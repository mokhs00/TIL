- [logger](#logger)
  - [morgan](#morgan)

# logger

- loggers

## morgan

- <https://www.npmjs.com/package/morgan>
- node.js용 HTTP 요청 logger
- 여러 옵션들이 있는데 개발 시 dev옵션을 사용

  ``` js
  const express = require('express');
  const logger = require('morgan');
  const app = express();

  app.use(logger('dev'));

  app.listen(3000, function(){
    console.log('server is running');
  })
  ```
