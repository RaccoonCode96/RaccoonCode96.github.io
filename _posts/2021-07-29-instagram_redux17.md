---
title: "20210729 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit17 : 스타일링 작업 방식 선택, SCSS 스타일링을 위한 디렉 구조, Load, Navigation 컴포넌트, 폴더 구조 확인 CLI command" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit17

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
  - [x] 스타일링 완료

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
  - [x] `이미지 리사이징`

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

# 2021.07.29 사항

<br/>

## 작업 개요

<br/>

- `스타일링 작업 방식에 대한 선택`
  - 방식1) styles 폴더에 각각 기능에 맞는 스타일시트를 만들어서 **나중에 main 이나 index 이름의 스타일 시트에 모두 모아서 app 최상단 컴포넌트에 import 시키는 방식**
  - 방식2) **스타일시트를 각 컴포넌트 마다 구성하여 import 하는 방식**
    - 물론, 나중에 build 하는 경우에는 하나의 css 파일이 되겠지 만
    - 개발 하면서 연관된 jsx, 기능, 스타일을 모아 component 별로 확인하는게 더 효율적이라고 생각함 (다른사람이 보기도 편할 듯 함)

<br/>

- `SCSS 스타일링 작업을 위한 디렉구조 작업화`
  - SCSS 스타일링을 각 컴포넌트에 import하는 구조로 작성하기 위해서 기존의 Component, Container 구조를 Component 폴더에 컴포넌트 별로 폴더를 만들어 Component, Container를 짝으로 구성해 놓았고, 해당 컴포넌트의 SCSS는 컴포넌트 폴더에 같이 첨부할 예정
  - react의 컴포넌트 프로그래밍의 컨셉에 맞게 스타일링도 컴포넌트에 결착하여 확인하기 편하게 만들고자 이렇게 구현하였다.
  - styles 폴더의 경우에는 공통적으로 사용하는 utils 역할의 scss 파일들이 보관 되어 있음
    - main : 모든 앱에서 공통적으로 사용되는 스타일
      - 예) font-family, box-sizing, a태그 text-decorate: none 등
      - reset css를 import 하여 받음
      - app.js에 직접적으로 연결 됨
    - colors : 자주 사용될 색 관련 값을 가진 변수들
    - mixins : 자주 사용될 수 있는 스타일 값 mixin들
    - sizes : 자주 사용되는 size 값 변수들
    - variables : 해당 변수들을 모두 import 하여 묶어주는 역할
      - 다른 파일에서는 variables만 import 하여 사용할 수 있음

<br/>

```
폴더 구조
├─components
│  ├─AuthForm
│  ├─common
│  ├─Init
│  ├─Post
│  ├─PostForm
│  ├─PostUpdate
│  ├─ProfileUpdate
│  ├─SignOut
│  ├─SocialSignIn
│  └─UserProfile
├─hooks
├─pages
│  ├─Auth
│  ├─Home
│  ├─Profile
│  ├─Update
│  ├─User
│  └─Write
├─redux
│  └─modules
└─styles

─styles
  main.scss
  reset.css
  _colors.scss
  _mixins.scss
  _sizes.scss
  _variables.scss
```

<br/>

- 완료한 스타일링
  - [x] `styled Load Component`
  - [x] `styled Navigation Component`
    - 구체적으로 드롭 메뉴는 추가적으로 구성할 예정

<br/>

- 폴더 구조 보기 command (window)
  - `tree` : 해당 폴더의 파일이 아닌 폴더 구조만 그래프로 보여줌
    - options
      - /f : 파일 까지 보여줌

<br/>

## 다음에 필요한 사항

<br/>

- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요

<br/>

- [ ] profile 정보 요청 시기 조정
  - 더 빠른 연산을 위해서, 화면이 render 되고 profile에 관련된 정보를 가져오지 말고 profile 보기위해 버튼을 눌렀을 때 부터 미리 profile 정보를 요청하게 하자

<br/>

- [ ] Post Detail View로 이동시 해당 글의 scrollX 위치로 이동하게 구현하기
