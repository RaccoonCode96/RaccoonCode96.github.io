---
title: "20210721 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit10 : 최초 소셜 로그인 및 가입 이후의 users 데이터 베이스 관리, Profile Update 구현(userIntro 포함, users 활용), falsy한 프로퍼티 값에 따른 선택적 프로퍼티 스프레드 할당" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit10

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
    - [ ] `타이틀 영역` : 최상단의 작성자 사진 + 이름, 게시글 수정 탭
      - [ ] `편집버튼` : 글 수정하기, 삭제하기 모달 -> 해당 버튼 누르면 삭제 또는 수정 페이지로 이동(아니면 모달이 수정하는 모달로 변경)
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

# 2021.07.21 사항

<br/>

## 작업 개요

<br/>

- **users 데이터 베이스 구축에 따른 update profile, login시 profile update 구현**
  - users 데이터 베이스를 활용하도록 변경함
  - userIntro를 받아 유저의 profile에 넣을 소개 text update를 구현함

<br/>

- `소셜 로그인시`
  - 기존의 유저 데이터베이스에 정보가 있는지를 확인해서 소셜 로그인의 정보로 세팅함, 없는 정보는 Default 값을 줌 (displayName이 없으면 uuid로 random으로 넣어줌, 이미지가 없으면 기본 이미지를 넣어줌)
  - 어쨌거나, 한번 최초 로그인 하면 유저 데이터 베이스에 정보가 생김
- `일반 이메일 가입시`
  - 닉네임은 무조건 받기로 되어 있고 유저 이미지는 Default 값을 줌

<br/>

- **단, redirect 로그인시 해당 profile update 작업이 안되기 때문에 popup 로그인으로 되돌아 갔음**

<br/>

- 유저 profile 수정시, 기존에 작성한 **글의 user 정보**와 **users 데이터 베이스에 있는 정보**도 수정하게 함
  - 기존의 updateDisplayName, updateImageUrl의 경우 통합하여 선택적으로 해당 기능을 하게 만들었음
    - `updatePostUserInfoThunk` : posts에 있는 유저가 작성한 post에 있는 유저 정보 수정
    - `setCurrentUserInfoThunk` : users 데이터베이스의 현재 유저의 user 데이터를 수정
    - `getCurrentUserInfoThunk` : 현재 users 데이터베이스의 현재 유저의 user 데이터를 가져옴

<br/>

## 깨달음

<br/>

- **데이터가 없는 경우에 유연하게 해당 데이터를 넣지 않는 스프레드 문법의 표현식**
  - falsy한 프로퍼티 값에 따른 선택적 프로퍼티 스프레드 할당
  - 기존의 값을 유지한 상태에서 변하는 값만 update 한 새로운 object를 만들 수 있음
  - 재사용성을 높여주면서 각각의 기능을 선택적으로 실시할 수 있어 좋음 (다른 기능에는 영향을 주지 않게 됨)

```js
const init = {
  name: "",
  weight: 0,
  tall: 0,
  isAnimal: false,
  isbird: false,
};

const Raccoon = {
  name: "Raccoon",
  weight: 75,
  tall: 180,
};

const Jerry = {
  name: "Jerry",
  weight: 10,
  tall: 14,
  isAnimal: true,
};

const { name, weight, tall, isAnimal, isbird } = Raccoon;
// 값이 없으면, 해당 프로퍼티는 undefined로 할당되어 없는 값을 undefined로 update함

const newInfo1 = { ...init, name, weight, tall, isAnimal, isbird };
console.log(newInfo1);
// {name: "Raccoon", weight: 75, tall: 180, isAnimal: undefined, isbird: undefined}

const newInfo = {
  ...init,
  ...(name && { name }),
  ...(weight && { weight }),
  ...(tall && { tall }),
  ...(isAnimal && { isAnimal }),
  ...(isbird && { isbird }),
};
console.log(newInfo); // {name: "Raccoon", weight: 75, tall: 180, isAnimal: false, isbird: false}
// 있는 정보만 스프레드 문법의 대상이 됨
```

<br/>

## 다음에 필요한 사항

<br/>

- [ ] user 검사를 실시하여 수정, 삭제 버튼 나타나지 않게 접근 막기
- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
- [ ] Post에 글 수정 메뉴 버튼을 통한 모달 구현
- [x] user intro를 입력 받아 database에 반영하는 작업 구현
- [x] profile update시 user database의 user 정보와 post에 있는 user 정보 일치 하게 둘다 update하는 작업 구현
