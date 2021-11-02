---
title: "NodeJS - 쿠키와 세션"
date: 2021-10-27T22:00:00+09:00
categories: ["study"]
tags: ["NodeJS"]
cover: ""
---
## 사용 이유
`HTTP`는 기본적으로 비연결성, 무상태성 특징을 가진다. 때문에 서버는 요청을 하는 클라이언트가 누구인지 기억하지 않으며 반복적인 인증을 필요로 한다. 이로 인한 속도 저하 이슈 또는 로그인 등의 상태 유지를 위해서 쿠키와 세션을 사용한다.<br>
쿠키는 주로 다음와 같은 목적을 위해서 사용된다.
1. 세션 관리
   로그인, 장바구니, 게임 스코어 등 정보 유지
2. 개인화
   사용자 선호, 테마 등의 세팅
3. 트래킹
   사용자의 행동을 기록 및 분석

## 쿠키
`HTTP 쿠키`는 서버가 헤더에 담아 사용자의 웹 브라우저에 전송하는 작은 데이터 조각. 브라우저는 쿠키를 응답과 함께 받은 쿠키를 저장해두었다가, 다음 요청시 쿠키를 포함하여 서버가 쿠키를 읽어 클라이언트를 식별할 수 있게 한다.

### 특징
 - 데이터를 서버가 아닌 클라이언트 측에 저장함.
 - 클라이언트는 총 300개의 쿠키를 저장 가능.
 - 하나의 도메인에서는 20개의 쿠키를 관리할 수 있음.
 - 하나의 쿠키는 최대 4KB까지 저장 가능.

### 구조
 - 쿠키명=쿠키값 : 기본적인 쿠키의 값. 키-값의 형태로 이루어져 있음.
 - Expires=날짜 : 만료 기한. 기본값은 클라이언트 종료시 삭제.
 - Max-age=초 : 날짜 대신 초로 입력하는 만료 기한. Expires보다 우선 됨.
 - Domain=도메인 명 : 쿠키가 전송될 특정 도메인. 기본값은 현재 도메인.
 - Path=URL : 쿠키가 전송될 특정 URL. 기본값은 '/'으로, 모든 URL에서 쿠키 전송이 가능.
 - Secure : HTTPS일 경우에만 쿠키를 전송.
 - HttpOnly : 일반적으로 쿠키는 클라이언트에서 JS로 조회가 가능하여 해킹에 취약함. 보안을 위해 브라우저에서 쿠키에 접근하지 못하게 설정.

## 세션
`세션` 또는 `세션 쿠키`는 클라이언트가 서버에 접속해있는 상태를 하나의 단위로 보고 그 상태를 유지시키기 위해 사용된다. 만료일자가 지정되지 않은 쿠키로, 연결이 종료되면 세션 또한 삭제됨.

### 특징
 - 데이터를 서버에서 직접 관리. 쿠키보다 보안성이 우수함.
 - 클라이언트에는 세션ID만 제공.
 - 용량 제한은 서버의 수용력에 좌우됨.

## Web Storage API
과거에는 클라이언트 측에 정보를 저장할 때 쿠키를 주로 사용. 이 경우 클라이언트에서의 모든 요청마다 쿠키가 함께 전송되기 때문에 성능 저하의 요인이 될 수 있음. 이 대안으로 `Web storage API`가 HTML5부터 제공됨.

### Session Storage
- 브라우저 또는 탭이 닫힐 때까지만 데이터를 저장함.
- 새록고침 또는 페이지 복구를 거쳐도 남아있음.
- 동일 페이지를 새로운 탭 또는 창에서 열면 각각의 탭 또는 창에 대해 새로운 세션이 생성됨.
- 데이터가 서버로 전송되지 않음.
- 쿠키보다 저장 공간이 큼(5MB).
- 페이지를 프로토콜별로 구분하여 `HTTP`와 `HTTPS`는 서로 다른 세션을 가짐.

### Local Storage
- 유효기간 없이 데이터를 저장.
- 데이터가 서버로 전송되지 않음.
- 저장 공간이 제일 큼(약 10MB).
- 브라우저를 닫았다 열어도 데이터가 남아있음.

### 사용방법
`Session Storage`와 `Local Storage`는 모든 메소드가 같음.

```js
localStorage.setItem('name', 'kms97'); //항목 추가
const userName = localStorage.getItem('name'); //항목 읽기

localStorage.removeItem('name'); // 항목 삭제
localStorage.clear() //전체 제거
```
`Storage`도 쿠키와 마찬가지로 값을 키-값의 형태로 저장한다. 값으로는 문자열, Boolean, 숫자, null, undefined 등을 저장 가능하지만 모두 문자열로 변환된다.  
객체를 저장할 때에는 키-값 형식으로 풀어 따로따로 저장하거나 `JSON.stringify`로 변환하여 저장한다.

```js
localStorage.setItem('age', 13);
localStorage.getItem('age'); // '13'

localStorage.setItem('object', {name: 'kms97', age: '24'});
localStorage.getItem('object') // [object Object]

localStorage.setItem('object', JSON.stringify{name: 'kms97', age: '24'});
JSON.parse(localStorage.getItem('object')) // { name: "kms97", age: "24" }
```