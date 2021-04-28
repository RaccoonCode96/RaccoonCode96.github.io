---
title: "20210428 instagram 만들기 15(React), proifle 반영 기능 개선(promise.all, 다중 await), 연속 submit 막기, firebase google로 계정 시작 시 google user 정보 가져오기" #제목
category: #카테고리
tag: #태그
  - React
  - Firebase
  - instagram CSS
  - promise.all
  - 다중 await
  - 연속 submit 막기
  - google user profile 가져오기
toc: true #옆에 목차
---

> # proifle 반영 기능 개선

## 문제점

- 병렬처리를 하였지만 비효율적으로 사용하고 있음 (동시에 두가지 photo , display update시 모두 변경시 속도지연과 setUpdate함수 호출의 순서 때문에 rerender시에 반영이 안되게 보이는 것들이 있음)
- forEach의 경우 병렬 처리하여 안에서 await로 순서처리 시켜도 넘어가 버림 그래서 setUpdated를 너무 빨리 하는 문제가 생김
- 중복되는 코드가 많아짐
- Rerendering 타이밍 문제, 병렬 효율적으로 사용하기

<br>

## 참고한 사이트

- [[번역] async/await 를 사용하기 전에 promise를 이해하기 by kiwanjung](https://kiwanjung.medium.com/%EB%B2%88%EC%97%AD-async-await-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-%EC%A0%84%EC%97%90-promise%EB%A5%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-955dbac2c4a4)

- [MDN : promise.all()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

- [배열에 비동기 작업을 실시할 때 알아두면 좋은 이야기들 by hanameee](https://velog.io/@hanameee/%EB%B0%B0%EC%97%B4%EC%97%90-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%9E%91%EC%97%85%EC%9D%84-%EC%8B%A4%EC%8B%9C%ED%95%A0-%EB%95%8C-%EC%95%8C%EC%95%84%EB%91%90%EB%A9%B4-%EC%A2%8B%EC%9D%84%EB%B2%95%ED%95%9C-%EC%9D%B4%EC%95%BC%EA%B8%B0%EB%93%A4)

<br>

## 방법

- 비동기적으로 구동시키고 모든 작업이 끝났을 때 `promise.all` 을 사용하여 Rerender 시키는 함수를 실행하도록 순서를 만들어준다.
- submit 함수에서 비동기적으로 구현해야할 사항은 두 가지로 photoURL, displayName를 firebase Auth user에 반영시키고 글에도 반영시키는 작업을 하는데, photoURL만 하는 경우도 있고, displayName만 하는 경우 또는 둘다 하는 경우가 있기 때문에 즉, 비동기적으로 `or` 관계의 기능을 구현하고자 하는데 두작업의 완료 속도는 모두 다르기에 두기능을 병렬적으로 처리할때에는 두가지가 모두 마쳐야 비로소 실행되어야 할 일이 있기 때문에 **다수의 값을 기다리는 `promise.all`을 통해 관리한다.**
- 또한, 이전의 `forEach` loop에 따른 `await 무시 현상`을 방지하기 위해서 `map`을 사용했다.

```js
// submit 요청 함수
const onSubmit = async (event) => {
  event.preventDefault();
  updatePhoto();
  updateDisplayName();
  await Promise.all([updatePhoto(), updateDisplayName()]);
  setAttachment("");
  refreshUser();
  setOnProfile(false);
  if (isProfile) {
    setUpdated(Date.now());
  }
};

// PhotoURL update 및 반영 함수
const updatePhoto = async () => {
  if (attachment !== "") {
    let attachmentUrl = "";
    const attachmentRef = storageService
      .ref()
      .child(`${userObj.uid}/${uuidv4()}`);
    const response = await attachmentRef.putString(attachment, "data_url");
    attachmentUrl = await response.ref.getDownloadURL();
    await userObj.updateProfile({
      photoURL: attachmentUrl,
    });
    const update = rweets.map(async (rweet) => {
      dbService.doc(`rweets/${rweet.id}`).update({
        photoUrl: attachmentUrl,
      });
    });
    await Promise.all(update);
  }
};

// displayName update 및 반영 함수
const updateDisplayName = async () => {
  if (userObj.displayName !== newDisplayName) {
    await userObj.updateProfile({
      displayName: newDisplayName,
    });
    const update = rweets.map(async (rweet) => {
      dbService.doc(`rweets/${rweet.id}`).update({
        displayName: newDisplayName,
      });
    });
    await Promise.all(update);
  }
};
```

<br>
<br>
<br>

> # 다중 submit 막기 (Double Submit)

- 처리하는 양이 많은 경우 submit이 눌리면 모두 처리되지 않는 이상 계속 form 화면에 남아 있기 때문에 submit을 계속 눌러 요청을 할수 있게 됨 (낭비 초래할 수 있음)

```js
const [doubleSubmit, setDoubleSubmit] = useState(false);

const onSubmit = async (event) => {
  event.preventDefault();
  if (!doubleSubmit) {
    setDoubleSubmit(true); // 처리 시작
    let attachmentUrl = "";
    if (attachment !== "") {
      const attachmentRef = storageService
        .ref()
        .child(`${userObj.uid}/${uuidv4()}`);
      const response = await attachmentRef.putString(attachment, "data_url");
      attachmentUrl = await response.ref.getDownloadURL();
    }
    setAttachment("");
    const rweetObj = {
      text: rweet,
      createdAt: Date.now(),
      creatorId: userObj.uid,
      attachmentUrl,
      photoUrl: userObj.photoURL,
      displayName: userObj.displayName,
    };
    await dbService.collection("rweets").add(rweetObj);
    setRweet("");
    setDoubleSubmit(false); // 처리 끝
  }
};
```

<br>
<br>
<br>

> # firebase google로 계정 시작 시 google user 정보 가져오기

- 최초로 계정을 만드는 경우 user의 기본적인 profile인 display name과 user photo를 가져오지 못하게 됨
- 그래서 임시 방편으로 일단은 해당 정보가 없으면 default_portrait, name으로 지정하게 만들었음
- socail 로그인의 경우 보통 user photo, display name이 있기 때문에 그것을 가져와 사용할 수 있다.
- 하지만, 일반 email, login의 경우에는 기본 값이 없기 때문에 위에서 말한 것과 같이 default값이 필요하다.
  - 나중에는, 애초에 displayname은 적어도 작성시켜서 바로 가져오게 해야 할 것 같다. (photo의 경우에는 youtube처럼 별명이 박힌 임시 photo를 자동으로 생성하여 지정하게 해야겠다.)

```js
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
  await provider.addScope("profile"); // profile을 가져오는 것임
  await authService.signInWithPopup(provider);
};
```
