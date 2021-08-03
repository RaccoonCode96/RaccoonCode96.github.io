---
title: "20210801 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit19 : Taget container is not a Dom element 에러 해결, React 렌더링에 대한 고찰" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit19

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
  - [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요

<br/>
<br/>
<br/>

> # 📅 TIL (Today I Learned, 오늘 깨달은 것들)

<br/>

# 2021.08.01 사항

<br/>

## 작업 및 고찰

<br/>

- `Target container is not a DOM element 에러 발생 해결`
  - createPortal을 사용하면서 target을 설정하게 되는데, 이러한 에러를 발생시키는 주요한 원인을 찾아냈다.
  - Document를 직접적으로 찾거나 접근하려고 하는 경우에, element를 찾지 못하는 현상 발생 (document.querySelector 사용시 발생함)

<br/>

- `해결 방법 접근01`
  - 처음에는 render 문제가 아니라, document를 통한 taget 설정 때문에 일어난 것으로 파악하여 useRef를 통한 target 설정하는 것으로 시도 해보았다.
  - JS의 document를 통한 Element 조작은 react가 추측할 수 없는 상황을 만들어 버리기 때문에 사용을 자제하라고 한다.
    - React에서는 useRef의 사용을 권장하지만, useRef의 ref를 자유롭게 contextAPI, redux등의 전역 state 처럼 다른 Component에 전달하고 싶지만 그런 사용은 불가하다고 한다.
    - 그래서 제한적으로나마 forwardRef를 사용하려고 해보았지만, forawrdRef도 깊은 Component 구조에서는 복잡해지고 불편해 지는 것 같다. 따라서 createPortal에서의 자유로운 타게팅을 위해서 document.querySelector의 사용은 불가피 해 보였다.
    - **하지만, useRef가 이 에러를 해결해 주지도 않았다. useRef도 결국에는 초기에 render가 되어 일단 element가 생성되어있어야 Ref가 들어오기 때문이다. 결국에는 React의 Render 방식 때문이었다.**
  - 접근 방식 1번을 통해서 React에서의 render 방식에 의해서 문제가 생긴다는 것을 알아내었다.

<br/>

- `해결 방법 접근02`
  - 초기에 element가 없어 찾지 못한다면, element를 만드는 것은 어떨까라는 생각이 들었다.
  - 이와 같은 문제에 있는 다른 사람들은 직접적으로 document.createElement로 createPortal의 target을 만들고 unmount시 제거하는 방향을 제시하였다.
    - 하지만, 현제 프로젝트 상황상 재사용 가능한 컴포넌트로 사용하다 보니, 많은 post 하나 하나에서 발생하는 modal target이 생성되기 때문에 HTML이 지저분해지는 결과를 초래한다.

<br/>

- `최종 해결`
  - document.querySelector를 사용하고 react의 렌더 방식을 고려함
  - **target을 미리 JSX로 만들어 놓고 document.querySelector가 target을 찾는 때를 조작함**
  - hasMounted라는 state를 정의하고 useEffect가 실행되는 때 (즉, 모두 렌더된 때) state를 변경하여, state 값을 조건으로 document.querySelector를 실행하여 createPortal를 return 하게하여 렌더 되기 전에 document.querySelector를 통해 렌더 되지 않은 element를 참조시키지 못하게 함

```js
const Modal = ({ children, toggle, isOn }) => {
  const [hasMounted, setHasMounted] = useState(false);
  useEffect(() => {
    setHasMounted(true);
  }, []);
  if (!hasMounted) {
    return <></>;
  }
  const modal = document.querySelector(".modal_root");
  return ReactDOM.createPortal(
    isOn ? (
      <div className="modal" onClick={toggle}>
        <div className="modal_container">{children}</div>
      </div>
    ) : (
      <></>
    ),
    modal
  );
};

export default Modal;
```

<br/>

- `React 렌더링에 관한 고찰`
  - 에러발생의 이유는 React의 render순서 및 작동 방식에 대해서 잘 모르고 코드를 작성했기 때문이였다.
  - Component가 차례대로 렌더링 완료가 되는 줄 았았지만, 페이지를 구성하는 모든 Component가 한번은 렌더링이 모두 다 완료 되어야 그제 서야 모두 한번에 DOM이 화면에 render 되는 것이 였다.
  - 그렇기에 **하위 컴포넌트에 렌더 초기단계에 상위 컴포넌트는 당연히 렌더가 되었다고 생각하여 해당 Element를 참조하거나, 변경하는 것은 불가하다.**
  - useRef도 마찬가지로 이러한 매커니즘으로 작동하기 때문에 useEffect로 모두 mount가 되고 나서 참조를 해야 값이 들어오고, mount 되기 전에 useRef를 사용하게 되면, useRef의 ref객체의 current 값은 undefined로 초기화가 되게 된다.
  - 이러한 탐구 과정에서 궁금증이 생겨서, useEffect의 실행 순서 및 function Component의 render test를 해보았다.

<br/>

- `컴포넌트의 렌더링과 useEffect 실험`

<br/>

```js
const Parents = React.forwardRef((_, ref) => {
  console.log("outSide of Parents", ref);
  useEffect(() => {
    console.log("Parents:", ref);
  }, [ref]);
  return (
    <>
      <div>Parents</div>
      <Child ref={ref} />
    </>
  );
});

const Child = React.forwardRef((_, ref) => {
  console.log("outSide of Child:", ref);
  useEffect(() => {
    console.log("Child : ", ref);
  }, [ref]);
  return <div>Child</div>;
});

const Brother = React.forwardRef((_, ref) => {
  console.log("outSide of Brother", ref);
  useEffect(() => {
    console.log("Brother :", ref);
  }, [ref]);
  return <div>Brother</div>;
});

function App() {
  useEffect(() => {
    console.log("App : ", appRef);
  }, [appRef]);

  console.log("outSide of App", appRef);
  return (
    <div className="App">
      <Brother ref={appRef} />
      <Parents ref={appRef} />
    </div>
  );
}

/*
컴포넌트 구조 
- App
  - Brother
  - Parents
    - child

함수 실행 순서
App -> Brother & Parents -> Child
useEffect 순서
child -> Brother -> Parents -> App

outSide of App {current: undefined}
outSide of Brother {current: undefined}
outSide of Parents {current: undefined}
outSide of Child: {current: undefined}

----- 모두 실행 된 이후에 모든 Element가 생성 됨 ---------------------------
----- 그리고 useEffect의 실행이 stack의 돌아오는 방식으로 실행됨 ------------

Brother : {current: div}
Child :  {current: div}
Parents: {current: div}
App :  {current: div}

즉, 함수의 stack과 동일하게 작동함 
(형제 컴포넌트로 나뉘는 경우에는 더이상 return할 자식 컴포넌트가 없는 경우 그때 서야 모두 DOM이 생성됨)
*/
```

<br/>

## 다음에 필요한 사항

<br/>

- [ ] Navigation-profile 눌렀을 때 로그아웃, 프로필 수정, 프로필 이동 드롭 다운 필요

<br/>

- [ ] profile 정보 요청 시기 조정
  - 더 빠른 연산을 위해서, 화면이 render 되고 profile에 관련된 정보를 가져오지 말고 profile 보기위해 버튼을 눌렀을 때 부터 미리 profile 정보를 요청하게 하자

<br/>

- [ ] Post Detail View로 이동시 해당 글의 scrollX 위치로 이동하게 구현하기 (스타일링 이후에 scroll 위치 계산이 필요한 작업임)
