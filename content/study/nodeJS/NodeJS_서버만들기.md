---
title: "NodeJS - 서버만들기"
date: 2021-11-01T21:00:00+09:00
categories: ["study"]
tags: ["NodeJS"]
cover: ""
---
## HTTP모듈을 이용한 서버
```js
import * as http from 'http'
// const http = require('http');

http.createServer((req, res) => { // 요청에 대한 응답
    // req는 요청에 관환 정보를, res는 응답에 관한 정보를 담고 있음.
    res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8'}); // 헤더 작성
    res.write('<h1>Hello Node!</h1>'); // 본문 작성
    res.end('<p>Hello Server!</p>')
}).listen(8080, () => { //서버 연결. 8080포트를 할당함.
    console.log();
};
```
`createServer`를 여러 개 호출하여 포트를 다르게 하면 동시에 여러 서버를 띄울 수 있다.<br>
`listen`에 콜백 함수를 넣는 대신 이벤트 리스너를 등록하는 방법도 있음.

```js
import * as http from 'http'

const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8'});
    res.write('<h1>Hello Node!</h1>');
    res.end('<p>Hello server</p>');
}).listen(8080);

server.on('listening', () => {
    console.log();
});

server.on('error', (err) => {
    console.log(err);
});
```

### 파일 전송
`server.html`
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>Node JS 웹 서버</title>
    </head>
    <body>
        <h1>Hello Node!</h1>
        <p>Hello server</p>
    </body>
</html>
```

`server.mjs`
```js
import * as http from 'http'
import fs from 'fs/promises'

const server = http.createServer(async (req, res) => {
    try {
        const data = await fs.readFile('./server.html'); // html 파일을 동기식으로 읽음
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8'});
        return res.end(data); // 본문에 html 데이터를 작성
    } catch (err) { // async 예외처리
        console.log(err)
        res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8'});
        // 에러 메세지는 일반 텍스트이기 때문에 html이 아닌 text/plain
        return res.end(err.message);
    }
}).listen(8080)

server.on('listening', () => {
    console.log()
})
```

## REST 서버
### 라우팅
<center>

![rest](https://user-images.githubusercontent.com/72490858/139902062-18c93ba0-67d5-427f-86ca-d3a7a8b20f31.png)
[by gmlwjd9405](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)
</center>
라우팅은 어떤 네트워크 안에서 통신 데이터를 보낼 때 최적의 경로를 선택하는 과정이다. 최적의 경로는 가장 짧은 거리 또는 가장 적은 시간 내에 데이터를 전송할 수 있는 경로로, 여러 통신망에서 사용된다.<br>
여기서의 라우팅은 클라이언트에서 보내는 `REST` 요청을 우선 `HTTP Method` 별로 구분하고, 그 뒤 요청 주소를 통해 알맞은 응답을 보내도록 하는 것을 말한다.<br>

### 예시
```js
import * as http from 'http'
import fs from 'fs/promises'

const users = {}; // 데이터 저장용

