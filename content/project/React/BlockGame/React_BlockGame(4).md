---
title: "React로 블럭 채우기 퍼즐 만들기(4) - 블럭 넣기 구현"
date: 2021-09-12T21:00:00+09:00
categories: ["project"]
tags: ["React", "javascript", "ToyProject", "Block Puzzle", "Context API"]
cover: "https://user-images.githubusercontent.com/72490858/132590612-ca232c1c-5832-4e26-8a11-b737490a3378.PNG"
draft: 
---
## 게임판 상호작용
블럭을 드래그&드롭 할 때, 게임판 위에 위치하는지와 어느 칸에 삽입하는지를 알기 위해 게임판의 좌표를 가져와야할 필요가 있다.

`ObjectList.js`
```jsx
    ...
    useEffect(() => {
            const container = document.getElementById("top-gameboard-container")
            const boundary = container.getBoundingClientRect()
            return (
                console.log(boundary) // 확인용
            )
            }
        )
    ...
```
useEffect를 사용하여 DOM이 렌더링되고 난 이후 게임판의 좌표를 `getBoundingCilentRect()` 통해서 가져오고자 했다.

<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/132590612-ca232c1c-5832-4e26-8a11-b737490a3378.PNG" width="1000px">
</p>

결과는 에러였는데, 서로 다른 컴포넌트에서 렌더링되어 useEffect로 접근이 불가능한 듯하다. 이를 해결하기 위해서 context를 통한 전역변수로 사용하고자 했는데, 게임판의 좌표 외에 각 격자마다의 좌표, 삽입할 경우의 style 또는 state 변경 등을 생각하면 모두 context로 전달하기에는 너무 복잡해질 것 같았다.<br>
그래서 `ObjectList.js`와 `Gameboard.js`를 합쳐 서로의 DOM에 접근할 수 있도록 하기로 했다.

`App.js`
```jsx
const App = () => {
  return (
    <div className="top-container">
      <GameDataProvider>
          <Gamemode/>
          <Scoreboard/>
          <Gameboard/>
      </GameDataProvider>
    </div>  
  )
}

export default App;
```

