---
title: "Javscript - 자료형"
date: 2021-09-15T16:00:00+09:00
categories: ["study"]
tags: ["javascript"]
cover: ""
---
## 원시형
### 숫자형
(2^53-1)부터 -(2^53-1)사이의 정수와 부동소수점 방식으로 표현된 실수

```javascript
const int = 3; // 정수 선언
const float = 7.12; // 실수 선언
const floatToInt = parseInt(float); // 7
const intTofloat = int.toFixed(3); // "3.000"
const StringToFloat = parseFloat('6.84px'); // 6.84
const StringToInt = parseInt('6.84px'); // 6
```
#### 무한대(Infinity)
`Infinity` 또는 `-Infinity`로 표현

```js
const inf = 3/0; // Infinity
const infDeclare = Infinity // 선언
inf === Infinify // true
// 판별
isFinite(inf) // false
```
#### NaN
아래의 경우 NaN을 반환
* 숫자로서 읽을 수 없음 (parseInt("어쩌구"), Number(undefined))
* 결과가 허수인 수학 계산식 (Math.sqrt(-1))
* 피연산자가 NaN (7 ** NaN)
* 정의할 수 없는 계산식 (0 * Infinity)
* 문자열을 포함하면서 덧셈이 아닌 계산식 ("가" / 3)

```javascript
const nan =  "숫자가 아님" / 2 // NaN
//판별
isNaN(nan) // true
isNaN('숫자가 아님') // true
isNaN() // true
isNaN(' ') // false
Number.isNaN(nan) // ture
Number.isNaN('숫자가 아님') // false
```
### BigInt
숫자형의 표현 범위를 넘어서는 매우 크거나 작은 정수<br>
Number형과의 연산이 불가능하며, 비교는 가능하다.
```javascript
const bigInt = 9007199254740991n; // 선언
const anotherBigInt = BigInt(9007199254740991); // 선언2
bigInt === anotherBigInt // true
typeof(bigInt) === 'bigint' // true

1n + 3 // TypeError
2n < 4 // true
3n == 3 // true
3n === 3 // false
```
### 문자형
```javascript
const str = '안녕하세요'; // '' 작은따옴표
const str2 = "안녕하세요"; // "" 큰따옴표
const str3 = `안녕하세요`; // `` 백틱
```
작은따옴표와 큰따옴표는 차이가 없으며, 백틱의 경우 여러 줄의 문자열 표현, 템플릿 리터럴 등의 표현이 가능
```js
alert(`안녕하세요
오늘은 날씨가
좋네요`) // 여러 줄 표현
alert(`1 + 2 = ${1+2}`) // 템플릿 리터럴, 1 + 2 = 3
```
텍스트 형식의 데이터는 문자'열' 형태로 저장
열이기 때문에 인덱스를 통해 접근할 수 있고 길이를 가짐
```js
(`안녕하세요`).length // 5
(`안녕하세요`)[1] // 녕
```
메소드를 통해 자르거나 단어 포함 여부를 검색할 수 있음
```js
// 부분 문자열 위치 찾기
('간장공장공장장').indexOf('간장') // 0
('간장공장공장장').indexOf('없음') // -1
('간장공장공장장').indexOf('공장', 2) // -1

// 부분 문자열 포함 여부
('간장공장공장장').includes('장공') // true

// 추출
('간장공장공장장').slice(1, 3) // '장공'
('간장공장공장장').slice(-7, -5) // '간장'
```
### 불린형
`ture` 또는 `false`
```js
const fact = true
const rumor = false

Boolean(1) // true
Boolean(0) // false
Boolean([]) // true
Boolean('') // false

0 == false // true
0 === false // false
undefined == false // false
NaN == false // false
```
### null
어떤 값의 '의도적으로' 비어있음을 표현<br>
존재하지 않거나, 알 수 없거나, 비어있음을 표현할 떄 사용
```js
let empty; // undefined
empty = null // 비어있음을 표현하기 위해 의도적으로 null로 변경
```
### undefined
값이 할당되지 않은 상태<br>
예약어가 아니기 때문에 변수명으로 활용이 가능하고, `undefined`를 할당할 수 있지만 하지 말 것
```js
// Do not this
let foo = undefined
let undefined = 'something`
```
### Symbol
변경 불가능하며, 다른 값과 중복되지 않는 고유한 값.
```js
let symbol = Symbol() // 선언
let idSymbol = Symbol('id')
alert(idSymbol.description) // 'id'
```