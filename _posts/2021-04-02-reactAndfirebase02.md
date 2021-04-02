---
title : "20210402 React and Firebase02,  firebase web api, Absolute Imports, login 여부 판단, Firebase Authentication, Auth Form  Nomadcoder  " #제목
category : #카테고리
tag : #태그
    - React
    - Firebase
    - 트위터 클론
    - Nomadcoder
    - firebase web api
    - Absolute Imports
    - login 여부 판단
    - Firebase Authentication
    - Auth Form
   
toc : true #옆에 목차
---

># Nomadcoder

- [노마드코더 : 트위터 클론 수업](https://nomadcoders.co/nwitter)
- 노마드코더님의 강의를 들으면서 공부한 내용을 정리하는 용도로 작성되었으며, 본내용이 틀릴 수 도 있습니다.

<br>

># Authentication (인증)

## Absolute Imports
  - import시에 dot을 쓰는 상대 경로는 지저분 하므로 절대 경로를 사용하는 import 기준을 만듦
  - [Absolute imports by create react app](https://create-react-app.dev/docs/importing-a-component/)
  - 이처럼 최상위 dir에 `jsconfig.json` 파일 만들면 아래처럼 src 기준으로 import path를 써도 됨

``` json
// 절대 경로 기준이 src로 시작됨
{
    "compilerOptions": {
      "baseUrl": "src"
    },
    "include": ["src"]
  }
```

<br>

## firebase web api 둘러보기

  - [Firebase : web API js reference](https://firebase.google.com/docs/reference/js)
  - [Firebase : firebase.auth.Auth](https://firebase.google.com/docs/reference/js/firebase.auth.Auth)
  - auth 기능을 사용하기 위해서 `fBase.js`에 auth를 import

<img src="../assets/img/import_firebase.png">

  - auth기능을 다른 곳에서 편하게 사용하기 위해서 default를 풀고 특정 authoService라는 변수에 auth기능을 담아 export시킴 

``` js
import firebase from "firebase/app";
import "firebase/auth";

const firebaseConfig = {
    apiKey: process.env.REACT_APP_API_KEY,
    authDomain: process.env.REACT_APP_AUTH_DOMAIN,
    projectId: process.env.REACT_APP_PROJECT_ID,
    storageBucket: process.env.REACT_APP_STORAGE_BUCKET,
    messagingSenderId: process.env.REACT_APP_MESSAGING_ID,
    appId: process.env.REACT_APP_APP_ID
  };

firebase.initializeApp(firebaseConfig);

export const authService = firebase.auth();  // import한곳에서 편하게 사용하기 위해서
```

<br>

## 유저를 가져와서 로그인 여부를 판단하는 기능

- Application(App.js)이 모든 로직들을 다루어야 함. 
- 그래서 AppRouter에 있는 useState를 제거하여 App.js에서 useState를 사용하게 하고 AppRouter에  isLoggedIn 값을 받아오는 형태로 하기 위해서 app.js에서 AppRouter component에 값을 연결시켜 전달함
- useState에 들어가는 인증 여부 값은 firebase로 부터 받는 `authService.currentUser` 를 넣음



``` js
// component/App.js
import React, {useState} from 'react';
import AppRouter from 'components/Router'; // src 기준 절대 경로
import { authService } from "fBase"; // fabase로 부터 authService 변수를 받아 편하게 사용


function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(authService.currentUser); // firebase의 인증 값 셋팅
  return (
    <>
    <AppRouter isLoggedIn={isLoggedIn}/> 
    <footer>&copy; {new Date().getFullYear()}  Rwitter </footer>
    </>
  );
}
// AppRouter에  값 전달
// footer는 단지 footer 임
export default App;
```

``` js
// component/Router.js
import React from 'react';
import {HashRouter as Router, Route, Switch} from "react-router-dom";
import Auth from 'routes/Auth';   // src 기준 절대 경로 
import Home from 'routes/Home';   // src 기준 절대 경로
// isLoggedIn 전달 받는 값 인자로 받고, 로그인 판단 삼항연산자로 처리
const AppRouter = ({isLoggedIn}) => {
    return (
        <Router>
            <Switch>
                {isLoggedIn ? (
                    <>
                    <Route exact path="/">
                        <Home />
                    </Route>
                </> 
                ) : (
                    <Route exact path="/">
                        <Auth />
                    </Route>
                )}
            </Switch>
        </Router>
    )
}

export default AppRouter; 


// <> : Fragment 인데 부모요소가 없을 때  많은 요소들을 render 하고 싶을 때씀
```

<br>
<br>
<br>

># 로그인 form 01

## Firebase Authentication

- firebase console  에서 Authentication에 들어가서 sign in method에서 어떤 종류의 인증 기능을 넣을지 선택 할 수 있음
  - google, github, email/password를 선택함 (github의 경우에는 자신의 github setting - dev set에서 Auth Application setting에서 client ID, secrets를 얻어서 firebase에 기재해줘야 함)

<br>

## Auth Form

- 전체적인 구조는 form에는 email, password, submit 의 input으로 구성 하고 하단에 다른 인증수단 button 2개 만들어 준다.
- `useState`를 이용해서 email, password 값을 저장 할 수 있게 함
- `onChange` event handler를 사용해서 input에 값이 입력되어 바뀌는 경우 event를 발생시키는데 event의 target의 name, value를 활용할 수 있다. name 값에 따른 setState를 설정한다.
- form의 `onSubmit` event handler를 이용해서 input 값을 처리하는데 `event.preventDefault`를 통해 새로고침 현상을 막고 input의 value를 queryString으로서 사용못하게 하기 위해서 임
  - [onSubmit vs action](https://penguingoon.tistory.com/188) : onSubmit는 event를 받아 특정 행동을 유도(callback), action은 url로 이동시키는 route 역할
- `required` 속성을 통해 필수 항목으로 만듦, `value` 속성에 해당 useState를 연결 시켜 줌

``` js
import React, { useState } from 'react';

const Auth = () => {
    const [email, setEmail] = useState("");
    const [password, setPassword] = useState("");
    const onChange = (event) => {
        const {target: {name, value}} = event;
        if(name === "email") {
            setEmail(value)
        } else if (name === "password") {
            setPassword(value)
        }
    }
    const onSubmit = (event) => {
        event.preventDefault();
    };
    return (
    <div>
        <form onSubmit={onSubmit}>
            <input name="email" type="email" placeholder="Email" required value={email} onChange={onChange}/>
            <input name="password" type="password" placeholder="Password" required value={password} onChange={onChange}/>
            <input type="submit" value="Log In" />
        </form>
        <div>
            <button>Continue with Google</button>
            <button>Continue with Github</button>
        </div>
    </div>
    );
}
export default Auth ;
```


