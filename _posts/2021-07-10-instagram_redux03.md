---
title: "20210710 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit03 : Auth 재편성, 회원가입 구현, Auth 관련 사항 마무리" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit03

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
  - [x] `로그인 에러` : Email로그인, google로그인, github 로그인 에러 발생시 사용자에게 출력
- [x] `회원가입 화면` : Email 로그인을 위한 계정을 만드는 화면, 회원가입 에러
  - [x] `Email 형식 가입` : Email, Password input, 회원가입 버튼
    - [x] 가입시 사용자 Nickname 지정 input (추가 사항)
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
<br/>

# 2021.07.10 사항

<br/>

## 작업 개요

<br/>

- [x] `Form 통합` : email 로그인 Form , 회원 가입 -> Auth (toggle로 로그인/회원가입 구분)
  - id, password 넣은 input은 공유하고, 가입화면에만 nickname input 추가 구현
- [x] `email 가입 구현` : 가입 input, 가입 요청 작업 구현
  - [x] `가입 nickname Update` : 가입시 nickname input으로 사용할 이름을 받아 사용자 profile displayName을 초기에 설정 (**사용자 info는 로그인 해야 업데이트 할 수 있어 바로 로그인 구현**)
  - [x] `바로 로그인 구현`
- [x] `Error` : 가입 Error, 로그인 Error (email, social) 통합으로 하나의 영역에 보이게 구현
- [x] `social 로그인 Redirect 구현` : 기존 Popup -> Redirect (브라우저는 popup 띄우는것을 안좋아함, popup시 error로 중지는 안되지만 콘솔에 경고가 뜸)

<br/>

## 고민, 생각 및 깨달음

<br/>

- **thunkAPI의 getState**는 안에 콜백으로 세세하게 state를 가져오지 못함
  - 무조건 전체 state라서 일단 받고, 분리해서 가져와야 함
- 화면에 띄울 에러 메세지는 따로 Error state를 만들어서 어떤 작업중 에러 발생시 error를 update하게 해서 상황에 맞는 Error를 가져와 처리 할수 있음
