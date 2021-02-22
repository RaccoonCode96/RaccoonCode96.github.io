---
title : "20210222_JavaScript10 Nomadcoder " #제목
category : #카테고리
    - JavaScript
tag : #태그
    - JavaScript
    - DOM
    - Event
    - Element
   
   
toc : true #옆에 목차
---

- js가 프로젝트에서 어떻게 사용되어지는지 알기 위해서 노마드 코더의 강의를 들었고 단지, 내 수준에서 필요한 것만 회고하듯 정리함
- [노마드 코더 강의](https://nomadcoders.co/courses) 잘되어 있으니 듣길 바란다.

># JavaScript with Nomadcoder

<br>

># JavaScript project

- nomadcoder에서 진행하는 이 강의의 프로젝트는 클론 코딩이다.
- 클론 대상은 [momentum](https://chrome.google.com/webstore/detail/momentum/laookkfknpbbblfpciffpaejjkokdgca?hl=ko) 이라는 Todolist 페이지 이다.
- 특징은 가운데 시계가 있고, 배경이 변하고, todolist기능, 날씨 정보기능이 있다.

<br>
<br>
<br>


># JavaScript 파일 셋팅

- 보통 웹사이트 처럼, html, css, js로 구성된다.
- 기능들은 js 한파일에 모으지 않고 기능별로 나누어 구성하여 html 연결한다.
- 기능 : 
  - 사용자 이름 입력시 인사(greeting.js)
  - 시계 기능 (clock.js)
  - 배경 화면 변화 (bg.js)
  - To Do list 기능 (todo.js)

<br>

- `<link rel="stylesheet" href="index.css">` : css 연결
- `<script src="clock.js"></script>` : js 연결 (body 끝에 둘 것!)
- 보통 기능 구현시 정보제출 요청을 구하기 때문에 `form`태그로 구성

<br>

- **html**

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="index.css">
    <title>Document</title>
</head>
<body>
    <div class="js-clock">
        <h1>00:00</h1>
    </div>
    <form class="js-form form">
        <input type="text" placeholder="What is your name?" />
    </form>
    <h4 class="js-greetings greetings"></h4>
    <form class="js-todoForm">
        <input type="text" placeholder="Write a to do" />
    </form>
    <ul class="js-todoList">
    </ul>
    <script src="bg.js"></script>
    <script src="clock.js"></script>
    <script src="greeting.js"></script>
    <script src="todo.js"></script>
</body>
</html>
```

<br>
<br>
<br>


># clock.js (시계 기능)

- 시계 기능구현은 현재의 시간을 계속 가져와서 업데이트 하면 된다.
- 특정 클래스, 태그 html 돔으로 가져오기

``` html
<div class="js-clock">
    <h1>00:00</h1>
</div>
```


``` js
const clockContainer = document.querySelector(".js-clock"),
	clockTitle = clockContainer.querySelector("h1");
```

- 초기화 시키기 
  - `getTime` 시간 가져오기
  - `setInterval` 1000 밀리세컨(1초)에 한번씩 getTime 콜백 함수 실행(즉, 업데이트)

``` js	
function init() {
	getTime();
	setInterval(getTime, 1000);
}

init();
```

- 시간 가져와서 화면에 표현하기
- Date class를 통해서 date를 인스터화 시킴
- minutes, hours, seconds 를 각각 가져옴
- ClockTitle은 h1 태그인데 innerText로 내용을 바꿈
- Ternary 연산자(삼항 조건 연산자)를 통해서 1자리 숫자도 두자리 숫자로 보이게 포맷 변경 
  - 조건 ? true : false

``` js
function getTime() {
	const date = new Date();
	const minutes = date.getMinutes();
	const hours = date.getHours();
	const seconds = date.getSeconds();
	clockTitle.innerText = `${hours < 10 ? `0${hours}` : hours}:${
		minutes < 10 ? `0${minutes}` : minutes}:${
		seconds < 10 ? `0${seconds}`: seconds
	}`;
}
```


<br>
<br>
<br>



># greeting.js (인사 기능)

- 사용자가 이름을 넣으면 입력받은 사용자 이름을 활용하여 hello 사용자 이름 출력 
- 입력 받은 내용을 기억하고 있어야 새로고침시에도 초기화 되지 않아야 하므로 로컬 스토리지 사용
- `로컬 스토리지` : 자바스크립트의 작은 정보들(데이터 set)을 Object 형태의 key와 value 묶음으로 브라우저 local storage에 저장 (새로고침 해도 그대로 남아 있음)
- **이를 통해서 데이터를 불러 올 수 있음 (근데 저장하는 데이터는 무조건 string형태) 그래서 Object의 내용을 온전히 표현하려면 JSON을 사용**

<br>
<br>

>## init 함수, 출력 (초기화 설정)

-  기본적인 표시 화면 구성을 위한 초기화

``` js
function init() {
    loadName();
}

init();
```

<br>

>## loadName 함수 만들기 (스토리지 관련 및 기본 출력)


- 특정 string을 사용할때도 변수에 할당해서 사용하는 것이 나중에 수정하기 좋음
  - `USER_LS` : 로컬 스토리지 user
- localStorage 기본변수를 통해서 `getItem` 함수를 이용하여 해당 key를 통해서 value를 가져와 할당
- 로컬 스토리지에 사용자 이름이 없으면 입력을 받게하여 최종 화면을 출력하고
- 이름이 있으면 그 이름을 활용하여 최종 화면을 출력

``` js
const USER_LS = "currentUser"

function loadName() {
	const currentUser = localStorage.getItem(USER_LS);
	if(currentUser === null) {
		askForName();
	} else {
		paintGreeting(currentUser);
	}
}
```

<br>
<br>
<br>


>## paintGreeting 함수 만들기 (화면 표시)

- html에서 최종 화면에는 form의 input은 안보이게 하고 greeting만 보이게 해야 하므로 css 에서는 showing class 조건을 display block으로 하고 greeting은 display none으로 지정하여 최종적으로는 js를 통해 class 추가 제거하여 보이거나 안보이게 함 

- html

``` html
<form class="js-form form">
        <input type="text" placeholder="What is your name?" />
</form>
<h4 class="js-greetings greetings"></h4>
```

- css

``` css 
.form,
.greetings {
  display: none;
}

.showing {
  display: block;
}
```

- **js**

  - 기본적으로 html을 dom요소로 가져오기위해서 `document`, `querySelector`사용 
  - 해당 클래스의 ClassList를 건드려서 form input은 안보이게, greeting은 보이게하고 `innerText`를 통해서 html 태그 안에 들어가 text를 설정  

``` js
const form = document.querySelector(".js-form"),
	input = form.querySelector("input"),
	greeting = document.querySelector(".js-greetings");

const SHOWING_CN = 	"showing";
function paintGreeting(text) {
	form.classList.remove(SHOWING_CN);
	greeting.classList.add(SHOWING_CN);
	greeting.innerText = `Hello! ${text}`; 
}
```

- 해당 이름에 맞게 표시하기 위해서는 storage에서 이름을 받아와야 함
- 그래서 loadName함수에서 `currentUSer` 를 넣음


<br>
<br>
<br>


>## askForName 함수 만들기 (input 표시 및 이벤트 받기)

- 스토리지에 currentUser가 없어서 input을 받아야 하므로 input form이 표시되게 ClassList를 조정함 (SHOWING_CN 클래스이름을 추가)
- form의 `addEventListener`를 통해서 submit(제출)하고 호출될 콜백함수를 `handleSubmit`으로 해놓음

``` js
const form = document.querySelector(".js-form"),
	input = form.querySelector("input");
const SHOWING_CN = 	"showing";
function askForName() {
	form.classList.add(SHOWING_CN);
	form.addEventListener("submit", handleSubmit);
}
```


<br>
<br>
<br>


>##  handleSubmit 함수 만들기 (제출한 이벤트 storage 저장 및 화면 출력)

- `event.preventDefault()` 를 통해서 submit은 작동하지만 새로고침을 방지
- `input.value`를 통해서 input 값을 가져옴
- `paintGreeting` 인사 출력 창에 값을 넣어 표시 되게 연결
- `saveName`이라는 스토리지 저장 함수에 input 값을 넣어 저장 실행

``` js
function handleSubmit(event) {
	event.preventDefault(); // event를 금지해놔서 enter눌러도 값이 이동하지 않음
	const currentValue = input.value;
	paintGreeting(currentValue);
	saveName(currentValue);
}
```

<br>
<br>
<br>


>##  saveName (스토리지 저장 함수)

- `setItem`을 통해서 key : USER_LS에 value : text 저장

``` js
function saveName(text) {
	localStorage.setItem(USER_LS, text);
}
```


<br>
<br>
<br>


># todo.js (todolist 기능)

>## init 초기화 함수 (기본 화면 출력, submit 요청)

- greeting과 다르게 따로 받고 표시하는 과정이 다른 시간에 있을 필요는 없으므로 같은 화면에 input과 출력을 모두 표시 

- 출력은 ul 태그 안에 li를 넣어서 표현

``` html
<form class="js-todoForm">
        <input type="text" placeholder="Write a to do" />
</form>
<ul class="js-todoList">
</ul>
```

<br>

- html 가져와서 돔으로 변환하기

``` js
const toDoForm = document.querySelector(".js-todoForm"),
    toDoInput = toDoForm.querySelector("input"), // 충돌 방지를 위한 변수 이름 다르게 지정
    toDoList = document.querySelector(".js-todoList");
let idNumber = 1
const TODOS_LS = "toDos";
```

<br>

- 기본 출력은 `loadToDos`로, 제출은 event 받아서 `handlesubmit` 함수 실행

``` js
function init() {
    loadToDos();
    toDoForm.addEventListener("submit", handleSubmit)
}

init();
```

<br>

- **handleSubmit 함수 (입력 받아서 스토리지 연결)**
  - `preventDefault`를 통해 새로고침 막고 event는 currentValue로 가져오고 `paintToDo` 함수로 출력
  - `ToDoInput의 value를 공백`으로 만들어 실행 됨을 알려줌

``` js
const toDoInput = toDoForm.querySelector("input"),
function handleSubmit(event) {
    event.preventDefault();
    const currentValue = toDoInput.value;
    paintToDo(currentValue);
    toDoInput.value = "";
}
```


<br>
<br>
<br>


>## loadToDos 함수 (있는거 활용해서 스토리지 연결)

- 스토리지에서 TODOS_LS 이름의 key value 담기
- 빈값이 아니면 JSON으로 된 스토리지 value를 가져와 분석하여 parsedToDos에 살려서 담음
- parse된 value는 Array이기 때문에 `forEach`를 활용하여 Array를 구성하는 인자인 object를 하나씩 뽑아 가져와 콜백함수에 넣어 실행
- 콜백함수는 Object의 text이름의 key value를 가져와서 `paintToDo`인 본격적인 화면 표현 함수에 넣음 

``` js
const TODOS_LS = "toDos";
function loadToDos() {
    const loadedToDos = localStorage.getItem(TODOS_LS);
    if (loadedToDos !== null) {
        const parsedToDos = JSON.parse(loadedToDos);
        parsedToDos.forEach((toDo) => {
            paintToDo(toDo.text);
            });
        }
    }
```

<br>
<br>
<br>



>## paintToDo 함수 (화면 출력)



- 예전에 `getElement`가 dom으로 불러와 class를 읽는 것이라면,
- `createElement`는 거꾸로 html에 class만들어 쓰는 것이다.
- ul 태그안에는 각각의 li태그가 있고 li 태그는 delBtn과 span요소로 구성되게 만듦

``` html
<form class="js-todoForm">
    <input type="text" placeholder="Write a to do" />
</form>
<ul class="js-todoList">
    <li>
        <button></button>
        <span></span>
    </li>
</ul>
```

<br>
<br>


- 각 요소를 만드는 것을 변수에 담고
- delBtn의 내용은 X 이모티콘을 가지게 하고, `addEventListener`를 통해서 이벤트를 받고 클릭시 항목을 제거하는 함수를 실행하게 함
- span은 text를 구성하게 함
- li 태그 안에 span, delBtn 요소가 들어갈수 있게 `appendChild`를 사용
- 또 li태그가 ul에 들어갈수 있게 `appendChild`사용
- 그리고 delBtn을 눌렀을 때 그것에 맞는 li가 삭제 될수 있게 id를 붙임

<br>

- **id를 붙이는 방법은 다양함**
  - `toDos.length + 1` 사용시 : storage에 저장시 필요한 Array를 활한것임
    - 다만, 중간 이나 처음의 항목을 제거하고 새로 만들면 id가 중복되는 현상발생함
  - `Date.now()` 사용시 : 날짜를 사용해서 겹칠수가 없으나, 컴퓨터 새로고침이 워낙에 빨라 같은 id가 발생함
  - `idNumber` 사용시 : id root번호를 지정해서 누를때마다 1씩 증가시켜 id로 저장하면 됨, 새로고침시에도 중복 되지 않음

- 스토리에 저장하기 위해서 객체를 만들어야 함 (`todoObj` text : text, id: number) 구조

- 입력한 값을 받아서 todoObj 객체를 만들어 toDos Array에 push하여 넣음
- `saveToDos`를 통해서 storage에 저장 

``` js
let idNumber = 1
let toDos = [];
function paintToDo(text) {
    const li = document.createElement("li");
    const delBtn = document.createElement("button");
    const span = document.createElement("span");
    let newId = idNumber
    // const newId = toDos.length + 1; // 중간이나 처음 id를 지우면 중복 id가 생김
    // const newId = Date.now()// 새로 고침시 같은 아이디 
    delBtn.innerText = "❌";
    delBtn.addEventListener("click", deleteToDo);
    span.innerText = text
    li.appendChild(delBtn);
    li.appendChild(span);
    li.id = newId;
    toDoList.appendChild(li);
    const todoObj = { // 이런식으로 따로 Obj를 품은 array를 만드는 이유는 local storage에 save해야 되서
        text: text,
        id: newId
    };
    toDos.push(todoObj);
    saveToDos();
    idNumber += 1
}
```

<br>
<br>
<br>

>## saveToDos 함수 (스토리지 저장)

- toDos의 value가 Obj이기 때문에JSON형태로 문자화 시켜서 storage에 TODOS_LS key에 value로 저장함

``` js
function saveToDos() {
    localStorage.setItem(TODOS_LS, JSON.stringify(toDos)); // 스토리지의 toDos키에 계속 값을 찍어줌 toDos가 생기는 것이 아니라 toDos key의 vaule가 계속 없뎃됨
}
```

<br>
<br>
<br>

>## deleteToDo 함수 (delhtn 눌렀을 경우 화면에서 제거, 스토리지 저장)

- event.target은 이벤트 버블링의 최하위 요소를 반환함
  - 이벤트 버블링이란 이벤트 발생시 상위 요소로 이벤트가 전달되는 것을 말함
  - 즉, 여기선 한단게 위 부모 요소라고 보면 됨
- 햇갈려서 dir 찍어보니 li를 반환하는 `parentNode`를 찾음
- `toDoList.removeChild(li);`를 통해서 ul태그에서 li 요소 제거
- `filter`를 통해서 조건에 맞는 함수만 모아 Array 구성
  - filter 조건에서 toDo.id num이지만 li.id의 경우 string이여서 `parseInt`를 통해서 num으로 변환해서 비교
- `cleanToDos`는 제거한 요소를 제외한 나머지 계속 남아 있어야할 Array 임
- 제거를 누르 요소를 제외한 `cleanToDos` Array를 원래 `toDos`에 (할당시켜서)업데이트 -> saveToDos로 스토리지에 저장

``` js
const toDoList = document.querySelector(".js-todoList");
let toDos = []; // 변해야 됨으로
function deleteToDo(event) {
    const btn = event.target;
    const li = btn.parentNode;
    toDoList.removeChild(li);
    const cleanToDos = toDos.filter((toDo) => {
        return toDo.id !== parseInt(li.id); // li.id 가 string으로 찍혀서 parseInt를 이용해서 number로 바꿈
    });
    // const reDos = [];
    //  for (const value of cleanToDos) {
    //     reDos.push({text: value.text, id: reDos.length + 1})
    // } 
    // toDos = reDos;
    toDos = cleanToDos;
    saveToDos();
}
```

<br>
<br>
<br>

># bg.js (배경화면 랜덤화)

>## 초기화 init

- img 개수 지정, dom으로 가져오기

``` js
const body = document.querySelector("body");
const IMG_NUMBER = 5;
function init() {
    const randomNumber = genRandom();
    painImage(randomNumber)
}

init();
```

<br>
<br>

>## getRandom 함수 (랜덤 숫자 뽑기 함수)

- 내장함수 Math 활용
- random 0.0000000000 ~ 1.00000000000 사이의 숫자를 random하게 내보냄 (자리수는 정확하지 않음)
- 그래서 보통 해당 숫자를 만들기 위해서 `floor`버림 , `ceil`올림을 통해서 자리수를 만들고, `*`를 통해서 범위를 정함

``` js
const IMG_NUMBER = 5;
function genRandom() { // floor 버림, ceil 올림
    const number = Math.floor(Math.random() * IMG_NUMBER)
    return number;
}
```

<br>
<br>

>## paintImage 함수 (img 표시)

- img class를 활용하여 img 객체를 만듦
- img 객체의 properties를 설정함

- `append()` : 컨텐츠를 선택된 요소 내부의 끝 부분에서 삽입
- `prepend()` : 콘텐츠를 선택한 요소 내부의 시작 부분에서 삽입
- `after()` : 선택한 요소 뒤에 컨텐츠 삽입
- `before()` : 선택된 요소 앞에 컨텐츠 삽입


``` js
function paintImage(imgNumber) {
    const image = new Image();
    image.src = `/nomadcode/project/imgs/${imgNumber + 1}.jpg`;
    image.classList.add("bgImage");
    body.prepend(image);
    // image.addEventListener("loadend", handleImgLoad) // API에서 한다면 이렇게

}
```

- `z-index : -1;` 을 통해서 배경을 뒤로 보냄
- `@keyframes` css에서 함수같은 느낌으로 애니메이션 설정시 키프레임으로 정의해야함
  - 좀더 디테일하게 설정가능함
- fadeIn을 지정해서 사이트 배경을 불러올때 흐림효과주어 자연스러움

- [CSS 애니메이션 관련 사이트](http://www.tcpschool.com/css/css3_transform_animation)



``` css

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.bgImage {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
  animation: fadeIn 0.5s linear;
}
```