`Gameboard.js`
```jsx
const Gameboard = () => {
    const blockListLength = 3 // 생성할 블럭의 개수

    const makeBlockList = (length) => {
        const blockList = []
        for (let i = 0; i < length; i++) {
            blockList.push(<Block key={i}/>)
        }
        return blockList
    }

    return (
        <div>
            <div id="block-list" className="alert alert-light rounded-3 border border-primary">
                {makeBlockList(blockListLength)}
            </div>
        </div>
    )
}

export default Gameboard

...


const Block = () => {
    
    return (
        <div className="block">
            <DisplayBlock/>
        </div>
    )
}

const DisplayBlock = () => {
    const [blockState, setBlockState] = useState({
        blockArray: blockUtil.makeBlock()['blockArray'],
        color: blockUtil.makeBlock()['color'],
        blockRotate: blockUtil.makeBlock()['blockRotate']
    })

    return (
        <div className="block-container" style={{transform: `rotate(${blockState.blockRotate}deg)`}} onDragStart={() => (false)} onMouseDown={onmousedown}>
            {
                blockState.blockArray.map((line) => {
                    return(
                        <div>
                            {line.map((each) => {
                                return(
                                    each === 1?
                                    <div className="block-child fill" style={{backgroundColor: blockState.color}}></div>
                                    :<div className="block-child blank"></div>
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
`ObjectUtil.js`의 이름을 좀 더 알기 쉽게 `BlockUtil.js`로 바꾸고, `DisplayBlock()`함수를 꺼내 컴포넌트로 만들었다. 반복을 위해 Gameboard 컴포넌트에 `blockListLength` 변수로 형성될 블럭 갯수를 설정하고 return 내에서 map으로 반복하였다.<br>
이 과정에서 꽤 많은 시간이 소모되었는데, 다른 컴포넌트, 여기선 형제 컴포넌트에서 만들어지는 DOM에 접근하기 위해 Portal이나 useRef와 같은 다른 방법들을 찾아봤지만 대부분 기대한 효과가 아니거나 적절하지 못했고, ref 자체를 context로 넘겨주는 방법도 고려했지만 지속적인 상호작용에서는 계속 발생할 렌더링이 그리 적합하지 않다고 생각해 합치는 방향으로 잡았다.<br>

## 드래그&드롭 이벤트 추가
### 드래그 이벤트
`Gameboard.js > DisplayBlock()`
```javascript
    useEffect(() => {
        const onmousedown = (event) => {
            const blockContainer = event.currentTarget  
            // 클릭 시 보드에 맞게 블록 크기 확장
            const childBlockList = blockContainer.querySelectorAll("div.block-child")
            const squareWidth = document.querySelector('.square').clientWidth
            childBlockList.forEach((childBlock) => {
                childBlock.style.width = squareWidth +'px'
                childBlock.style.height = squareWidth +'px'
            })

            let shiftX = event.clientX - blockContainer.getBoundingClientRect().left;
            let shiftY = event.clientY - blockContainer.getBoundingClientRect().top;

            // (1) absolute 속성과 zIndex 프로퍼티를 수정해 공이 제일 위에서 움직이기 위한 준비를 합니다.
            blockContainer.style.position = 'absolute';
            blockContainer.style.zIndex = 1000;

            // 현재 위치한 부모에서 body로 직접 이동하여
            // body를 기준으로 위치를 지정합니다.
            // 유효하지 않은 위치일 경우 원상복귀를 위한 부모 노드 저장
            const parentNode = blockContainer.parentNode
            document.body.append(blockContainer);

            // 공을 pageX, pageY 좌표 중앙에 위치하게 합니다.
            function moveAt(pageX, pageY) {
                blockContainer.style.left = pageX - shiftX + 'px';
                blockContainer.style.top = pageY - shiftY + 'px';
            }

            // 포인터 아래로 공을 이동시킵니다.
            moveAt(event.pageX, event.pageY);

            function onMouseMove(event) {
                moveAt(event.pageX, event.pageY);
            }

            // (2) mousemove로 공을 움직입니다.
            document.addEventListener('mousemove', onMouseMove);

            // (3) 공을 드롭하고, 불필요한 핸들러를 제거합니다.
            blockContainer.onmouseup = function() {
                document.removeEventListener('mousemove', onMouseMove);
            }
        }
    })
```
드래그&드롭 이벤트 구현에 있어서는 [모던 JavaScript 튜토리얼](https://ko.javascript.info/mouse-drag-and-drop)의 내용을 많이 참고했다. 코드와 알고리즘이 자세히 설명되어있어 이해하기 쉽고 매우 도움이 되었다. 드래그 시작 시 보드의 크기에 맞게 블럭 확장을 위해 clientWidth로 값을 가져와 변경해주었고, 이동 시 body 태그로 노드를 옮겨 위치를 지정하기 때문에, 이후 적절한 위치에 드롭되지 않을 경우 다시 제자리로 이동하기 위해 부모노드를 변수에 담아두도록 했다.

### 드롭 이벤트
`Gameboard.js > DisplayBlock()`
```jsx
        blockContainer.onmouseup = function() {
            document.removeEventListener('mousemove', onMouseMove);

            // 블럭이 유효한 위치인지 확인(.sqaure 위에 위치해있는지, 아니면 반환)
            const isValidPosition = (block) => {
                const blockList = block.querySelectorAll(".block-child")
                try {
                    for (let i = 0; i < blockList.length; i++) {
                        const childBlock = blockList.item(i)
                        // 색이 채워진 블럭의 위치만 판정
                        if (childBlock.style["background-color"] === 'transparent') continue

                        // 드롭된 위치를 기준으로 밑에 위치한 element 찾기
                        const {x: presentX, y: presentY} = childBlock.getBoundingClientRect()
                        block.hidden = true
                        const objectBelow = document.elementFromPoint(presentX + squareWidth/2, presentY + squareWidth/2)
                        block.hidden = false

                        // 요소 중 하나라도 유효하지 않은 위치에 있으면 원위치
                        if (!(objectBelow && (objectBelow.className === 'square'))) {
                            throw('not valid position')
                        }
                    }
                } catch(err) {
                    parentNode.append(block)
                    block.style.position = "relative"
                    block.style.left = '';
                    block.style.top = '';
                    blockList.forEach((childBlock) => {
                        childBlock.style.width = '40px'
                        childBlock.style.height = '40px'
                    })
                }
            }
            
            isValidPosition(blockContainer)
        };
