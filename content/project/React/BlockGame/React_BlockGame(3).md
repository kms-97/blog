---
title: "React로 블럭 채우기 퍼즐 만들기(3) - 블럭 만들기"
date: 2021-09-09T05:31:00+09:00
categories: ["project"]
tags: ["React", "javascript", "ToyProject", "Block Puzzle", "Context API"]
cover: "https://user-images.githubusercontent.com/72490858/132589269-f118bdf8-d9a3-4687-9347-f22e60729138.PNG"
---
## 블럭 오브젝트 만들기
줄곧 빈 칸으로 있던 `ObjectList.js`를 먼저 채워주기로 했다. 하나의 파일 안에 코드를 몰아넣으면 복잡해질 것 같아 생성하는 함수는 따로 작성하여 import 해주기로 했다.

`ObjectUtil.js`
```jsx
const blockShape = [
    [[1]],
    [[0,1],[1,1]],
    [[1,1],[1,1]],
    [[1,0,0],[1,1,1]],
    [[0,1,0],[1,1,1]],
    [[0,0,1],[1,1,1]],
    [[0,0,1],[0,0,1],[1,1,1]],
    [[1,1,1],[1,1,1],[1,1,1]],
] // 블럭 모양 설정

const blockColor = [
    'black',
    'gray',
    'blue',
    'red',
    'purple',
    'yellowgreen'
] // 블럭 색깔 설정

const rotate = [
    0,
    90,
    180,
    270
] // 블럭 회전

const randomNumber = () => {
    return ({
        shape: Math.floor(Math.random() * 8), 
        color: Math.floor(Math.random() * 6),
        rotate: Math.floor(Math.random() * 4)
    })
} // 랜덤 생성을 위한 난수

const makeBlock = () => {
    const num = randomNumber()
    const blockArray = blockShape[num.shape]
    const color = blockColor[num.color]
    const blockRotate = rotate[num.rotate]

    return {
        blockArray,
        color,
        blockRotate
    }
}

export const displayBlock = () => {
    const {blockArray, color, blockRotate} = makeBlock()

    return (
        <div className="block-container" style={{transform: `rotate(${blockRotate}deg)`}}>
            {
                blockArray.map((line) => {
                    return(
                        <div>
                            {line.map((each) => {
                                return(
                                    each === 1?
                                    <div className="object-child block" style={{backgroundColor: color}}></div>
                                    :<div className="object-child"></div>
                                )
                            })}
                        </div>
                    )
                })
            }
        </div>
    )
}
```

`ObjectList.js`
```jsx
import * as blockUtil from "./ObjectUtil"

const ObjectList = () => {

    return (
        <div id="object-list" className="alert alert-light rounded-3 border border-primary">
            <div className="object">
                {blockUtil.displayBlock()}
            </div>
            <div className="object">
                {blockUtil.displayBlock()}
            </div>
            <div className="object">
                {blockUtil.displayBlock()}
            </div>
        </div>
    )
}

export default ObjectList
```
블럭을 이미지로 삽입하는 방법도 생각해봤는데, 나중에 `Gameboard.js`의 element와의 좌표 중첩을 원활히 확인하려면 이 방법이 낫겠다고 생각했다.
map으로 블럭을 만들기 위해서 배열로 모양을 설정했으며, 3개씩 나누어 3x2, 3x3 형태로 출력하기 위해 깊이 2의 배열로 만들었다.

<br>
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/132588982-947c0cc2-9b8c-41f6-adf2-eadac237e6ca.gif" width="600px">
</p>

현재는 블럭이 제대로 출력되는지 확인하기 위해 그냥 집어넣어 두었는데, 이후 드래그&드롭 이벤트로 삭제 후 재생성을 위해서 state로 상태관리를 해야할 수도 있다.