---
title: "20210808 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit25 : Post Detail View로 이동시 해당 글의 scrollTop 위치로 이동 구현하기" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit25

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

# 2021.08.08 사항

<br/>

## 작업개요 및 고찰

<br/>

### 1. Post Detail View로 이동시 해당 글의 scrollTop 위치로 이동하게 구현하기

- 다른 유저 및 자신의 프로필에 있는 post Image table에서 특정 이미지 클릭시 해당 글위치로 이동 함
- ProfilePostImages 컴포넌트에서 유저의 이미지 테이블을 보여주는데 해당 이미지를 클릭하는 경우 해당 이미지가 몇번째 글인지 postNum울 Posts라는 페이지로 전달하여 Posts 페이지에서 보여줄 해당글에 스크롤이 위치하도록 함
  - Posts 페이지는 자신 또는 특정 유저가 작성한 글만을 Home 페이지 (Feed) 처럼 글을 보여줌

<p align="center">
<img src="../assets/img/move_to_post.gif" width="600px" height="500px">
</p>

- Posts 페이지로 이동 시키는 함수
  - 현재 유저의 Profile 컴포넌트와 특정 유저의 User 컴포넌트의 경우에는 거의 비슷하여 Route는 다르지만 Profile 컴포넌트로 통합하여 사용하기 때문에, 각 함수를 구현할 때는 pathname을 조건으로 다른 라우트로 갈수 있게 해야함
    - 여기서 드는 생각은 현재 profile과 user pathname을 분리하여 놓았기 때문에 오히려 복잡한 로직 함수를 사용하게 되므로 pathname을 현재 유저도 userName을 현재 유저 이름으로 하여 `/user/userName`를 같이 사용하는게 좋을 듯 해 보인다.
  - Posts 페이지로 이동 시키는 함수는 선택한 글이 몇번째 글인지를 같이해서 보냄

```js
	const goPosts = useCallback(
		(postNum) => {
			if (pathname === '/profile') {
				history.push({ pathname: '/profile/posts', state: { postNum } });
			} else if (pathname === `/user/${userName}`) {
				history.push({
					pathname: `/user/${userName}/posts`,
					state: { postNum },
				});
			} else {
				console.log('invalid location request');
			}
		},
		[hist
```

<br/>

- 선택한 이미지가 몇번째 글의 이미지인지 계산하기 (postNum)
  - 매개변수 postNum은 이미지 테이블 내에서 몇번째 row인지, row안에서 몇번째 인지를 계산해서 전달
  - ProfilePostImages 컴포넌트의 이미지 테이블은 1 row당 3개의 이미지가 들어가있음
  - 예를 들어, 5번째 이미지의 경우 row_index 1번 row의 index_in_row 1번에 해당하므로 `3 * row_index + index_in_row` 로 계산하여 전체 posts array에서 계산된 인덱스를 사용하여 해당 post를 가져올 수 있음

```js
// 생략 ...
return (
  <div className="post_table">
    {devidePosts(posts).map((row, index) => (
      <div className="posts_row" key={index.toString()}>
        {[0, 1, 2].map((i) =>
          row[i] ? (
            <div
              className="post_image_container"
              onClick={() => {
                goPosts(3 * index + i);
              }}
              key={`postTable/${row[i].postId.toString()}`}
            >
              <img
                src={row[i].postImageUrl}
                alt={"postImageUrl"}
                className="post_image"
              />
            </div>
          ) : (
            <div className="none_image" key={i.toString()}></div>
          )
        )}
      </div>
    ))}
  </div>
);
```

<br/>

- 들어온 postNum을 사용하여 해당 글로 스크롤 움직이기
  - 글들을 감싸는 글(post)의 부모 요소 ref로 접근
  - 부모 요소 ref의 children 접근하여 해당 순서의 글을 인덱싱
  - 인덱싱한 요소의 offsetTop 값을 가져옴
  - offsetTop에서 navigatino height 만큼 뺀 값만큼 `window.scrollTo()` 함수로 이동
  - `window.scrollBy()` 함수 이용시, transition을 줄 수 있음

```js
const scrollToPost = (postNum) => {
  if (!postNum) {
    return;
  }
  const targetRef = useRef();
  const top = targetRef.current.children[num].offsetTop;
  // window.scrollBy({
  // 	top: top - 54,
  // 	left: 0,
  // 	behavior: 'smooth',
  // });
  window.scrollTo(0, top - 54);
};
```

<br/>

## 다음에 필요한 사항

- [ ] profile 정보 요청 시기 조정
  - 더 빠른 연산을 위해서, 화면이 render 되고 profile에 관련된 정보를 가져오지 말고 profile 보기위해 버튼을 눌렀을 때 부터 미리 profile 정보를 요청하게 하자

<br/>

- [x] Post Detail View로 이동시 해당 글의 scrollX 위치로 이동하게 구현하기 (스타일링 이후에 scroll 위치 계산이 필요한 작업임)

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
