---
title: "Javscript - 생성자 함수와 프로토타입"
date: 2021-09-28T18:00:00+09:00
categories: ["study"]
tags: ["javascript"]
cover: ""
---
## 생성자 함수
일반적으로 객체 리터럴 `{...}`을 통해 객체를 생성하지만, 유사한 객체를 여러 개 생성해야 할 때 `new` 연산자와 생성자 함수를 사용.<br>

생성자 함수의 선언은 일반 함수와 큰 차이가 없지만 두 가지 관례를 따름.
- 함수 이름의 첫 글자는 대문자로 시작.
- 반드시 `new`연산자를 붙여 실행. 

```js
function User(name, sex, age) {
  this.name = name;
  this.sex = sex;
  this.age = age;
  this.isAdmin = false;
}

let user = new User('John', 'M', '21');

alert(user.name); // John
alert(user.age); // 21
alert(user.isAdmin); // false
```

### 생성자 함수의 접근자 프로퍼티
```js
function User(name) {
    this.name = name;

    Object.defineProperty(this, 'foo', {
        get: function() {
            return this._foo;
        },
        set: function(value) {
            this._foo = value;
        }
    });
}
```

## 프로토타입
자바스크립트는 프로토타입 기반 언어라고 불림.<br>
모든 객체가 "메소드"와 "속성"을 "상속"받기 위한 템플릿으로써 프로토타입 객체(Prototype Object)를 가진다는 의미.<br>
기존에 존재하는 객체의 메서드 또는 속성을 다시 구현하지 않고 약간의 기능을 추가하여 새로운 객체를 만들고자 할 때 상속을 사용.<br>
ES6에서 Class가 추가되기 전까지 자바스크립트에서는 상속을 프로토타입으로 구현하였음.

프로토타입은 크게 두 가지로 구분하여 이해하는 편이 쉬움.
- 생성자 함수 객체의 `prototype` 속성
- 모든 객체의 `[[Prototype]]` 숨겨진 속성(암묵적 프로토타입 링크)

### 생성자 함수 객체의 prototype
함수 객체는 4가지 기본 프로퍼티를 가짐.
- arguments : 함수를 호출할 때 전달된 인자
- caller : 자신을 호출한 함수
- length : 정의된 인자의 갯수
- prototype : 함수 자신을 가리키는 `constructor` 프로퍼티 하나만 있는 객체

여기서 `prototype` 프로퍼티는 단순한 객체이지만, 생성자 함수로 사용될 때에는 특이한 역할을 함.<br>
`new Function()`으로 생성된 객체는 `[[Prototype]]`으로 생성자 함수의 `prototype` 프로퍼티가 가리키는 객체를 상속하게 됨.

```js
function Person() {}

let John = new Person()
Object.getPrototypeOf(John) // Object

Person.prototype = Array

let Smith = new Person()
Object.getPrototypeOf(Smith) // function Array
```

생성자 함수의 `prototype`에 메소드 혹은 프로퍼티를 추가하는 것으로 상속을 구현할 수 있음.

```js
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Person.prototype.sayHello = function() {
    alert('Hello ' + this.name + '!!')
}

let John = new Person('John', '23')
let Smith = new Person('Smith', '24')

John.sayHello() // Hello John!!
Smith.sayHello() // Hello Smith!!
```

### 암묵적 프로토타입 링크
함수를 포함한 모든 객체가 가지고 있는 `[[Prototype]]` 링크는 해당 객체가 생성된 생성자 함수의 `prototype` 프로퍼티가 가리키는 객체로 연결됨.
```js
function Person() {}

let John = new Person()
let Smith = new Person()

Object.getPrototypeOf(Smith) === Object.getPrototypeOf(John) // true
```

### 프로토타입 설정
프로토타입의 접근자로 사용되던 `__proto__`은 지양하는 편이 좋음.<br>
대신 아래와 같은 메소드를 사용하여 프로토타입에 접근하고 설정이 가능함.
- Object.create(proto, [descriptors]) : `[[Prototype]]`이 `proto`를 참조하는 빈 객체를 반환. 설명자를 추가로 넘길 수 있음.
- Object.getPrototypeOf(obj) : obj의 `[[Prototype]]`을 반환.
- Object.setPrototypeOf(obj, proto) : obj의 `[[Prototype]]`을 `proto`로 설정.


### 상속의 이점
```js
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.sayHello = function() {
        alert('Hello ' + this.name + '!!')
    }
}

/*
Person.prototype.sayHello = function() {
    alert('Hello ' + this.name + '!!')
}
*/

let John = new Person('John', '23')
let Smith = new Person('Smith', '24')

John.sayHello() // Hello John!!
Smith.sayHello() // Hello Smith!!
```

`[[Prototype]]`을 이용한 상속으로 구현하지 않고 생성자 함수 내 메서드로 선언하더라도 동일하게 동작함.<br>
그러나 이 경우 `sayHello()` 함수가 생성자 함수를 통해 생성된 객체 각각 할당되어 그 만큼 메모리를 차지함.<br>


