---
title: "Javscript - 이터레이션 프로토콜"
date: 2021-09-28T21:00:00+09:00
categories: ["study"]
tags: ["javascript"]
cover: ""
---
## 이터레이션 프로토콜
이터레이션 프로토콜(iteration protocol)은 데이터 컬렉션을 순회하기 위한 규칙.<br>
이를 준수한 객체는 `for...of`로 순회 가능하고 Spread 문법의 피연산자가 될 수 있다.

### 이터러블
이터러블 프로토콜을 준수한 객체를 이터러블이라 하며, `Symbol.iterator` 메소드를 구현하거나 상속한 객체를 말한다.<br>
배열은 이터러블이며, 일반 객체는 아니다.
```js
const array = [1,2,3];
alert(Symbol.iterator in array); //true

const obj = {a:1, b:2, c:3};
alert(Symbol.iterator in obj); //false
```

ES6에서 제공하는 빌트인 이터러블은 다음과 같다.

    Array, String, Map, Set, TypedArray(Int8Array, Uint8Array, Uint8ClampedArray, Int16Array, Uint16Array,
    Int32Array, Uint32Array, Float32Array, Float64Array), DOM data structure(NodeList, HTMLCollection), Arguments

### 이터레이터
이터레이터 프로토콜은 next 메서드를 가지고 있어야 하며, next 메서드는 반복이 끝났는지에 대한 done 프로퍼티와 이터레이터로부터 반환되는 값인 value 프로퍼티를 갖는 객체를 반환해야 한다. 이를 준수하는 객체가 이터레이터이다.<br>
이터러블 객체의 `Symbol.iterator` 메소드를 호출하면 이터레이터를 반환한다.
```js
const array = [1, 2, 3];

const iterator = array[Symbol.iterator]();

console.log('next' in iterator); // true

console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next()); // {value: 2, done: false}
```