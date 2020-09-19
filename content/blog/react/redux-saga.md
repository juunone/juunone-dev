---
title: "Redux Thunk to Redux Saga"
date: 2019-08-12 02:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

*참조* 
- [Redux-saga github](https://github.com/redux-saga/redux-saga)
- [Redux-saga tutorial](https://redux-saga.js.org/docs/introduction/BeginnerTutorial.html)

개인적으로 진행하던 익명 투표 시스템의 비동기 부분을 담당하던  
redux-thunk를 redux-saga로 교체한 코드들에 대한 변경점을 공유하고자 한다.

### what is redux-middleware?
- 리덕스 미들웨어는 리덕스액션이 리듀서에 도착하기 전 가로채는 행동을 하는 코드이다.

### redux-saga and redux-thunk?
- 리덕스사가 혹은 리덕스청크는 리덕스 라이브러리와 함께 사용되는  
  리덕스 미들웨어의 종류들이다. 그 외에는 (redux-logger 등이 있다),

### Redux-thunk?
- redux-thunk는 액션 creator가 액션 대신 thunk라는 함수를 반환한다.  
thunk는 Redux의 store에 디스패치 기능에도 액세스 할 수 있다.  
thunk 내에서 API가 호출되며, API 응답에 따라 다음 함수가 전달된다.

### what is diffrent Redux-saga?
- 리덕스사가와 리덕스청크는 컨셉이 다르다.  
청크는 함수를 반환할수 있는 반면, 사가는 순수 객체만 전달한다.  
청크 = mutable, 사가 = immutable 환경이다.  
사가는 리듀서에 전달되기전 비동기 통신에 대한 응답을 전달해줄수 있는반면,
청크는 함수자체를 내려 준다.
즉 사가는 사이드 이펙트를 줄이고, 에러에 대한 핸들링이 더 유연하다고
볼수 있다.  
[Saga는 Thunk와는 달리 콜백 문제가 발생하지 않고, 비동기식 흐름을 쉽게 테스트 할 수 있으며 작업이 immutable하게 유지된다."](https://engineering.universe.com/what-is-redux-saga-c1252fc2f4d1)

### Workspace with Redux-thunk (before)

```sh
|-- actions
    |-- ActionType.js
    |-- index.js
|-- store
    |-- index.js
```

```js
//store index.js

import { createStore, applyMiddleware } from 'redux';
import rootReducer from '../reducers/index';
import thunk from 'redux-thunk'

export default function configureStore(initialState={}) {
  let log = [];
  if(
    window.location &&
    window.location.host &&
    window.location.host.indexOf('localhost') !== -1){
    const { logger } = require("redux-logger");
    log.push(logger);
  }

  return createStore(
    rootReducer,
    initialState,
    applyMiddleware(thunk, ...log)
  );
}
```

```js
// actions index.js

import * as types from './ActionTypes'

export const fetchDataStart = () => {
  return{
    type: types.FETCH_DATA_START
  }
};

export const fetchDataSuccess = data => {
  return{
    type: types.FETCH_DATA_SUCCESS,
    payload: { data }
  }
};

export const fetchDataFailure = error => {
  return{
    type: types.FETCH_DATA_FAILURE,
    payload: { error }
  }
};

export const fetchData = (method = 'GET', data, path = '') => {
  let id = "";
  if(method === 'PUT' || method === 'DELETE') id = data && data.id;
  let header = {'Content-Type':'application/json', 'Accept': 'application/json'};
  if(data && method !== 'DELETE'){            
    header = {'Content-Type':'application/x-www-form-urlencoded'}
  }  

  const searchParams = (params) => {
    return Object.keys(params).map((key) => {
      if(key === 'contents') return encodeURIComponent(key) + '=' + encodeURIComponent(JSON.stringify(params[key]));
      else return encodeURIComponent(key) + '=' + encodeURIComponent(params[key]);
    }).join('&');
  }

  return dispatch => {
    dispatch(fetchDataStart());
    return fetch(`/api/votes/${path}${id}`, {
      headers : header,
      method: method && method,
      body: data && method !== 'DELETE' ? searchParams(data) : undefined
    })
      .then(handleErrors)
      .then(res => {
        return res.json()
      })
      .then(json => {
        dispatch(fetchDataSuccess(json));
      })
      .catch(error => dispatch(fetchDataFailure(error)));
  };
}

const handleErrors = (response) => {
  if (!response.ok) {
    throw Error(response.statusText);
  }
  return response;
}

export const getHeaderItems = (headerType) => {
  return{
    type:types.HEADER_TYPE,
    headerType: headerType
  }
}
```

- 리덕스 청크를 썼을때의 비동기 부분을 보면 dispatch에 함수 자체를 보내는걸 볼 수 있다. mutable한 환경을 제공해줌으로서 사이드이펙트의 발생과 비동기 통신에 대한 에러 핸들링도 어려움이 있을수 있다.

### Workspace with Redux-saga (After)

```sh
|-- actions
    |-- ActionType.js
|-- sagas
    |-- index.js
|-- store
    |-- configureStore.dev.js
    |-- configureStore.js
    |-- configureStore.prod.js
```

- 사가의 구조는 위와 같이 CRA없이 웹팩을 이용해 리액트를 같이 사용할 경우와 같이  
  개발모드의 상태관리 및 프로덕션 레벨의 상태관리를 나누고 모드에 따라 모듈을 내보낸다.
- action 폴더에 존재하던 비동기 함수들은 모두 sagas 폴더에 들어갔고 액션은 액션타입에 대한 상수들만 존재한다.

```js
// sagas index.js

import * as types from '../actions/ActionTypes'
import { all, put, takeEvery } from 'redux-saga/effects'

function* fetchDataStart() {
  yield takeEvery(types.FETCH_DATA_START, fetching);
}

function* fetchDataSuccess(data) {
  yield put({ 
    type: types.FETCH_DATA_SUCCESS,
    payload: { data } 
  })
}

function* fetchDataFailure(error) {
  yield put({ 
    type: types.FETCH_DATA_FAILURE,
    payload: { error }
  })
}

function* fetching(action) {
  try {
    const data = yield fetchData(action.payload.method, action.payload.data, action.payload.path);
    yield fetchDataSuccess(data)
  } catch (error) {
    yield fetchDataFailure(error)
  }
}

export default function* rootSaga() {
  yield all([
    fetchDataStart(),
  ])
}

const fetchData = (method = 'GET', data, path = '') => {
  let id = "";
  if(method === 'PUT' || method === 'DELETE') id = data && data.id;
  let header = {'Content-Type':'application/json', 'Accept': 'application/json'};
  if(data && method !== 'DELETE'){            
    header = {'Content-Type':'application/x-www-form-urlencoded'}
  }  

  const searchParams = (params) => {
    return Object.keys(params).map((key) => {
      if(key === 'contents') return encodeURIComponent(key) + '=' + encodeURIComponent(JSON.stringify(params[key]));
      else return encodeURIComponent(key) + '=' + encodeURIComponent(params[key]);
    }).join('&');
  }

  return fetch(`/api/votes/${path}${id}`, {
    headers : header,
    method: method && method,
    body: data && method !== 'DELETE' ? searchParams(data) : undefined
  })
    .then(handleErrors)
    .then(res => {
      return res.json()
    })
    .catch(error => {
      return error
    });
}

const handleErrors = (response) => {
  if (!response.ok) {
    throw Error(response.statusText);
  }
  return response;
}
```

```js
// store configureStore.dev.js

import { createStore, applyMiddleware, compose } from 'redux'
import { createLogger } from 'redux-logger'
import createSagaMiddleware, { END } from 'redux-saga'
import sagaMonitor from '@redux-saga/simple-saga-monitor'
import DevTools from '../containers/DevTools'
import rootReducer from '../reducers'

export default function configureStore(initialState) {
  const sagaMiddleware = createSagaMiddleware({ sagaMonitor })

  const store = createStore(
    rootReducer,
    initialState,
    compose(
      applyMiddleware(sagaMiddleware, createLogger()),
      DevTools.instrument(),
    ),
  )

  if (module.hot) {
    module.hot.accept('../reducers', () => {
      const nextRootReducer = require('../reducers').default
      store.replaceReducer(nextRootReducer)
    })
  }
  store.runSaga = sagaMiddleware.run
  store.close = () => store.dispatch(END)
  return store
}
```

```js
// store configureStore.prod.js

import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware, { END } from 'redux-saga'
import sagaMonitor from '@redux-saga/simple-saga-monitor'
import rootReducer from '../reducers'

export default function configureStore(initialState) {
  const sagaMiddleware = createSagaMiddleware({ sagaMonitor })
  const store = createStore(rootReducer, initialState, applyMiddleware(sagaMiddleware))

  store.runSaga = sagaMiddleware.run
  store.close = () => store.dispatch(END)
  return store
}
```

```js
// store configureStore.js

if (process.env.NODE_ENV === 'production') {
  module.exports = require('./configureStore.prod')
} else {
  module.exports = require('./configureStore.dev')
}
```

- sagas/index.js 를 보면 [사가의 이펙트 API](https://redux-saga.js.org/docs/api/)를 통해 generator를 이용해
비동기에 대한 관리를 해준다.
requests를 보내고 그에 대한 응답만 순수하게 리듀서로 전달해주는 역할을한다.
generator의 yield 키워드를 이용해 더 세밀하게 상태관리를 가능케한다.
- 그리고 개발모드와 프로덕션 모드를 나눔으로서 redux-logger에 대한 구분점과 saga-monitor 라이브러리를 통해 현재 스토어의 상태확인도 더 용이해졌다.  
이 부분은 DevTools 라는 컴포넌트를 하나 생성해 로깅을 하는데 나중에 다른 포스팅을 통해 시간이 되면 다루겠다.