http.createServer(async (req, res) => {
  try {
    if (req.method === 'GET') { // Method 구분
      if (req.url === '/') { // URL 구분
        const data = await fs.readFile('./restFront.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/users') {
        // URL에 따라 html 파일을 보내거나 데이터를 보낼 수 있음. 여기서는 Object를 Json으로 보내고 있음.
        res.writeHead(200, { 'Content-Type': 'application/json; charset=utf-8' });
        return res.end(JSON.stringify(users));
      }
      // /도 /about도 /users도 아닌 경우.
      // html 파일 내의 Link 태그에 연결된 외부 css 또는 js파일을 보냄.
      // Express를 사용하게 되면 static 미들웨어로 따로 관리하게 됨.
      try {
        const data = await fs.readFile(`.${req.url}`);
        return res.end(data);
      } catch (err) {
        // 주소에 해당하는 라우트를 못 찾았다는 404 Not Found error 발생
      }
    } else if (req.method === 'POST') {
      if (req.url === '/user') {
        let body = '';
        // 요청의 body를 stream 형식으로 받음
        req.on('data', (data) => {
          body += data;
        });
        // 요청의 body를 다 받은 후 실행됨
        return req.on('end', () => {
          // body(data)를 데이터 베이스(여기서는 users)에 등록
          // POST 메소드를 통한 등록이므로 201 Created 상태코드 반환
          res.writeHead(201, { 'Content-Type': 'text/plain; charset=utf-8' });
          return res.end();
        });
      }
    } else if (req.method === 'PUT') {
      if (req.url.startsWith('/user/')) {
        const key = req.url.split('/')[2];
        let body = '';
        req.on('data', (data) => {
          body += data;
        });
        return req.on('end', () => {
          // key를 통해 데이터 베이스에서 알맞은 항목을 body(data)로 수정
          res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
          return res.end();
        });
      }
    } else if (req.method === 'DELETE') {
      if (req.url.startsWith('/user/')) {
        const key = req.url.split('/')[2];
        // 데이터 삭제
        res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
        return res.end('ok');
      }
    }
    // URL이 어떤 항목과도 맞지 않을 경우 404 Not Found 반환
    res.writeHead(404);
    return res.end('NOT FOUND');
  } catch (err) {
    // 요청에 대한 오류 발생 시 500 에러 반환
    console.error(err);
    res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8' });
    res.end(err.message);
  }
})
  .listen(8082);
```

## 쿠키와 세션
### 쿠키
#### 삽입
```js
import * as http from 'http'

http.createServer((req, res) => {
    res.writeHead(200, { 'Set-Cookie': 'key=value' });
    return res.end()
}).listen(8080);
```

#### 활용
```js
import * as http from 'http'
import fs from 'fs/promises'
import url from 'url'
import qs from 'querystring'

const parseCookie = (cookie = "") => {
    // 쿠키를 문자열에서 객체로 변환하는 함수
    // 첫 서버 연결 시 쿠키가 존재하지 않음으로 기본값을 설정
};

http.createSever((req, res) => {
    const cookie = parseCookie(req.headers.cookie); //요청 헤더의 쿠키를 읽음.

    if (req.url.startWith('/login')) {
        const { query } = url.parse(req.url); //요청 url을 파싱하여 query만 구조분해할당.
        const { name } = qs.parse(query) // query 문자열을 파싱하여 name만 구조분해할당.

        // 현재 시각을 받아 5분 뒤로 유효기간을 설정
        const expires = new Date();
        expires.setMinutes(expires.getMinutes() + 5);

        // 쿠키를 설정하고 302 redirect 상태 코드와 함께 메인 페이지로 이동
        // 302 redirect 태그는 이동할 Location을 요구함.
        // 헤더에는 한글을 넣을 수 없어 `encodeURIComponent` 메서드로 인코딩
        // Set-Cookie 값으로는 줄바꿈을 넣으면 안되지만 편의상 넣었음.
        res.writeHead(302, {
            Location: '/',
            'Set-Cookie': 
                `name=${encodeURIComponent(name)};
                 Expires=${expires.toGMTString()};
                 HttpOnly;
                 Path=/`,
        });
        return res.end()
    } else if (cookie.name) { // url이 '/login'으로 시작하지 않고, cookie가 있는 경우
        // 로그인 된 화면을 반환
        res.writeHead(200, {});
        return res.end()
    } else {
        try {
            const data = await fs.readFile('./foo.html');
            
            res.writeHead(200, {});
            return res.end(data);
        } catch (err) {
            res.writeHead(500, {});
            return res.end(err.message)
        }
    }
}).listen(8080);
```

### 세션
세션은 쿠키와 달리 정보를 직접 담아 보내지 않고, 정보는 서버(데이터 베이스)에 저장한 뒤 세션 아이디를 클라이언트에 전달하여 아이디로만 소통한다. 따라서 쿠키보다 보안 측면에서 우수하나, 아래의 방식은 여전히 세션 아이디가 노출되어 있어 실제로 사용하기에는 적합하지 않음.

```js
import * as http from 'http'
import fs from 'fs/promises'
import url from 'url'
import qs from 'querystring'

const parseCookie = (cookie = "") => {
    // 쿠키를 문자열에서 객체로 변환하는 함수
    // 첫 서버 연결 시 쿠키가 존재하지 않음으로 기본값을 설정
};

const session = {}; //일반적으로 변수가 아닌 별도의 세션 저장소 사용

http.createSever((req, res) => {
    const cookie = parseCookie(req.headers.cookie); //요청 헤더의 쿠키를 읽음.

    if (req.url.startWith('/login')) {
        const { query } = url.parse(req.url); //요청 url을 파싱하여 query만 구조분해할당.
        const { name } = qs.parse(query) // query 문자열을 파싱하여 name만 구조분해할당.

        // 클라이언트에게 보낼 고유한 세션 아이디 생성
        // 저장소에도 세션 아이디를 Key로 하여 정보를 저장.
        const uniqueInt = Date.now();
        session[uniqueInt] = {
            name,
        }

        res.writeHead(302, {
            Location: '/',
            'Set-Cookie': 
                `session=${uniqueInt)};
                 HttpOnly;
                 Path=/`,
        });
        return res.end()
    } else if (cookie.session) { //session이 있다면
        const data = session[cookie.session]; // 저장소에서 세션 아이디를 사용해 정보를 가져옴
        res.writeHead(200, {});
        return res.end()
    } else {
        try {
            const data = await fs.readFile('./foo.html');
            
            res.writeHead(200, {});
            return res.end(data);
        } catch (err) {
            res.writeHead(500, {});
            return res.end(err.message)
        }
    }
}).listen(8080);
```