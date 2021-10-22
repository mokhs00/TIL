- [logger](#logger)
  - [morgan](#morgan)
- [error](#error)
  - [custom error middleware](#custom-error-middleware)

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

# error

## custom error middleware
- error middleware는 매개변수가 4개

``` js
const express = require("express");
const app = express();

function commonMiddleware(req, res, next) {
  console.log("commonMiddleware");
  next(new Error("error"));
}

function errorMiddleware(err, req, res, next) {
  console.log(err.message);
  next();
}

app.use(commonMiddleware);
app.use(errorMiddleware);



app.listen(3000, function () {
  console.log("server is running");
});


```