---
title: "Express와 MySQL"
weight: 2
pre: "<b>2. </b>"
tags: ["Nodejs","Node.js", "Express", "Mysql"]
---

## Express & MySQL

>프로젝트 진행하며 찾은 내용들 정리

### 1. MySQL 사용

- `npm install mysql` 으로 mysql모듈 설치

```js
var mysql = require('mysql');
// 기본적으로 이렇게 사용
let client = mysql.createConnection({
    user: '',
    password: '',
    database: '',
  })
```

- 프로젝트를 진행하며 사용한 코드 샘플

```js
router.post('/measure', function(req, res, next) {
  let info = req.body
  let temp = info.temp
  let datetime = info.datetime
  let serial = info.serial
  let studentNumber = null
  client.query(`SELECT no FROM student WHERE serial = "${serial}";`, function(err, result, fields) {
    if (err) {
      console.log(err)
    } else {
      studentNumber = result[0].no
      client.query(`INSERT INTO temperature (temp, datetime, stu_no) VALUE ("${temp}", "${datetime}", "${studentNumber}");`, function(err, result) {
        if (err) {
          console.log(err)
        } else {
          res.json("완료")
        }
      })
    }
  })
})
```

### 2. 만났던 오류

- Vue.js <--> Node.js <--> MySQL에서 timezone 때문에 시간이 안맞는 오류가 발생
- MySQL에서 timezone을 한국으로 바꾸고 데이터를 저장하고 가져와도 +0900이 아닌 000Z가 계속 붙어서 왔음. 그리고 이미 Vue에선 moment로 timezone을 서울로 바꿔서 사용 중이었기 때문에 18시간이 더해지는 결과가 발생.
- 쉬운 방법으로 node.js에서 mysql에서 가져온 datetime을 string으로 바꿔 시간 계산을 하여 필요한 시간을 가져다 씀

```js
let client = mysql.createConnection({
    user: '',
    host: '서버',
    port: 3306,
    password: '',
    database: '',
    timezone: '+09:00',
    dateStrings: 'date'
  })
...
let reGrouped = []
      grouped.forEach(e => {
        let tem = {
          "student_number": e.student_number,
          "student_name": e.student_info.name,
          "student_gisu": e.student_info.gisu,
          "student_area": e.student_info.area,
          "student_part": e.student_info.part,
          "student_class": e.student_info.stu_class,
        }
        e.temp_info.forEach(el => {
          console.log(el.datetime);
          let measuringHour =  el.datetime.substr(11,2)
          measuringHour *= 1
          // console.log(measuringHour)
          // console.log(dateChange.length)
          if (8<= measuringHour && measuringHour <= 10) {
            tem["morning_temp"] = el.temperature
            tem["morning_time"]= el.datetime
          } else if (11 <= measuringHour && measuringHour <= 14) {
            tem["lunch_temp"] = el.temperature
            tem["lunch_time"]= el.datetime
          }
        })
        reGrouped.push(tem)
      })
      res.json(reGrouped);
.....
```

> 이런식으로 사용했음

