---
title: "20210715 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit07 : profile 이미지 url 삭제에 관한 에러 처리 문제 해결" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit07

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

# 2021.07.15 사항

<br/>

## 작업 개요

<br/>

- profile 이미지 url 삭제에 관한 에러 처리 문제 해결
  - 특정 작업에서의 에러에 대해서만 무시하고 다른 작업을 수행하는 것을 구현하고 싶었음
    - 그냥, 따로 action을 만들어서 error를 처리받고 updateProfile에서는 try에 넣지 않음으로서 이미지 삭제 에러가 발생해도 실패만 redux로 뜨고, 나머지는 진행하게됨
    - 즉, 해당 작업만 따로 구현하여 에러 처리를 하여 redux로 상태를 볼수 있게 하고, 호출하는 함수에서는 try에서 제외 시키면 됨

<br/>

## 고민, 생각 및 깨달음

<br/>

### 생각

<br/>

- 나중에, User에 대한 정보를 firebase database에 만들어서 관리해야함
  - 현재는 post에 userPhotoUrl, displayName이 존재하여 모든 글을 찾아서 업데이트하기란 성능에 좋지 않음
  - post의 userId 만 가지고 데이터베이스의 유저정보를 가져와서 글상자의 상단을 구성하면 굳이 글을 모두 update할 필요가 없음
- **결국에는 firebase의 currentUser의 photoUrl, displayName은 한계가 있으므로 User를 관리하는 데이터베이스가 필요함**
  - User 데이터 베이스 구성하고 User 데이터 베이스 중심의 profile update 및 가입시 User데이터 지정 작업을 구현해야함
  - 로그인시 제공하는 uid를 가지고 User 데이터를 활용하도록 개편이 필요함

<br/>

## 다음에 필요한 사항

<br/>

- [ ] user 검사를 실시하여 수정, 삭제 버튼 나타나지 않게 접근 막기
- [ ] slice 모듈화하여 관리하기, 기능별로 재편성 필요함
  - [ ] getImageUrl 위치 옮기기 공통 사용 데이터 쪽으로 올려야 함
- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
- [ ] Post에 글 수정 메뉴 버튼을 통한 모달 구현
- [ ] user profile info도 구현 필요
