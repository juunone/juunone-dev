---
title: "What is Redux rematch?"
date: 2019-11-05 14:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

*References* 
- [redux-rematch github repo](https://github.com/rematch/rematch)
- [redux-rematch docs](https://rematch.github.io/rematch/#/)

## redux-rematch ?
리덕스의 복잡한 configuations를 조금더 간편하고,  
손쉽게 도와주는 redux-rematch 에 대해서 설명하고자 한다.  
action type에 대한 정의도 필요없고, 상태관리 함수 추가에 대한 공수를 줄일 수 있다.

## init
비즈니스 모델이 여러개 필요한경우 생성해서 스토어에 결합 시킬 수 있다.
`./model` 에 여러 모델들을 바인딩 해놓고 하나의 스토어에 `init` 해주는 형태다.

```js
import { init } from '@rematch/core'
import * as models from './models'

const store = init({
    models,
})

export default store
```

## model
state,reducer, async function 등 비동기 및 순수함수 그리고 상태까지 모두 한공간에서 만들 수 있다.  
state = 기존과 동일한 state들  
reducer = 비동기가 없는 순수한 리듀스 함수들 객체  
effects = 비동기 함수를 모아놓는 객체  

```js
export const count = {
    state: 0, // initial state
    reducers: {
        // handle state changes with pure functions
        increment(state, payload) {
            return state + payload
        },
    },
    effects: dispatch => ({
        // handle state changes with impure functions.
        // use async/await for async actions
        async incrementAsync(payload, rootState) {
            await new Promise(resolve => setTimeout(resolve, 1000))
            dispatch.count.increment(payload)
        },
    }),
}
```

## props, dispatch
아래 예시 코드를 보면 mapState, mapDispatch 연결하는 부분은 똑같고, mapDispatch에 더이상 actionType을 설정할 필요없이 객체에 접근하듯이 접근후 함수 호출이 가능하다.

```js
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider, connect } from 'react-redux'
import store from './store'

const Count = props => (
    <div>
        The count is {props.count}
        <button onClick={props.increment}>increment</button>
        <button onClick={props.incrementAsync}>incrementAsync</button>
    </div>
)

const mapState = state => ({
    count: state.count,
})

const mapDispatch = ({ count: { increment, incrementAsync } }) => ({
    increment: () => increment(1),
    incrementAsync: () => incrementAsync(1),
})

const CountContainer = connect(
    mapState,
    mapDispatch
)(Count)

ReactDOM.render(
    <Provider store={store}>
        <CountContainer />
    </Provider>,
    document.getElementById('root')
)
```

> 그 외에도 다양한 사용법이나 플러그인이 존재하지만 자세한건 [rematch docs](https://rematch.github.io/rematch/#/)를 참조하자.

`store.getState()` 스토어가 가지고있는 상태들을 불러오는 함수.  
`rootState` 스토어에 등록한 최상위 상태를 불러오는, `effects` 내부의 비동기 함수에서 인자로 불러오는 객체.  
등 다양한 사용법이나 객체가 존재한다. 하지만 제일 중요한건, 
더이상 스토어를 dispatch 함수를 생성할때마다 여러 파일들을 수정하거나 추가하는 작업에  
**공수(유지보수비용)를 줄일 수 있다는게** 제일 좋은 장점이다.
