---
title : "20210226_JavaScript14 Nomadcoder-ToDoList project 끝, CSS, js추가 기능 설명  " #제목
category : #카테고리
    - JavaScript
tag : #태그
    - JavaScript
    - CSS
    - Nomadcoder
    - project
    - 기능추가
   
   
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
- 현재 해당 강의는 모두 들어서 프로젝트는 끝냈으나 추가적인 기능 및 CSS를 추가하여 마무리 하였다.

<br>
<br>
<br>

># CSS 적용하기

- **기본적으로 dorder-box 단위 처리**

``` css 
* {
  box-sizing: border-box;
}

```

<br>
<br>

- **기본 폰트 size 설정**

``` css

html {
  font-size: 40px;
}
```

<br>
<br>

- **h1태그인 시계들어 가는 부분**
  - 가운데 정렬을 하기 위해서 margin  auto를 줌 (반응형이어도 가운데 알아서 맞음)
  - 그리고 반응형으로 하기 위해서 글씨는 `vw`를 통해서 뷰 크기에 따라 설정되게 했다.

``` css
h1 {
  font-weight: normal;
  font-size: 9vw;
  margin: 20vw auto 0;
}
```

<br>
<br>

- **username, todo input 폼**
  - 일단 기본적으로 기존의 경우에는 input이 question까지 포함하고 있었다. 하지만 원래 사이트의 경우 question과 input이 분리되어 있어서 구현했다.
  - input에서 text-align center은 안에 들어가는 text를 가운데로 정렬함
  - border은 0, focus시에 보이는 border같은것도 지우기 위해서 `outline` transparent로 처리했음
  - 그래도 input위치는 알아야 하기에 border bottom은 살려서 보이게 했음 style은 `solid`로 실선 처리
  - 그외 규격은 `vh`, `vw`를 통해서 맞추었다.
  - 그리고 block 처리하여 다른 요소와 같은 라인에 섞는것을 방지 했다.
  - background는 transparent 또는 0으로 투명 처리 가능하다.

``` css
input {
  text-align: center;
  height: 5vh;
  width: 50vw;
  margin: 0 auto;
  padding: 0 20px 5px 20px;
  display: block;
  background: 0;
  outline: transparent;
  border: 0;
  border-bottom: 3px solid aliceblue;
  color: aliceblue;
  font-size: 2vw;
}
``` 

<br>
<br>

- **question 부분과 인사하는 부분**
  -  반응형을 위해 적정한 size인 3vw로 설정했다.

``` css

.question-name {
  font-size: 3vw;
}

.question-todo {
  font-size: 3vw;
}

.js-greetings {
  font-size: 3vw;
  margin: 0.3vw;
}
```

<br>
<br>

- **버튼**
  - cusor를 pointer로 바꾸어서 위에 올리면 손가락 표시가 생기게 설정
  - 동그라미 x 버튼을 만들기 위해서 border-redius를 100%를 해서 원은 만듦
  - 그리고 요소가 컨테이너의 가운데 위치하게 하기 위해서, vertical-ailign middle로 설정하였음


``` css
button {
  cursor: pointer;
  background: transparent;
  font-size: 0.5vw;
  color: aliceblue;
  border-color: aliceblue;
  border-radius: 100%;
  margin-right: 10px;
  vertical-align: middle;
}
```

<br>
<br>

- **todo 항목 이름, 날씨 요소 이름**

``` css
span {
  margin: 0;
  padding: 0;
  font-size: 1.5vw;
  vertical-align: middle;
}
```

<br>
<br>


- **todolist 컨테이너**
  - `overflow hidden`을 통해서 todo가 너무 많으면 표시되지 않도록 하여 스크롤바가 생기는 것을 방지함

``` css
.js-todoList {
  height: 50vw;
  overflow: hidden;
  margin: 0;
  padding: 0 20px 0;
}
``` 

<br>
<br>

- **todo 항목(버튼, 이름 포함)**
  - 여기서 제일 중요한 작업인 `inline-block` 처리를 하여서 기존의 세로형 배치에서 가로형 배치로 바꾸었다.

``` css
li {
  display: inline-block;
  margin: 0.5vw 1vw;
}
```

<br>
<br>


- **기본 body**
  - font 설정
  - 구글 폰트를 사용해서 html link를 걸어줌

