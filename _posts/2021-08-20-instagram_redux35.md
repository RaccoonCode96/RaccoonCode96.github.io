---
title: "20210820 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit35 : 좋아요 기능 구현, 좋아요 db 설계" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit35

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

# 2021.08.20 사항

<br/>

이전에 댓글 보기 버튼과 하트 모양의 좋아요 버튼을 구성하고 있는 PostControl이라는 컴포넌트를 만들었고, 기능적으로는 댓글 기능까지 추가가 완료 되었습니다.

오늘은 좋아요 버튼을 기능적으로 추가하였습니다. 처음에는 좋아요 기능이 단순하게 구현할 수 있을 거라 생각했지만, 그렇게 간단한 작업은 아니였습니다.

<br/>

## 1. 좋아요 기능 구현

<br/>

<p align="center">
<img src="../assets/img/likes_res.gif" width="600px" height="400px">
</p>

<br/>

좋아요 기능의 경우 단순하게 만든다면 쉽겠지만, 인스타그램을 클론하는 입장에서는 조금 다릅니다.

인스타그램의 경우 유저가 좋아요를 누르면 좋아요가 활성화(active) 되고, 다른 페이지를 갔다가 와도 계속 좋아요가 활성화(active) 상태여야 하기 때문입니다.

<br/>

> ### 사용자 입장에서 화면 유지 고려

<br/>

사용자 입장에서는 사용자가 직접 변경을 가한 상태가 유지된 상태로 사용하길 원합니다. 그래서 이전에 다른 탭으로 이동해도 이전 스크롤 위치를 기억하여 유지하도록 구현하였고, 이번 좋아요 기능도 사용자가 좋아요 버튼을 누른것을 기억해야 합니다.

- 이전 스크롤 기억 구현시 최초에 글(db 진입점)이 생성되면, 새로고침 또는 로고를 클릭하지 않는 이상 진입점을 새로 갱신하도록 하지 않게 하여 사용자의 지속적인 사용을 도울수 있도록 설계 하였습니다. (너무 최신을 보여주는 것도 불편할 수 있다는 생각이 들었습니다.)

이런 지속적 사용성을 위해서 이미 가져온 post 데이터는 해당 화면에서 나가서 작업하지 않는 이상 최신화 되지 않고, 다른 페이지에서의 수정 및 삭제 시에만 새로 최신화 시켰습니다.

- 댓글 작성, 글 작성 등의 경우 모두 다른 페이지에서 요청을 하고, 이후 진입점 갱신을 시행하여 post가 변경된것을 화면에 update 하였습니다.
- 이렇게 하면 다른 사용자도 사용하면서 갑자기 댓글이 변하고, 글이 변하는 현상을 직접 눈으로 보는 것을 막을 수 있기 때문입니다.

<br/>

> ### 판단

<br/>

물론, 화면적으로만 변하게 하고 페이지를 나갔을 때 최종적으로 사용자의 좋아요가 변경된 상태를 db에 요청 처리하는 것도 생각해 볼수 있습니다.

하지만, 좋아요가 활성화된 상태에서 무한 스크롤로 다른 데이터를 추가하여 다시 렌더링 하게 되면 기존의 활성화된 좋아요는 해제가 되게 됩니다.

**이러한 이유로 인스타그램의 좋아요 기능의 경우에는 사용자가 변경을 가하는 경우 db에 빠르게 반영되고 다시 화면에도 반영되어야 한다고 판단되었습니다.**

<br/>

> ### 좋아요 DB 설계

<br/>

좋아요 기능은 현재 유저에 따라 각 post에 좋아요를 했는지 안했는지를 구분하여 화면에 나타내야 합니다.

- 구분 방법 : like 데이터에 좋아요를 한 유저의 id를 가지게하여 로그인한 유저ID와 렌더링된 글의 좋아요 데이터에 있는 userId와 비교하여 상태를 나타냅니다.

```js
// 해당 postId의 like 데이터 예시
const like = {
  likeCount: 3,
  likeUsers: ["고유ID01", "고유ID02", "고유ID03"],
};
```

<br/>

### 좋아요 DB 관리 방식 생각하기

<br/>

좋아요 기능의 DB를 설계를 하면서 두 가지 경우를 생각했습니다.

- 경우1 ) post 데이터 안에서 관리할 것인가?
- 경우2 ) postId를 doc 이름으로 하여 따로 likes collection에서 관리 할 것인가?

<br/>

### 1) Post 데이터 안에서 관리

post 데이터 안에서 관리하는 경우에는 post에 이미 사용자의 이전 상태가 기록되어 있기 때문에 이전 상태값을 post 마다 연결하기 매우 수월하여 좋습니다.

<br/>

```js
// post에서 관리하는 db 구조
const post = {
  postText,
  postDate,
  userId,
  userPhotoUrl,
  userDisplayName,
  postImageUrl,
  commentArray: [],
  // 좋아요
  likes: {
    likeUsers: [], // 사용자 고유 id
    likeCount: 0,
  },
};
```

<br/>

하지만, 위에서 말한 것과 같이 db에 반영하면 반영한 결과를 바로 화면에 반영해야 합니다. 그렇다 보니 화면에 반영하려면 데이터를 새롭게 db에서 불러와야 합니다.

post에서 관리하는 데이터를 단지 좋아요 상태값 하나 때문에 다시 불러오게 되면 **큰 사이즈의 데이터를 다시 렌더링 시켜야 하고, 굳이 다시 렌더링 될 필요가 없는 부분도 다시 렌더링 되어야 함으로서 사용자의 사용성에 영향을 미치게 될것이라고 생각했습니다.**

