---
title: "React로 블럭 채우기 퍼즐 만들기(1)"
date: 2021-09-06T23:50:00+09:00
categories: ["project"]
tags: ["React", "javascript", "ToyProject", "Block Puzzle"]
cover: "https://user-images.githubusercontent.com/72490858/132231751-adb886ad-6f00-4375-bacc-1e78888b7954.PNG"
---
## 시작
지난 번 [작혼 확장프로그램](https://github.com/kms-97/mahjang_extension)을 리액트로 만들어 본 이후,
리액트와 자바스크립트에 좀 더 익숙해지고자 새로 시도해 볼 토이프로젝트를 고심하던 중 모바일 게임을 보고 구현해보고자 한다.

## 구상
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/132230491-ef0fdb72-41b1-4e90-84f7-c082d3b36ba1.PNG">
</p>

- 게임 모드 버튼을 통해 게임판 크기 변경 가능
- 랜덤으로 생성되는 3가지의 블록을 드래그&드롭을 통해 배치
- 가로 또는 세로 한 줄이 다 차면 삭제
- 배치된 블럭 수, 삭제된 라인 또는 블럭 수 등으로 점수 표시

## 화면 구현
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/132231751-adb886ad-6f00-4375-bacc-1e78888b7954.PNG" width="600px">
</p>
우선 간단하게 화면을 구성해보았다. 하나하나 기능을 구현해 갈 예정이다.

### 게임 모드
`Gamemode.js`
```jsx
const Gamemode = () => {
    const [currentMode, setCurrentMode] = useState(7)
    const modeList = [5, 7, 9]

    const modeChange = (e) => {
        setCurrentMode(Number(e.target.value))
    }

    const modeButton = modeList.map((mode) => {
        return(
            (mode === currentMode) ?
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
```
`ModeButton.js`
```jsx
const ModeButton = ({mode, isActive, onClick}) => {
    return (
        <li>
            <button type="button" className={`btn btn-primary ${isActive?'active':''}`} data-bs-toggle="button" autoComplete="off" onClick={onClick} value={mode}>{mode}x{mode}</button>
        </li>
    )
}
```
게임 모드는 우선 5x5, 7x7, 9x9 3가지로 만들어두었다.<br>
선택된 모드를 state로 관리하여 active class를 통해활성화 된 버튼을 표시하고, 게임판 생성을 위해 `Gameboard.js`로 넘겨주고자 한다.


### 게임판
`Gameboard.js`
```jsx
const Gameboard = () => {
    const [gamemode, setGamemode] = useState([[1,2,3,4],[1,2,3,4],[1,2,3,4],[1,2,3,4]])

    return (
        <div className="top-gameboard-container">
            {
                gamemode.map((horizon, index) => {
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
게임판의 경우 각각의 칸이 채워졌을 경우를 class로 표시할지, state를 사용할지 고민 중이다.<br>
state를 사용하면 변화되는 칸만 리렌더링 될 것 같아 class보다 이점이 있을 것 같은데 좀 더 알아봐야 될 듯.<br>
현재는 선택된 모드의 props를 받지 않고 있어 확인을 위해 임의로 배열을 입력해두었다.<br>