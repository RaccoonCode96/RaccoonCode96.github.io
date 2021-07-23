---
title: "20210722 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit11 : imageUrl에 대한 에러(디테일) 처리, file타입 input 에러 처리, 모달 HOC 구현, Post 수정&제거 접근 제한 구현" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit11

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

- [ ] `현재 유저 프로필 화면` : 로그인한 현재 유저의 게시물과 대략적인 프로필를 표시하는 화면
  - [x] `유저 프로필 수정하기` : 유저 프로필을 수정하는 화면 (userImage, userDisplayname, userIntro)
  - [ ] `작성 글` : 유저가 작성한 작성 글의 image 표 -> 클릭시 post detail
  - [x] `로그아웃`

<br/>

- [ ] `다른 유저 프로필 화면` : 다른 유저가 작성한 글의 유저 이름을 클릭하여 해당 유저의 프로필 화면 구현
  - [ ] `프로필 보기` : userImage, userDisplayname, userIntro
  - [ ] `작성 글` : 유저가 작성한 작성 글의 image 표 -> 클릭시 post detail

<br/>

- [x] `네비게이션 바` : 앱로고 - 피드(Home)탭 - 글 작성탭 - 현재 유저 프로필(프로필 수정, 프로필 이동, 로그아웃) 탭
  - [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.07.22 사항

<br/>

## 작업 개요

<br/>

- `storage에서 imageUrl의 디테일한 delete 처리`
  - **Post, User image를 다루는 경우, delete를 디테일 하게 다루는 작업 구현**
  - Post 또는 User를 update 하는 경우, 이미지가 이전과 달라 졌는지 조건을 주어, 변하지 않은 경우 데이터베이스에서 기존 data를 삭제하지 않도록 함

<br/>

- `redux imageUrl state -> Reset 처리`
  - imageUrl을 가져오는 action을 공유하기 때문에 해당 imageUrl
  - imageUrl을 사용하여 처리한 경우, getimageUrl의 imageUrl을 reset 시키는 작업 구현

<br/>

- `file input 에러 처리`
  - file input에 값이 들어 왔다가 취소되는 경우 file 처리를 안하게 조건을 줌 (없는 값을 참조하는 경우 에러 발생하기 때문에)

<br/>

- `재사용 가능한 모달 컴포넌트 구현`
  - 모달이 on off만 하도록 하였고, styled-Component로 css를 주어야 함

```js
const Modal = ({ children, isOn }) => {
  return isOn ? <>{children}</> : <></>;
};

export default Modal;

/* 
Modal 컴포넌트로 표현할 컴포넌트를 감싸서 children으로 대입
isOn에 보일지 안보일지의 값을 전달해서 제어 함
*/
```

<br/>

- `Post 제거&수정 접근 제한 모달 버튼 구현`
  - -> 현재 사용자와 작성자 일치 여부에 따른 접근 제한

## 다음에 필요한 사항

<br/>

- [x] user 검사를 실시하여 수정, 삭제 버튼 나타나지 않게 접근 막기
- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
- [x] Post에 글 수정 메뉴 버튼을 통한 모달 구현
