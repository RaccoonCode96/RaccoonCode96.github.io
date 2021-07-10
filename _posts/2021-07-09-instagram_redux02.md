---
title: "20210709 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit02 : 로그인 옵저버(리스너), 로그인 구현(Social, Email 로그인)" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit02

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
- [x] `로그인 화면` : 기본 Email 로그인, Social 로그인, 로그인 에러
  - [x] `Email 로그인` : Email, Password input, 로그인 버튼
  - [x] `Social 로그인` : google로그인 버튼, github로그인 버튼
  - [ ] `로그인 에러` : Email로그인, google로그인, github 로그인 에러 발생시 사용자에게 출력
- [ ] `회원가입 화면` : Email 로그인을 위한 계정을 만드는 화면, 회원가입 에러
  - [ ] `Email 형식 가입` : Email, Password input, 회원가입 버튼
    - [ ] 가입시 사용자 Nickname 지정 input (추가 사항)
- [ ] `피드 화면` : 사용 유저의 모든 게시글을 표시하는 화면
  - [ ] `게시글 박스` :
    - [ ] `타이틀 영역` : 최상단의 작성자 사진 + 이름, 게시글 수정 탭
    - [ ] `사진 영역` : 기존에는 1개만 가능했음 (욕심내면, 여러개 슬라이드 형식으로 가능하게 하고 싶음)
    - [ ] `내용 영역` : 게시글 내용
- [ ] `현재 유저 프로필 화면` : 로그인한 현재 유저의 게시물과 대략적인 프로필를 표시하는 화면
- [ ] `유저 프로필 화면` : 유저의 게시물과 프로필을 확인 할수 있는 화면 (피드 게시글 작성자 유저 이름을 눌러 유저 프로필 화면으로 이동)
- [ ] `네비게이션 바` : 앱로고 - 피드(Home)탭 - 글 작성탭 - 현재 유저 프로필(프로필 수정, 프로필 이동, 로그아웃) 탭
  - [x] `로그아웃`

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.07.09 사항

<br/>

## 작업 개요

<br/>

- [x] `유저 로그인/로그아웃 옵저버(리스너) 작업` : 리스너 실행을 앱 초기화로 설정, 로그인시 유저 정보 처리, 로그아웃시 유저 정보처리
- [x] `Social 로그인` : google, github 로그인 요청 작업 구현
- [x] `email 로그인` : email 로그인 Form 구현 (email, password input) 및 로그인 요청 작업 구현
- [x] `로그아웃` : 로그아웃 컴포넌트, 로그아웃 요청 작업 구현 (로그인, 로그아웃이 잘되는지 확인하기 위해서 버튼으로 만듦)

<br/>

## 고민, 생각 및 깨달음

<br/>

### 고민, 생각

<br/>

- redux를 사용하는데, react-redux에서 제시하는 container component 개념으로 component를 깨끗하게 template로만 사용하는 용도 하고 싶다. 하지만, 너무 그렇게 하면 오히려 component를 더 많이 만드는 것이 아닌가라는 생각도 든다.
- **Container 패턴**에 딱 맞게 하는게 좋을 까 아니면, 융통성 있게 component를 작업 함수로 오염을 시켜도 될까?
- **contextAPI vs redux-toolkit vs graphql + apollo** -> 과연 어느 상태 관리가 좋을 까?

<br/>

### 깨달음

<br/>

- **input을 구현**할 때 controlled component 방식은 input의 event를 통해서 입력을 받아 react의 상태값을 일치 시키고 해당 값을 다시 input 컴포넌트에 반영하여 일치시키는 작업

  - 여러 input을 사용하는 경우 객체에 한번에 담아 사용하는 게 보기 좋은 듯함
  - 객체 안의 값은 변하는 값이지만, 객체 식별자가 참조하는 값(객체가 들어있는 메모리 주소)은 변하지 않기 때문에 react에서는 변한 값이라고 인식하지 못하여 render를 하지 않음
  - useState 사용시 화면이 rendering 될수 있게 새로운 객체가 setState 되게 해야함 (새로운 값이라고 인식하게 해야함)

- **form을 사용하는 이유**는 짐작으로는 단지, enter를 통해서 해당 액션이 일어나게 하려는 것 같다.

  - 어차피 controlled component 방식으로 input을 사용하는 경우, react state에 반영되어서 이미 값을 가진 상태인데, button onClick으로 그냥 해당 액션을 요청하면 되지 않나라는 생각이 든다.

- **useRef**는 실제 돔의 여러가지 상태를 조작해야 하는 경우 사용하는 것(비제어 컴포넌트)
