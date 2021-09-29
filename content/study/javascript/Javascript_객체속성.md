---
title: "Javscript - 객체 프로퍼티"
date: 2021-09-28T16:00:00+09:00
categories: ["study"]
tags: ["javascript"]
cover: ""
---
## 객체 프로퍼티
객체의 프로퍼티는 두 가지로 분류된다.
- 데이터 프로퍼티 : 키와 값으로 분류된 일반적인 프로퍼티.
- 접근자 프로퍼티 : 자체적으로 값을 가지지 않고, 다른 데이터 프로퍼티의 값에 접근할 때 호출되는 접근자 함수(set, get)으로 구성된 프로퍼티

### 데이터 프로퍼티

데이터 프로퍼티는 값(value)과 함께 특별한 세 가지의 속성(플래그, flag)을 가짐.
- writable : 값 수정
- enumerable : 반복을 통한 나열
- configurable : 프로퍼티 삭제 또는 플래그 수정

일반적인 선언(지금까지 하던 객체 프로퍼티 선언)으로는 기본값인 true로 설정됨.

#### 플래그 확인
- `Object.getOwnPropertyDescription` : 특정 프로퍼티에 대한 정보(설명자)
- `Object.getOwnPropertyDescriptors` : 주어진 객체의 모든 프로퍼티에 대한 정보(설명자)

```js
let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);
let allDescriptor = Object.getOwnPropertyDescriptors(obj);
```

#### 플래그 변경
`Object.defineProperty`를 사용하여 플래그를 변경할 수 있음.
```js
Object.defineProperty(obj, propertyName, descriptor)
```
```js
let user = {};

Object.defineProperty(user, "name", {
  value: "John",
  configurable: true
}); // 프로퍼티가 없다면 인수로 넘겨받은 정보로 새로운 프로퍼티를 생성

let descriptor = Object.getOwnPropertyDescriptor(user, 'name');

alert( JSON.stringify(descriptor, null, 2 ) );
/*
{
  "value": "John",
  "writable": false,
  "enumerable": false,
  "configurable": true
}
 */
```
`Object.defineProperty`를 사용하여 프로퍼티를 설정할 때, 일부 플래그를 생략할 수 있으며 생략시 기본값은 다음과 같음.
- value : undefined
- get : undefined
- set : undefined
- writable : false
- enumerable : false
- configurable : false


`Object.defineProperties`를 통해 여러 프로퍼티의 플래그를 한 번에 수정할 수 있음.

```js
Object.defineProperties(user, {
  name: { value: "John", writable: false },
  surname: { value: "Smith", writable: false },
  // ...
});
```
### 접근자 프로퍼티
접근자 프로퍼티는 'getter(획득자)'와 ‘setter(설정자)’ 메서드로 표현됨.
객체 리터럴 안에서 getter와 setter 메서드는 각각 get과 set으로 표현.

```js
let obj = {
  get propName() {
    // getter, obj.propName을 실행할 때 실행되는 코드
    // return `${this.name} ${this.surname}`;
  },

  set propName(value) {
    // setter, obj.propName = value를 실행할 때 실행되는 코드
    // [this.name, this.surname] = value.split(" ");
  }
};
```

이미 선언된 객체에는 다음과 같이 선언함.
```js
let obj = {}
Object.defineProperty(obj, 'foo', {
  get: function() {},
  set: function(value) {}
});
```


#### 접근자 프로퍼티의 플래그
접근자 프로퍼티는 함수로 구성되기 때문에 데이터 프로퍼티의 플래그와는 다른 플래그를 가지며,
`value`와 `writable`가 없는 대신에 `get`과 `set`이라는 함수가 있습니다.

- get : 인수가 없는 함수로, 프로퍼티를 읽을 때 동작함
- set : 인수가 하나인 함수로, 프로퍼티에 값을 쓸 때 호출됨
- enumerable : 데이터 프로퍼티와 동일함
- configurable : 데이터 프로퍼티와 동일함

get 또는 set을 활용하여 프로퍼티의 값을 원하는 대로 통제할 수 있음.

#### 접근자 동적 선언
```js
let expr = "foo";

let obj = {
  name: 'John',

  get [expr]() {
    // getter, obj.propName을 실행할 때 실행되는 코드
    // return `${this.name} ${this.surname}`;
  },

  set [expr](value) {
    // setter, obj.propName = value를 실행할 때 실행되는 코드
    // [this.name, this.surname] = value.split(" ");
  }
};

obj.foo // "John"
```
