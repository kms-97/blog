---
title: "React로 블럭 채우기 퍼즐 만들기(5) - 점수 올리기"
date: 2021-09-13T19:10:00+09:00
categories: ["project"]
tags: ["React", "javascript", "ToyProject", "Block Puzzle", "Context API"]
cover: "https://user-images.githubusercontent.com/72490858/133068451-3b1d35e6-365b-41ec-bc0b-547470bf10c7.gif"
draft: 
---
## 점수 기능 구현
게임판의 칸이 세로 또는 가로로 한 줄이 전부 채워지면 해당 줄을 지우고 점수를 올려야한다.
### 점수 관리
`GameData.js`
```jsx
import React, { useState, createContext } from "react";

const GameDataContext = createContext({
    state: {
        gamemode : 7,
        score : 0
    },
    actions : {
        setGamemode: () => {},
        setScore: () => {},
    }
})

const GameDataProvider = ({ children }) => {
    const [gamemode, setGamemode] = useState(7)
    const [score, setScore] = useState(0)

    const context = {
        state : {
            gamemode,
            score
        },
        actions : {
            setGamemode,
            setScore
        }
    }

    return (
        <GameDataContext.Provider value={context}>
            { children }
        </GameDataContext.Provider>
    ) 
}

const { Consumer: GameDataConsumer } = GameDataContext

export { GameDataProvider, GameDataConsumer }

export default GameDataContext
```

기존에는 `ScoreBoard.js`에서 state로 가지고 있던 Score 변수를 `Gameboard.js`에서 수정할 수 있도록 하기 위해 Context API에 포함시켰다. 점수가 초기화되는 경우는 게임모드를 변경하는 경우이기 때문에, 같은 Context에 작성하였다.

### 채워진 줄 비우기
`Gameboard.js > DisplayBlock()`
```jsx
    const GameData = useContext(GameDataContext)

    useEffect(() => {
        let filledLine = 0
        let filled = []
        const allSquare = document.querySelectorAll('.square')

        for (let i = 0; i < GameData.state.gamemode; i++) {
            const rowStart = i*GameData.state.gamemode
            const rowfilled = []
            const colfilled = []
            for (let j = 0; j < GameData.state.gamemode; j ++) {
                if (allSquare[rowStart+j].classList.length === 1) continue
                rowfilled.push(allSquare[rowStart+j])
            }
            for (let j = 0; j < GameData.state.gamemode; j ++) {
                if (allSquare[i+(j*GameData.state.gamemode)].classList.length === 1) continue
                colfilled.push(allSquare[i+(j*GameData.state.gamemode)])
            }

            if (rowfilled.length === GameData.state.gamemode) {
                filled = filled.concat(rowfilled)
                filledLine++
            }
            if (colfilled.length === GameData.state.gamemode) {
                filled = filled.concat(colfilled)
                filledLine++
            }
        }

        if (filledLine > 0) {
            filled.forEach((node) => {
                node.className = 'square'
            })
            GameData.actions.setScore(GameData.state.gamemode * filledLine + GameData.state.score)
        }
    })
```
원래 비어있던 줄이 채워지는 경우는 새로운 블럭이 게임판에 위치하는 경우이기 떄문에, `DisplayBlock()` 컴포넌트에서 useEffect를 사용하기로 했다. 기존의 블럭이 판을 채우고 새로운 블럭 생성을 위해 `blockState` state가 변화하면 렌더링이 되면서 useEffect의 함수가 작동하게 된다.<br>
querySelectorAll 메소드로 게임판의 각각의 칸을 전부 가져와서 세로와 가로 모든 경우를 탐색한다. 한 줄이 채워진 경우에는 `filledLine`의 값을 하나 올리고, 그 줄의 element를 전부 `filled` array에 채운다. 채워진 array 내부의 element들은 class를 `square`로 변경하여 색을 없애주고, Context에서 가져온 `setScore` 메소드로 점수를 증가시킨다.
<br>
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/133068451-3b1d35e6-365b-41ec-bc0b-547470bf10c7.gif" width="600px">
</p>

게임판 비우기와 점수 증가가 잘 작동하는 것을 볼 수 있다.