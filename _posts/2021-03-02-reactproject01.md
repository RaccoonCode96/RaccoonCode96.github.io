---
title : "20210302_React.js01 React 환경설정, 작동원리, App 만들고 실행, component 만들고 사용, JSX" #제목
category : #카테고리
tag : #태그
    - React.js
    - React 환경설정
    - React 작동원리
    - React App
    - React Component
   
   
toc : true #옆에 목차
---

># React.js and React project(영화 웹 서비스 만들기) with nomadcoder

&nbsp;&nbsp; 저번에는 자바스크립트 기초강의의 todolist 크롬 앱을 구현했는데 이번에는 React를 배우고 입문하기 위해서 노마드 코더의 react js로 만드는 영화 웹서비스 만들기 강의를 수강하였다.
물론, 무료이다. 정말 좋은 강의 이고 저번 todolist 강의에서 처럼 어떤 형태로 쓰이는지 전체적인 감이 올것이라고 생각한다.

<br>

- [노마드 코더 사이트!](https://nomadcoders.co/?gclid=Cj0KCQiA4feBBhC9ARIsABp_nbVxRSolGl8kEqrti8PVF9rv5l4jJxXcNIGeNSUaDEm6zqH79HPTH5kaAitcEALw_wcB)

<br>

- 단지, 노마드 코더 강의를 듣고 공부 과정을 서술하듯 쓸 것이다. 본 자료를 무턱대고 믿지는 말았으면 좋겠다. 내가 이해하는 대로 쓴 것일뿐이니...


<br>
<br>
<br>


># project를 위한 환경설정

1. node.js 설치
- node.js 홈페이지에서 설치 가능(cmd에 `node -v`를 쳐서 설치되었는지 확인 가능)

2. npm 설치 확인
- 보통 nodejs 설치시 같이 설치됨 (cmd에서 `npm -v`로 확인 가능)

3. npx 설치
- cmd 에서 `npm install npx -g` 를 쳐서 설치
- `npx -v` 로 확인 가능

4. VSC 설치

5. git 설치 (`git --version`)


<br>
<br>
<br>

># 사전 지식

- html
- css
- javascirpt(바닐라 js)


<br>
<br>
<br>

># React 사용 이유

- 페이스북에 의해 만들어짐 (대부분 거의 모든 회사들이 기본 framework로 사용)
- 예) 에어비엔비, spotify, netflix, slack ...
- 2018년 프론트엔드 생태계 설문(64%사용 압도적임)
- react 커뮤니티가 잘되어 있음
- js로 되어 있어 좋음 (없어지더라도 js 가 기반으로 되어있기 때문에)
- angular는 새로 배워야 하기 때문에 react와 대조됨

<br>

&nbsp; 즉, 유명하고, 인기많고, 점점더 유명해 져서(잠재력)

<br>
<br>
<br>


># React 사용 시작(APP 만들기와 실행)

- React만으로는 앱을 못 만들음(현대적인 코드(아름다운 코드)와 함꼐 동작하기 때문에)
- 원래는, Browser 스스로 이런 아름다운 코드를 이해 못하기 때문에 이해할수 있는 못생긴 코드로 만들어줘야함
  - 1. Webpack 다운로드
  - 2. Babel 다운로드

<br>
<br>

>## React로 App 만들기

- **Create react app**이라는 것 덕분에 이러한 위의 과정들이 필요 없게 됨
  - 하나의 명령을 실행해서 React Web app을 set up할수있게 함

- cmd -> 원하는 폴더에서 `npx create-react-app 앱 이름`  -> 해당 폴더안에 app 뼈대 설치 완료
- 후에 npm을 통한 여러가지 명령어가 추가됨
  - `npm start` , `npm run build`, `npm test`, `npm run eject`

- VSC에서 만든 앱 폴더를 열기
- README 파일에 간단하게 소개글 적기
- `package.json` 파일에서 scripts 부분을의 명령 확인
  - `test`, `eject`는 지우기 (이 프로젝트에서는 사용 안함)
- `npm` 는 `yarn`과 동일함 

<br>
<br>

>## React로 App 실행하기

- **Application 실행** (`npm start`)
  - `Compiled successfully! You can now view movie_app in the browser.`   
  - 브라우저가 실행되어 확인 가능함
  - 2개의 url을 줌 (local, network) 
    - local은 우리 컴퓨터 자신을 의미 host
    - network는 접속된 wifi를 의미 (wifi에 접속된 친구 또는 폰에 테스트 하고자 하면 사용가능)

<br>
<br>
<br>


># React App github 연동

- `git init`
- `git remote add origin 깃헙 repo 주소`
- `git add .` , `git commit -m"메세지"`, `git push -u origin master`


