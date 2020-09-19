---
title: "React Unit test"
date: 2019-07-15 12:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

*참조* 
- [리액트 테스팅 튜토리얼](https://rinae.dev/posts/react-testing-tutorial-kr)
- [origin repo](https://github.com/the-road-to-learn-react/react-testing-mocha-chai-enzyme)

위 2가지 테스팅 튜토리얼 문서를 레퍼런스로 참조하고 React Hooks로
코드를 작성하며, 카운팅 유닛테스트 포스팅을 작성하게 됐다.
특히 튜토리얼 문서는 번역된지 1년이 지났지만 이제야 본걸 후회할 정도로
정말 디테일하게 설명되어있다.  
**꼭 선행후에 진행 하기 바랍니다.**

테스트 같은 경우 크게 4가지로 나뉜다.  
계층 구조로 점차 범위가 좁아진다.

- Unit test
- Integration test
- E2E(end-to-end) test 
- UI test

---

위 포스팅을 선행했다면, 기본적인 리액트,mocha,chai,enzyme 설치와 웹팩 설정
스크립트 설정등을 진행했다고 가정하고 이후에 App에 대한 코드베이스 레벨부분에 대해 진행하려고 한다.

## Counter app 코드 작성

- state에 기본값 0인 counter를 할당하고
해당 값을 h1 태그에 노출시킨다.
- setState 는 이후에 이어서 사용하겠다.

```js
import React, {useState} from 'react';

export const App = () => {
  const [ state, setState ] = useState({counter:0});

  return (
    <div>
      <h1>{state.counter}</h1>
    </div>
  );
};

export default App;
```

- setState로 증가, 감소 함수를 실행시켜주고 그함수들은 export로 내보낸다.
- 함수단위로 유닛테스트를 진행하기위해 인자를 넘겨받는 형태가아닌 전체 함수를 넘겨준다.

```js
import React, {useState} from 'react';

export const onIncrement = ({...state}) => {
  state.counter = state.counter + 1;
  return state;
};

export const onDecrement = ({...state}) => {
  state.counter = state.counter - 1;
  return state; 
};

export const App = () => {
  const [ state, setState ] = useState({counter:0});
  
  const doIncrement = () => {
    setState(onIncrement);
  };
  const doDecrement = () => {
    setState(onDecrement);
  };

  return (
    <div>
      <h1>{state.counter}</h1>
      <button onClick={doIncrement}>플러스 +</button>
      <button onClick={doDecrement}>마이너스 -</button>
    </div>
  );
};

export default App;
```

- 카운터도 유닛테스트에 포함시키기 위해 함수로 내보낸다.

```js
import React, {useState} from 'react';

// ...

export const Counter = ({ counter }) => {
  return <p>{counter}</p>
};

export const App = () => {
  const [ state, setState ] = useState({counter:0});
  
  // ...

  return (
    <div>
      <Counter counter={state.counter}/>
      // ...
    </div>
  );
};

export default App;
```

- - -

## Test case 작성

모든 함수 작성을 끝내고, 단위 테스트를 진행하기 위한
테스트코드를 작성해보겠다.

- 이 코드는 튜토리얼에 나온 코드 그대로 작성한 것이다.
- 함수형 컴포넌트에서 내보내진 증가 와 감소 함수가 각각의 단위테스트로 동작한다.
- *describe*는 테스트 묶음을 정의하고, *it*은 테스트 케이스를 정의한다.
- 값을 정의하고(arrange), 실행하고(act), 단언한다(assert).

```js
import {onIncrement, onDecrement} from './App';

describe('Counter State', () => {
  it('should increment the counter in state', () => {
    const state = { counter: 0 }; //정의
    const newState = onIncrement(state); //실행

    expect(newState.counter).to.equal(1); //단언
  });

  it('should decrement the counter in state', () => {
    const state = { counter: 0 };
    const newState = onDecrement(state);

    expect(newState.counter).to.equal(-1);
  });
});
```

- Enzyme로 단위테스트 및 간단한 통합테스트도 같이 작성해본다.
- mount, render, shallow, configure 같은 Enzyme 에서 제공해주는 함수를 통해 앱을 렌더링한다. [참조: airbnb Enzyme](https://airbnb.io/enzyme/)
- Chai 의 단언 문법 중 **setState({ }), state()** 는 **React Hooks의 함수형 컴포넌트에서 동작을 하지않는다.** 테스트를 실행할 경우
_Error: ShallowWrapper::setState() can only be called on class components_ 와 같이 class 컴포넌트에서만 사용가능하다는 에러가 뜬다.
즉 테스트 케이스를 통과하지 못한다 평생.
다른 방법으로 테스트를 진행해야 한다.

```js
describe('App Component', () => {
  it('Counter 래퍼를 그려낸다', () => {
    const wrapper = shallow(<App />);
    expect(wrapper.find(Counter)).to.have.length(1);
  });

  it('Counter 래퍼에 모든 Prop이 전달되었다', () => {
    const wrapper = shallow(<App />);
    let counterWrapper = wrapper.find(Counter);

    expect(counterWrapper.props().counter).to.equal(0);
  });

  it('counter를 증가 시킨다', () => {
    const wrapper = shallow(<App />);
    wrapper.find('button').at(0).simulate('click');
    let counterWrapper = wrapper.find(Counter);

    expect(counterWrapper.props().counter).to.equal(1);
  });

  it('counter를 감소 시킨다', () => {
    const wrapper = shallow(<App />);
    wrapper.find('button').at(1).simulate('click');
    let counterWrapper = wrapper.find(Counter);

    expect(counterWrapper.props().counter).to.equal(-1);
  });
});
```

위와 같이 2개의 테스트 묶음, 총 6개의 테스트 케이스를 작성하고
Mocha로 테스트를 실행할 경우 모든 케이스가 통과하는걸 확인 할 수 있다.
테스팅 튜토리얼을 한번 따라해보았으면 클래스 컴포넌트만 함수형 컴포넌트로 변경후에
약간의 테스트 케이스만 수정해주면 쉽게 따라할 수 있다.