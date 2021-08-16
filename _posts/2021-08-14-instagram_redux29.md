---
title: "20210814 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit29 : 댓글 기능을 위한 Component 및 페이지 Component 설계,  Comments DB 설계, Redux Comment Slice 설계 및 Read, Create 구현" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit29

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

- [x] `무한 스크롤`
  - [x] 유저 스크롤 위치 기억 (뒤로가기가 아닌 페이지 변해도 기억 함)

<br/>

- [x] `랜덤 유저 추천`

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.08.14 사항

<br/>

## 1. 댓글 기능을 위한 Component 및 페이지 Component 설계

### 개요

- 각 글을 박스(Post Component)안에서 댓글 보기로 이동하는 버튼을 클릭시 해당 글에 대한 댓글 페이지로 이동
- 댓글 페이지는 댓글을 작성하는 Form 과 해당 글에 작성된 댓글을 보여줌

### 라우터 설정

- 기존의 라우터에 Comments 페이지를 연결하는 라우트를 추가 함
- 각 글마다 존재하는 postId를 동적으로 path 이름의 부분으로 사용함

```js
<Route path="/:postId/comments" exact component={Comments} />
```

<br/>

### 컴포넌트

<br/>

- PostControl : 모든 글 마다 해당 글의 댓글을 보도록 이동시키는 버튼 및 좋아요 버튼을 다룸
  - PostControl 컴포넌트의 경우 Post 컴포넌트에서 사용 됨
  - PostControlContainer 컴포넌트와 짝을 이룸 (goComments 함수를 전달 함)

<br/>

- Comments : 사용자가 확인하려는 글에 대한 댓글들과 댓글을 입력하는 Form을 보여주는 페이지
  - CommentForm : 댓글을 작성하는 Form을 표시하는 컴포넌트 (CommentFormContainer와 짝을 이룸)
  - Comment : 댓글 하나의 모양을 표시하는 컴포넌트 (Comments 페이지에서 Comment Array를 받아 map 함수를 통해 각각의 댓글이 Comment 컴포넌트로 전달 됨)

```js
// improt ... 생략

const Comments = () => {
  const {
    state: { post },
  } = useLocation();
  const comments = useSelector((state) => state.comment.comments);

  return (
    <>
      <Navigation />
      <div className="page">
        <div className="inner">
          <main className="main comments_main">
            <CommentFormContainer postId={post.postId} />
            <ul className="comments">
              {comments.map((commentObj) => (
                <Comment commentObj={commentObj} key={commentObj.commentId} />
              ))}
            </ul>
          </main>
          <Side />
        </div>
      </div>
      <div className="modal_root"></div>
    </>
  );
};

export default Comments;
```

<br/>

## 2. Comments DB 설계

<br/>

- Comments DB 설계는 두 가지 방식을 생각해 보았다.

<br/>

### 방식 1) Post 데이터 안에서 Comments 데이터 관리하는 방식

- 댓글의 경우에는 사실 각 글에 존재하는 데이터로 Posts Collection에 각 Post 안에 Comments라는 필드 형식으로 만들어도 된다.
- 단, Comments 필드는 Comment를 요소로 갖는 Array 형태이어야 한다.
- 이렇게 중첩적으로 데이터를 구성하면, Comment가 몇개 안되면 상관 없겠지만 많아질 수록 Post 하나가 가지는 데이터의 크기가 너무 커지게 되어 **단순히 Post와 관련된 정보로만 빠르게 Home 화면을 구성하기에 정보를 가져오는 속도가 느려지게 된다.**

<br/>

### 방식 2) Posts와 Comments 데이터 분리하여 관리하는 방식

- **방식 1의 대안으로 Posts와 Comments를 분리하여 관리하는 것이다.**
- 대신 Comments를 분리하여 관리하려면 Comments의 각 Comment들은 PostId를 가져야 한다. 이를 통해서, 어떤 Post에 작성된 Comment인지 식별할 수 있다.
- 또한 이렇게 분리하면, Comment가 많아져도 Post에서 정보를 가져오는데 부담을 가지지 않게 된다.

<br/>

### comment의 데이터 구조 (방식 2 채택)

- 기본적으로 각 comment의 고유 ID는 firebase doc 단위에서 자동으로 doc 이름을 만들게 함
  - 나중에 data를 가져오게 되면, doc.id를 가져와서 반복되어 화면에 표시되는 요소의 key로 활용할 수 있음
- postId : 작성되어진 글의 고유 id
- userDisplayName : 댓글을 작성한 유저 이름
- userPhotoUrl : 댓글을 작성한 유저 Profile image
- commentDate : 댓글을 작성한 시간
- comment : 댓글 내용
- count : 해당 post에서 작성된 현재 댓글의 순번 (댓글 개수를 빠르게 파악하기 위함)
  - count 필드는 동시에 같은 글에서 댓글을 작성하는 경우 어떻게 될지 우려가 되긴 함

```js
const commentObj = {
  postId,
  userDisplayName: currentUserInfo.displayName,
  userPhotoUrl: currentUserInfo.userPhotoUrl,
  commentDate: Date.now(),
  comment,
  count: comments[0] ? comments[0].count + 1 : 0,
};
```

<br/>

## 3. Redux Comment Slice 설계 및 Read, Create 구현

<br/>

