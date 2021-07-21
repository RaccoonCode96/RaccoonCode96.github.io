---
title: "20210719 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit08 : 전체적인 redux state 관리 구조 개편, common slice 생성" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit08

<br/>

<p align="center">
<img src="../assets/img/instagram_logo.png" width="400px" height="400px">
</p>

<br/>

> # 📄 프로젝트 설명

<br/>

이 프로젝트는 기존에 React & firebase를 통해서 만든 인스타그램 클론 프로젝트 리팩토링 프로젝트 입니다. (해당 프로젝트는 프로젝트 카테고리에서 확인 가능합니다.)

<br/>

해당 프로젝트에서는 `redux-toolkit(Slice 모델)`을 사용하여 상태관리를 구현하고 있습니다.

<br/>

더불어 나중에, styled component나 sass 중에 채택하여 css 작업을 할 예정 입니다.

<br/>
<br/>
<br/>

# 💻 화면 개요

<br/>

체크는 현재 기능적으로 구현된 상황을 의미합니다.

<br/>

- [x] `로딩 화면 또는 Component` : 앱 실행 초기화 작업시 로딩 또는 다른 작업시 사용할 로딩 화면 및 Component
- [x] `로그인 화면` : 기본 Email 로그인, Social 로그인, 로그인 에러
  - [x] `Email 로그인` : Email, Password input, 로그인 버튼
  - [x] `Social 로그인` : google로그인 버튼, github로그인 버튼
  - [x] `로그인 에러` : Email로그인, google로그인, github 로그인 에러 발생시 사용자에게 출력
- [x] `회원가입 화면` : Email 로그인을 위한 계정을 만드는 화면, 회원가입 에러
  - [x] `Email 형식 가입` : Email, Password input, 회원가입 버튼
    - [x] 가입시 사용자 Nickname 지정 input (추가 사항)
- [x] `피드 화면` : 사용 유저의 모든 게시글을 표시하는 화면
  - [x] `게시글 박스` :
    - [ ] `타이틀 영역` : 최상단의 작성자 사진 + 이름, 게시글 수정 탭
      - [ ] `편집버튼` : 글 수정하기, 삭제하기 모달 -> 해당 버튼 누르면 삭제 또는 수정 페이지로 이동(아니면 모달이 수정하는 모달로 변경)
        - [x] `삭제하기`
        - [x] `수정하기`
    - [x] `사진 영역` : 기존에는 1개만 가능했음 (욕심내면, 여러개 슬라이드 형식으로 가능하게 하고 싶음)
    - [x] `내용 영역` : 게시글 내용
- [x] `글 작성 화면` : 글을 작성하는 화면
- [ ] `현재 유저 프로필 화면` : 로그인한 현재 유저의 게시물과 대략적인 프로필를 표시하는 화면
- [ ] `유저 프로필 화면` : 유저의 게시물과 프로필을 확인 할수 있는 화면 (피드 게시글 작성자 유저 이름을 눌러 유저 프로필 화면으로 이동)
  - [x] `유저 프로필 수정하기` : 유저 프로필을 수정하는 화면
- [x] `네비게이션 바` : 앱로고 - 피드(Home)탭 - 글 작성탭 - 현재 유저 프로필(프로필 수정, 프로필 이동, 로그아웃) 탭
  - [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
  - [x] `로그아웃`

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.07.19 사항

<br/>

## 작업 개요

<br/>

- 전체적인 redux 상태 관리 구조 개편
  - 공통적으로 사용되는 것은 common state에서 관리
    - imageUrl을 가져오고 제거하는 thunk 관리
    - updateSelector 관리 (update시 profile인지 post인지 구분하는 값)
  - init, auth, post, profile, common 으로 구조 개편
  - 기존 init의 currentUser -> profile에서 관리
- 상태 관리 구조 개편에 따른 전체적인 참조 수정 작업

```js
state = {
  init: {
    isInit: false,
  },
  auth: {
    newAccount: false,
    errorSelector: "",
    emailSignUp: {
      isSignUp: false,
      loading: false,
      signUpError: "",
    },
    emailSignIn: {
      isSignIn: false,
      loading: false,
      signInError: "",
    },
    socialSignIn: {
      isSignIn: false,
      loading: false,
      signInError: "",
    },
  },
  post: {
    postList: [],
    postSelector: "",
    updatePost: {
      isUpdate: false,
      loading: false,
      updateError: "",
    },
    deletePost: {
      isDelete: false,
      loading: false,
      deleteError: "",
    },
    getPostList: {
      isGet: false,
      loading: false,
      getError: "",
    },
    setPostObj: {
      isSet: false,
      loading: false,
      setError: "",
    },
  },
  profile: {
    updateProfile: {
      loading: false,
      isUpdate: false,
      updateError: "",
    },
    currentUser: {
      isSignIn: false,
      photoURL: "",
      displayName: "",
      uid: "",
    },
    updateDisplayName: {
      isUpdate: false,
      loading: false,
      updateError: "",
    },
    updatePhotoUrl: {
      isUpdate: false,
      loading: false,
      updateError: "",
    },
  },
  common: {
    updateSelector: "",
    getImageUrl: {
      isGet: false,
      loading: false,
      getError: "",
      imageUrl: "",
    },
    deleteImageUrl: {
      loading: false,
      isDelete: false,
      deleteError: "",
    },
  },
};
```

<br/>

## 고민, 생각 및 깨달음

<br/>

- user의 displayName, photoUrl만 관리할 수 있는 **firebase의 한계 발생**으로 userInfo를 관리하는 users database 필요성을 깨달음
- 다른 유저의 페이지에 들어가 profile을 확인하는 구조를 구현하려면, users database가 필요함
- 비동기 작업에 대한 이해도가 조금 떨어짐, 더 깊은 공부가 필요함

<br/>

## 다음에 필요한 사항

<br/>

- [ ] user 검사를 실시하여 수정, 삭제 버튼 나타나지 않게 접근 막기
- [x] slice 모듈화하여 관리하기, 기능별로 재편성 필요함
  - [x] getImageUrl 위치 옮기기 공통 사용 데이터 쪽으로 올려야 함
- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
- [ ] Post에 글 수정 메뉴 버튼을 통한 모달 구현
- [ ] user profile info도 구현 필요
