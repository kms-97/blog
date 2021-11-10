---
title: "NodeJS - Express"
date: 2021-11-09T21:00:00+09:00
categories: ["study"]
tags: ["NodeJS"]
cover: ""
---
## Express
### Express란?
> Node.js를 위한 빠르고 간결한 웹 프레임워크

Express 공식 사이트에서는 스스로를 "빠르고 간결한 웹 프레임워크"라고 소개하고 있다. 프레임워크는 '복잡하게 얽혀있는 문제를 해결하거나 서술하기 위해 사용되는 개념 구조'이다. 일반적으로 '웹 애플리케이션 프레임워크'라고 알려진 서버사이드 웹 프레임워크는 적절한 URL 핸들러로 라우팅, 데이터베이스와의 상호작용, 유저 인증 및 세션, 보안 등 일반적인 웹 개발 작업을 단순화하는 도구와 라이브러리를 제공한다.

웹 프레임워크는 매우 다양하고 각 언어별로 존재하는데, 인기있고 유명한 대표적 프레임워크는 다음과 같다.
1. Django / Python (풀스택)
2. Flask / Python
3. Express / Node.js & JavaScript
4. Ruby on Rails / Ruby
5. Laravel / PHP
6. ASP.NET
7. Spring / Java

### 사용 이유
즉, `Express`는 JavaScript로 작성되어 `Node.js` 런타임 환경에서 구동되는 웹 프레임워크라고 할 수 있다. 그렇다면 왜 많은 프레임워크 중 `Express`가 [점유율 1위](!https://2020.stateofjs.com/ko-KR/technologies/back-end-frameworks/)(2020 사용량 기준)를 차지하고 수 많은 팬을 가질 만큼 인기가 많은걸까?

우선 `Node.js`를 사용함으로써 얻는 장점이 있다. 코드를 직접 작성하며 모든 기능을 구현하는 것은 어려운 일이다. 세상에 무수히 많은 자바스크립트 프로그래머들이 미리 작성해 놓은 코드를 공개하여 누구든 사용할 수 있도록 한 `모듈`을 노드를 통해 받아 사용할 수 있다. 이 외에도 웹 서버 관점에서 노드가 가지는 많은 장점들은 다음과 같다.
 - 자바스크립트로 작성되는 코드로 새로운 언어를 배울 노력을 줄여줌.
 - 높은 성능, 빠른 속도.
 - 많은 사용자를 보유한 만큼 풍부한 문서와 활성화 된 커뮤니티.

그렇다면 `Express`의 장점은 무엇이 있을까? 아래와 같이 수 많은 장점들이 있지만 가장 큰 것은 미들웨어라고 생각된다. 필요한 만큼 기능을 확장할 수 있고, 필요없는 기능은 제외되는 만큼 가벼움과 성능의 이점을 챙길 수 있다는 점이 `MEAN stack`이라는 하나의 표준을 형성하고 다른 프레임워크의 기반이 된 이유일 것이다.
 - 웹 애플리케이션을 쉽고 빠르게 만들 수 있으며 설정과 커스터마이징이 자유로움.
 - `HTTP Method`와 `URL`을 기반으로 한 라우팅.
 - Jade, Vash 등의 템플릿 엔진과의 결합.
 - 에러 핸들링 미들웨어 지원.
 - `REST API` 설계 지원.
 - MongoDB, Redis, MySQL 등 데이터베이스와의 쉬운 결합.

### 미들웨어
<center>

![middleware](https://user-images.githubusercontent.com/72490858/141056858-905d5506-76f6-440c-a04e-92cb8df4d26d.png) <br>
[by NodeJS 교과서](https://thebook.io/080229/)
</center>
  
> Express는 최소한의 기능만을 갖춘 라우팅 및 미들웨어 웹 프레임워크이며, Express 애플리케이션은 일련의 미들웨어 함수 호출이다.

`미들웨어` 함수는 요청(req), 응답(res) 그리고 그 다음의 미들웨어에 대한 액세스 권한을 갖는 함수이다. request와 response 사이에서 특정 작업을 처리하고 조작하기 때문에 미들(middle) 웨어라고 불린다. 일반적으로 미들웨어는 모든 요청에 대해 코드를 실행하고, 요청 및 응답 오브젝트를 변경하며, `next()`를 통해 스택 내 다음 미들웨어를 호출하고, 요청-응답 주기를 종료한다.

미들웨어는 유형에 따라 5가지로 분류할 수 있으며, 각각의 정의와 사용법은 다음과 같다.

#### 애플리케이션 레벨 미들웨어
```js
import express from 'express'

const app = express();

// 앱이 요청을 수신할 때마다 실행됨.
app.use((req, res, next) => {
    console.log('Request Type :' + req.method);
    next();
});

// /user 경로에 대한 모든 유형의 HTTP 요청에 대해 마운트 된 미들웨어를 실행함.
app.use('/user', (req, res, next) => {
    console.log('Request URL :' + req.originUrl);
    next()
});

// /user 경로에 대한 get 요청을 처리함.
// 첫 번째 미들웨어에서 next('route')를 통해 다음 라우트를 호출하기 때문에 나머지 미들웨어는 생략됨.
app.get('/user', (req, res, next) => {
    console.log('ID :' + req.params.id);
    next('route')
}, (req, res, method) => {
    console.log('This middleware never run');
});

// /user 경로에 대한 get 요청을 처리함.
// 첫 번째 미들웨어에서 next()가 호출되지 않기 때문에 다음 미들웨어가 실행되지 않음.
app.get('/user', (req, res, next) => {
    console.log('Password :' + req.params.pw);
}, (req, res, method) => {
    console.log('This middleware never run');
});
```

#### 라우터 레벨 미들웨어
라우팅이란 URI(또는 경로) 및 특정한 HTTP 요청 메소드인 엔드포인트에 대한 클라이언트의 요청에 애플리케이션의 응답 방법을 결정하는 것이다. 라우트는 기본적으로 `app.METHOD(PATH, HANDLER)`의 구조를 가지며, 따라서 앞서 살펴본 `애플리케이션 레벨 미들웨어`에서 사용된 함수들도 모두 라우트이다.<br>
아래에서 살펴볼 `express.Router` 클래스는 모듈식으로 마운팅 가능한, 완전한 미들웨어이자 라우팅 시스템을 작성하는 방법이다. `express.Router()` 인스턴스에 바인드되어 `애플리케이션 레벨 미들웨어`와 완전히 동일하게 작동하기 때문에 "미니 앱(mini-app)"이라고도 불리운다.

`bird.js`
```js
import express from 'express'

const router = express.Router();

router.get('/', (req, res) => {
    console.log('/bird 경로에 대한 get 요청 처리');
    res.send();
});

router.post('/', (req, res) => {
    console.log('/bird 경로에 대한 post 요청 처리');
    res.send();
});

router.get('/about', (req, res) => {
    console.log('/bird/about 경로에 대한 get 요청 처리');
    res.send()
})

module.exports = router;
```

`app.js`
```js
import express from 'express'
import router from './path/bird'

const app = express();

// /bird 경로의 모든 요청에 대해 라우터를 실행함.
app.use('/bird', router);
```

`express.Router`를 사용하여 라우팅 경로를 나누게 되면, 메인 앱인 `app` 객체와 다른 `router = express.Router()` 객체에 각각의 경로 코드가 마운팅되기 때문에 서로 독립되어 다른 라우팅에 미치게 될 영향을 고려하지 않아도 되며, 메인 앱을 간결하게 유지할 수 있다.<br>

위의 `bird.js` 코드는 `router.route()`를 사용하여 아래와 같이 작성할 수 있음.
```js
router.route('/')
    .get((req, res) => {
        console.log('/bird 경로에 대한 get 요청 처리');
        res.send();
    })
    .post((req, res) => {
        console.log('/bird 경로에 대한 post 요청 처리');
        res.send();
    });
```

#### 오류 처리 미들웨어
오류 처리 미들웨어에는 항상 (err, req, res, next)의 총 4개 인수가 필요하다. 이는 해당 함수를 `오류 처리 미들웨어`로 인식하기 위한 필요 조건으로, 사용되지 않더라도 항상 지정되어야 한다.

```js
function logErrors(err, req, res, next) {
  console.error(err.stack);
  next(err);
}

function clientErrorHandler(err, req, res, next) {
  if (req.xhr) { // 요청이 Ajax라면
    res.status(500).send({ error: 'Something failed!' });
  } else {
    next(err);
  }
}

function errorHandler(err, req, res, next) {
  res.status(500);
  res.render('error', { error: err });
}

app.get('/bird', (req, res, next) => {
    if (!isBird(req.params.name) {
        next(err)
    });
    res.send('About bird');
    next();
}, (req, res, next) => {
    do something more...;
});
app.use(logErrors);
app.use(clientErrorHandler);
app.use(errorHandler);
```

위 같은 경우에 만일 첫 라우터의 첫 미들웨어에서 오류가 발생하지 않는다면 다음 미들웨어가 실행되겠지만, 오류가 발생할 경우 `next(err)`는 모든 핸들러를 건너 뛰고 오류 처리 핸들러를 실행시키기 때문에 `logErrors`, `clientErrorHandler`, `errorHandler`가 순차적으로 실행되게 된다. 여기서 마지막 `errorHandler`는 '모든 오류를 처리하는(catch-all)' 핸들러이다.

#### 기본 제공 미들웨어
`Express`의 유일한 기본 제공 미들웨어는 `express.static`이다. 정적인 파일들을 제공하는 라우터 역할을 한다.

```js
// /public/folder/foo.css를 /folder/foo.css로 로드.
app.use(express.static(__dirname + '/public'));

// 여러 디렉토리를 허용할 수 있음.
app.use(express.static(__dirname + '/public'));
app.use(express.static(__dirname + '/files'));

// 가상 경로를 설정 할 수 있음.
// /public/folder/foo.css를 /static/folder/foo.css로 접근하여 로드함.
app.use('/static', express.static(__dirname + '/public'));
```
이와 같은 방식은 요청 경로와 서버 폴더 경로를 다르게 하여 외부인이 서버 구조를 파악할 수 없어 보안에 이점을 가져다 주며, 정적 파일을 알아서 제공해주므로 `fs.readfile`을 사용할 필요가 없다. 만약 요청 경로에 해당하는 파일이 없다면 내부적으로 `next()`를 호출하고, 파일을 발견한다면 다음 미들웨어는 실행되지 않는다. 

#### 서드 파티 미들웨어
기본으로 제공되는 `express.static`외에 더 많은 추가 기능을 필요로 한다면 `Node.js` 모듈을 설치한 뒤, 앱에 로드하여 사용할 수 있다. 

`console`
```
$ npm install morgan cookie-parser
```

`app.js`
```js
import express from 'express'
import morgan from 'morgan'
import cookieParser from 'cookie-parser'

const app = express();

app.use(morgan('dev'));
app.use(cookieParser(process.env.COOKIE_SECRET));

app.use((req, res, next) => {
    do something
});
```

위 예시의 `morgan` 미들웨어는 요청과 응답에 대한 정보를 추가적으로 로그에 기록해주는 미들웨어이고, `cookie-parser`의 경우 쿠키 구문 분석 미들웨어이다. 미들웨어 내부에 `next()`가 들어있어 다음 미들웨어로 자동으로 넘어간다.<br>
아래와 같이 동시에 여러 개의 미들웨어를 장착할 수도 있다. 이 때, 정적 파일을 요구하는 요청에 해당 파일을 제공할 경우, `express.static` 미들웨어에서 요청-응답이 종료되어 하위 미들웨어는 실행되지 않는다.
```js
app.use(
    morgan('dev'),
    express.static('/', (__dirname + '/public')),
    express.json(),
    express.urlencoded({ extended: false }),
);
```
자주 사용되는 미들웨어와 Express 팀에서 유지하는 미들웨어들은 [여기](!https://expressjs.com/ko/resources/middleware.html)서 확인할 수 있다.

#### 활용

1. 미들웨어 간 데이터 전송
   ```js
   app.use((req, res, next) => {
       req.key = value; // 데이터 삽입
       next();
   }, (req, res, next) => {
       console.log(req.key); // 데이터 사용
       next();
   })
   ```
    세션을 사용한다면 `req.session` 객체를 사용하여 데이터를 전달해도 되지만, 이 경우 세션이 유지되는 동안에는 데이터 또한 계속 유지된다는 단점이 있다. 한 번의 요청-응답 사이클에서만 데이터를 유지하고자 한다면 `req` 객체에 대이터를 넣어둘 수 있다. 속성명은 자유롭게 설정해도 되나, 다른 미들웨어와 겹치지 않도록 해야한다.<br>
    `app.set`으로 설정하여 `app.get` 또는 `req.app.get`으로 어디서든 데이터를 가져올 수 있느나, 이 방식은 익스프레스에서 전역적으로 사용되는 객체이기 때문에 사용자(클라이언트) 각각의 값을 넣기에는 부적절하며, 앱 전체의 설정을 공유할 때 적합한 방식이다.

2. 미들웨어 속 미들웨어
   ```js
    app.use((req, res, next) => {
        if (process.env.NODE_ENV === 'production') {
            morgan('combined')(req, res, next);
        } else {
            morgan('dev')(req, res, next);
        }
    });
   ```
   미들 웨어 안에 미들웨어를 넣어 분기 처리를 할 수 있다. 위의 예시는 `.env` 파일에 설정된 `NODE_ENV`의 값에 따라 `morgan()` 미들웨어를 개발 환경으로 실행할지, 배포 환경으로 실행할지를 처리해주는 함수이다.

3. `app.all`과 `app.use`의 차이
   ```js
    app.use( "/product" , mymiddleware);
    // will match /product
    // will match /product/cool
    // will match /product/foo

    app.all( "/product" , handler);
    // will match /product
    // won't match /product/cool   <-- important
    // won't match /product/foo    <-- important

    app.all( "/product/*" , handler);
    // won't match /product        <-- Important
    // will match /product/
    // will match /product/cool
    // will match /product/foo
   ```
    `app.use`는 애플케이션의 메인 스택에 header, cookie, session 등을 위해 미들웨어를 삽입할 때 사용되며, 다른 `app.METHOD` 이전에 작성되어야 실행되고 그렇지 않을 경우 실행되지 않는다. 작성 순서대로 실행된다.
    `app.all`의 경우 라우터의 개념으로 바라보아야하며, 지정된 `path`의 모든 유형의 `HTTP 요청`에 대해 실행된다. 여러 콜백(핸들러)을 가질 수 있고, `path`가 정확히 일치해야만 실행된다.

    [Difference between app.all('*') and app.use('/') - stack overflow](!https://stackoverflow.com/questions/14125997/difference-between-app-all-and-app-use)

