---
title: "20210427  instagram 만들기 14(React), Img 태그-OnError 처리, profile update시 글 작성자 표시 반영 구현" #제목
category: #카테고리
tag: #태그
  - React
  - Firebase
  - onError
  - instagram CSS
  - useRef
  - useState
  - profile 반영
toc: true #옆에 목차
---

> # Img Error 처리

- img를 가져오는 경우 보통 src로 img url 또는 server에 존재하는 경로를 연결하곤 한다.
- 이번 프로젝트의 경우에는, img를 Data URL 형식으로 가져오게 된다.
- 그러다 보니 URL이 옳지 못하거나, 더이상 web에 올려지지 않아 있는 경우 img를 로드 하지 못하고 흔히 말하는 엑박이 뜨게 되는 것이다. (alt 속성으로 지정한 이름이 뜸)

<br>

- 이 프로젝트의 경우 firebase의 storage에서 의도치 않게 img file이 지워져서 더이상 url을 load못하게되는 경우 어떻게 엑박을 안뜨게 하고 자연스럽게 user 화면을 표시할 것인가에 중점을 두고 있다.

<br>
<br>
<br>

> ## 방법

## img 태그의 onError 속성 사용

- HTML 에서는 `onerror` React(JSX)에서는 `onError` 속성으로 표기 된다.
- 말그대로 img를 load하는 과정에서 error가 발생하면 어떻게 할것인지를 표시하는 것이다.
- 보통은 default로 error 발생시 흔히 보이는 `엑박` 또는 `img icon과 alt 속성의 단어`가 보이게 된다.

## 방법01 : onError 를 활용한 style - display none 사용 (안보이게)

- onError를 이용하여 연결해주는 callback 함수를 통해 img tag의 dom에 접근하여 style 속성에 변화를 주는 것이다.
- react hook의 사용을 통해서 구현하면 `useRef` current.style을 통해서 안보이게 함

<br>

## 방법02 : onError 를 활용한 src - default img url (특정 default img load)

- 위와같은 방법으로 하되 `useRef`를 통해서 current.src를 통해서 default img load로 설정하게 함

<br>

## 방법03 : onError를 활용하여 useState 값을 변경시켜 img element를 없애고 다른 element로 대체

- `onError` 로 연결하는 함수를 img scr에 연결한 photoURL 값을 null 값으로 변경시키게 하는 함수로 지정
- `삼항 연산자`를 통해서 default icon 태그로 변경 시킴
- 변경하고 바로 반영되어 보이게 하려면 refreshUser함수를 통해서 State를 사용하는 element의 경우 reload하게 해줘야하 됨(userObj의 경우 state로 받아오고 있기 때문에(직접 사용X) state를 refresh 해줄 필요가 있음)

```js
// refreshUser가 있는 Component
const refreshUser = () => {
  const user = authService.currentUser;
  setUserObj({ // userObj 반영
    photoURL: user.photoURL,
    displayName: user.displayName,
    uid: user.uid,
    updateProfile: (args) => user.updateProfile(args),
  });
};

// 사용되어지는 Compoenet
const handleImgError = async (event) => {
  await userObj.updateProfile({
    photoURL: null,
  });
  refreshUser();
};

return (
  <>
  {
  userObj.photoURL ? (
    <img
      onError={handleImgError}
      className="nav_portrait"
      alt="user_photo"
      src={userObj.photoURL}
      width="27px"
      height="27px"
    />
  ) : (
    <FontAwesomeIcon className="nav_NoPortrait" icon={faUserCircle} size="2x" />
  );
}
  </>
)

```

<br>
<br>
<br>

> # 작성했던 글에 profile img update 반영시키기

- profile을 변경하여 submit 하는 경우 상위 component에서 현재 유저가 작성한 글만 가지고있는 rweets이라는 array를 보냄
- rweets Array를 forEach를 통해서 해당 작성글들 하나하나 profile과 관련된 변수인 photoURL과 displayName을 update 함
- setUpdated는 get방식의 profile 창을 위해서 reload 하기 위함이고, home component와 이 component를 공유하기 때문에 현재 url을 인식하는 isProfile 변수를 사용

```js
const onSubmit = async (event) => {
  event.preventDefault();
  let attachmentUrl = "";
  if (attachment !== "") {
    const attachmentRef = storageService
      .ref()
      .child(`${userObj.uid}/${uuidv4()}`);
    const response = await attachmentRef.putString(attachment, "data_url");
    attachmentUrl = await response.ref.getDownloadURL();
    await userObj.updateProfile({
      photoURL: attachmentUrl,
    });
    // 글에 profile 반영하는 부분
    rweets.forEach(async (rweet) => {
      await dbService.doc(`rweets/${rweet.id}`).update({
        photoUrl: attachmentUrl,
      });
    });
    //
    if (isProfile) {
      setUpdated(Date.now());
    }
  }
  setAttachment("");
  if (userObj.displayName !== newDisplayName) {
    await userObj.updateProfile({
      displayName: newDisplayName,
    });
    // 글에 profile 반영하는 부분
    rweets.forEach(async (rweet) => {
      await dbService.doc(`rweets/${rweet.id}`).update({
        displayName: newDisplayName,
      });
    });
    //
    if (isProfile) {
      setUpdated(Date.now());
    }
  }
  refreshUser();
  setOnProfile(false);
};
```

## 문제점

- 병렬처리를 하였지만 비효율적으로 사용하고 있음 (동시에 두가지 photo , display update시 모두 변경시 속도지연과 setUpdate함수 호출의 순서 때문에 rerender시에 반영이 안되게 보이는 것들이 있음)
- forEach의 경우 병렬 처리하여 안에서 await로 순서처리 시켜도 넘어가 버림 그래서 setUpdated를 너무 빨리 하는 문제가 생김
- 중복되는 코드가 많아짐
- Rerendering 타이밍 문제, 병렬 효율적으로 사용하기

[[번역] async/await 를 사용하기 전에 promise를 이해하기 by kiwanjung](https://kiwanjung.medium.com/%EB%B2%88%EC%97%AD-async-await-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-%EC%A0%84%EC%97%90-promise%EB%A5%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-955dbac2c4a4)

[MDN : promise.all()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

[배열에 비동기 작업을 실시할 때 알아두면 좋은 이야기들 by hanameee](https://velog.io/@hanameee/%EB%B0%B0%EC%97%B4%EC%97%90-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%9E%91%EC%97%85%EC%9D%84-%EC%8B%A4%EC%8B%9C%ED%95%A0-%EB%95%8C-%EC%95%8C%EC%95%84%EB%91%90%EB%A9%B4-%EC%A2%8B%EC%9D%84%EB%B2%95%ED%95%9C-%EC%9D%B4%EC%95%BC%EA%B8%B0%EB%93%A4)
