---
title: "Javscript - 클래스"
date: 2021-09-28T23:00:00+09:00
categories: ["study"]
tags: ["javascript"]
cover: ""
---
## 클래스
자바스크립트에서의 클래스는 함수의 한 종류로, 생성자의 기능을 대체하여 상속을 보다 간결하게 작성할 수 있음.<br>
별도의 문법을 사용하여 메소드 또는 프로퍼티 등을 선언할 수 있음.<br>
단, 단순한 편의 문법이 아닌 다음과 같은 차이점을 가진다.
- 반드시 `new`와 함께 사용해야 함.
- 클래스의 메서드는 열거할 수 없음. 즉, `for...in`을 통한 순회 시 메서드는 제외됨.
- 항상 엄격 모드(`use strict`)로 실행됨.

### 기본 문법
```js
class User {
    //클래스 필드
    isHuman = true;
    //생성자
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    //메서드
    getAge() {
        this.age++
    }
    //setter
    set age(value) {
        if (value < 0) {
            alert("나이는 음수가 될 수 없습니다.");
            return;
        }
        this._age = value;
    }
    //getter
    get age() {
        return this._age;
    }
}
```
메서드는 `function()` 괄호 붙여서 호출<br>
getter, setter는 `function` 괄호 없이 호출

### 상속
```js
class Parent {
    parentName = 'Parent';

    constructor(foo) {
        this._foo = foo;
    }

    get foo() {
        return this._foo
    }

    foobar() {
        return this.parentName;
    }
}

class Child extends Parent {
    childName = 'Child'

    // 생성자 오버라이딩
    constructor(foo, bar) {
        super(foo);
        this._bar = bar;
    }

    get bar() {
        return this._bar
    }
    // 메서드 오버라이딩
    foobar() {
        return this.childName;
    }
}

let child = new Child(1, 2)
child.foo // 1
child.bar // 2
child.foobar() //Child
```

### Static
`[[prototype]]`이 아닌 클래스 자체에 설정된 메서드 혹은 프로퍼티.<br>
특정 클래스 인스턴스가 아닌 클래스 '전체'에 필요한 기능을 만들 때 사용.<br>
생성자 함수를 `new` 연산자 없이 함수로써 호출할 때만 의미가 있으며, 인스턴스에서 직접적으로 접근이 불가능 하지만 상속은 가능함.

```js
class foo {
    // 정적 프로퍼티
    static foo = 'foobar';
    // 정적 메서드
    static bar() {
        return;
    }
}
```

### 프로퍼티 보호
#### 읽기 전용 프로퍼티
```js
class Parent {
    constructor(foo) {
        this._foo = foo;
    }

    get foo() {
        return this._foo;
    }
}

let bar = new Parent('foo');
bar.foo = 'bar'; // error
```
getter만 설정함으로써 초기 생성 시에만 값을 할당할 수 있고, 이후 수정이 불가능한 프로퍼티를 생성할 수 있음.

#### private