``` css

body {
  overflow: hidden;
  min-width: 400px;
  background-color: #353b48;
  color: white;
  text-align: center;
  font-family: Roboto, Helvetica, Arial, sans-serif;
}
```

<br>
<br>

- **날씨 위치**
  - position absolute
  - top, right로 기준 정해줌

``` css
.js-weather {
  position: absolute;
  top: 20px;
  right: 20px;
}
```

<br>
<br>

- **javascript를 위한 항목 숨기기 보이기**

``` css
.form,
.greetings {
  display: none;
}

.showing {
  display: block;
}
```

<br>
<br>

- **배경화면**
  - 배경화면
    - 배경화면은 width, height값을 100%로 주어 브라우저에 꽉차게 만들어 놓음
    - position은 absolute와 z-index를 -1로 지정함으로써 뒤로 위치하게 시켰다.
    - 그리고 반응형 웹에 맞게 줄여도 비율을 유지하면서 작아지게 하기 위해서 `object-fit`을 사용했다.
  - animation
    - fadeIn이라는 keyframes을 통해서 적용할 animation 스타일을 커스터마이징 하여 animation에서 사용
    - 사용시에는 from 에서 to 로 변화되는 시간을 같이 옆에 적어주고 스타일 변화 스타일도 적용가능하다.
    - 여기에서는 linear로 일정하게 변화되게 설정 되었다.

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
  min-width: 500px;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: -1;
  animation: fadeIn 0.5s linear;
}
```



<br>
<br>
<br>

># js를 통해 추가한 기능

- **user 이름이 없으면 todolist input 안뜨게 하기**
  - 즉, 이름값이 없으면 무조건 이름을 넣어야 todolist기능을 사용할 수 있게
  - 그래서 기본적으로 display none상태인 form class를 todolist class에도 넣어 기본적으로 안보이게 하고 이름을 넣어 submit하여 paintGreeting 함수 호출시에 toDoForm classlist에 showing class를 넣게 하여 보이게 하였다.

``` js
function paintGreeting(text) {
	const toDoForm = document.querySelector(".js-todoForm");
	form.classList.remove(SHOWING_CN);
	greeting.classList.add(SHOWING_CN);
	greeting.innerText = `Have a good day!  ${text}`;
	toDoForm.classList.add(SHOWING_CN); 
}
```

<br>
<br>

- **user이름이 없으면 todolist 데이터 모두 지우기**
  - 처음 로드 될때 user이름이 있는지 확인을 하는데 없을경우 `removeItem`을 통해서 로컬 스토리지 toDos key와 value를 없애 초기화 시키는 코드를 넣었다.

``` js
function loadName() {
	const currentUser = localStorage.getItem(USER_LS);
	if(currentUser === null) {
		const TODOS_LS = "toDos"; 
		localStorage.removeItem(TODOS_LS);
		askForName();
	} else {
		paintGreeting(currentUser);
	}
}
```

<br>
<br>

- **todolist input 빈값 추가 못하게 하기**
  - 빈값 체크해서 빈값은 실행안되게 했음

``` js
function handleSubmit(event) {
    event.preventDefault();
    const currentValue = toDoInput.value;
    if (currentValue !== "") {
        paintToDo(currentValue);
        toDoInput.value = "";
    }
}
```

<br>
<br>

- **날씨 받아와서 이모티콘으로 표현**
  - 기존의 날씨 정보에서 description을 받아와서 해당 이모티콘을 반환하게 만듦

``` js
const iconObj = {
    "clear sky" : "☀️",
    "few clouds" : "⛅",
    "scattered clouds" : "☁",
    "broken clouds" : "☁",
    "shower rain" : "☔",
    "rain" : "☂",
    "thunderstorm" : "⚡",
    "snow" : "❄️",
    "mist" : "💦",
}

function getWeather(lat, lon) {
    fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${API_KEY}&units=metric`) // 백틱 조심
    .then((response) => {
    return response.json();
    })
    .then((json) => {
        const weatherObj = json.weather["0"];
        const dsWeather = weatherObj.description;
        const temperature = json.main.temp;
        const place = json.name;
        weather.innerText = `${place} \n ${temperature} ℃ ${iconObj[dsWeather]} `;
    })
}

```






