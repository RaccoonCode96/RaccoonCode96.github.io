---
title: "20210408 React and Firebase06, 작성한 트윗 삭제 및 수정(Delete and Update), 데이터베이스 반영, doc(), delete(), update(), useState 토글 활용, Nomadcoder  " #제목
category: #카테고리
tag: #태그
  - React
  - Firebase
  - 트위터 클론
  - Nomadcoder
  - 작성한 트윗 삭제 및 수정
  - Delete and Update
  - doc()
  - delete()
  - update()
  - useState 토글
toc: true #옆에 목차
---

> # Nomadcoder

- [노마드코더 : 트위터 클론 수업](https://nomadcoders.co/nwitter)
- 노마드코더님의 강의를 들으면서 공부한 내용을 정리하는 용도로 작성되었으며, 본내용이 틀릴 수 도 있습니다.

<br>

> # Delete and Update

- 지금 까지 진행했던 실시간 데이터 update는 정말 좋은 기능이지만 원하지 않을 때도 너무 실시간으로 실행이 됨
- 예를 들어, 트위터에서 누군가가 추가시키고 지우는게 나타나고, 댓글들이 생성되는 것은 보고 싶지만 실시간으로 수정되고 사라지는 모습은 사용자에게 불편함(물론, 실시간 채팅 앱에서는 필요하지만)

<br>

## 트윗 Component 분리와 사용자와 작성자 일치에 따른 delete, edit 버튼 표시

### Rweet Component 생성(분리 관리)

- 효율적인 관리를 위해서 트윗을 하는 부분을 따로 component화 시킬 필요가 있다.
- 그래서 Rweet이라는 component를 만들 었고, 이 component는 Home component의 `rweetObj(트윗되는 문서 객체)`와 home component에서 사용자와 작성자가 일치하는지(`isOwner`)에 대한 bool값을 받아온다.
- 그래서 받아온 트윗은 h4 태그에 text 필드 값을 넣어 준다
- 그리고 isOwner를 버튼과 and 연산으로 만들어 true일때 표시, false일때 표시가 안되게 한다. (**논리 연산을 통한 표시 활용**)

```js
// Rweet.js
import React from "react";

const Rweet = ({ rweetObj, isOwner }) => (
  <div>
    <h4>{rweetObj.text}</h4>
    {isOwner && (
      <>
        <button>Delete Nweet</button>
        <button>Edit Nweet</button>
      </>
    )}
  </div>
);

export default Rweet;
```

<br>

### Home Component에 연결

- 트윗 되는 부분에 Rweet component를 연결시키고 key 값을 넣어주고(react에서 권장하는 것임), rweetObj에 문서 객체인 rweet 값, isOwner에 rweet의 creatorId 필드값과 home에서 현재 사용자 정보를 받아오는 인자인 userObj의 uid와 비교시켜 bool 값을 Rweet component에 보내준다.

```js
// Home.js의 JSX 부분

return (
  <div>
    <form onSubmit={onSubmit}>
      <input
        value={rweet}
        onChange={onChange}
        type="text"
        placeholder="What's on your mind?"
        maxLength={120}
      />
      <input type="submit" value="Rweet" />
    </form>
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
```

<br>

> ## 🖐 주의
>
> - **function 사용시 function 바로 '()' 사용시 return 부분을 의미함 그래서 JSX를 넣어줄때 사용해야 함.**
> - **그냥 '{}'사용해서 JSX넣으면 안됨 '{}' 부분은 js 기능 이라고 생각해야함 함수 선언시 '()','{}' 사용에 대해서 주의해야 함**

<br>
<br>
<br>

## Delete 버튼 database와 연동하기

- delete 버튼이 눌려서 onClick event가 발생하면 onDeletClick 함수가 호출 된다.
- `onDeleteClick` 함수는 먼저 `confirm` 알림창을 먼저 띄워 지움에 대한 true, false값을 ok 변수에 받아온다.
  - (`confirm` vs `alert` : alert은 ok 버튼만 있고 comfirm은 ok, cancel 버튼이 있음 / 즉, 사용자와의 Interaction 차이임)
- 그래서 if 문으로 ok가 true 이면 db(firebase의 firestore interface를 통해 받은 firestore class)의 `doc` method 를 통해서 바로 document reference class를 return 받아 해당 class의 `delete()` method로 삭제함

<br>

- [**<span style="color: blue">doc ( documentPath : string ) : DocumentReference < DocumentData ></span>**](https://firebase.google.com/docs/reference/js/firebase.firestore.Firestore#doc) : firestore에서의 document path를 넣어 해당 document reference를 return
- [**<span style="color: blue">DocumentReference</span>**](https://firebase.google.com/docs/reference/js/firebase.firestore.DocumentReference) : delete, get, set, update 등의 method들로 firebase의 database에 접근하여 Document를 처리할 수 있음
- [**<span style="color: blue">delete ( ) : Promise < void ></span>**](https://firebase.google.com/docs/reference/js/firebase.firestore.DocumentReference#delete) : backend firebase database에서 Document를 삭제 함

```js
import { dbService } from "fBase";
import React, { useState } from "react";

const Rweet = ({ rweetObj, isOwner }) => {
  const [editing, setEditing] = useState(false);
  const [newRweet, setNewRweet] = useState(rweetObj.text);

  const onDeleteClick = async () => {
    const ok = window.confirm("Are you sure you want to delete this rweet?");
    if (ok) {
      await dbService.doc(`rweets/${rweetObj.id}`).delete(); // 반영할 글 지정하기 위해서 rweetObj.id 사용
    }
  };
  return (
    <div>
      <h4>{rweetObj.text}</h4>
      {isOwner && (
        <>
          <button onClick={onDeleteClick}>Delete Nweet</button>
          <button>Edit Nweet</button>
        </>
      )}
    </div>
  );
};

export default Rweet;
```

<br>
<br>
<br>

## Update 버튼 database와 연동하기

### Hooks

- `editing`, `newRweet` 두가지 hook 사용
- `editing`을 통해서 해당 트윗의 수정버튼을 눌렀을 때 edit 관련 form이 대체되게하는 기준 값임(bool)
- `newRweet`을 통해서 수정 input에 표시될 값으로 원래 트윗 text 또는 현재 수정하고 있는 값으로 사용가능하고, 나중에 submit을 통해서 database에 반영될 값이기도 함
  - 초기 값을 `rweetObj.text`로 해놓았기 때문에 누르면 원래 쓰여져 있던 값을 고대로 보여지면서 수정 가능함

```js
import { dbService } from "fBase";
import React, {useState} from "react";

const Rweet = ({rweetObj, isOwner}) => {
    // Hooks
        // Editing 모드 Hook
    const [editing, setEditing] = useState(false);
        // 수정할 값 Hook
    const [newRweet, setNewRweet] = useState(rweetObj.text)
```

<br>

### Methods

- `toggleEditing` method는 수정할 수 있는 form을 보이거나 아니면 숨기는 값인 editing hook의 상태를 역으로 변경해 줌
- `onChange` method는 수정하는 form input을 통해서 넣은 입력 값에 대한 event의 value를 즉각적으로 `setNewRweet`을 통해 값을 세팅 해줌
- `onSubmit` method는 submit 에 대한 event 발생시 database에 `newRweet` hook 값을 반영함
  - delete 처럼 doc의 `update()` method 를 통해서 반영
  - [**<span style="color: blue">update ( data : UpdateData ) : Promise < void ></span>**](https://firebase.google.com/docs/reference/js/firebase.firestore.DocumentReference#update) : update할 data를 인자로 받아서 반영

```js
// Methods
// 트윗 지우기 기능
const onDeleteClick = async () => {
  const ok = window.confirm("Are you sure you want to delete this rweet?");
  if (ok) {
    await dbService.doc(`rweets/${rweetObj.id}`).delete();
  }
};

// Editing 모드 값 변경 기능(화면 구성을 조건부로 하기 위해서)
const toggleEditing = () => {
  setEditing((prev) => !prev);
};

// 수정할 rweet database에 반영 기능
const onSubmit = async (event) => {
  event.preventDefault();
  if (newRweet !== rweetObj.text) {
    await dbService.doc(`rweets/${rweetObj.id}`).update({
      text: newRweet,
    });
  }
  setEditing(false);
};

// 수정 할 input 태그의 값 태그에 반영하는 기능
const onChange = (event) => {
  const {
    target: { value },
  } = event;
  setNewRweet(value);
};
```

<br>

### JSX

- `editing` 값을 통해 삼항 연산자로 보여질 태그들을 나눔 (false: 원래 트윗이 표현되는 태그들 / true : 수정 form 표시로 교체)
- `Edit Nweet` 버튼에 `editing` 값을 변경 시키는 `toggleEditing` method를 `onClick` event로 연결
- 수정 값을 받을 input 태그는 `value`로 `newRweet` hook으로 연결하여 newRweet 초기값 또는 변경되는 값들을 input태그에 반영하여 보여질수 있도록 함
- 그리고 input태그에 `onChange` event를 `onChange` method로 연결하여 쓰여지는 값들을 받아 바로 newRweet에 반영할수 있도록 하여 input태그가 쓰여지는 값이 바로 표시됨
- form 태그에 `onSubmit` event로 `onSubmit` method를 연결하여 database에 반영
- form 밖에 cancel 버튼을 만들어 `toggleEditing`을 연결하여 false로 만들어 취소 가능하게 함

```js
    return (
        <div>
            {
                editing ? (
                <>
                <form onSubmit={onSubmit}>
                    <input type="text" placeholder="Edit your Rweet" value={newRweet} required onChange={onChange} />
                    <input type="submit" value="Update Rweet" />
                </form>
                <button onClick={toggleEditing}>Cancel</button>
                </>
                ) : (
                    <><h4>{rweetObj.text}</h4>
                {isOwner && (
                    <>
                    <button onClick={onDeleteClick}>Delete Nweet</button>
                    <button onClick={toggleEditing}>Edit Nweet</button>
                    </>
                )}
                </>
                )
            }
        </div>
    );
}

export default Rweet;
```
