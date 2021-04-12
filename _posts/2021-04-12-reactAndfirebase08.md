---
title: "20210412 React and Firebase08, firebase storage file 지우기(delete, refFromURL), profile 수정하기, profile로 내가 쓴 글만 보기(where, orderBy), profile 이름 바꾸기(user, displayName, updateProfile), 객체 커스텀(object resizing), Cleaning Code, Nomadcoder  " #제목
category: #카테고리
tag: #태그
  - React
  - Firebase
  - 트위터 클론
  - Nomadcoder
  - storage file 지우기
  - refFromURL
  - Profile 수정하기
  - Object resizing
  - Cleaning code
  - displayName
  - updateProfile
  - where
  - orderBy
toc: true #옆에 목차
---

> # Nomadcoder

- [노마드코더 : 트위터 클론 수업](https://nomadcoders.co/nwitter)
- 노마드코더님의 강의를 들으면서 공부한 내용을 정리하는 용도로 작성되었으며, 본내용이 틀릴 수 도 있습니다.

<br>

> # Deleting Files

- [Reference : delete()](https://firebase.google.com/docs/reference/js/firebase.storage.Reference#delete)
  - `delete ( ) : Promise < any >` : 현재 reference 위치의 object를 제거함 **(즉, 해당 reference를 제거할려면 reference location을 알아야함)**

<br>

- 결국에는 document, collection 과 같이 video img와 같은 파일을 다루게 되어 정보를 저장하는 경우에는 reference location이 항상 필요하게 되고, 우리는 현재 storage에 저장되어 있는 파일의 url을 document에 가지고 있으므로 이를 활용하여 reference location을 얻어야함

<br>

- [firebase storage : methods](https://firebase.google.com/docs/reference/js/firebase.storage.Storage)
  - [refFromURL](https://firebase.google.com/docs/reference/js/firebase.storage.Storage#reffromurl)
  - `refFromURL ( url : string ) : Reference` : 해당 파일의 절대주소의 URL을 넣어주면 해당 파일의 reference를 return **(reference location을 얻을 수 있음)**

```js
const onDeleteClick = async () => {
  const ok = window.confirm("Are you sure you want to delete this rweet?");
  if (ok) {
    await dbService.doc(`rweets/${rweetObj.id}`).delete();
    await storageService.refFromURL(rweetObj.attachmentUrl).delete();
  }
};
```

<br>
<br>
<br>

> # Edit Profile

## Get my Own Rweets

- 자신의 트윗만 모아서 profile에서 보고자 하는 경우
  - firestore db에서 해당 data를 가져오는데 자신의 것이어야 하므로 Auth에서 받은 user의 id와 data안에 있는 craetorId를 비교하여 필터링 하여 가져와야 한다.
  - 그러므로 user를 router에서 받아와야 함 (router -> profile)
  - firestore에서 data를 필터링 하는 경우에는 `where`을 사용함
  - 순서를 가지게 하여 보여주고 싶으면 `orderBy`를 활용함

<br>

- [collection : where](https://firebase.google.com/docs/reference/js/firebase.firestore.CollectionReference#where) : field 경로(data의 field), filterOption(비교 연산자), value(data와 비교할 값)를 인자로 받아서 filtering 하여 Query를 return
- [collection : orderBy](https://firebase.google.com/docs/reference/js/firebase.firestore.CollectionReference#orderby) : field 경로(정렬되어질 필드), 정렬스타일(asc, desc) 을 받아서 순서를 정해줌 (중요한 것은, firebase는 색인 작업이 필요함 순서변경시, console에 뜨는 url로 들어가서 색인 추가를 해줘야 함)

```js
// Profile Component (Get my Own Rweets)
const getMyRweets = async () => {
  const rweets = await dbService
    .collection("rweets")
    .where("creatorId", "==", userObj.uid)
    .orderBy("createdAt")
    .get();
  console.log(rweets.docs.map((doc) => doc.data()));
};

useEffect(() => {
  getMyRweets();
}, []);
```

<br>
<br>
<br>

## Update Profile

### 1) navigation인 My Profile 링크를 사용자 이름으로 보이게 하기

- 여기에서도 user를 가져와서 firebase에서 제공하는 여러 정보, 기능을 사용할 수 있음
- [Auth : User](https://firebase.google.com/docs/reference/js/firebase.User) : user의 계정 관련해서 데이터에 접근 가능함
- `user : displayName` 사용

```js
// Navigation Component (for displaying user name at a profile link)
import React from "react";
import { Link } from "react-router-dom";

const Navigation = ({ userObj }) => (
  <nav>
    <ul>
      <li>
        <Link to="/">Home</Link>
      </li>
      <li>
        <Link to="/profile">{userObj.displayName}'s Profile</Link>
      </li>
    </ul>
  </nav>
);
export default Navigation;
```

<br>

### 2) Profile에서 사용자 이름 수정하기

- 사용자 이름을 수정할 수 있게 함
  - 수정값을 받을 form을 만들어야 함
  - user 의 정보를 수정할 수 있게 하는 firestore의 method는 `updateProfile`이 있다.

<br>

- [User : updateProfile](https://firebase.google.com/docs/reference/js/firebase.User#updateprofile) : user display name과 photo url 딱 두개만 변경 가능함(한계 발생)

#### Hook

```js
// Profile component (for Editing a name of my profile)
import { authService, dbService } from 'fBase';
import React, { useEffect, useState } from 'react';
import { useHistory } from 'react-router-dom';

const Profile = ({ userObj, refreshUser }) => { // refreshUser 함수 가져옴 (app component에서 사용자정보를 hook에 반영시키기 위해서)
	const history = useHistory();
  // 수정갑 저장하는 hook
	const [newDisplayName, setNewDisplayName] = useState(userObj.displayName);
```

<br>

#### Methods

- `updateProfile` 함수를 이용해서 firebase auth의 user를 변경함
- App component에서 Auth user 정보를 가지고 있는 hook인 `userObj`에 App component에서 정의된 `refreshUser` 함수를 통해서 수정된 현재 firebase의 Auth user정보로 반영시켜줌 (새로고침 없이 화면으로 반영되어 표시됨)

```js
// 입력값 변동 시 hook 값 변경
const onChange = (event) => {
  const {
    target: { value },
  } = event;
  setNewDisplayName(value);
};

// 제출시 userObj를 통해서 firebase user를 update함
const onSubmit = async (event) => {
  event.preventDefault();
  if (userObj.displayName !== newDisplayName) {
    await userObj.updateProfile({
      displayName: newDisplayName,
    });
  }
  // hook에도 반영 시켜주어야 바로 표시가 됨 (새로고침 없이)
  refreshUser();
};
```

<br>

#### JSX

```js
return (
  <>
    <form onSubmit={onSubmit}>
      <input
        type="text"
        placeholder="Display name"
        onChange={onChange}
        value={newDisplayName}
      />
      <input type="submit" value="Update Profile" />
    </form>
    <button onClick={onLogOutClick}>Log Out</button>
  </>
);
// };
// export default Profile;
```

<br>
<br>
<br>

> ## 🖐 Resizing Object
>
> **Auth의 user 객체의 경우 사이즈가 엄청 크고 많은 정보를 담고 있기 때문에 작은 하나가 바뀐다고 해서 react에서 인식을 못할 수도 있음, 그래서 hook에 resizing을 하여 필요한 정보만 뽑아 담은 객체로 만들어줘야 함**

### 방법 01

- 사용하는 함수와 값만 넣어서 resizing

```js
const [userObj, setUserObj] = useState(null);

useEffect(() => {
  authService.onAuthStateChanged((user) => {
    if (user) {
      // Resizing Object
      setUserObj({
        displayName: user.displayName,
        uid: user.uid,
        updateProfile: (args) => user.updateProfile(args),
      });
    }
    setInit(true);
  });
}, []);

// method
const refreshUser = () => {
  const user = authService.currentUser;
  setUserObj({
    displayName: user.displayName,
    uid: user.uid,
    updateProfile: (args) => user.updateProfile(args),
  });
};
```

<br>

### 방법02

- react가 값이 변했다는 것을 인지시키기 위해서 완전 새로운 객체를 만들어서 resizing 하지 않고 받아와서 사용
- 그런데 여러번 사용할 때는 인식하지 못하므로 사용하지 말고 01번 방법을 사용하자

```js
const [userObj, setUserObj] = useState(null);

useEffect(() => {
  authService.onAuthStateChanged((user) => {
    if (user) {
      setUserObj(user);
    }
    setInit(true);
  });
}, []);

// method
const refreshUser = () => {
  const user = authService.currentUser;
  setUserObj(Object.assign({}, user));
};
```

<br>
<br>
<br>

> # Cleaning JS

- 복잡도를 낮추고 각 component의 특징별로 전문성을 더하고, 재사용을 편리하게 하기 위해서 cleaning을 진행함

## 분리하여 만든 Component

- AuthForm component (Auth의 Form 부분)

- RweetFactory component (home component에서 Rweet을 만드는 Form 부분)

- Component를 만들어 붙여넣기로 옮기고 돌려서 browser의 console이나, 그냥 페이지에서 잘못된 부분을 확인하여 바로 잡기 쉬움

<br>

## Home Component (After Cleaning)

```js
import Rweet from "components/Rweet";
import { dbService } from "fBase";
import React, { useEffect, useState } from "react";
import RweetFactory from "components/RweetFactory";

const Home = ({ userObj }) => {
  const [rweets, setRweets] = useState([]);

  useEffect(() => {
    dbService.collection("rweets").onSnapshot((snapshot) => {
      const rweetArray = snapshot.docs.map((doc) => ({
        id: doc.id,
        ...doc.data(),
      }));
      setRweets(rweetArray);
    });
  }, []);

  return (
    <div>
      <RweetFactory userObj={userObj} />
      <div>
        {rweets.map((rweet) => (
          <Rweet
            key={rweet.id}
            rweetObj={rweet}
            isOwner={rweet.creatorId === userObj.uid}
          />
        ))}
      </div>
    </div>
  );
};

export default Home;
```

<br>

## Auth Component (After Cleaning)

```js
import AuthForm from "components/AuthForm";
import { authService, firebaseInstance } from "fBase";
import React from "react";

const Auth = () => {
  const onSocialClick = async (event) => {
    const {
      target: { name },
    } = event;
    let provider;
    if (name === "google") {
      provider = new firebaseInstance.auth.GoogleAuthProvider();
    } else if (name === "github") {
      provider = new firebaseInstance.auth.GithubAuthProvider();
    }
    await authService.signInWithPopup(provider);
  };

  return (
    <div>
      <AuthForm />
      <div>
        <button onClick={onSocialClick} name="google">
          Continue with Google
        </button>
        <button onClick={onSocialClick} name="github">
          Continue with Github
        </button>
      </div>
    </div>
  );
};
export default Auth;
```

> # 다음에 할 것

- error 수정 : 트윗후 profile 갔다가 다시 home와서 트윗하면 console에 error 뜸

- `Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.`

- [참고할 사이트](https://kyounghwan01.github.io/blog/React/cant-perform-a-React-state-update-on-an-unmounted-component/#%E1%84%86%E1%85%AE%E1%86%AB%E1%84%8C%E1%85%A6-%E1%84%8F%E1%85%A9%E1%84%83%E1%85%B3)

- CSS, 기능 수정, Cleaning Code
