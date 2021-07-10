---
title: "20210708 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit01 : 프로젝트 시작, 기본 환경설정, 필요한 폴더 구조 및 파일 Template 작업, Router 연결 구현" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit01

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

체크는 현재 구현된 상황을 의미합니다.

<br/>

- [x] `로딩 화면 또는 Component` : 앱 실행 초기화 작업시 로딩 또는 다른 작업시 사용할 로딩 화면 및 Component
- [ ] `로그인 화면` : 기본 Email 로그인, Social 로그인, 로그인 에러
  - [ ] `Email 로그인` : Email, Password input, 로그인 버튼
  - [ ] `Social 로그인` : google로그인 버튼, github로그인 버튼
  - [ ] `로그인 에러` : Email로그인, google로그인, github 로그인 에러 발생시 사용자에게 출력
- [ ] `회원가입 화면` : Email 로그인을 위한 계정을 만드는 화면, 회원가입 에러
  - [ ] `Email 형식 가입` : Email, Password input, 회원가입 버튼
    - [] 가입시 사용자 Nickname 지정 input (추가 사항)
- [ ] `피드 화면` : 사용 유저의 모든 게시글을 표시하는 화면
  - [ ] `게시글 박스` :
    - [ ] `타이틀 영역` : 최상단의 작성자 사진 + 이름, 게시글 수정 탭
    - [ ] `사진 영역` : 기존에는 1개만 가능했음 (욕심내면, 여러개 슬라이드 형식으로 가능하게 하고 싶음)
    - [ ] `내용 영역` : 게시글 내용
- [ ] `현재 유저 프로필 화면` : 로그인한 현재 유저의 게시물과 대략적인 프로필를 표시하는 화면
- [ ] `유저 프로필 화면` : 유저의 게시물과 프로필을 확인 할수 있는 화면 (피드 게시글 작성자 유저 이름을 눌러 유저 프로필 화면으로 이동)
- [ ] `네비게이션 바` : 앱로고 - 피드(Home)탭 - 글 작성탭 - 현재 유저 프로필(프로필 수정, 프로필 이동) 탭

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.07.08 이전 사항

<br/>

## 작업 개요

<br/>

- [x] `기본적인 프로젝트 환경 설정` : redux-toolkit 환경의 파일 구조 만들기
  - [x] index.js provider 달아 store 연결
  - [x] app.js 초기화 container 달기
  - [x] router 파일 생성 및 대략적인 template 구성
  - [x] components 폴더와 component template 파일 생성
  - [x] containers 폴더와 container template 파일 생성
  - [x] pages 폴더와 page template 파일 생성
  - [x] redux/modulse - store에 reducer 연결, reducer(Root), slice template 작업

<br/>

- [x] `사용할 대략적인 Component, Container 파일만 만들어 두기` : 뭘 구현해야할지 대충 떠올리고자 미리 만들어 놓음

<br/>
<br/>
<br/>

# 2021.07.08 사항

<br/>

## 작업 개요

<br/>

- [x] `Router 구현 및 사용할 페이지 연결` :Home, Auth, User, Profile 등 대략적으로 연결함

- [x] `로딩 화면 또는 Component` : 앱 실행 초기화 작업시 로딩 또는 다른 작업시 사용할 로딩 화면 및 Component
- [x] `앱 초기화 상태 구현`

<br/>

## 고민, 생각 및 깨달음

<br/>

- **Page의 Container는 없는게 좋을듯 하다.**
  - page는 말 그대로 page니까, Container 또는 Component 만을 배치하는 용도로 사용하는 것이 좋을 듯 하다.

<br/>

- **Error : A non-serializable value was detected in an action**
  - **엄청 큰 데이터를 받아서 하나의 Action 객체를 만들려고 하면 에러 발생함**
  - ActionCreator에 데이터를 넣어 Action을 만들기 전에, 해당 데이터에 필요한 데이터만 가져오도록 하자
  - toolkit을 사용하면, slice에서 prepare 프로퍼티를 지정하면 action을 만들 데이터를 사전에 가공할 수 있음

<br/>

- **dispatch** 사용시 ActionCreator를 참조하지 말고 꼭 **호출하는 것을 잊지말자**
  - `dispatch( ActionCreator( ) )`

<br/>

- **createAsyncThunk 사용시 `try-catch, async-await`를 잊지 말자**
  - slice 방식을 사용하는 비동기 작업은 createAsyncThunk를 통해서 쉽게 구현할 수 있다.
  - 에러를 받고, 비동기 작업을 연속으로 처리하는 과정은 try-catch문, async-await 조합으로 하는게 깔끔하니 좋다.
  - then, catch 방식은 가독성이 떨어지는듯 하다.

<br/>

- **Detail Error 받기**
  - Detail Error를 받으려면 ExtraReducers에 `action.payload`로 Error를 받아오도록 해야한다.
  - action.error는 단지 rejected message 밖에 없다.

<br/>

- 나중에, redux-toolkit용 component, container, module/slice, rootReducer, store까지 설정해 놓은 **template를 만들어 놓으면 좋을 듯하다.**
  - modules/slice : Async, Initial State, Slice, actionCreator 영역을 주석으로 구분하고 각각 어느정도 구성을 해놓은 형태
  - container : component를 연결하는 형태
  - component : 간단한 jsx를 return하여 구분할 수 있는 형태
  - Router component : 간단한 Route 묶음 형태
  - rootReducer : slice.reducer를 묶는 combineReducers의 형태
  - store : rootReducer를 연결해 놓는 형태
