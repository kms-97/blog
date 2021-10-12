---
title: "React로 블럭 채우기 퍼즐 만들기(7) - 게임 오버 팝업"
date: 2021-10-12T18:00:00+09:00
categories: ["project"]
tags: ["React", "javascript", "ToyProject", "Block Puzzle", "Portal"]
cover: "https://user-images.githubusercontent.com/72490858/136984881-c7ce431f-62aa-4537-90a7-f528592f3646.PNG"
draft: 
---
## 게임 오버 팝업 띄우기
더 이상 블럭을 넣을 수 없을 때 나오는 팝업은 화면 전체를 가려야하기 때문에 `root`가 아닌 동등한 레벨의 새로운 노드로 넣어줘야 할 필요가 있었다. 이를 구현하기 위해 리액트의 `Portals`를 사용하기로 하였다.

### 팝업창 만들기
`GameoverModel.js`
```js
import { useContext } from 'react'
import ReactDOM from 'react-dom'
import GameModeContext from './context/GameMode'
import GameScoreContext from './context/GameScore'
import './GameoverModal.css'

const GameoverModal = () => {
    const gameMode = useContext(GameModeContext)
    const gameScore = useContext(GameScoreContext)
    const link = document.getElementById('game-over')

    const reStart = () => {
        gameMode.actions.setGamemode(new Number(gameMode.state.gamemode))
        gameScore.actions.setGameScore(0)
        
        link.style.display = 'none'
    }

    return ReactDOM.createPortal(
    <div className="modal">
        <div className="content">
            <h1>Score : {gameScore.state.gameScore}</h1>
            <h3>Game Over</h3>
            <div>더 이상 놓을 수 있는 블럭이 없습니다.</div>
            <button className="btn btn-primary" onClick={reStart}>재시작</button>
        </div>
    </div>
        , link)
}

export default GameoverModal
```
점수를 보여줌과 동시에 재시작 버튼을 통해 새로운 게임을 시작할 수 있도록 하였다. 점수를 0으로 초기화하고 `new Number()`를 통해 이전 게임 모드를 다시 설정해줌으로써 `Gameboard.js`의 블럭 재생성 및 게임판 초기화가 작동하도록 구성하였다. 다만 이 경우, `new Number()`로 생성된 결과가 `Object`이기 때문에, 다른 컴포넌트의 비교 부분을 엄격한 비교(===)에서 동등 비교(==)로 바꾸어주었다.<br>
특징은 `ReactDOM.createPortal()`을 통해 일반적인 컴포넌트 구조에서 벗어나 다른 DOM에 모달창을 삽입한 것. 이를 위해여 `index.html`에 'game-over'를 id로 가지는 div를 하나 구성해두었다. 초기에는 보일 필요 없으므로 css에서 `display` 속성을 none으로 설정해두었다.

### 팝업창 띄우기
`Gameboard.js`
```js
const DisplayBlock = () => {
    ...
    useEffect(() => {
        const allBlockContainer = document.getElementsByClassName('block-container')
        const board = getGameBoard()
        isItOver:
            try {
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
                throw new Error('game over')
            } catch(error) {
                document.getElementById('game-over').style.display = 'block'
            }
    }, [blockState])
    ...
}
```
이전에 작성해둔 게임 오버 판별 로직에서 이제 더 이상 움직일 블럭이 없어 Error를 반환할 경우, `catch`를 통해 검출하여 만들어둔 모달창의 `display`속성을 none에서 block으로 변경해준다.
<br>
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/136984881-c7ce431f-62aa-4537-90a7-f528592f3646.PNG" width="600px">
</p>

모달창이 root가 아닌 제대로 의도한 DOM에 삽입되어 출력되었다.
<br>
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/136985351-06c31084-3501-490d-97dc-ff79f008132b.gif" width="600px">
</p>

게임 플레이 시에도 정상적으로 출력되며, 재시작 또한 제대로 작동된다.