- Comment와 관련된 서버 요청 및 전역 state를 사용하기 위해서 Redux-toolkit의 Slice 방식으로 Comment module을 만듦
- 기본적으로, Comment의 CRUD를 생각하고 있고 무한 스크롤 형식의 getMore도 구현할 예정
- 주의 해야 할점은 post를 뿌려 주는 화면에도 대표적인 댓글 2개 정도는 보여주어야 함으로 post에도 comment 데이터가 필요하다.
  - comment를 작성 요청하는 경우, 댓글이 post 데이터에도 제일 최근 comment 데이터 2개 정도는 입력되고 계속 update 되게 해주어야 한다.
- 일단, post에 대한 로직을 생각하지 않은체 기본적인 Comment의 Read와 Create를 구현하였다.

<br/>

- Initail State

```js
// Initial State
const initialState = {
  comments: [],
  setComment: {
    loading: false,
    isSet: false,
    setError: "",
  },
  getComments: {
    loading: false,
    isGet: false,
    getError: "",
  },
};
```

<br/>

- Create 요청

```js
export const setCommentThunk = createAsyncThunk(
  "redux-racstagram/comment/setCommentThunk",
  async ({ comment, postId }, thunkAPI) => {
    // 댓글 내용과 postId를 인수로 받아옴
    try {
      // 작성자 정보와 이미 최근에 작성된 댓글의 count 가져 옴
      const {
        users: { currentUserInfo },
        comment: { comments },
      } = thunkAPI.getState();

      // firestore에 작성될 Comment 데이터 구조
      const commentObj = {
        postId,
        userDisplayName: currentUserInfo.displayName,
        userPhotoUrl: currentUserInfo.userPhotoUrl,
        commentDate: Date.now(),
        comment,
        count: comments[0] ? comments[0].count + 1 : 0, // 데이터를 받아올때 commentDate의 내림차순으로 가져오기 때문에 0번 인덱스가 최근 Comment임
      };

      // firestore에 해당 comment 데이터를 작성하게 요청
      await dbService.collection("comments").doc().set(commentObj);
      thunkAPI.dispatch(getCommentsThunk(postId)); // 보고 있던 화면을 다시 렌더링 해주기 위해서 요청함
      // comment 작성 요청에 대한 결과 true를 isGet에 update 함
      return true;
    } catch ({ code, message }) {
      return thunkAPI.rejectWithValue({ code, message });
    }
  }
);
```

<br/>

- Read 요청
  - firestore에서 자동적으로 만드는 doc.id를 가져와서 commentId로 활용함

```js
export const getCommentsThunk = createAsyncThunk(
  "redux-racstagram/comment/getCommentsThunk",
  async (postId, thunkAPI) => {
    try {
      const { docs } = await dbService
        .collection("comments")
        .where("postId", "==", postId)
        .orderBy("commentDate", "desc")
        .get();
      const comments = docs.map((doc) => ({
        commentId: doc.id,
        ...doc.data(),
      }));
      return comments;
    } catch ({ code, message }) {
      return thunkAPI.rejectWithValue({ code, message });
    }
  }
);
```

<br/>
<br/>
<br/>

## 다음에 필요한 사항

- [ ] 댓글 좋아요 기능 -> 진행중
  - [ ] 기본적인 comments page, comment, commentForm 스타일링
  - [ ] comment read, create 요청 함수 컴포넌트에 연결 하기

<br/>

- [ ] 자동 input 체크 (이름 중복 확인시)

<br/>

- [ ] 새 게시글 보기 버튼 또는 로고 클릭시 데이터 진입점 갱신 기능 구현하기

<br/>

- [ ] validation 구현 필요함
  - input 같은 경우, display none 적용시 browser에서 제공하는 validation 말풍선이 뜨지 않기 때문에 따로 구현 필요함
  - required를 사용하지 말고, submit 함수 단에서 input값이 들어 왔는지 체크하여 validation error 구현 필요
  - [x] post 관련한 input의 check 대략적인 (PostUpdateContainer, postFormContainer)
  - [x] auth 관련한 input의 check 대략적인 조건 구현
  - [ ] 각 input 별로 데이터 형태에 따른 구체적인 조건 설정이 필요함
    - [ ] 이메일, 패스워드, 유저 네임, 글 내용의 형식(조건, 제한) 지정 필요

<br/>

- [ ] 스켈레톤 UI 필요

<br/>

- [ ] propType으로 type 지정 또는 typeScript 도입

<br/>

- [ ] sementic tag 적절한 태그로 수정하기 (검토)

<br/>

- [ ] 효과적인 렌더링 제한을 위해서 container에 있는 함수들을 hook으로 만들어 구현하기

<br/>

- [ ] route '/profile' pathName을 '/user/:userName' pathName 사용하게 통합하여 pathname에 대한 조건을 줄여 보자

<br/>

- [ ] profileUpdateContainer과 postFormContainer 통합 시도

<br/>

- [ ] 글 작성 시간 (클라이언트 단에서 뿌리는 경우 로컬 시간 변경으로 조작 가능한지 테스트 필요함)

<br/>
<br/>
<br/>

# 나중에 구현하고 싶은 기술

<br/>

- [ ] 댓글 기능
- [ ] 좋아요 기능
- [ ] 유저 이름 검색을 통한 프로필 보기 (이름 검색)
- [ ] 게시글 장소 태그로 장소 지도 보기 (지도 API)
