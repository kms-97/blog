---
title: "백준 JavaScript 입출력"
date: 2022-01-24T22:00:00+09:00
categories: ["problem"]
tags: ["JavaScript"]
cover: "https://user-images.githubusercontent.com/72490858/150789658-0936846c-37bc-49a9-b9de-de9a3fd14714.PNG"
---

<center>

![JavaScript Input](https://user-images.githubusercontent.com/72490858/150789658-0936846c-37bc-49a9-b9de-de9a3fd14714.PNG)
[by Baekjoon Online Judge](https://help.acmicpc.net/language/info)
</center>


## 1줄 입력
`백준 2231 분해합`
```js
const fs = require('fs');
const input = fs.readFileSync('input.txt').toString();
let start = 1;

const sum = (num) => {
    let sum = 0;
    while(num > 0) {
        sum += num % 10;
        num = Math.floor(num/10);
    }

    return sum;
}

while(true) {
    if ((start + sum(start)) == input) {
        console.log(start);
        break;
    }
    if (start == input) {
        console.log(0);
        break;
    }
    start++;
}
```
테스트케이스의 수 또는 조건에 따른 수가 주어지지 않는 단일 입력의 경우 한 번만 받으면 된다.<br>
fs모듈을 사용해 입력을 받는데, import로 모듈이 불러와지지 않는 듯 했다.<br>

## 여러줄 입력
`백준 8958 OX퀴즈`
```js
const fs = require('fs');
const input = fs.readFileSync('input.txt').toString().split('\n');
let answer = '';

for (let i = 1; i <= input[0]; i++) {
    let totalScore = 0;
    let score = 0;
    let str = input[i];
    
    for (let j = 0; j < str.length; j++) {
        if(str.charAt(j) == 'O') {
            totalScore += ++score;
        } else {
            score = 0;
        }
    }
    answer += totalScore + '\n';
}

console.log(answer);
```
여러줄 입력의 경우 `'\n'`으로 분할하여 배열 형태로 한 번에!! 읽는다.<br>
처음 주어진 테스트 케이스가 String이어도 반복문 돌리는데 지장이 없으며, 이후 조건을 String으로 받아 구현하면 된다.<br>
`console.log`에 시간이 많이 소요되므로 결과를 String으로 합쳐 계산이 끝난 뒤 한 번에 출력하는 것이 좋다.<br>

## 참고
[[JS] 입출력 시간초과, 빈 어레이 체크, node.js 실행(BOJ 10828) by awesome-hong](https://velog.io/@awesome-hong/JS-%EC%9E%85%EC%B6%9C%EB%A0%A5-%EC%8B%9C%EA%B0%84%EC%B4%88%EA%B3%BC-%EB%B9%88-%EC%96%B4%EB%A0%88%EC%9D%B4-%EC%B2%B4%ED%81%AC%ED%95%98%EA%B8%B0BOJ-10828)