---
title: "React로 블럭 채우기 퍼즐 만들기(4) - 블럭 넣기 구현"
date: 2021-09-09T05:31:00+09:00
categories: ["project"]
tags: ["React", "javascript", "ToyProject", "Block Puzzle", "Context API", "Portal"]
cover: ""
---
## 게임판 좌표 가져오기
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

<br>
<p align="center">
　<img src="https://user-images.githubusercontent.com/72490858/132590612-ca232c1c-5832-4e26-8a11-b737490a3378.PNG" width="1000px">
</p>

결과는 에러였는데, 서로 다른 컴포넌트에서 렌더링되어 useEffect로 접근이 불가능한 듯하다. 이를 해결하기 위해서 context를 통한 전역변수로 사용하고자 했는데, 게임판의 좌표 외에 각 격자마다의 좌표, 삽입할 경우의 style 또는 state 변경 등을 생각하면 모두 context로 전달하기에는 너무 복잡해질 것 같았다.<br>
그래서 `ObjectList.js`와 `Gameboard.js`를 합쳐 서로의 Html element에 접근할 수 있도록 하기로 했다.
