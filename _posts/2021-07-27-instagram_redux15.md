---
title: "20210727 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit15 : 리덕스 store state 구조 개편, useSelector에 대한 렌더링에 대한 고민, history를 활용한 정보 전달" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit15

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

<br/>

- [x] `로그인 화면` : 기본 Email 로그인, Social 로그인, 로그인 에러
  - [x] `Email 로그인` : Email, Password input, 로그인 버튼
  - [x] `Social 로그인` : google로그인 버튼, github로그인 버튼
  - [x] `로그인 에러` : Email로그인, google로그인, github 로그인 에러 발생시 사용자에게 출력

<br/>

- [x] `회원가입 화면` : Email 로그인을 위한 계정을 만드는 화면, 회원가입 에러
  - [x] `Email 형식 가입` : Email, Password input, 회원가입 버튼
    - [x] 가입시 사용자 Nickname 지정 input (추가 사항)

<br/>

- [x] `피드 화면` : 사용 유저의 모든 게시글을 표시하는 화면
  - [x] `게시글 박스` :
    - [x] `타이틀 영역` : 최상단의 작성자 사진 + 이름, 게시글 수정 탭
      - [x] `편집버튼` : 글 수정하기, 삭제하기 모달 -> 해당 버튼 누르면 삭제 또는 수정 페이지로 이동(아니면 모달이 수정하는 모달로 변경)
        - [x] `삭제하기`
        - [x] `수정하기`
    - [x] `사진 영역` : 기존에는 1개만 가능했음 (욕심내면, 여러개 슬라이드 형식으로 가능하게 하고 싶음)
    - [x] `내용 영역` : 게시글 내용

<br/>

- [x] `글 작성 화면` : 글을 작성하는 화면

<br/>

- [x] `현재 유저 프로필 화면` : 로그인한 현재 유저의 게시물과 대략적인 프로필를 표시하는 화면
  - [x] `유저 프로필 수정하기` : 유저 프로필을 수정하는 화면 (userImage, userDisplayname, userIntro)
  - [x] `작성 글` : 유저가 작성한 작성 글의 image 표 -> 클릭시 post detail
    - [x] `작성 글 detail view` : image 표에서 해당 이미지 클릭시 해당 글 detail view 화면
  - [x] `로그아웃`

<br/>

- [x] `다른 유저 프로필 화면` : 다른 유저가 작성한 글의 유저 이름을 클릭하여 해당 유저의 프로필 화면 구현
  - [x] `프로필 보기` : userImage, userDisplayname, userIntro
  - [x] `작성 글` : 유저가 작성한 작성 글의 image 표 -> 클릭시 post detail
    - [x] `작성 글 detail view` : image 표에서 해당 이미지 클릭시 해당 글 detail view 화면

<br/>

- [x] `네비게이션 바` : 앱로고 - 피드(Home)탭 - 글 작성탭 - 현재 유저 프로필(프로필 수정, 프로필 이동, 로그아웃) 탭
  - [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.07.27 사항

<br/>

## 작업 개요

<br/>

- `리덕스 스토어 구조 변경`

```js
const state = {
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
    signOut: {
      isSignOut: false,
      loading: false,
      signOutError: "",
    },
  },
  image: {
    imageUrl: "",
    getImageUrl: {
      isGet: false,
      loading: false,
      getError: "",
    },
    deleteImageUrl: {
      loading: false,
      isDelete: false,
      deleteError: "",
    },
  },
  init: {
    isInit: false,
  },
  post: {
    allPosts: [],
    userPosts: [],
    currentUserPosts: [],
    getAllPosts: {
      isGet: false,
      loading: false,
      getError: "",
    },
    getUserPosts: {
      isGet: false,
      loading: false,
      getError: "",
    },
    getCurrentUserPosts: {
      isGet: false,
      loading: false,
      getError: "",
    },
    createPost: {
      isSet: false,
      loading: false,
      setError: "",
    },
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
    updatePostsUserInfo: {
      isUpdate: false,
      loading: false,
      updateError: "",
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
      uid: "",
    },
  },
  users: {
    currentUserInfo: {
      userPhotoUrl: "",
      userDisplayName: "",
      userIntro: "",
    },
    userInfo: {
      userId: "",
      userPhotoUrl: "",
      userDisplayName: "",
      userIntro: "",
    },
    getCurrentUserInfo: {
      isGet: false,
      loading: false,
      getError: "",
    },
    getUserInfo: {
      isGet: false,
      loading: false,
      getError: "",
    },
    setCurrentUserInfo: {
      isSet: false,
      loading: false,
      setError: "",
    },
    checkDisplayName: {
      loading: false,
      isCheck: false,
      checkError: "",
      exist: [false, ""],
    },
  },
};
```

- 최대한 코드 중복(보일러 플레이트 코드)을 줄일려고 했으나, 코드 중복을 줄일려고 하면 오히려 더 가독성이 떨어지는 것 같아서 고민이 많다.

<br/>

- `useSelector에 대한 렌더링에 대한 고민`
  - useSelector의 위치의 중요성 증가 (렌더링 최적화) -> 상위 포지션이 아닌 적절한 하위 포지션에 두어야 필요 없는 렌더링을 제거 할 수 있음
  - useSelector를 사용하는 경우 만약 비동기 작업의 상황을 알려주는 state안에 값을 참조하여 가져오면 불필요한 렌더링이 많이 발생할 것 같다는 생각이 든다(pending, fullfilled 에 의한 loading, 완료 여부 값의 변화시 새로운 객체가 들어오기 때문에 같이 새롭게 변했다고 인지할 것 같다는 생각이 든다.)
  - 물론, 실험을 해봐야 할겠지만 우려가 되는 부분이다.

<br/>

- `history를 활용한 정보 전달`
  - 기존 updateSelector, postSelector를 push와 함께 경로 이동시 state를 전달하는 방식으로 변경

```js
const history = useHistory();
history.push({
  pathname: "/update",
  state: { post, profileInfo: {}, type: "post" },
});
```

<br/>

### component, container 구조 재계획

<br/>

- Home
  - allPostsContainer
    - post

<br/>

- Profile
- User
  - ProfileContainer
    - CurrentUserProfileContainer
    - UserProfileContainer
      - UserProfile
      - ProfilePostImages
  - PostContainer
    - PostOnToggle
      - CurrentUserPostsContainer
      - UserPostsContainer
        - Post

<br/>

- currentUser 와 user에 대한 container를 어떻게 제어 할 것인지가 중요 사항임
- 그리고 postOnToggle을 어떻게 비집고 넣을 것인지 중요함 (modal 방식의 children 사용한 HOC 방식을 사용할지 고민중)

<br/>

## 다음에 필요한 사항

<br/>

- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요

<br/>

- [ ] profile 정보 요청 시기 조정
  - 더 빠른 연산을 위해서, 화면이 render 되고 profile에 관련된 정보를 가져오지 말고 profile 보기위해 버튼을 눌렀을 때 부터 미리 profile 정보를 요청하게 하자

<br/>

- [x] profile 요청 작업 보일러 플레이트 코드 줄이기
  - 현재 유저와 특정 유저 profile을 요청하는 작업이 비슷하여 보일러 플레이트 코드 발생함
  - 조건을 주어 특정 유저 profile 요청 작업으로 통합이 필요함

<br/>

- [x] redux state 식별자 관리(변수명 관리)
  - 각 redux에서 사용하는 state의 이름을 통일감 있게 관리 필요
