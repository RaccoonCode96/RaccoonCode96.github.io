---
title: "20210811 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit27 : 무한스크롤 구현, IntersectionObserver, 불필요한 데이터 요청 제거 및 데이터 요청 시기 조정, 코드 중복 제거를 위한 통합에 대한 고찰" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit27

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

# 2021.08.11 사항

<br/>

## 작업개요 및 고찰

<br/>

### 1. 무한 스크롤 구현

<br/>

<p align="center">
<img src="../assets/img/infinite_scroll.gif" width="600px" height="400px">
</p>

<br/>

- 무한 스크롤 구현 방식 2가지
  - 방식1) clientHeight + scrollTop = scrollHeight를 이용하는 방식
    - 하지만, 이런 방법을 사용하면 계속해서 DOM에 접근하기 때문에 성능 이슈가 발생함
  - 방식2) IntersectionObserver 방식
    - Observer를 설정하여 target Element를 설정하여, target이 viewPort에 보이는 경우를 감지하여 필요한 함수를 실행시킴
    - 성능적 낭비가 없으며, 현재 MDN에 실험적인 기능으로 올라와 있다.

<br/>

- **방식2 : IntersectionObserver 방식 선택**

<br/>
  
- 전체적인 동작 방식
  - (1) 기본적으로 일정 개수의 데이터를 요청하여 가져와 화면에 나타냄
    - 이때, target도 이미 JSX로 render 되고 ref로 target을 가져올 수 있게함
  - (2) render 이후 targetRef가 들어온 이후 IntersectionObserver의 target에 Observer의 observe를 실행시킴
  - (3) target이 화면에 보여 감지되는 경우 현재 state에 있는 데이터의 마지막 데이터 이후 부터의 데이터를 일정 개수 요청하도록 하는 함수 실행
  - (4) 요청 반복
  - (5) 요청 반복후 더이상 가져올 데이터가 없는 경우, error message 값을 state로 올림
  - (6) error message가 발생하면, target에 설정된 intersectionObserver의 Observe를 unobserve 또는 disconnect 메서드를 실행시켜 Observe를 끔
    - 이때, 더 이상 데이터가 없음을 표시함

```js
const component = () => {
  const target = useRef(null);
  const _onIntersect = useCallback(
    ([{ isIntersecting }]) => {
      if (getError.message) {
        return;
      }
      if (isIntersecting) {
        getMorePosts();
      }
    },
    [getMorePosts, getError]
  );

  useEffect(() => {
    let observer;
    // 더이상 불러올 자료가 없는 경우
    if (getError.message) {
      observer && observer.disconnect();

      // target이 있고, 처음에 불러온 데이터가 있는 경우 observe
    } else if (target && posts.length) {
      observer = new IntersectionObserver(_onIntersect, {
        rootMargin: `1px`,
        threshold: 0.5,
      });
      observer.observe(target.current);
    }
    return () => {
      observer && observer.disconnect();
    };
  }, [getError, _onIntersect, posts]);

  return (
    // 여러 가지 posts 코드 생략
    <div ref={target}></div>
  );
};
// 아직, 개선이 필요한 코드임
```

<br/>

- 신경쓴 부분
  - 최대한 모바일 환경과 비슷한 경험을 주도록 신경씀
  - 사용자가 다른 페이지(탭)로 이동하여도 뒤로가기 버튼시에는 기존에 보던 **스크롤 위치를 유지**하고자 함
  - 유저의 스크롤에 의해 계속 요청되어 보여진 데이터도 따로 reset 또는 다시 불러들이지 않도록 하여 **server 요청 낭비를 제어**하고자 함
  - 그리하여, 로그인 이후 처음에만 가장 최근 데이터를 가져오게 하여 무한 스크롤 진입점을 제시하고 무한 스크롤을 통한 데이터 요청하도록 함

<br/>

- 문제점
  - 하지만 위처럼 하게 되면 다른 유저에 의해서 update된 데이터를 새로 갱신할 시점을 정해야함 모바일 환경에서는 보통 Pull down refresh 기능 또는 새로운 게시물 UI 버튼을 표시하여 사용자가 직접 refresh를 유도하게 함

<br/>

