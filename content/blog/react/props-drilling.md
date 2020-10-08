---
title: React Prop Drilling
date: 2020-10-08 11:10:30
category: react
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- Kent C. Dodds
    - [props drilling](https://kentcdodds.com/blog/prop-drilling)
    - [multiple components](https://kentcdodds.com/blog/when-to-break-up-a-component-into-multiple-components)
- [Olusola Samuel](https://medium.com/javascript-in-plain-english/how-to-avoid-prop-drilling-in-react-using-component-composition-c42adfcdde1b#:~:text=Prop%20Drilling%20is%20the%20process,help%20in%20passing%20it)

# 학습 목표

- 왜 Props Drilling 을 피해야 하는지 알아본다.

# Props Drilling

Prop Drilling("threading" 라고도 부른다)는 데이터가 직접적으로 필요하지 않고 전달에만 도움이 되는,

React Component 트리의 한 부분에서 다른 부분으로 데이터를 전달하는 프로세스입니다.

간단한 토글 컴포넌트 작성.

```jsx
function Toggle() {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(o => !o)
  return (
    <div>
      <div>The button is {on ? 'on' : 'off'}</div>
      <button onClick={toggle}>Toggle</button>
    </div>
  )
}
```

위 컴포넌트를 2개 컴포넌트로 리팩토링 한다.

```jsx
function Toggle() {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(o => !o)
  return <Switch on={on} onToggle={toggle} />
}
function Switch({on, onToggle}) {
  return (
    <div>
      <div>The button is {on ? 'on' : 'off'}</div>
      <button onClick={onToggle}>Toggle</button>
    </div>
  )
}
```

스위치는 'toggle' 및 'on'  핸들러가 필요하므로 컴포넌트 하위 트리 구조로 코드를 리팩토링 했다.

```jsx
function Toggle() {
  const [on, setOn] = React.useState(false)
  const toggle = () => setOn(o => !o)
  return <Switch on={on} onToggle={toggle} />
}
function Switch({on, onToggle}) {
  return (
    <div>
      <SwitchMessage on={on} />
      <SwitchButton onToggle={onToggle} />
    </div>
  )
}
function SwitchMessage({on}) {
  return <div>The button is {on ? 'on' : 'off'}</div>
}
function SwitchButton({onToggle}) {
  return <button onClick={onToggle}>Toggle</button>
}
```

'on' 과 'toggle' 전달하려면 Switch컴포넌트를 통해 props를 드릴 해야 한다.

Switch 컴포넌트는 실제로 함수에 그 값을 필요로 하지 않는다. 

컴포넌트 들의 자식이 핸들러를 필요로 하기 때문에 그 소품을 전달해야 한다.

props drilling이 무조건 적으로 문제 되는 건 아니다. 2~3 계층 사이에서의 데이터 전달은 괜찮다. 데이터의 흐름을 쉽게 추적할 수 있다는 장점도 있다.

# Props Drilling의 단점

- 계층 구조로 된 컴포넌트 들을 분리하기 어려워진다.
- props가 변경 될 때마다 전역 코드를 수정 해야 한다.

# 대응 방법

- 글로벌 상태 관리 모듈을 도입한다. (e.g. Redux, context API, MobX)
- 컴포넌트를 여러 구성 요소로 나눈다.