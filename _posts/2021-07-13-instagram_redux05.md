---
title: "20210713 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit05 : 글 수정, 글 삭제 (Update, Delete) 구현" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit05

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
- [x] `네비게이션 바` : 앱로고 - 피드(Home)탭 - 글 작성탭 - 현재 유저 프로필(프로필 수정, 프로필 이동, 로그아웃) 탭
  - [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
  - [x] `로그아웃`

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.07.13 사항

<br/>

## 작업 개요

- [x] `글 삭제 하기 구현`
  - storage, store에 글삭제 요청 후 redux postList에 삭제하여 반영해야 함

<br/>

- [x] `글 수정 구현`
  - 수정 클릭시 해당 포스트의 정보가 redux store의 postSelector에 보내지고 바로 update 페이지로 이동함
  - update 페이지에서 postSelector의 해당 포스트 정보를 불러와 input에 기본값으로 지정하여 기존의 내용이 input에 표시되도록 함
  - preview 창을 만들어서 기존 이미지를 표시해주고, 파일선택시 preview도 변경되게 함
  - 수정 요청시,
    - 현재 유저와 글 작성자가 일치하는지 검사
    - 변경할 이미지의 base64를 storage에 보내어 url을 받아옴
    - 기존 이미지 url은 firebase storage에서 지움
    - 변경할 이미지의 url과 변경할 text를 firebase store에 update 요청함
  - 수정 완료후 홈으로 이동
  - 홈 이동시 useEffect cleanUp으로 redux의 post관련 상태 모두 초기화(reset)

<br/>

- 수정, 삭제의 경우 user 방어코드를 작성하여 firebase, redux에 값 변경 요청전에 검사시키게 해놓았다

<br/>

## 고민, 생각 및 깨달음

<br/>

- button onClick 이벤트에 연결하는 함수에 인수를 전달하기
  - 화살표 함수로 한번 묶어서 실행하고자 하는 함수에 인수를 넣어 호출시키게 하면 됨

```js
// component
const component = ({ post, deletePost }) => {
  return (
    <button
      onClick={() => {
        deletePost(post);
      }}
    >
      삭제
    </button>
  );
};
```

- 현재 post slice가 많이 큰 관계로, 기능별로 나누어야 겠다는 생각이 들었다.
  - 나누는 경우에는 연관된 자료가 있는지 주의하여야 될것 같다.

<br/>

## 다음에 필요한 사항

<br/>

- [ ] user 검사를 실시하여 수정, 삭제 버튼 나타나지 않게 접근 막기
- [ ] slice 모듈화 분리하기
- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요
- [ ] Post에 글 수정 메뉴 버튼을 통한 모달 구현
  - [x] 삭제하기
  - [x] 수정하기
