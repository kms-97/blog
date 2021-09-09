---
title: "React로 블럭 채우기 퍼즐 만들기(2) - Context API"
date: 2021-09-09T05:30:00+09:00
categories: ["project"]
tags: ["React", "javascript", "ToyProject", "Block Puzzle", "Context API"]
cover: "https://user-images.githubusercontent.com/72490858/132579463-d5ab4303-ba6c-4b08-816d-20dcab741efa.gif"
---
## Context API 도입
(1)에서 만들었던 화면에서는 게임 모드를 선택해도 게임판의 격자가 바뀌지 않았다. `Gamemode.js`의 state가 `Gameboard.js`로 전달되지 않아서인데, state를 공통 상위 컴포넌트로 올려도 되지만 이후 게임판의 좌표를 통해서 드래그&드롭되는 위치를 확인할 계획이기 때문에 컴포넌트 간 여러 변수를 공유할 계획으로 전역변수를 위한 Context API를 사용해 보기로 했다.

`context/GameData.js`
```jsx
const GameDataContext = createContext({
    state: {
        gamemode : 7,
    },
    actions : {
        setGamemode: () => {},
    }
})

const GameDataProvider = ({ children }) => {
    const [gamemode, setGamemode] = useState(7)

    const context = {
        state : {
            gamemode
        },
        actions : {
            setGamemode
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

우선은 게임 모드 하나만 전달하기로 하고 초기 상태를 7로 주었다. Context를 사용하는 컴포넌트에서 값을 업데이트하기 위해서 `GameDataProvider` 함수에서 메소드를 같이 전달해주었다.

`App.js`
```jsx
import { GameDataProvider } from './component/context/GameData';

const App = () => {
  return (
    <div className="top-container">
      <GameDataProvider>
          <Gamemode/>
          <Scoreboard/>
          <Gameboard/>
          <ObjectList/>
      </GameDataProvider>
    </div>  
  )
}

export default App;
```

Context를 사용할 최상위 컴포넌트(여기서는 `App.js`)에서 Provider를 import해준 뒤 하위 컴포넌트들을 감싸주었다.

`GameMode.js`
```jsx
import GameDataContext from "./context/GameData"

const Gamemode = () => {
    const GameData = useContext(GameDataContext)
    const modeList = [5, 7, 9]

    const modeChange = (e) => {
        GameData.actions.setGamemode(Number(e.target.value))
    }

    const modeButton = modeList.map((mode) => {
        return(
            (mode === GameData.state.gamemode) ?
            <ModeButton key={mode.toString()} mode={mode} isActive={true} onClick={modeChange}/>
            :<ModeButton key={mode.toString()} mode={mode} isActive={false} onClick={modeChange}/>
        )
    })  

    return (
        <ul>
            { modeButton }
        </ul>
    )
}

export default React.memo(Gamemode)
```


`Gameboard.js`
```jsx
import GameDataContext from "./context/GameData"

const Gameboard = () => {
    const GameData = useContext(GameDataContext)

    const makeBlankArray = (size) => {
        const blankArray = []
        for (let i = 0; i < size; i++) {
            blankArray.push([])
            for (let j = 0; j < size; j++) {
                blankArray[i].push(j)
            }
        }
        return blankArray
    }

    return (
        <div className="top-gameboard-container">
            {
                makeBlankArray(GameData.state.gamemode).map((horizon, index) => {
                    return(
                        <Horizonboard horizon={horizon} line={index} key={"h" + index}/>
                    )
                })
            }
        </div>
    )
}

export default Gameboard

const Horizonboard = ({horizon, line}) => {

    return (
        <div className="horizon-gameboard">
            {
                horizon.map((order) => {
                    return(
                        <Eachboard order={order} line={line} key={line + String(order)}/>
                    )
                })
            }
        </div>
    )
}

const Eachboard = ({order, line}) => {

    return (
        <div className="square" id={line + String(order)}>
        </div>
    )
}
```
하위 컴포넌트에서는 useContext Hook을 사용하여 접근하였다. 이렇게 하면 Consumer를 사용하지 않아도 돼 보다 간략하게 작성이 가능하다. 다만 여기서 Context를 import하지 않고 useContext만 사용하면 undefined 에러가 발생한다.<br>

<br>
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/132579463-d5ab4303-ba6c-4b08-816d-20dcab741efa.gif" width="600px">
</p>

`GameMode.js`에서는 actions를 통해 각 버튼을 클릭할 때마다 모드를 바꿔주었고, `GameBoard.js`에서 크기만큼 빈 행렬을 만들어 출력해주었다.<br>
이제 Context에 게임 스코어나 게임판 좌표 등을 포함하여 전역변수로 이용할 계획이다.

