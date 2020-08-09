---
title: "Express를 이용한 백엔드 구축"
weight: 1
pre: "<b>1. </b>"
tags: ["Nodejs","Node.js", "Express"]
---

## Express 시작

>백엔드에서 사용할 Nodejs Express 공부한 내용 정리.

### 1. Express 설치 및 Generator 사용

- express generator를 사용하면 스켈레톤 코드 생성됨(Django에서 startproject와 유사함.)

 ```sh
$ npm install express --save
$ npx express-generator
$ express --view=pug 사용할 이름
 ```

> `npx` command (available in Node.js 8.2.0)
>
> `--view=pug`를 사용안하면 `jade`파일로 구성됨. 

### 2. Routes 이용하기

- app.js  기본 구조

```js
var indexRouter = require('./routes/index');
...
app.use('/', indexRouter);
...
```

- 미리 만들어져 있는 index와 같이 새로 만들 route를 routes폴더에 생성 후 app.js에 추가
- routes/만들.js 구조

```js
let express = require('express');
let router = express.Router();
// Mysql을 사용하기 위해
let mysql = require('mysql');

let client = mysql.createConnection({
  user: '',
  password: '',
  database: ''
})
//router. 뒤에는 get, post, put, delete 와 같은 method 사용 가능
router.get('/ddd', function(req, res, next) {
//    res.render() 는 view 폴더의 pug 파일과 연동
// 나는 RESTful API로 백엔드로 사용하기 위해 res.json을 사용
// mysql에서 필요한 데이터를 가져오기 위한 쿼리문 작성    
    let queryString = "SELECT * FROM tableName;"
    client.query(queryString, function(err, result, fields) {
        if (err) {
            console.log(err)//대부분 쿼리문 오류시
        } else {
            res.json(result)
        }
    })
})
```

- function(req, res, next) 에서 req.body, req.query 등 이용하여 body나 query에 담겨있는 정보를 사용할 수 있음
- url 부분에 `/:string`과 같이 사용하면 req.params.string을 사용하여 url 변동에 맞춰 사용 가능

