---
title: "NodeJS - 모듈"
date: 2021-09-15T16:00:00+09:00
categories: ["study"]
tags: ["NodeJS"]
cover: ""
---
## 모듈
기존의 NodeJS에서는 CommonJS 모듈 시스템을 채택하여 `require()`로 모듈을 사용했으나,
현재는 ES모듈을 지원하여 `export` `import`를 사용할 수 있다.

### CommonJS
CommonJS 방식을 통한 모듈 생성에는 두 가지 방법이 있다.<br>
`module.exports`를 사용하는 방식과 `exports`를 사용하는 방법인데, 단일 객체를 내보내는 경우와 여러 객체를 내보내는 경우로 나누어 사용하게 된다.<br>
`require()`을 통해 프로젝트 내부 모듈을 불러올 때는 확장자를 생략할 수 있다.

#### exports
`calculator.js`
```js
const add = (a, b) => {
    return a + b
};

const subtract = (a, b) => {
    return a - b
};

exports.add = add;
exports.subtract = subtract;
```

`main.js`
```js
const calculator = require('./calculator');
// 구조 분해 할당 가능
// const { add, subtract } = require('./calculator'); 

console.log(calculator.add(1, 2)); //3
console.log(calculator.subtract(2, 1)); //1
```

#### module.exports
`calculator.js`
```js
const add = (a, b) => {
    return a + b
};

const subtract = (a, b) => {
    return a - b
};

// 여러 함수를 묶어 하나의 객체로 내보낼 수 있다
module.exports = {
    add : add,
    subtract : subtract,
};
```

`main.js`
```js
const calculator = require('./calculator');
// 구조 분해 할당 가능
// const { add, subtract } = require('./calculator'); 

console.log(calculator.add(1, 2)); //3
console.log(calculator.subtract(2, 1)); //1
```

### ES6 모듈
ES6 모듈의 경우 CommonJS 방식을 사용할 때와 달리 주의할 점이 두 가지 있다.<br>
- import를 통해 내부 모듈을 불러올 때는 반드시 확장자를 포함하여 경로를 명시해야 함.
- `package.json`을 통한 설정을 하지 않으면 확장자는 `.mjs`가 되어야 함.

#### export
`calculator.mjs`
```js
export const add = (a, b) => {
    return a + b
};

export const subtract = (a, b) => {
    return a - b
};
```

`main.mjs`
```js
import { add, subtract } from './calculator.mjs'
// import * as calculator from './calculator.mjs'

console.log(calculator.add(1, 2)); //3
console.log(calculator.subtract(2, 1)); //1
// console.log(calculator.add(1, 2));
// console.log(calculator.subtract(2, 1));
```