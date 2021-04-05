---
title : "20210405 React and Firebase04 google로그인, github로그인, 로그아웃, 데이터베이스 연결(firestore), useHistroy, navigation, signOut, GoogleAuthProvider, Nomadcoder  " #제목
category : #카테고리
tag : #태그
    - React
    - Firebase
    - 트위터 클론
    - Nomadcoder
    - google로그인
    - github로그인
    - 로그아웃
    - 데이터베이스 연결(firestore)
    - useHistroy
    - navigation
    - signOut
    - GoogleAuthProvider
toc : true #옆에 목차
---

># Nomadcoder

- [노마드코더 : 트위터 클론 수업](https://nomadcoders.co/nwitter)
- 노마드코더님의 강의를 들으면서 공부한 내용을 정리하는 용도로 작성되었으며, 본내용이 틀릴 수 도 있습니다.

<br>

># Social Login

- [signinwithpopup](https://firebase.google.com/docs/reference/js/firebase.auth.Auth#signinwithpopup)

- [signinwithredirect](https://firebase.google.com/docs/reference/js/firebase.auth.Auth#signinwithredirect)

- [GoogleAuthProvider](https://firebase.google.com/docs/reference/js/firebase.auth.GoogleAuthProvider)

- 소셜 로그인 기능을 사용하기 위해서는 `signinwithpopup` 또는 `signinwithredirect`를 활용 할 수 있다.

<br>

## Auth : Social Login

- 해당 Social Login 버튼을 눌렀을 때 처리 할 `onSocialClick` 함수를 만든다.
  - 로그인을 하려면 `signinwithpopup`에 해당 provider를 넣어 처리 한다.
  - provider에는 google, facebook, github 등이 존재하는데 전에 firebase 콘솔에서 사용자 받는 것을 구글, 깃허브로 해두었기 때문에 GoogleAuthProvider와 GithubAuthProvider를 사용할 것임
  - 그러면 해당 버튼 별로 provider를 구별해 주기 위해서 해당 이름의 버튼 name 속성을 넣어 준다. (google, github) 그리고 onclick 속성을 주어 onSocialClick을 연결 시켜 event를 받아 온다.
  - `onSocialClick` 함수에서는 해당 버튼의 event를 받아와서 `name`을 가져오고 name에 따라서 provider를 다르게 받기 위해서 if 문을 통해서 통제한다. (provider 변수는 가변적이기 때문에 let으로 정의함)
  - 그리고 provider가 결정 되면 `onSocialClick`에 provider를 넣어 처리시키고 `data` 변수에 가져온다. data를 가져올 때는 `await`를 사용해서 동기화 시켜서 data에 할당이 끝날때 까지 기다린다. 

``` js
import { authService, firebaseInstance } from 'fBase';
import React, { useState } from 'react';

const Auth = () => {
    const [email, setEmail] = useState("");
    const [password, setPassword] = useState("");
    const [newAccount, setNewAccount] = useState(true);
    const [error, setError] = useState("");
    const onChange = (event) => {
        const {target: {name, value}} = event;
        if(name === "email") {
            setEmail(value)
        } else if (name === "password") {
            setPassword(value)
        }
    }
    const onSubmit = async (event) => {
        event.preventDefault();
        try {
            let data;
            if(newAccount) {
                data = await authService.createUserWithEmailAndPassword(email, password);
            } else {
                data = await authService.signInWithEmailAndPassword(email, password);
            }
            console.log(data);
        } catch(error) {
            setError(error.message);
        }
    };
    const toggleAccount = () => setNewAccount((prev) => !prev);

    // onSocialClick 함수 : 소셜 로그인 처리 시키는 함수 (async 비동기 함수)
    const onSocialClick = async (event) => {
        const {target: {name}} = event;
        let provider;
        if (name === "google") {
          provider = new firebaseInstance.auth.GoogleAuthProvider();
        } else if (name === "github") {
            provider = new firebaseInstance.auth.GithubAuthProvider();
        }
        const data = await authService.signInWithPopup(provider);
        console.log(data);
    };  

    return (
    <div>
        <form onSubmit={onSubmit}>
            <input name="email" type="email" placeholder="Email" required value={email} onChange={onChange}/>
            <input name="password" type="password" placeholder="Password" required value={password} onChange={onChange}/>
            <input type="submit" value={newAccount ? "Create Account" : "Sign In"} />
            {error}
        </form>
        <span onClick={toggleAccount}>{newAccount ? "Sign In" : "Create Account"}</span>
        <div>
            <button onClick={onSocialClick} name="google">Continue with Google</button>
            <button onClick={onSocialClick} name="github">Continue with Github</button>
        </div>
    </div>
    );
}
export default Auth ;
```

<br>
<br>
<br>

># Log Out

- navigation : 홈으로 가기, 내 프로필로 가기 등,, 
- redirect가 필요함 react-router-dom에서 Redirect 또는 usehistory를 사용할 수 있음

<br>
<br>
<br>

## Router

- 로그인 되었을 때 profile component가 표시 됨
- 그런데 component가 표시되고 button을 눌러서 로그아웃이 되어도 home으로 돌아가지 않기 때문에 JSX부분에서 `react-router-dom : Redirect`를 사용해서 새로고침시 홈으로 갈수 있게 만듦
- 또는 profile component에서 `useHistory`를 사용하여 홈으로 가기를 구현 할 수도 있다.

``` js
import React from 'react';
import {HashRouter as Router, Redirect, Route, Switch} from "react-router-dom";
import Auth from 'routes/Auth'; 
import Home from 'routes/Home';
import Profile from 'routes/Profile';
import Navigation from 'components/Navigation';

const AppRouter = ({isLoggedIn}) => {
    return (
        <Router>
            {isLoggedIn && <Navigation />}
            <Switch>
                {isLoggedIn ? (
                    <>
                    <Route exact path="/">
                        <Home />
                    </Route>
                    <Route exact path="/profile">
                        <Profile />
                    </Route>
                    <Redirect from="*" to="/" />
                    </> 
                ) : (
                    <>
                    <Route exact path="/">
                        <Auth />
                    </Route>
                    <Redirect from="*" to="/" />
                    </>
                )}
            </Switch>
        </Router>
    )
}

export default AppRouter; 
```

<br>
<br>
<br>

## Profile

- profile 선택해서 들어 가는 경우 onLogOutClick 함수를 실행하게 함
  - `onLogOutClick` 함수의 경우 `signOut`을 사용해서 로그아웃이 가능하다. 그래서 profile component는 log out 버튼을 표시하고 클릭하는 경우 onLogOutClick 함수를 실행해서 로그아웃 됨
  - 그런데 log out시에 다시 홈으로 돌아가지 않기 때문에 `useHistory`를 사용해서 로그아웃시에 홈으로 가게 함 (`react-router-dom : Hook` 사용시) 

``` js
import { authService } from 'fBase';
import React from 'react';
import { useHistory } from 'react-router-dom';

const Profile = () => {
    const history = useHistory();
    const onLogOutClick = () => {
        authService.signOut();
        history.push("/");
    }
    return (
    <>
        <button onClick={onLogOutClick}>Log Out</button>
    </>
    )
};
export default Profile;
```

<br>
<br>
<br>

># Form and Database Setup

- 본격적인 트윗을 구현할 예정

## Home Form

- form을 만들어 트윗 할 수 있는 input을 만듦
- 전체적인 코드는 일반적인 useState를 이용한 코드들임
- 기억해야 할 것은 `useState`, `onSubmit`, `onChnage` 를 사용한다는 것

``` js
import React, { useState } from 'react';

const Home = () => {
    const [rweet, setRweet] = useState("");
    const onSubmit = (event) => {
        event.preventDefault();
    };
    const onChange = (event) => {
        const { target: {value}} = event;
        setRweet(value);
    };   
    return (
    <div>
        <form onSubmit={onSubmit}>
            <input value={rweet} onChange={onChange} type="text" placeholder="What's on your mind?" maxLength={120} />
            <input type="submit" value="Rweet" />
        </form>
    </div>
    ); 
}; 
export default Home; 
```

<br>
<br>
<br>

## Database

- firebase의 Database를 붙일려면 Firebase 콘솔에서 Firestore Database에 들어가서 Database를 만들면 됨(현재는 test 모드로 개설함)

- Cloud Firebase의 경우 NoSQL database로서 많은 것들을 프로그램 할 필요 없이 유연하지만 많은 제한들이 있다.
- SQL database의 경우에는 많은 규칙과 많은 관습들이 있지만 자유도가 높음

<br>

- NoSQL database : Collection(기본적인 폴더와 같음), Document(문서 같은 텍스트 임)이 존재함 (database안에는 collection들이 있고 collection안에는 Document 들이 있음)

- Database > Collections > Documents

- [Firestore](https://firebase.google.com/docs/reference/js/firebase.firestore.Firestore)

<br>
<br>
<br>

## database 사용을 위한 import (fbase.js)

``` js
import "firebase/firestore";
export const dbService = firebase.firestore();
```

<br>
<br>
<br>

## firestore 활용

- [firestore_collection](https://firebase.google.com/docs/reference/js/firebase.firestore.Firestore#collection)

  - `collection ( collectionPath :  string ) : CollectionReference < DocumentData >`
  - 컬렉션 경로를 받아서 [CollectionReference](https://firebase.google.com/docs/reference/js/firebase.firestore.CollectionReference)를 return
    - CollectionReference는 Object로서 documents를 추가하는데 사용 되어짐
    - [add](https://firebase.google.com/docs/reference/js/firebase.firestore.CollectionReference#add) Method를 통해서 해당 collection에 data를 가지고 새로운 document를 추가함

<br>

- onSubmit 실행시 database에 데이터를 추가함
- collection이름이 reweets이고 add를 통해서 필드와 데이터가 추가됨 여기에서 document이름은 정해지지 않아서 자동으로 id코드로 정해짐 
- 필드 reweet - 데이터 rweet useState / 필드 createdAt - 데이터 Date.now()  

``` js
import { dbService } from 'fBase';
import React, { useState } from 'react';

const Home = () => {
    const [rweet, setRweet] = useState("");
    
    const onSubmit = async (event) => {
        event.preventDefault();
        await dbService.collection("rweets").add({ // add가 promise를 return
            rweet,
            createdAt: Date.now(),
        })
        setRweet("");
    };
    const onChange = (event) => {
        const { target: {value}} = event;
        setRweet(value);
    };   
    return (
    <div>
        <form onSubmit={onSubmit}>
            <input value={rweet} onChange={onChange} type="text" placeholder="What's on your mind?" maxLength={120} />
            <input type="submit" value="Rweet" />
        </form>
    </div>
    ); 
}; 

    
export default Home; 
```

- 심지어 실시간 데이터 베이스 이기 때문에 바로 추가 됨!



