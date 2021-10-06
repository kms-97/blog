---
title: "NodeJS - 파일 시스템 접근하기"
date: 2021-10-05T23:00:00+09:00
categories: ["study"]
tags: ["NodeJS"]
cover: ""
---
## fs모듈
NodeJS에서 파일 시스템 접근을 위해서는 파일을 생성, 삭제, 수정이 가능한 `fs` 모듈을 사용한다.<br>
대부분의 메소드들이 동기/비동기로 나뉘며, 동기방식의 메소드는 `Sync`라는 이름이 붙어있다.<br>
동기적 방식을 사용하면 다른 작업을 동시에 수행할 수 없으며, 비동기적 방식은 다른 작업을 병행할 수 있으며 완료된 후 매개변수로 전달된 callback 함수를 호출한다. 비동기 방식은 항상 수행 완료시 호출될 callback 함수가 작성되어야 한다.

### 비동기 방식
#### 파일 쓰기
`main.mjs`
```js
import fs from 'fs';

const text = 'fs.writeFile test'

fs.writeFile('text.txt', text, 'utf8', function(err) {
    if (err === null) {
        console.log('완료')
    } else {
        console.log('실패')
    }
});
```
실행시키면 `text.txt` 파일이 생성되고 callback 함수가 실행되는 것을 확인할 수 있다.<br>
비동기적 방식의 예외처리는 callback 함수에 전달되는 `err` 매개변수로 처리한다.

#### 파일 읽기
`main.mjs`
```js
import fs from 'fs';

fs.readFile('./text.txt', 'utf8', (err, data) => {
    if (err === null) {
        console.log('완료')
        console.log(data)
    } else {
        console.log('실패')
    }
});

// encoding을 넘기지 않으면 Buffer로 출력된다.
fs.readFile('./text.txt', (err, data) => {
    if (err === null) {
        console.log('완료')
        console.log(data)
        console.log(data.toString()) // Buffer 변환
    } else {
        console.log('실패')
    }
});
```
이 외에도 `readdir`를 통한 파일 목록 출력, `rename`을 통한 파일명 수정 등이 가능하다.

### 동기적 방식
기존의 `readfile`이나 `writefile`은 비동기적으로 작동한다. 그 결과를 callback함수로 전달하여 사용하게 되는데, 이를 동기적으로 바꾸고 싶거나 callback 함수를 피하고 싶다면 `Sync`가 붙은 동기적 방식을 이용하거나 `Promise`를 사용하게 된다.
#### Sync
`main.mjs`
```js
import fs from 'fs';

try {
    const description = fs.readFileSync('text.txt', 'utf8');
    console.log('성공')
    console.log(description)
} catch(err) {
    console.log('실패')
}
```
동기적 방식을 사용한 파일 읽기 예시이다. 동기적 방식의 경우 `err`를 callback에 전달하지 않기 때문에, `try... catch`문을 사용하여 예외처리를 수행한다.

#### util.promise
`main.mjs`
```js
import fs from 'fs';
import util from 'util';

const getDataFromFile = (filePath, callback) => {
    fs.readFile(filePath, 'utf-8', (err, result) => {
        if (err === null) {
            callback(null, result)
        } else {
            callback(err, null)
        }
    })
}

const readFile = util.promisify(getDataFromFile)

readFile('text1.txt')
    .then((result) => {
        console.log(result)
    })
    .catch((err) => {
        console.log(err)
    }) // 에러처리
```
`util.promisify`는 함수의 마지막 인자가 callback 함수인 경우 Promise로 쉽게 감싸줄 수 있는 유틸리티.<br>
`getDataFromFile`와 같이 callback 함수를 마지막 인자로 가지는 비동기적 함수를 `util.promisify`의 인자로 넘겨주어
`Promise`를 반환하게 한다.

#### fs.promises
`main.mjs`
```js
import fs from 'fs/promises';
// const fs = require('fs').promises;

const readFile = async (filepath) => {
    try {
        const description = await fs.readFile(filepath, 'utf-8')
    
        console.log(description)
    } catch(err) {
        console.log(err)
    }
}

readFile('text.txt')
```