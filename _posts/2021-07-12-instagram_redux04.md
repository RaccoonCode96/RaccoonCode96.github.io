---
title: "20210712 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit04 : Navigation, 글 작성, 글 가져오기 구현 (Create, Read 구현)" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit04

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
        - [ ] `삭제하기`
        - [ ] `수정하기`
    - [ ] `사진 영역` : 기존에는 1개만 가능했음 (욕심내면, 여러개 슬라이드 형식으로 가능하게 하고 싶음)
    - [ ] `내용 영역` : 게시글 내용
- [x] `글 작성 화면` : 글을 작성하는 화면
- [ ] `현재 유저 프로필 화면` : 로그인한 현재 유저의 게시물과 대략적인 프로필를 표시하는 화면
- [ ] `유저 프로필 화면` : 유저의 게시물과 프로필을 확인 할수 있는 화면 (피드 게시글 작성자 유저 이름을 눌러 유저 프로필 화면으로 이동)
- [x] `네비게이션 바` : 앱로고 - 피드(Home)탭 - 글 작성탭 - 현재 유저 프로필(프로필 수정, 프로필 이동, 로그아웃) 탭
  - [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
  - [x] `로그아웃`

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>
<br/>

# 2021.07.12 사항

<br/>

## 작업 개요

<br/>

- [x] `Navigation 구현` : 네비게이션 바를 통한 페이지 이동 기능 구현
  - Home - 글작성 - 프로필 형태로 각 페이지로 이동할 수 있게 구현함
  - Home(/), write(/write), proflie(/profile)

<br/>

- [x] `PostForm(write) 구현`: postForm 글작성 기능 구현 (Firebase에 저장까지)
  - 파일 이미지 -> base64 -> Firebase storage로 넣어 url 가져오기 -> store에 추가
  - text -> store에 추가
  - firebase database 글 데이터 구성

<br/>

```js
{
  postText: String, postDate: Number, userId: String, userPhotoUrl: String, userDisplayName: String, postImageUrl: String;
}
```

<br/>

- [x] `Post(피드) 구현` : firebase store로 부터 글 가져와서 화면에 표현 기능 구현

<br/>

## 고민, 생각 및 깨달음

<br/>

- 파일을 다루는 작업을 하다 보니 web API를 사용하게 되고 그전에 했었던 기억을 되살리는데 좋았다.
  - https://developer.mozilla.org/ko/docs/Web/API/FileReader
  - 나중에 web API에 대해서 더 깊게 알아볼 필요는 있겠다.

<br/>

웹앱 만들 때, 어떤 순서로 진행해야 하는지 고민이 생긴다.

물론, 이 프로젝트의 경우 기존의 인스타그램 클론 해놓은 것을 리팩토링이지만, 해당 코드가 이미 있기 때문에 뭔가 처음 부터 디테일하게 작업하게 되는것 같다.

<br/>

이렇게 되면, 오히려 더 복잡하게 되는것 같기도 하다. 차라리 처음 앱을 만드는 것 처럼 기능적인 부분을 먼저 Component로 구현하고 배치나, 화면에 어떻게 보이게 될지에 대한 부분을 짜는게 더 효율적인 방법인것 같다.

- 예를 들어, 기존에 수정과 삭제가 모달에 들어있었기 때문에 모달먼저 구현하는게 아니고, 수정과 삭제를 각각 먼저 구현하고 모달에 대해서 구현하는게 좋은 방법인것 같다.

그래서, 일단 핵심이 되는 기능을 위주로 먼저 구현하고 디테일을 잡아야 겠다라는 생각이 든다. CRUD를 먼저 기능적으로 구현해 놓은 다음에 하는게 좋을 듯 하다.

<br/>

## 다음에 필요한 사항

<br/>

- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
- [ ] Post에 글 수정 메뉴 버튼을 통한 모달 구현
  - [ ] 삭제하기
  - [ ] 수정하기