```
`Displayblock()`함수 내부의 `onmouseup` 속성 내부에 적절한 위치에 드랍되었는지를 확인하는 `isValidPosition()`함수를 추가하였다. 옮겨진 블럭에서 `elementFromPoint()`함수로 가져온 하위 element가 게임판의 square가 아니거나 없을 경우, 그리고 칸이 이미 채워진 경우 drag 이벤트가 시작되기 전 부모 노드로 돌아오도록 했다. 
<br>
<p align="center">s
　<img src="https://user-images.githubusercontent.com/72490858/133054222-4a2892d0-8c15-4d88-8cd2-de97ec9052be.gif" width="600px">
</p>

### 게임판 채우기
이제 블럭이 유효한 위치에서 드랍되면 아래의 게임판의 칸을 채우고 블럭은 사라져야한다. 그 뒤, 새로운 블럭이 생성되게 해준다<br>

`Gameboard.js > DisplayBlock()`
```jsx
const [blockState, setBlockState] = useState({
    blockArray: blockUtil.makeBlock()['blockArray'],
    color: blockUtil.makeBlock()['color'],
    blockRotate: blockUtil.makeBlock()['blockRotate']
})
...

    const isValidPosition = (block) => {
            const blockList = block.querySelectorAll(".block-child")
            const targetSqaure = []
            const blockColor = []
            try {
                for (let i = 0; i < blockList.length; i++) {
                    const childBlock = blockList.item(i)
                    if (childBlock.style["background-color"] === 'transparent') continue
                    blockColor.push(childBlock.style["background-color"])

                    const {x: presentX, y: presentY} = childBlock.getBoundingClientRect()
                    block.hidden = true
                    const objectBelow = document.elementFromPoint(presentX + squareWidth/2, presentY + squareWidth/2)
                    block.hidden = false

                    // 요소 중 하나라도 유효하지 않은 위치에 있으면 원위치
                    if (!(objectBelow && (objectBelow.className === 'square'))) {
                        return
                    }
                    targetSqaure.push(objectBelow)
                }

                targetSqaure.forEach((square) => {
                    square.className = `square ${blockColor[0]}`
                })

                const newState = blockUtil.makeBlock()
                setBlockState({...blockState, blockArray: newState.blockArray, color: newState.color, blockRotate: newState.blockRotate})
                
            } finally {
                parentNode.append(block)
                block.style.position = "relative"
                block.style.left = '';
                block.style.top = '';
                blockList.forEach((childBlock) => {
                    childBlock.style.width = '40px'
                    childBlock.style.height = '40px'
                })
            }
        }
        
        isValidPosition(blockContainer)
    };
```
렌더링을 유발시키기 위해 state로 만들어줄 블럭 상태를 관리하기로 했다. `blockUtil.js`에서 형태 리스트, 색상, 회전을 만들어주는 `makeBlock()` 함수를 가져와 state에 담았다.
`isValidPosition()`를 수정하여 `targetSquare` 변수에 `elementFromPoint()` 함수로 가져온 게임판의 square element를 담은 뒤, 블럭의 색과 동일하게 class를 추가해주었다.<br>
이전에는 유효하지 않은 위치에서는 error를 반환하도록 했는데, 지금은 유효하지 않은 위치와 블럭이 새로 만들어지는 경우 모두 원래의 위치에 표시해야하기 때문에 에러처리를 따로 하지 않고 finally로 받아주었다.

`Gameboard.css`
```css
.gray {background-color: gray;}
.blue {background-color: blue;}
.red {background-color: red;}
.purple {background-color: purple;}
.yellowgreen {background-color: yellowgreen;} 
```
class를 통해서 채워진 칸의 색을 다루니 css에도 클래스별로 추가해주었다.
<br>
<p align="center">s
　<img src="https://user-images.githubusercontent.com/72490858/133065948-9cfe534b-9644-4a7e-94e7-e52d20e4adb4.gif" width="600px">
</p>