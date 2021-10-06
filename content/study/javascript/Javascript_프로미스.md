---
title: "Javscript - Promise, async/await"
date: 2021-09-29T07:00:00+09:00
categories: ["study"]
tags: ["javascript"]
cover: ""
---
## Promise
`Promise` 객체는 두 개의 내부 프로퍼티를 갖는다.
- state : 진행 상태. pending(보류) / fulfilled(이행) / rejected(거부) / settled(완료)로 나뉨.
- result : 결과. 진행 중 `revolve(value)`를 만나면 value로, `reject(error)`를 만나면 error로 바뀜.

### 활용
```js
const promise = (props) => {
    return new Promise((revolve, reject) => {
        // Do something...
        if () {revolve(result)}
        reject(error)
    })
}

// 프로미스 체이닝
promise(props).then((result) => {
    // Do something...
    return result
}).then((result) => {
    // Do something...
    return result
}).catch((err) => {
    // error handling...
    // 모든 then 뒤에 catch를 작성하면
    // then 핸들러에서 발생한 모든 에러를 처리 가능
}).finally(() => {
    // 무조건 실행...
    // finally 절에는 인수가 없음...
    // Promise가 이행되었는지, 거부되었는지 알 수 없음
})
```

### Promise API
      

## async/await
```js
const Async = async (props) => {
    try {
        let result = await promiseFunction();
        let anotherResult = await anotherPromiseFunction(result);

        return promiseObject
    } catch(err) {
        // error handling
    } finally {
        // Do something
    }
}
```
- await 
진행 중 `await` 연사자를 만나게 되면, 리턴되는 `Promise`가 `fulfill` 혹은 `reject` 될 때 까지 `async`함수의 실행을 일시 정지한다.<br>
만일 `await` 연사자 다음에 나오는 문의 값이 `Promise`가 아니라면, 해당 값을 `resolved Promise`로 변환시킨다.