---
title: "20210831 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit38 : 좋아요 기능 보완을 위한 대대적인 코드 리팩토링, 좋아요 기능 보완" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit38

<br/>

<p align="center">
<img src="../assets/img/instagram_logo.png" width="400px" height="400px">
</p>

<br/>

> # 📄 프로젝트 안내

<br/>

해당 프로젝트에 관한 자세한 화면 개요 및 스타일, 상태 관리, 코드에 관한 사항은 [Github : RaccoonCode96/redux_racstagram ](https://github.com/RaccoonCode96/redux_racstagram)을 확인해 주세요.

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.08.31 사항

<br/>

## 좋아요 기능 보완을 위한 Post관련 리팩토링

<br/>

이전글의 '좋아요 기능 구현의 딜레마'에서 다루었던 문제를 해결하고자 Post와 관련된 데이터 요청 함수 및 redux state를 대대적으로 리팩토링하였습니다. 이를 통해 Post에 상응하여 구현해야하는 Like 관련 데이터 요청 함수 및 redux state를 그나마 깔끔하게 보완하여 구현할 수 있었습니다.

<br/>

먼저 Like를 구현하기 앞서 Like와 Post는 관계가 강하게 만들어져 있기 떄문에, Post를 조금더 간단하게 코드를 만들어야 Like 구현시에도 편할 것을 생각하였습니다. 그래서 Post의 코드를 줄이는 시도를 해보았습니다.

<br/>
<br/>

### 과거 구현 방식

Post 관련한 서버에 데이터를 요청 하는 함수(redux dispatch)는 4가지로 구성되어 있습니다.

- Dispatch
  - getAllPosts : 모든 글 요청하는 함수로 받아온 일정 개수의 데이터는 Home에서 활용됩니다.
  - getCurrentUserPosts : 현재 유저가 작성한 글만을 요청하는 함수로 받아온 일정 개수의 데이터는 Profile 페이지에서 활용됩니다.
  - getUserPosts : 특정 유저가 작성한 글만을 요청하는 함수로 받아온 일정 개수의 데이터는 User 페이지에서 활용됩니다.
  - getMorePosts : 무한스크롤 observer가 작동하여 getMorePosts를 트리거 하면 **각 페이지 특성에 맞는 데이터를 추가로 가져와 allPosts, currentUserPosts, userPosts State에 추가합니다.** (즉, 공유되어 재사용됩니다.)
- State
  - allPosts : getAllPosts로 받아온 데이터를 저장하는 redux state (Home과 연결)
  - currentUserPosts : getCurrentUserPosts로 받아온 데이터를 저장하는 redux state (Profile과 연결)
  - userPosts : getUserPosts로 받아온 데이터를 저장하는 redux state (User와 연결)

<br/>

> ### 시도1) getMorePosts 다른 함수에 통합하여 줄이기

<br/>

단순히 초기 데이터 몇개만 가져와 무한스크롤 동작 준비를 초기화 시키는 getAllPosts, getCurrentUserPosts, getUserPosts 함수에 getMore을 통합 시키는 시도를 해보았습니다.

- getMorePosts의 경우 getAllPosts, getCurrentUserPosts, getUserPosts를 통해 초기 데이터가 들어오고 나서 무한스크롤 observer가 동작하고 스크롤이 특정 하단 부분에 도착하면 getMorePosts가 트리거 되는 방식입니다.
- 위를 변형하여 getAllPosts, getCurrentUserPosts, getUserPosts에 기존 역할 수행하는 Init 상태, getMorePosts 기능을 수행할 More, None 상태를 구분하여 동작하도록 설계 하였습니다.

```js
// getMorePosts 기능이 흡수된 getAllPostsThunk
export const getAllPostsThunk = createAsyncThunk(
	'redux-racstagram/post/getAllPostsThunk',
	async (_, thunkAPI) => {
		try {
     const {post: {allPosts}} = thunkAPI.getState()

    // 데이터 끝값의 postDate
    const { postDate } = allPosts[allPosts.length - 1]

      let type // 각 상황에 맞게 reducer에서 처리되도록 하기 위한 type
			let qeury = dbService
				.collection('posts')
				.orderBy('postDate', 'desc')

      // 더불러올 기준 데이터 값인 postDate가 있는 경우 -> more
      if (postDate) {
        qeury = .startAfter(postDate)
        type = 'more'
      } else {
        // 처음에 가져온 데이터가 없는 경우 -> init (데이터 가져오기)
        type = 'init'
      }

     const {docs}  = await qeury.limit(6).get();

      // 더이상 가져올 데이터가 없는 경우
      if (!docs) {
        return {type: 'none'}
      }

			const posts = docs.map((doc) => ({
				postId: doc.id,
				...doc.data(),
			}));

			return {type, posts};

    } catch ({ code, message }) {
			return thunkAPI.rejectWithValue({ code, message });
		}
	}
);

// ------- ExtraReducers of redux-toolkit slice -----
const post = createSlice({
  name: 'post'
  initialState,
  reducers: {},
  extraReducer: {
  [getAllPostsThunk.pending]: (state) => ({
			...state,
			getAllPosts: { ...state.getAllPosts, loading: true },
		}),
		[getAllPostsThunk.fulfilled]: (state, { payload }) => {
      switch (payload.type) {
        case 'init':
          return {
        ...state,
        allPosts: payload.posts,
        getAllPosts: { ...state.getAllPosts, loading: false, isGet: true, getState: payload.type },
        }
        case 'more':
          return {
            ...state,
        allPosts: [...state.allPosts, ...payload.posts],
        getAllPosts: { ...state.getAllPosts, loading: false, isGet: true, getState: payload.type },
          }
        }
        case 'none':
          return {
            ...state,
        getAllPosts: { ...state.getAllPosts, loading: false, isNone: true, getState: payload.type },
          }
        default:
          return {...state}
        }
    },
		[getAllPostsThunk.rejected]: (state, { payload }) => ({
			...state,
			getAllPosts: {
				...state.getAllPosts,
				loading: false,
				getError: payload,
			},
		})
  }
})
```

<br/>

> ### 통합방식의 문제점

<br/>

위처럼 init, more, none type을 통해서 getMore 기능을 통합 시켰습니다.
하지만, 나중에 isNone 상태를 observer에 달아야 하는데 currentUser Profile, user Profile의 경우 하나의 컴포넌트를 재사용하여 공유하는데 isNone 상태를 각각 고려해서 달아주어야 하기때문에 기존의 isNone 부착 방식보다 까다로워 졌습니다.

**차라리 getMorePost 함수의 경우 처음에 구현한 방식이 더 맞는 방식인 것을 깨달았습니다.**

통합하는 과정에서 여러 기능을 같이 가지고 있다보니 더 복잡해짐을 느꼈으며 하나의 기능은 하나의 함수가 가지는게 옳바름을 다시 한번 느꼈습니다.

<br/>

### 다른 방식 생각하기

<br/>

과연, '조건을 복잡하게 만드는게 무엇일까?'를 생각해 보았습니다. 재사용성을 높이고자 한 컴포넌트에서 계속 서로 다른 것을 맞추려고 조건을 넣어 통합하려다 보니 더 복잡하게 만드는 것 같다고 생각이 되어졌습니다.

기존에 만들어진 재사용가능한 컴포넌트들을 다시한번 바라보며, pathname에 대한 조건식이 많다는 것을 인지하였고 path 통합 또는 상위 컴포넌트에서 페이지 상황에 맞는 function 및 state를 props로 전달하는 방식으로 개선해야 겠다고 생각했습니다.

애초에 처음부터 **현재 유저과 특정 유저의 Profile, Posts의 경우 공통점이 많아 통합했어야 했는데,** route pathname을 '/profile', '/user/:userId'로 나누고 시작하다 보니 이에 대한 데이터 요청 함수도 나누어서 구현하였던게 문제인것 같았습니다.

그래서 대대적으로 **현재 유저와 특정 유저에 관련된 항목은 모두 같은 route pathname으로 시작하여 같은 요청 함수를 사용하도록 리팩토링을 진행하였습니다.**

<br/>
<br/>
<br/>

> ### 시도2) 현재 유저와 특정 유저 관련 코드 통합

<br/>

currentUser, User 데이터 요청을 따로 나누는게 아닌 User로 통합하여 currentUser에 대한 코드를 없앴습니다. 단순히 글, 댓글, 프로필 수정에 대한 접근만 uid 비교를 통해 허용 하고 똑같이 user 로직을 사용하도록 변경하였습니다.

<br/>
<br/>
<br/>

> ### 시도3) 재사용하는 컴포넌트 내부 조건식 제거

<br/>

재사용하는 컴포넌트 내부의 조건식을 제거하고, **조건이 필요한 함수 및 state의 경우 컴포넌트 외부에서 prop으로 전달하여 동적으로 상황에 맞게 처리하도록 변경하였습니다.**

<br/>

> ### 결과

<br/>

결과적으로, 기존의 post 관련 dispatch 4개에서 3개로 줄여 졌습니다.

또한, getMorePosts는 내부적으로 All, CurrentUser, User 3개와 관련된 로직을 작성하였었는데 2개로 관련 로직이 줄여졌습니다.

- 변경 전 : getAllPosts, getCurrentUserPosts, getUserPosts, getMorePosts(all, currentUser, user)
- 변경 후 : getAllPosts, getUserPosts, getMorePosts(all, user)

<br/>
<br/>
<br/>

## 좋아요 기능 보완하기

<br/>

Post 관련 로직이 줄었기 때문에, getAllLikes, getUserLikes, getMoreLikes 3개의 distpatch를 구현하였습니다.
동작 시기는 Post와 똑같기 때문에 Post관련 dispatch 내부에서 병렬적으로 요청하는 방식으로 구현하였습니다.

<br/>

기존에 Like 상태를 변경하는 setLikeOnThunk, setLikeOffThunk 동작의 경우 db만 변경하고 다시 getLikes로 like 정보를 요청하는 방식이였습니다.

이 방식에 무한스크롤를 반영하게 되면 set하고 다시 get할 때 기존의 무한스크롤로 불려진 다수의 post 데이터들과 다시 불러온 like 데이터가 맞지 않게 됩니다.

<br/>

그래서, 다시 db에서 데이터를 불러오는 것이 아니라 **like 상태를 set하면 db에 반영하고 바로 redux state 값에도 반영하여 기존에 불러온 like 데이터들이 초기화 되지않고 화면에서도 값이 반영되게 하였습니다.**

또한, allLikes에 반영할지 userLikes에 반영하지 사전에 set요청시 pathName을 조건으로 type을 정해 type과 더불러올 like 기준 값을 같이 dispatch 인자로 넣어 실행시키도록 하였습니다.
