---
title: "React로 블럭 채우기 퍼즐 만들기(6) - 게임 오버"
date: 2021-10-11T18:00:00+09:00
categories: ["project"]
tags: ["React", "javascript", "ToyProject", "Block Puzzle"]
cover: "https://user-images.githubusercontent.com/72490858/136763736-e218a402-41b5-415d-86af-3f9eb2795240.PNG"
draft: 
---
## 게임 오버 판별
블록을 게임판에 하나 둘 때마다, 남은 칸에 넣을 수 있는 블럭이 1개 이상 있는지 판별하여, 불가능한 경우 게임 오버로 판정해야한다.<br>
그러려면 3개의 블럭 형태를 모두 게임판과 비교하여 확인해야하는데, 기존의 블럭 형태로는 어려워 생성 로직에 약간 수정이 필요했다.

### 블럭 생성 함수 수정
`BlockUtil.js`
```js
const blockShape = [
    [[1]],
    [[0,1],[1,1]],
    [[1,1],[1,1]],
    [[1,0,0],[1,1,1]],
    [[0,1,0],[1,1,1]],
    [[1],[1]],
    [[0,0,1],[1,1,1]],
    [[1,1,1]],
    [[0,0,1],[0,0,1],[1,1,1]],
    [[1,1,1],[0,0,1],[0,0,1]],
    [[1,1,1],[1,1,1],[1,1,1]],
]
...
const rotateShape = (shape, rotate) => {
    let rotated = []
    const row = shape[0].length
    const col = shape.length
    switch (rotate) {
        case 0:
            return shape
        case 90:
            for (let i = 0; i < row; i++) {
                let line = []
                for (let j = col - 1; j >= 0; j--) {
                    line.push(shape[j][i])
                }
                rotated.push(line)
            }
            break
        case 180:
            for (let i = col - 1; i >= 0; i--) {
                let line = []
                for (let j = row - 1; j >= 0; j--) {
                    line.push(shape[i][j])
                }
                rotated.push(line)
            }
            break
        case 270:
            for (let i = row - 1; i >= 0; i--) {
                let line = []
                for (let j = 0; j < col; j++) {
                    line.push(shape[j][i])
                }
                rotated.push(line)
            }
            break
    }
    return rotated
}

export const makeBlock = () => {
    const num = randomNumber()
    const blockArray = rotateShape(blockShape[num.shape], rotate[num.rotate])
    const color = blockColor[num.color]

    return {
        blockArray,
        color,
    }
}
```
기존에 가로로 한 줄 형태인 블럭을 `[[0,1,0],[0,1,0],[0,1,0]]` 등으로 빈칸도 포함하여 최대한 정사각형에 가깝게 출력했지만, 쓸데없는 빈칸이 포함될 경우 게임판 전체를 순회하는데 있어 범위 설정에 애로사항이 있어 `[[1],[1]]`과 같이 일렬로 수정하였다. <br>
추가로 각 블럭에 커스텀 데이터 속성으로 원래의 블럭 Array를 저장할 예정이기 때문에, 애초에 회전을 적용한 뒤 출력하기 위해 `rotateShape`함수를 추가하였다. 이제 별도로 rotate를 내보내지 않아도 생성이 가능하다.

### 블럭 Array 데이터 저장
`GameBoard.js`
```js
const DisplayBlock = () => {
    const GameMode = useContext(GameModeContext)
    const GameScore = useContext(GameScoreContext)
    const [blockState, setBlockState] = useState({
        blockShape: blockUtil.makeBlock()['blockArray'],
        color: blockUtil.makeBlock()['color']
    })

    ...

    return (
        <div className="block-container" onDragStart={() => (false)} onMouseDown={onmousedown} data-shape={blockState.blockShape.join('/')}>
            {
                blockState.blockShape.map((line) => {
                    return(
                        <div className="sub-block-container">
                            {line.map((each) => {
                                return(
                                    each === 1?
                                    <div className="block-child fill" style={{backgroundColor: blockState.color}}></div>
                                    :<div className="block-child blank" style={{backgroundColor: "transparent"}}></div>
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
`blockUtil.js`에서 더 이상 `Rotate` 프로퍼티를 내보내지 않으니 State를 수정해주고, JSX에 `data-shape` 속성을 추가하여 각 블럭의 Array를 기억하도록 하였다. <br>
`[[1,1],[0,1],[0,1]]`과 `[[1,1,1],[1,0,0]]` 등 Array를 String으로 변환하고, 다시 Array로 변환할 때 계층을 구별하기 위해 `join('/')`으로 구분하였다.

### 게임 오버 판별
`Gameboard.js`
```js
const DisplayBlock = () => {
    ...

    useEffect(() => {
            const allBlockContainer = document.getElementsByClassName('block-container')
            const board = getGameBoard()
            isItOver : try {
                for (let i = 0; i < allBlockContainer.length; i++) {
                    const blockContainer = allBlockContainer[i]
                    const block = blockContainer.getAttribute('data-shape').split('/').map((each) => (each.split(',').map((str) => (Number(str)))))
                    const row = block[0].length
                    const col = block.length

                    for (let i = 0; i <= board.length - col; i++) {
                        for (let j = 0; j <= board.length - row; j ++) {
                            if (isCanMerge(board, block, i, j, row, col) === 1) break isItOver
                        }
                    }
                }
                throw 'game over'
            } catch(error) {
                console.log(error)
            }
        }, [blockState])

    const getGameBoard = () => {
        const board = []
        document.getElementsByClassName('top-gameboard-container')[0].childNodes.forEach((horizon) => {
            let line = []
            horizon.childNodes.forEach((each) => {
                if (each.classList.length === 1) line.push(0)
                else line.push(1)
            })
            board.push(line)
        })

        return board
    }

    const isCanMerge = (board, block, x, y, row, col) => {
        for (let i = 0; i < col; i++) {
            for (let j = 0; j < row; j++) {
                if (board[x + i][y + j] && block[i][j]) return 0
            }
        }
        return 1
    }
```
게임 오버 판별은 기본적으로 모든 경우의 수를 탐색하는 것으로, `getGameBoard()`로 현재의 게임판 상태를 배열 형태로 읽어온 뒤, `isCanMerge()` 함수를 통해 좌측상단부터 기준점을 잡아 블럭이 삽입 가능한지를 한 칸씩 이동하며 탐색한다. 만약 모든 경우의 수에 블럭이 들어갈 수 없다고 하면 다음 블럭의 경우를 탐색하며, 중간에 하나라도 가능하다면 탐색을 중지, 모두 불가능하다면 현재는 Error를 발생하도록 해두었다. 추후 게임 오버 팝업을 띄우도록 수정할 예정. <br>
블럭을 하나 둘 때마다 새로운 블럭이 생성되고, 그 후 탐색해야하기 때문에 `useEffect`에 `blockState`를 참조하여 실행되는데, 단점으로는 초기 렌더링 시에 블럭의 수만큼 실행이 된다는 점이다. 이 함수로 인해 출력이 멈추지는 않지만 초기 렌더링 시에만 방지할 방법이 있다면 수정하고자 하나 아직 찾지 못했다.
<br>
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/136763736-e218a402-41b5-415d-86af-3f9eb2795240.PNG" width="600px">
</p>

넣을 수 있는 블럭이 없을 경우 콘솔창에 Game over Error가 출력되는 것을 볼 수 있다.