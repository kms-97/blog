---
title: "Javscript - 객체"
date: 2021-09-15T16:00:00+09:00
categories: ["study"]
tags: ["javascript"]
cover: ""
---
## 객체
### 선언
```js
const obj = new Object()
const obj = {}
```
두 방법에 큰 차이는 없으나 객체 리터럴 방법(2번째)이 가독성과 성능 등의 이유로 권장됨
### 프로퍼티 생성
```js
const person = {
    name: 'kim',
    age: 25,
}  // 선언 시 입력

person.bloodtype = 'A' // 동적 선언
```
프로퍼티의 값으로는 어떤 자료형이든 가능
```js
const person ={
    name: 'kim',
    age: 25,
    live: true,
    friends: ['song', 'ho', 'kang'],
    say: function(word) {
        alert(`${this.name} : ${word}`)
    }
}
```
### 프로퍼티 접근
```js
const person = {
    name: 'kim',
    age: 25,
}

person.name // 'kim'
person['age'] // 25
```
### 프로퍼티 삭제
```js
delete person.age
delete person['name']
```
### 객체 반복
```js
for (key in object) {
    ...do something
}

Object.keys(object).map((key) => {
    ...do something
})
```
`Object.keys()`는 괄호 안의 객체의 키 값을 배열로 반환<br>
반환된 배열은 반복 가능한 객체이기 때문에 map, forEach, reduce, for of 같은 다양한 메서드가 사용 가능<br>
Object 생성자의 메서드는 따로 정리