---
title: Test universe guide (Unit test)
date: 2020-10-07 10:10:55
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
`
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

# Jest 를 이용한 Unit test

jest 는 JavaScript 테스팅 프레임워크다.

먼저 jest 공식 문서의 설명을 보면 

> Jest is a delightful JavaScript Testing Framework with a focus on simplicity.

직역 하자면 Jest는 단순함에 초점을 맞춘 JavaScript Testing Framework 이다.

Jest는 다양한 JavaScript의 테스트 프레임워크 중 하나인데, create-react-app 에 내장 되어있다.

__test__/${name}.test.js 에 존재하는 파일들은 모두 jest CLI 를 통해 테스트 가능하다.

프로그램을 테스트 할때는 반드시 'Assertion: 단언' 이라는게 필요하다.

비교할 두 대상을 가지고 참을 따지는걸 코드 상으로 풀이하는 것이다.

BDD 방식의 API가 사용된다.

```jsx
function sum(a, b) {
  return a + b;
}

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3); //단언문
});
```

위 코드의 단언문을 보면 sum(1, 2) 을 통해 나온 합산 값이 3이여야 한다.

합산 값이 3이 나온다면 test는 성공이고, 아니라면 fail 이다.

아래에서 TU의 유닛 테스트를 확인 해보자.

`testing-library` 은 모든 테스트를 DOM 위주로 진행한다.

컴포넌트의 구현 방식과 상관없이 결과만 같다면 테스트에 실패하지 않는다.

즉, 행위주도 테스트를 가능하게 하는 유틸이다.

## Input component

`input`은 입력을 받고 그 출력을 해당 `input`에 보여주는데 여기서 2가지 테스트가 있다.

1. 입력 받은 값이 정상적으로 출력 되는가
2. input의 `onChage`  이벤트가 정상 발생 하는가

```jsx
//Input.js
import React from "react";
import PropTypes from "prop-types";

export const Input = ({ placeholder, color, value, onChange, ...rest }) => {
  return (
    <input
      data-testid="todoInput"
      type="text"
      placeholder={placeholder}
      style={color && { color }}
      onChange={onChange}
      value={value}
      {...rest}
    />
  );
};

Input.propTypes = {
  placeholder: PropTypes.string,
  color: PropTypes.string,
  value: PropTypes.string,
  onChange: PropTypes.func
};

Input.defaultProps = {
  placeholder: "투두리스트 입력",
  color: "black",
  value: "",
  onChange: undefined
};

//Input.test.js
import React from "react";
import { render, screen, fireEvent } from "@testing-library/react";

import { Input } from "../Input";

describe("Input", () => {
  it("should call onchange event and change value", () => {
    const mockFn = jest.fn(); //임시의 목업 함수 생성.
    const changedValue = "lion";

    render(<Input onChange={mockFn} value="test" />); //input 컴포넌트를 render함수로 그려준다.
    const input = screen.getByTestId("todoInput"); //input의 'date-testid' attr를 가져온다

    fireEvent.change(input, { //fireEvent는 어떤 이벤트를 발생시킬때 사용한다.
      target: { value: changedValue }
    });
    input.value = changedValue; //input의 value를 직접적으로 변경해준다.

    expect(mockFn).toBeCalled(); //목업 함수가 콜이 됐으면 true
    expect(input.value).toBe(changedValue); // value가 'lion' 으로 변경됐다면 true
  });
});
```

## List component

`List`는 data 배열을 순회 하면서  todo들을 만들어주는 컴포넌트다.

1가지 테스트가 존재한다.

1. data 배열의 내용 및 길이 만큼 순회 하면서 `li` 를 그려 주는가

span의 `onClick` 이벤트는 유틸 함수라서 유틸에서 유닛테스트 에서 진행한다. 

```jsx
//List.js
import React from "react";
import PropTypes from "prop-types";
import { css } from "@emotion/core";

export const List = ({ data, deleteTodoList }) => {
  return (
    <ul data-testid="todoList">
      {data.map((value, index) => {
        return (
          <li
            key={index}
            css={css`
              margin: 1em 0;
              text-align: center;
              list-style: none;
              display: flex;
              justify-content: space-between;
              width: 100%;
            `}
          >
            {value}
            <span
              css={css`
                margin-left: 0.5em;
                border: 1px solid black;
                padding: 0.5em;
                font-size: 0.8rem;
              `}
              style={{ cursor: "pointer" }}
              onClick={() => {
                deleteTodoList(index);
              }}
            >
              X
            </span>
          </li>
        );
      })}
    </ul>
  );
};

List.propTypes = {
  data: PropTypes.array.isRequired,
  deleteTodoList: PropTypes.func.isRequired
};

//List.test.js
import React from "react";
import { render } from "@testing-library/react";

import { deleteOne } from "../../utils";
import { List } from "../List";

describe("List", () => {
  it("should render as long as the data", () => {
    const mockData = ["첫번째", "두번째"];

    render(<List data={mockData} deleteTodoList={deleteOne} />); //deleteTodoList props는 필수로 설정되어있기때문에 필수로 넘겨주어야 한다.

    const li = document.querySelectorAll("li");

    expect(li.length).toEqual(mockData.length); //render된 li의 갯수가 mockData 길이와 일치하는지 비교
  });
});
```

## Utils

재사용 가능한 함수들을 유틸로 만들고, 각각의 유틸들에 대한 유닛 테스트를 진행한다.

유닛테스트를 하기 제일 적합한 환경이다.

1. deleteOne 을 통해 들어온 list,key 를 통해 들어온 값들이 각각 동일한 경우 동일한 결과를 내보내는가

```jsx
//utils/index.js

export const deleteOne = ({ list, key }) => {
  const newList = [...list];
  const filtered = newList.findIndex((_value, index) => {
    return index === key;
  });
  newList.splice(filtered, 1);
  return newList;
};

//index.test.js
import { deleteOne } from "../../utils";

describe("utils", () => {
  it("should delete one of list data", () => {
    const a = { list: ["a", "b", "c"], key: 0 }; //a,b,c 중 0번째 index가 지워진 배열이 결과로 나와야한다.
    const b = deleteOne(a);
    expect(b).toStrictEqual(deleteOne(a)); //stringEqual을 통해 값과 타입까지 같이 비교한다.
  });
});
```