<br>
<br>
<br>

># React App 폴더 구성과 교육을 위한 초기 파일 설정

>## 폴더 구성

  - `node_modules` 폴더 : 건들지 말것 일단.
  - `public` 폴더 : 
    - `favicon` :  document title icon임
    - `index` : 그냥 index html 파일임
    - `manifest.json` : PWA인데 코스에서 벗어나므로 나중에 설명함(알아보고 싶으면 알아봐라)
    - 그외의 여러가지
  - `src` 폴더 : `serviceWorker.js(reportWebVitals.js)`, `index.js`, `logo` 등등 많다.

<br>

>## 초기 파일 설정 (지워야 할 것)

**일단, 기본부터 설명하기 해서 대부분을 지운다고 함**

- **src 폴더**
  - `index.js` 에서 `import index.css`, `import reportWebVitals`, `주석과 reportWebVials 함수`를 지움

- `logo.svg`, `reportWebVitals.js`, `setupTests.js`, `index.css`, `App.test.js`, `App.css`  파일 지움 
- -> 오직 `App.js`, `index.js`만 남김
- App.js 파일 -> import react만 남기고 나머지 import 삭제
- App 함수안에 div태그만 남기고 header 없애기
- npm start로 실행시키면 기존의 react 로고 등의 모양은 없어지고 백지의 상태로 창이 나타남
- 그리고 App.js 안의 함수에 html 요소들을 변경하면 바로 반영됨


<br>
<br>
<br>

># React 동작 원리

- App.js파일의 함수를 통해서 html요소를 만들어 브라우저의 localhost에서 나타낼때는 public 폴더의 index.html 기본베이스와 합성하여 보여줌(실제로 파일에 추가되는 건 아니고) 

- react : html에 쓰는 모든 요소를 생성한다는 것임 (index.html에 element를 넣는 역할)

- `index.js` 파일을 확인해 보면 Dom요소를 처리하는 함수가 있고 id root를 가지고 있다. 이것은 `index.html`의 body에 기본적으로 들어가는 div id root와 **연결되어 있어서 id를 둘다 맞춰줘야 에러없이 동작한다.**

- react는 소스코드에 처음부터 html을 넣지 않고, html에서 html을 추가하거나 제거하는 법을 알고 있음. (소스코드 = index.html )

- virtual DOM (virtual document object model) : 가상 돔을 리엑트가 만들어냄

- 그래서 기존에 요소가 존재하지 않기에 react가 빠름


<br>
<br>
<br>

># React 살펴보기

- 브라우저에 react가 계속 연동되게 하려면 `npm start` 실행후 console를 죽이면 안됨 (terminal kill X, close는 상관 없음) -> 서버를 계속 유지해야함

<br>

>## component 개념

- `component`
- 우리는 component를 보기 좋게 만들고 component가 data를 보기 좋게 보여줌
- HTML을 반환하는 함수 임 (App.js안에 있는 함수임) 
- **컴포넌트를 사용할때의 표현은** index.js 의 `<APP />` 을 말함 (react는component와 함께 동작하고 모든것이 component임)
  - `<APP />`과 같은 표현으로 javascript와 html사이의 위와 같은 조합을 `jsx`라고 함 (유일하게 react에서만 나오는 개념)

<br>

>## component 만들기

- src안에 새로운 파일 만들기 (ex. potato.js)
- component를 작성할때 마다 처음에 `import React from "react"`를 써줘야 만들어지는 함수가 jsx element로 만들어짐 (없으면 jsx가 있는 component를 사용하는 것을 이해 못함)
- 함수명은 대문자로하고 마지막에 밖에서 참조하기 위해서 `exprot default Potato`를 명령


>## component 사용하기

- react application이 하나의 component만을 rendering 해야함
- `<App />` 하나만 index에 연결시키고 App.js에 Potato.js 연결시켜 같이 표시시기키 
  - 이렇게 index에서 다중 연결이 안되기 때문에 App에서 다른 component를 원하는 대로 import 하여 사용하면 됨

``` js
import React from 'react';
import Potato from './Potato';


function App() {
  return (
    <div>
      <h1>Hello!!!</h1>
      <Potato />
    </div>
  );
}

export default App;

```



<br>
<br>

- **최신 버전에는 다중 component 렌더링을 지원** 
- 근데 왜? `import potato` 하고 `<App />,<Potato />,`는 될까?

``` js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import Potato from './Potato';

ReactDOM.render(
  // <React.StrictMode>
    <App />,<Potato />,
  // </React.StrictMode>,
  document.getElementById('root')
);
```

- 그리고 최신 버전에서는 자동으로 `improt React from 'react';`를 해줌(생략 가능)