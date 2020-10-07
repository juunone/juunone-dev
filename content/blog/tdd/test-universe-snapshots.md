---
title: Test universe guide (Snapshots test)
date: 2020-10-07 10:10:72
category: tdd
thumbnail: { thumbnailSrc }
draft: false
---

# 참고

- [https://github.com/juunone/test-universe](https://github.com/juunone/test-universe)
- [BDD-Behavior-Driven-Development](https://www.notion.so/BDD-Behavior-Driven-Development-0e6bdf1abcea41d881a6dc637d15da15)
- [Blackbox test](https://www.notion.so/f1976fb35a694ec6992535e7a12a053c) 

# 학습 목표

- [test-universe](https://github.com/juunone/test-universe) repository 테스트 코드 튜토리얼

# 들어가며

Front-End 에는 다양한 테스트 영역이 존재한다.

E2E, Unit, UI 테스트 등.. 테스트는 개인적으로 주관이 많이 개입되는 영역이라고 생각한다.

그래서 각자가 생각하는 테스트의 개념이 다르지만, 그 Goal 은 **소프트웨어 품질**로 동일하다.

이 글은 테스트를 다뤄 보는 목표 지향적이며, 세세한 함수들이나 명렁어는 다루지 않는다.

test-universe(이하 'TU' 라고 칭함) 는 간단한 TO-DO 앱을 통해 테스트 코드를 전반적으로 다뤄 본다.

# 사용 된 기술

- React with create-react-app
- [jest](https://jestjs.io/docs/en/snapshot-testing)
- [testing-library](https://testing-library.com/)
- [react-test-renderer](https://www.npmjs.com/package/react-test-renderer)
- [storybook](https://storybook.js.org/)
- [applitools](https://applitools.com/)
- [cypress](https://www.cypress.io/)

# react-test-renderer

테스트 렌더러 같은 경우는 [리액트 공식문서](https://ko.reactjs.org/docs/test-renderer.html) 에도 예제가 들어가 있는 만큼

스냅샷 테스트를 진행하기 좋은 패키지다.

테스트 렌더러를 통해 계층화된 컴포넌트의 스냅샷을 찍을 수 있다.

스냅샷된 파일은 모두 해당 테스트 폴더 하위의 __snapshot__ 에 저장된다.

- `create`함수는 컴포넌트 인스턴스를 생성한다. 트리 전체를 메모리상에 렌더링 하게 되기 때문에 원하는 값을 가졌는지 검증이 가능하다.
- `act`는 검증을 위한 컴포넌트의 사전 준비단계인데, BDD 에서의 'When' 영역과 같다.
- `toJSON` 메소드를 통해 노드의 속성이 포함된 객체를 반환하는데, 스냅샷 테스트시에 사용할 수 있다.
- `toMatchSnapshot` jest의 단언문을 통해서 `toJSON` 을 통해 반환된 객체를 스냅샷으로 저장한다.
    - jest로 테스트를 하면 스냅샷에 변경 점이 생길 때마다 업데이트 가능하다.
    - 한 스냅에 한 객체만 저장되기 때문에 내부 텍스트나 element가 변경되면 테스트가 실패하게 된다.

```jsx
//TodoListContainer.js
import React, { useState } from "react";

import { Input } from "../components/Input";
import { Button } from "../components/Button";
import { List } from "../components/List";
import { deleteOne } from "../utils";

export const TodoListContainer = () => {
  const [state, setState] = useState({ value: "", list: [] });

  const deleteTodoList = key => {
    const res = deleteOne({ list: [...state.list], key });
    setState({
      ...state,
      list: res
    });
  };

  return (
    <div>
      <Input
        value={state.value}
        onChange={e => {
          setState({
            ...state,
            value: e.target.value
          });
        }}
      />
      <Button
        primary
        size="large"
        label="Add"
        backgroundColor="purple"
        color="white"
        onClick={() => {
          const newList = [...state.list];
          if (state.value !== "") newList.push(state.value);
          setState({
            value: "",
            list: newList
          });
        }}
      />
      <List data={state.list} deleteTodoList={deleteTodoList} />
    </div>
  );
};

// __snapshots__/TodoListContainer.test.js
import React from "react";
import { create, act } from "react-test-renderer";

import { TodoListContainer } from "../TodoLIstContainer";

describe("TodoListContainer", () => {
  let root;
  act(() => {
    root = create(<TodoListContainer />);
  });

  it("renders correctly", () => {
    expect(root.toJSON()).toMatchSnapshot();
  });
});
```

# Storybook and Applitools

이번 TU를 만들면서 [스토리북](https://storybook.js.org/) 이라는걸 사용했는데, 만들어진 컴포넌트들을 눈으로 확인하고,

GUI 를 통해 전달되는 props도 바로 바꾸면서 확인할 수 있는 디자인 시스템을 구축할 수 있는 플랫폼이다.

이건 테스트를 다루는 문서이니, 사용법은 패스하겠다.

~~근데 그게 테스트랑은 무슨 상관인가? 라고 생각할 수 있다~~

[Applitools](https://applitools.com/storybook/) 라는 툴을 스토리북에 올라간 컴포넌트 스냅샷 테스트가 가능하다.

Applitools 에서 본인들이 설명하기는 AI를 활용한 차세대 자동화 테스트 플랫폼이다.

## Setup

[eyes-storybook](https://www.npmjs.com/package/@applitools/eyes-storybook) 를 통해서, 스냅샷을 [applitools eyes](https://applitools.com/products-eyes/) 라는 제품에서 확인 할 수 있다.

```bash
npm install --save-dev @applitools/eyes-storybook //install

export APPLITOOLS_API_KEY=<your_key> //applitools eyes 에서 사용자 키를 발급받을 수 있다.
```

```json
//package.json
"scripts": {
    "eyes-storybook": "eyes-storybook",
},

// 위 명령어를 통해 스토리북에 올라간 컴포넌트들의 스냅샷을 찍는다.
```

## Usage

이제 `yarn eyes-storybook` 스크립트를 통해 정상적으로 통과된 5개의 스냅샷을 터미널에서 확인할 수 있다.

### 모든 케이스 성공시

![img1](https://user-images.githubusercontent.com/35126809/95276471-3ef7d000-0886-11eb-9262-457ee23d8bb3.png)

그리고 applitools eyes 대시보드에 들어가보면 아래와 같이 볼 수 있다.

![img2](https://user-images.githubusercontent.com/35126809/95276477-415a2a00-0886-11eb-9767-7a011ecabccd.png)

5개 모두 성공한 스냅샷 테스트 케이스

- 왼쪽의 리스트는 내가 실행한 테스트 리스트
- 오른쪽은 선택된 테스트의 스토리북 스냅샷

### 실패 케이스 존재시

![img3](https://user-images.githubusercontent.com/35126809/95276480-41f2c080-0886-11eb-8ef7-c685efedd1b4.png)

![img4](https://user-images.githubusercontent.com/35126809/95276483-4323ed80-0886-11eb-8de4-8bf39fea5c41.png)

1개의 실패한 테스트가 존재할 경우

![img5](https://user-images.githubusercontent.com/35126809/95276485-43bc8400-0886-11eb-95f5-e9b39839f7a1.png)

위에서 언급했던  AI 머신러닝을 통해서 어떤점이 변경됐는지 확인해서 표시 해준다.

개발자는 이걸 보고 해당 케이스가 의도한건지 아닌지 판단을 내려 `thumbs up`, `thubms down` 아이콘을 눌러주고 해당 케이스를 마무리하면 된다.

만약 의도하지 않은 거라면 수정되거나 혹은  resolve 할때까지 계속해서 해당 케이스는 실패할것이다.