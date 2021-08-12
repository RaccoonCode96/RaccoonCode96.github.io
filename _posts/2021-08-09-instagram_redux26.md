---
title: "20210809 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit26 : 본인을 제외한 랜덤 유저 추천 기능 구현, side 컴포넌트 스타일링 (회원 추천 + 푸터)" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit26

<br/>

<p align="center">
<img src="../assets/img/instagram_logo.png" width="400px" height="400px">
</p>

<br/>

> # 📄 프로젝트 설명

<br/>

이 프로젝트는 기존에 React & firebase를 통해서 만든 인스타그램 클론 프로젝트 리팩토링 프로젝트 입니다. (해당 프로젝트는 프로젝트 카테고리에서 확인 가능합니다.)

<br/>

## 상태 관리

해당 프로젝트에서는 `redux-toolkit(Slice 모델)`을 사용하여 상태관리를 구현하고 있습니다.

<br/>

## 스타일

현재 SCSS를 채택하여 css 작업을 진행중에 있으며, 부분적으로 Material UI를 사용하고 있습니다.
대부분의 경우에는, Material UI와 React 호환성 문제로 대부분은 SCSS로 직접 구현하고 있습니다.

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
  - [x] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.08.09 사항

<br/>

## 작업개요 및 고찰

<br/>

### 1. 유저 추천 기능 구현 (제한적으로 구현함)

<br/>

- 유저 추천 기능 : 자신을 제외한 랜덤 유저 몇명의 정보를 띄워 추천 함
  - 접근01) 랜덤 유저 정보를 가져오기 위해서 각 사용자 마다 고유의 숫자를 주고 랜덤한 숫자에 맞는 유저를 가져옴
    - 랜덤한 숫자의 array를 만들수 있게 useRandom hook을 만듦
    - useRandom은 count 라는 몇개를 뽑을지에 대한 매개변수와 range라는 몇의 범위에서 뽑을지에 대한 매개변수 이다.
    - while문을 통해서 set 집합에 있는 요소 개수와 뽑을 개수가 같아지는 순간 뽑는것을 그만 둔다.

```js
const useRandom = (count, range) => {
  const randomSet = new Set();
  while (randomSet.size < count) {
    randomSet.add(Math.floor(Math.random() * range));
  }
  return [...randomSet];
};

export default useRandom;
```

<br/>

- 만들어진 랜덤 숫자 리스트를 가지고, firestore에서 해당 숫자를 가진 유저의 정보를 가져오게 함

<br/>

- 문제 발생
  - firebase 특성상 2중 필드 조건을 허용하지 않는다. (dispalyName이 자신이 아닌 것 중에서 count가 해당 숫자인 것을 가져오게 하고 싶었지만 불가능 했다.)

<br/>

- 임시 방편
  - 그래서 임시적으로 displayName이 자신이 아닌 것을 기준으로 색인에서 몇개를 가져오게 하는 firestore의 limit() 함수를 사용하였다.
  - 하지만 이렇게 하면, 가입유저에 대한 변동이 없는 이상 색인이 변하지 않아서 계속 주변 동일한 사용자를 가져오게 되는 문제가 발생한다.

```js
const getRandomUserInfo = async() => {

  /*
  // 동일 필드가 아닌 2중 복합 쿼리 불가
  const { docs } = await dbService
		  .collection('users')
      .where('displayName', '!=', displayName)
      .where('count', 'in', useRandom(2, userMaxCount))
      .get();
  */

  // 임시방편
  const { docs } = await dbService
				.collection('users')
				.where('displayName', '!=', displayName)
				.limit(2)
				.get();

  const res = docs.map((doc) => {
    const { displayName, userPhotoUrl } = doc.data();
    return { displayName, userPhotoUrl };
}
```

<br/>

- 나중에 시도해 볼 아이디어
  - **현재 유저의 user info의 count 값을 가져와서 해당 값을 제외한 랜덤 숫자 리스트 만들기로 useRandom을 수정해 보자**

<br/>

- 유저 추천 표시가 들어 있는 side 영역의 컴포넌트 스타일링 작업 실시

<p align="center">
<img src="../assets/img/recommend_user.gif" width="600px" height="450px">
</p>

<br/>

## 다음에 필요한 사항

- [ ] 랜덤 유저 개선하기 : useRandom 제외 값 지정하게 변경하기

<br/>

- [ ] profile 정보 요청 시기 조정
  - 더 빠른 연산을 위해서, 화면이 render 되고 profile에 관련된 정보를 가져오지 말고 profile 보기위해 버튼을 눌렀을 때 부터 미리 profile 정보를 요청하게 하자

<br/>

- [ ] validation 구현 필요함
  - input 같은 경우, display none 적용시 browser에서 제공하는 validation 말풍선이 뜨지 않기 때문에 따로 구현 필요함
  - required를 사용하지 말고, submit 함수 단에서 input값이 들어 왔는지 체크하여 validation error 구현 필요
  - [x] post 관련한 input의 check 대략적인 (PostUpdateContainer, postFormContainer)
  - [x] auth 관련한 input의 check 대략적인 조건 구현
  - [ ] 각 input 별로 데이터 형태에 따른 구체적인 조건 설정이 필요함
    - [ ] 이메일, 패스워드, 유저 네임, 글 내용의 형식(조건, 제한) 지정 필요

<br/>

- [ ] 글 작성 시간 (클라이언트 단에서 뿌리는 경우 로컬 시간 변경으로 조작 가능한지 테스트 필요함)

<br/>

- [ ] profileUpdateContainer과 postFormContainer 통합 시도

<br/>

- [ ] 효과적인 렌더링 제한을 위해서 container에 있는 함수들을 hook으로 만들어 구현하기

<br/>
<br/>
<br/>

# 나중에 구현하고 싶은 기술

<br/>

- input 자동 체크(중복 검사 기능에 추가)
- side 바에 유저 랜덤 추천 및 푸터 정보
- 유저 이름 검색을 통한 프로필 보기 (이름 검색)
- 무한 스크롤
- 게시글 장소 태그로 장소 지도 보기 (지도 API)