<br/>

### 2) 새로운 collection으로 따로 관리 (postId 값으로 연결)

따로 관리 하게 되면, post 데이터는 그대로 있는 상태에서 좋아요 데이터만 요청하고 화면에 반영하여 렌더링 할 수 있게 됩니다.

대신, 각 post 마다 해당하는 like 정보를 맞춰서 주어야 함으로 정보를 맞추어 주는 작업에 시간을 소비해야하는 단점이 발생하긴 합니다. 그럼에도 다른 데이터와 독립적으로 빠르게 화면에 반영할 수 있다는 장점이 있습니다.

<br/>

```js
// like를 따로 관리하는 DB 구조 (Firestore)
// likes collection - like doc(docName: postId)
const like = {
  likeCount: 0,
  likeUsers: [],
};

// 화면에 필요한 like 데이터를 가져올 때 (Redux State)
const likes = docs.map((doc) => ({
  postId: doc.id,
  likeCount: doc.data().likeCount,
  isLike: doc.data().likeUsers.includes(uid),
}));

// [{postId, likeCount, isLike}, {postId, likeCount, isLike}, {postId, likeCount, isLike}, ...]
```

<br/>
<br/>

> ### like 데이터 지정 또는 가져오기 요청 함수

<br/>

- on, off 요청 함수는 통합하여 하나로 재구성 가능합니다.

```js
// Likes 가져오기
export const getLikesThunk = createAsyncThunk(
  "redux-racstagram/post/getLikesThunk",
  async (_, thunkAPI) => {
    try {
      const {
        profile: {
          currentUser: { uid },
        },
      } = thunkAPI.getState();
      const { docs } = await dbService.collection("likes").get();
      const likes = docs.map((doc) => ({
        postId: doc.id,
        likeCount: doc.data().likeCount,
        isLike: doc.data().likeUsers.includes(uid),
      }));
      return likes;
    } catch ({ code, message }) {
      return thunkAPI.rejectWithValue({ code, message });
    }
  }
);

// Like On 상태 지정하기
export const setLikeOnThunk = createAsyncThunk(
  "redux-racstagram/post/setLikeOnThunk",
  async (postId, thunkAPI) => {
    try {
      const {
        profile: {
          currentUser: { uid },
        },
      } = thunkAPI.getState();
      const doc = dbService.collection("likes").doc(postId);
      const prevDoc = await doc.get();

      await doc.set(
        {
          likeCount: prevDoc.data().likeCount + 1,
          likeUsers: [...prevDoc.data().likeUsers, uid],
        },
        { merge: true }
      );
      thunkAPI.dispatch(getLikesThunk());
      return true;
    } catch ({ code, message }) {
      return thunkAPI.rejectWithValue({ code, message });
    }
  }
);

// Like Off 상태 지정하기
export const setLikeOffThunk = createAsyncThunk(
  "redux-racstagram/post/setLikeOffThunk",
  async (postId, thunkAPI) => {
    try {
      const {
        profile: {
          currentUser: { uid },
        },
      } = thunkAPI.getState();
      const doc = dbService.collection("likes").doc(postId);
      const prevDoc = await doc.get();

      await doc.set(
        {
          likeCount: prevDoc.data().likeCount - 1,
          likeUsers: prevDoc
            .data()
            .likeUsers.filter((userId) => userId !== uid),
        },
        { merge: true }
      );
      thunkAPI.dispatch(getLikesThunk());
      return true;
    } catch ({ code, message }) {
      return thunkAPI.rejectWithValue({ code, message });
    }
  }
);
```

<br/>

> ### like 요청 함수 연결하기

<br/>

likes 데이터를 가져온 후, post에 맞는 like 정보를 선택하기 위해서 findeLike 함수를 만들었고 이를 실행하여 like의 초기 상태 값으로 설정할 수 있게 하였습니다.

<br/>

물론, 코드 자체는 복잡하지만 구현 해놓고 다시 리팩토링을 진행해야하는 부분입니다.

```js
// post 외부에서 getLikeThunk 요청 후 likes 데이터 가져와 사용
const likes = useSelector((state) => state.like.likes);

// likes에서 post에 맞는 like 찾기
const findLike = useCallback(() => {
  const like = likes.find((like) => like.postId === post.postId);
  return like;
}, [likes, post]);

// like 상태 초기값 설정 하기
const initIsLike = useMemo(() => findLike()?.isLike, [findLike]);
const [isLike, setLike] = useState(initIsLike);

// like 설정 요청 하기
const toggleDebounce = useMemo(
  () =>
    debounce((checked) => {
      if (initIsLike !== checked) {
        if (checked) {
          dispatch(setLikeOnThunk(post.postId));
          console.log("setLike : On");
        } else {
          dispatch(setLikeOffThunk(post.postId));
          console.log("setLike : Off");
        }
      }
    }, 900),
  [initIsLike, dispatch, post]
);

const onChange = useCallback(
  (event) => {
    const {
      target: { checked },
    } = event;
    setLike(checked);
    toggleDebounce(checked);
  },
  [toggleDebounce]
);
```

<br/>

> ### 완성 화면과 firestore

<br/>

- 하트를 누르면 db에 반영됩니다. (혹시 모를 사용자의 많은 수의 클릭 제어를 위해 debounce를 적용했습니다. db 반영은 900ms 뒤에 요청 합니다.)

<br/>

<p align="center">
<img src="../assets/img/likes_res.gif" width="600px" height="400px">
</p>

<br/>

- db 반영 모습

<p align="center">
<img src="../assets/img/likes_firestore.gif" width="600px" height="400px">
</p>
