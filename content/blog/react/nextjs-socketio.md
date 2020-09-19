---
title: "NextJS, SocketIO chat application"
date: 2019-10-11 12:00:00
category: react
thumbnail: { thumbnailSrc }
draft: false
---

*Demo*
- [react-socket-chat](https://github.com/juunone/react-socket-chat)

*References* 
- [WebSocket Chat App with Socket.io and React](https://itnext.io/building-a-node-js-websocket-chat-app-with-socket-io-and-react-473a0686d1e1)
- [react-express-socketio](https://codeburst.io/isomorphic-web-app-react-js-express-socket-io-e2f03a469cd3)
- [Naver D2 socket-io](https://d2.naver.com/helloworld/1336)

NextJS + SocketIO 로 구성해본 리액트 채팅 어플리케이션 제작 경험을 공유하고자 한다.
SocketIO 를 처음접하고 채팅목록과 채팅화면까지 구현하기의 일련의 과정을 보여준다.
React는 기본적으로 이해하고 프로젝트 구성까지 가능한 이해수준을 가진 전제하에 포스팅을 작성했습니다.

### Socket-io ?
JavaScript NodeJS 프레임워크 기반의 실시간 웹을 구현할 수 있는 기술이다.

<img alt="스크린샷 2019-10-24 15 46 11" src="https://user-images.githubusercontent.com/35126809/67460229-6c4b6e00-f675-11e9-8268-6e10a745e57c.png">

> 브라우저 지원범위 (2019.10.24 기준)  
> 출처 : https://github.com/socketio/socket.io

### NextJS ? 

[NextJS](https://nextjs.org/)는 SSR(Server Side Rendering) 서버에서 렌더링을
진행하는 웹 프레임워크이다.  
SPA 환경을 좀 더 간편하게 구축하기위해 NextJS를 사용해 프로젝트를 구축했다.  
NextJS를 사용하지 않는다면 CRA를 이용해 react-router 보일러플레이트를 설치하는 방법도 있다.

### server.js

express를 이용해 서버를 구축하고, socket.io 에 연결시켰다.  
db는 따로 만들지 않고, mockup 데이터를 사용하였다.

```js
const express = require('express')
const http = require('http')
const socketIO = require('socket.io')
const port = 3001;
const app = express()
const server = http.createServer(app)
const io = socketIO(server)

io.on('connection', socket => {
  // console.log('connected!');

  socket.on('send message', (user, target, msg, isPicture) => {
    const copyData = [...data];
    const newDate = + new Date();

    copyData.forEach(v => {
      if(v.id === user){
        v.contents.forEach(key => {
          if(key.name === target){
            key.endedAt = newDate;
            key.messages.push({
              user: user,
              message: isPicture === true ? '' : msg,
              picture: isPicture === true ? msg : '',
              isRead: true
            })
          }
        });
      } else if (v.id === target) {
        v.contents.forEach(key => {
          if(key.name === user){
            key.endedAt = newDate;
            key.messages.push({
              user: user,
              message: isPicture === true ? '' : msg,
              picture: isPicture === true ? msg : '',
              isRead: false
            })
          }
        });
      }
    })

    const targetData = copyData.filter(v => v.id === user)[0];
    const targetMessages = targetData ? targetData.contents.filter(value => value.name === target)[0].messages : [];
    io.sockets.emit('receive message', targetMessages);

    const reduceTargetData = copyData.filter(v => v.id === target)[0];
    socket.broadcast.emit('receive data', reduceTargetData);
  })

  socket.on('receive data', (user) => {
    const newData = data.filter(v => v.id === user)[0];
    io.sockets.to(socket.id).emit('receive data', newData);
  });

  socket.on('receive message', (user, target) => {
    const targetData = data.filter(v => v.id === user)[0];
    const targetMessages = targetData ? targetData.contents.filter(value => value.name === target)[0].messages : [];
    io.sockets.emit('receive message', targetMessages);
  });

  socket.on('read message', (user, target) => {
    const copyData = [...data];
    const userIdx = copyData.findIndex(v => v.id === user);
    if(userIdx !== -1){
      const mappingData = copyData[userIdx].contents.map(key => {
        if(key.name === target){
          key.messages.forEach(value => {
            if(value.user === target) value.isRead = true;
          }) 
        }
        return key
      });
      copyData[userIdx].contents = mappingData;
    }

    const newData = copyData.filter(v => v.id === user)[0];
    io.sockets.to(socket.id).emit('receive data', newData);
  });

  socket.on('disconnect', () => {
    console.log('user disconnected!')
  })
})

server.listen(port, () => console.log(`Listening on port ${port}`))
```

`io.sockets()` 괄호안에 들어가는 문자는 클라이언트와 서버사이드가 주고받는 메소드명이다.  
`emit` 은 메소드를 실행시키고, `on` 은 클라이언트에서 해당 메소드가 `emit`되면 서버사이드에서 실행되는 메소드다.  
`io.sockets.to(socket.id).emit('receive data', newData)` 는 현재 연결된 id에만 'receive data'를 실행한다.  
`socket.broadcast.emit('receive data', reduceTargetData)` 는 현재 연결된 id를 제외한 다른 모든 id들에 'receive data'를 실행한다.


## socket-context.js

context-api 를 이용해서 접속시 한번씩만 클라이언트에 소켓을 연결해준다.
아래와 같이 3001번 서버와 `socketIOClient` 메소드를 통해 연결된다.

```js
import React from 'react'
import socketIOClient from "socket.io-client";

const socket = socketIOClient('localhost:3001');

export const SocketContext = React.createContext(socket);
``` 

## index.js

초기 진입시 유저 구분을 위해서 기존에 configuration 되어있는 유저를  
선택할 수 있게 ui를 구성하고, 선택 후 router의 link로 채팅방 리스트로 이동한다.

```js
import React, { useState, useCallback } from "react";
import Head from 'next/head';
import Link from 'next/link'
import Layout from '../components/layout';
import { Title, SelectUserWidget, SelectList, SelectButton } from '../components/styled';

const App = () => { 
  const [state, setState] = useState({
    user:''
  });

  const selectUser = useCallback((e) => {
    setState({
      ...state,
      user:e.target.value
    })
  }, []);

  return (
    <Layout>
      <Head>
        <title>React Socket.io Chatting</title>
        <link rel='icon' href='/favicon.ico' />
        <meta name="description" content="React Socket.io Chatting application"/>
        <meta name="keywords" content="react,socket.io,chatting,javascript" />
      </Head>
      <SelectUserWidget>
        <Title>사용자를 선택해주세요. &#x1F64F;</Title>
        <SelectList value={state.user} onChange={(e)=>{selectUser(e)}}>
          <option value="">선택</option>
          <option value="최준원 회장님">최준원 회장님</option>
          <option value="장만월 사장님">장만월 사장님</option>
          <option value="이미라 의사">이미라 의사</option>
          <option value="구찬성 지배인">구찬성 지배인</option>
          <option value="노준석 총지배인">노준석 총지배인</option>
          <option value="김유나 인턴">김유나 인턴</option>
        </SelectList>
        <Link href={`/list?user=${state.user}`} as='/list'>
          <SelectButton disabled={!state.user}>Select</SelectButton>
        </Link>
      </SelectUserWidget>
    </Layout>
  )
}

export default App;
```

## list.js

채팅방 리스트 페이지다.
`withRouter` HOC로 `const { router } = props;`    
router에서 전달해준 값을 props로 건네받은 유저정보를 획득한 후 사용한다.

```js
import React, { useState, useEffect, useContext } from "react";
import PropTypes from 'prop-types';
import Head from 'next/head';
import Router, { withRouter } from 'next/router'
import dynamic from 'next/dynamic'

import { SocketContext } from '../socket-context';
import Layout from '../components/layout';

const DynamicHeader = dynamic(() => import('../components/header'))
const DynamicChatRoomWidget = dynamic(() => import('../components/chatRoomWidget'))

const List = (props) => {
  const socket = useContext(SocketContext);
  const { router } = props;
  const [state, setState] = useState({
    user: router.query.user,
    target: router.query.target,
    read: router.query.read ? true : false
  })

  const receiveData = () => {
    socket.emit('receive data', state.user);  

    socket.on('receive data', (data) => {
      setState({ 
        ...state,
        data 
      })
    }); 

    if(!state.user){
      Router.push({
        pathname: '/'
      })
    }
  };

  const readMessages = () => {
    socket.emit('read message', state.user, state.target);
  };

  useEffect(() => {
    receiveData();

    if(state.read){
      readMessages();
    }

    return () => {
      socket.off('receive data');
    }
  }, []);

  return (
    <Layout>
      <Head>
        <title>채팅</title>
        <link rel='icon' href='/favicon.ico' />
        <meta name="description" content="React Socket.io Chatting application"/>
        <meta name="keywords" content="react,socket.io,chatting,javascript" />
      </Head>
      <DynamicHeader user={state.user} />
      <main>{state.data && <DynamicChatRoomWidget user={state.user} data={state.data} />}</main>
    </Layout>
  )
};

export default withRouter(List);

List.propTypes = {
  router: PropTypes.object,
};
```

## chat.js

`DynamicChatRoomWidget` 컴포넌트에서 라우팅된 채팅 화면이다.  
전체 코드를 올리기엔 너무 길어서 렌더쪽만 살펴보자면,  
헤더부분은 타겟의 이름이 표시되고, `renderChatMessages` 함수가 채팅을 렌더링 해주는
코어 컴포넌트다.  
푸터에는 `debounceMessage` = 인풋에 쓰고있는 텍스트를 debounce로 처리.
`setDebounceMessage` = 인풋에 onChange 이벤트로 텍스틀 변경해줌.
`sendMessages` = 텍스트를 엔터 혹은 버튼을 통해 socket으로 전달한다.  
socket.emit의 'send message' 이벤트로 전달하고, `receiveMessage` 함수로 다시
socket.on 'receive message' 이벤트로 변경된 데이터 값을 전달받는다.

```js
const sendMessages = () => {
  socket.emit('send message', state.user, state.target, debounceMessage, false)
  receiveMessage();  
};
```

```js
return (
  <Layout>
    <Head>
      <title>{state.target}과 채팅</title>
      <link rel='icon' href='/favicon.ico' />
      <meta name="description" content="React Socket.io Chatting application"/>
      <meta name="keywords" content="react,socket.io,chatting,javascript" />
    </Head>
    <DynamicHeader user={state.user} target={state.target} />
    {state.messages.length ? renderChatMessages() : ''}
    <DynamicFooter debounceMessage={debounceMessage} setDebounceMessage={setDebounceMessage} sendMessages={sendMessages} />
    <div ref={myRef} style={visibility:"hidden"}></div>
  </Layout>
)
```

- 코드는 본문 위의 demo 깃헙 repo를 참고하면 된다. NextJS를 쓰면서, routing에 대한 configuration 공수가 줄었다.
- socket.io 로 크로스브라우징의 socket 처리에 대한 부담을 줄여서, 좀더 간편하게 채팅앱을 만들어 볼 수 있는 프로젝트였다.