- 아이디어
  - 데이터 진입점 갱신을 **racstagram 로고 클릭**을 통해 하거나, 사용자 로그인 이후 일정 시간 마다 진입점 이후 update된 게시물이 있는지 확인하여 **새 게시물 보기 버튼**을 화면에 띄워 사용자가 진입점 갱신을 선택하도록 개선 할 수 있음

<br/>
<br/>
<br/>

### 2. 불필요한 데이터 요청 최적화

- 로그인 이후 기본적으로 필요한 데이터를 미리 요청함
  - 기존에는 render이후 useEffect로 요청하는 형태 였음 그렇기에 처음 부터 바로 표시하지 않고 빈 값을 화면에 뿌리고, 그 이후 요청에 의한 데이터가 들어와 다시 render 시키는 형태 였음 (render만 2번 일으킴)
  - 로그인 이후 보여질 화면에서 요청하는게 아닌 그 전에 요청을 하기 위해서 Router 부분에서 로그인 true가 되는 경우 요청하도록 함
  - randomUserInfo, currentUserInfo, currentUserPosts 요청
- 기존에 페이지에서 요청하는 불필요한 데이터 요청 코드 제거
- 글을 수정, 작성, 삭제 등 또는 프로필 변경이 있을 경우에만 필요한 데이터를 재요청하도록 함

<br/>
<br/>
<br/>

## 재사용성과 중복 제거에 대한 고찰

<br/>

코드를 짜면서 개발자는 항상 개발 입장에서 코드를 재사용할 수 있게 하는 것을 목표로 한다. 그래서 내가 코드를 짤때는 어느정도 중복(보일러 플레이트 코드)가 발생하는 경우 이것들을 합칠까 생각하고, 합치기도 한다.

<br/>

**과연 중복 제거를 위해서 합치는게 과연 옳은 판단인지 생각하게 된다.**

<br/>

예를 들어, A 컴포넌트 와 B컴포넌트가 있다고 가정하면 나는 코드를 작성하였고 확인해 보니 A와 B가 내용적으로 많이 겹치는 부분이 있지만 조금씩 다른 부분도 존재하였다. 그러면, A와 B를 합칠 것을 고려하게 된다.

하지만, A와 B가 조금 다른 것을 고려하여 C라는 컴포넌트로 합치는 경우 A와 B를 고려한 코드 작성에 의해서 A, B에 대한 조건을 C에 많이 작성하게 되는 것 같다.

<br/>

물론, 내가 이 프로젝트를 하면서 큰 계획없이 대략적인 계획만 세워놓고 만들었기 때문에 그럴 수도 있다고 생각은 한다.

(_동시에 계획이 얼마나 중요하지 새삼 느끼게 되었다. 또, 실무에서의 제품 개발에 계획을 어떻게 구성하는지 궁금하고 좋은 선임 개발자에게 배우거나 동료와 토의하여 경험해 보고 싶다는 생각이 들었다._)

<br/>

**그럼에도 불구하고 기능 추가와 유지 보수는 필연적으로 발생하는 상황이라고 본다.**

<br/>

이러한 상황 때문에 A, B의 차이를 C에서 다루기 위해 C에서의 **로직이 너무 복잡해 지고 다른 개발자가 보았을 때 이해하기가 힘들어지며 본인도 개발 과정에서도 추적하기가 어려워 지는 것 같다.**

<br/>

이를 위해서, 컴포넌트를 더 잘게 잘게 잘라서 관리 해야 하는 것일 까?
어쨌거나 중복제거를 위한 통합은 신중하게 결정해야 하는 것 같다.

<br/>

이런 고찰 중에 개발 방법론의 애자일 방식(필연적인 기능 추가와 유지 보수 발생)과 처음부터 엄청난 설계를 통한 개발 방식인 워터폴 방식이 떠올랐다. 둘 중에 정답은 없으며, 요즘 트렌드는 애자일이라고는 하지만 장단점이 있는것 같다.

<br/>
<br/>
<br/>

## 다음에 필요한 사항

<br/>

- [ ] 랜덤 유저 개선하기 : useRandom 제외 값 지정하게 변경하기

<br/>

- [ ] 새게시글 보기 버튼 또는 로고 클릭시 데이터 진입점 갱신 기능 구현하기

<br/>

- [x] profile 정보 요청 시기 조정
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

- [ ] route '/profile' pathName을 '/user/:userName' pathName 사용하게 통합하여 pathname에 대한 조건을 줄여 보자

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
