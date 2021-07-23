---
title: "20210720 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit09 : 현재 유저의 profile 보여주기 구현 ,users database 구현, profile update시 users db로 관리 구현" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit09

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
- [x] `현재 유저 프로필 화면` : 로그인한 현재 유저의 게시물과 대략적인 프로필를 표시하는 화면
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

# 2021.07.20 사항

<br/>

## 작업 개요

<br/>

- users database 구현
  - profile 수정시 users database에 정보를 추가하여 user 정보 관리
  - users는 다른 사람의 user 데이터 까지 보관할 예정
  - 글상자에서, user 관련 데이터를 사용하게 되는 경우 uid로 가져올 수 있게 할 예정(update가 빠름)
- [x] `현재 유저 프로필 화면 구현`
  - update profile, get profile를 데이터 베이스를 통하는 것으로 개편
- currentUserInfo를 만들어 현재 유저 User 관련 정보만 담는 state를 만듦
  - profile의 currentUser는 uid만 관리하게 하도록 할 예정

<br/>

## 고민, 생각 및 깨달음

<br/>

### 작성자 정보를 post가 가지는것 vs uid만 post가 가지고 작성자 정보를 따로 관리

- `post가 가지는 경우`
  - 저장 공간이 늘어남, 유저 정보가 update되면 해당 글을 모두 찾아 update해주어야 함
  - 유저가 많아져서 글의 개수가 많아지면, update하는데 현재 만들어진 모든 글의 개수만큼 연산하는 시간이 걸림
  - update는 많이 일어 나진 않을 것 같음
  - 글 불러오는데 O(n) 예상
  - update 하는데 O(n) 예상

<br/>
  
- `따로 관리하는 경우` 
  - 저장 공간이 줄어듬, 글을 불러 올 때 마다 uid를 통해서 작성자 정보를 가져와야함(글을 찾아서 update를 할 필요는 없음)
  - 유저가 많아져서 글의 개수가 많아져도, update하는 시간은 유저 수 정도만 연산하는 시간이 걸림
  - 글 불러오는데 O(n^2) 예상
  - update 하는데 O(n) 예상

<br/>

- 따로 관리하는 경우가 더 느릴 것으로 예상됨 하지만, user intro를 관리하려면 필요하고, 다른 유저들의 프로필을 보려면 결국에는 user를 따로 관리를 해야함
- **메모리를 감수하고, 속도를 위해 post에서 작성자 정보를 같이 가지게 하여 글을 불러오는 속도를 빠르게 하고, 또 다른 유저들에 대한 정보도 관리가 필요하므로 둘다 채택하여 중복된 값을 가지게 관리를 해야 할 듯 함**

<br/>

## 다음에 필요한 사항

<br/>

- [ ] user 검사를 실시하여 수정, 삭제 버튼 나타나지 않게 접근 막기
- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
- [ ] Post에 글 수정 메뉴 버튼을 통한 모달 구현
- [ ] user intro를 입력 받아 database에 반영하는 작업 구현
- [ ] profile update시 user database의 user 정보와 post에 있는 user 정보 일치 하게 둘다 update하는 작업 구현
