---
title : "20210218_JavaScript09 Nomardcoder, DOM, Element, Event " #제목
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

>## JavaScript basic

- why js
- ES

<br>

- Variable
  - let var const를 활용 
  - 변수명은 calmel case(소문자로 시작해서 스페이스 필요하면 대문자를 섞어 씀)
  - 변수 작동 과정 : 생성(Create) -> 초기화(Initialize) -> 사용(Use)
    - `a = 12`인 경우 a는 변수 생성, = 12는 초기화 과정, 사용은 필요할때 a를 호출하여 활용

<br>

- Comment (`//` or `/*` )

<br>

- Data Type 
  - String
    - text
    - use with ""
  - Boolean
    - true or false
    - use lower
  - Number
    - 1,2.. number
  - Float 
    - number with dot

<br>

- Organizing data
  - Array 
    - list형태로 string을 하나로 묶음, 
    - use with [] 
    - 안에 string, boolean, numbers, floats 넣을 수 있음
    - `[index]`를 통해서 해당 index의 값을 호출 할 수있음
  - Object
    - dict형태 (index 대신에 key 값을 정해 필요한 값에 접근이 가능)
    - `{}` 를 사용함
    - 데이터 타입 및 Array, Object도 넣을 수 있음
  - 주의) `,`, `""`, 

<br>

- Function
  - 어떤걸 수행하려는 한부분을 원하는 만큼 쓸수 있게 하는 것
  - 인자 or 매개변수(argument or parameter) : 변수처럼 우리가 주는 값을 저장함(함수에게 외부의 데이터를 주는 방법)
  - 백틱 사용(문자 포멧팅) 
``` js
function sayHello(name, age) {
    return `Hello ${name}!, your age is ${age}  age`;
}
const greetRaccoon = sayHello("raccoon", 26);
console.log(greetRaccoon);
```

``` js
const calculator = {
    add: function(a, b) {
        return a + b;
    },
    sub: function(a, b) {
        return a - b;
    },
    mul: function(a, b) {
        return a * b;
    },
    div: function(a, b) {
        return a / b;
    }
}
console.log(calculator.add(1, 1))
```

<br>
<br>
<br>

>## JS DOM Functions

- js 에서는 document 함수를 통해서 html에 접근 가능함
- 즉, document 함수를 통해서 html document를 jasvascript객체로 만들어 버림
- 그래서 document 객체라고 `DOM(Document Object Module)`이라고 부름
- 객체이기 때문에 내부적으로 많은 key를 가지고 있음, 여러가지 함수, 프로퍼티, 등

<br>

- 예)
  - `document.getElementByID()` : html의 id를 가져옴 (그외 getElementByClassName, Name, TagName 등등 이 있음)
  - `document.querySelector()` : html의 원하는 요소를 선택해서 가져옴 안에 대신에 css와 같이 선택자를 넣어줘야 함 (class면 ., id면 # 등)
    - querySelector를 쓰면 더 자유롭게 뽑아 올수 있음
  - 이렇게 `DOM API`에 의해 document 객체에서 뽑힌 것을 `Element` 라고 함
  - 보통 `Element`를 변수에 담아 사용함
    - 그리고 Element 상태에서 또 활용할 수 있는 properties가 있음 이를 활용해서 필요한 값들에 접근할 수 있음
- 중요한것은 html 파일 자체를 변경하는 게 아닌 브라우저 상에서만 변화시킴

<br>

``` js
console.log(document);
const title1 = document.getElementById("title");
const title = document.querySelector("#title");
console.log(title)
title.innerHTML = "Hi! From JS"; // 해당 html에 string에 접근
title.style.color = "red"; // 해당 html의 css color에 접근(폰트)
console.dir(title); // dir로 찍으면 다나오니 그것을 보고 참고 해도 좋음
console.dir(document);
document.title = "I own you now"; // document 제목에 접근
```

<br>
<br>
<br>

>## JS Events and event handlers

- 하지만 위처럼 단지JavaScript가 html과 CSS를 바꾸고 싶어서 만들어 진 것은 아님
- 이벤트에 반응하기 위해서 만들어짐
- 이벤트 : 웹사이트에서 발생하는 것들 (click, resize, submit, input, change, load ..)
- 이 이벤트들이 발생하면 어떻게 하겠다를 javascript를 통해 할수 있음

<br>

- 그래서 일단 이벤트를 받는 함수인 `addEventListener()`를 활용해 특정 이벤트 이름과 특정 기능을 수행하는 콜백 함수를 param으로 받음

``` js
title.addEventListener("click", handleClick); // 해당 Element에 대한 click이벤트를 받기위해 대기하고 있고, click시 콜백 함수인 handleClick을 호출함
// 중요한 것은 callback()의 괄호를 넣으면 즉시 실행되기 때문에 이런 포맷으로 하면 안됨
window.addEventListener("resize", handleResize); // window는 dom의 상위 객체로서 브라우저라고 생각하면 편할 듯 하다. 브라우저 창이 resize 되면 함수 호출
```

- **callback 함수 정의**

``` js
function handleResize() {
    console.log("I have been resized")
}

"주의!"
 function handleResize(event) {
    console.log(event)
} // parm을 받으면 안됨, event는 event자체 변수 명이 이미 Event라는 property로 지정 되어 있기도 하고, 다른 변수를 쓴다고 해도 listener에서 조건을 기다리지 않기 때문에 안됨!
```

<br>

- 그러면, CSS요소를 건들수 있는데 이를 javascript에서 이용하여 기능을 수행해보자

- 보통 사용되는 Color들을 변수에 넣을 땐 변수명으로 모두 대문자로 씀

- 조건문을 통한 클릭시 글 색 변화 시키기

``` js 
"DOM 가져오기"
const title = document.querySelector("#title");

"색깔 변수"
const BASE_COLOR = "rgb(52, 73, 94)"
const OTHER_COLOR = "#1abc9c"

"Callbakc 만들기"
function handleClick() {
    const currentColor = title.style.color;  // 현재 color 담기
    if (currentColor === BASE_COLOR) { // 현재, base컬러 비교
        title.style.color = OTHER_COLOR;
    } else { title.style.color = BASE_COLOR; }
}

"실행 초기화 함수 만들기"
function init(){
    title.style.color = BASE_COLOR;
    title.addEventListener("click", handleClick); // addEventListener는 꺼지지 않음 계속 듣고 있는 상태임
}
init();
```

<br>
<br>

- 번외) Event 종류 중 offline, online -> 네트워크 연결, 끊김에 따라 호출

``` js
function handleOffline() {
    console.log("offline")
}

function handleOnline() {
    console.log("online")
}

window.addEventListener("offline", handleOffline)
window.addEventListener("online", handleOnline)
```

<br>
<br>
<br>

>## JS CSS 와 JavaScript의 영역 분할

- 하지만, 위처럼 javaScript에서 색상을 쓰게 되면 업무 영역이 혼재 되기 때문에 CSS는 CSS에서 다루고 class를 제어해서 컬러 제어를 구현 하는 것이 좋다.

- if 문 안에서 그냥 `title.className = "";` , `title.className = CLICKED_CLASS; ` 활용하면 다른 클래스를 사용하면서 계속 그 클래스가 존재해야 하면 기능에 문제가 생김
- ClassName을 여러개 가질 경우 list 형태를 가지므로 이에 대한 함수가 존재함
- `classList`형태에서 지원하는 함수들인 `remove`, `add`함수가 있음 그리고 `contain`이라는 함수도 지원하는데 이는 list안에 특정 className이 존재하는지 체크 할수 있음
- `remove`, `add`를 활용하여 없으면 넣고, 있으면 제거하는 조건을 걸음

``` js
const title = document.querySelector("#title");

const  CLICKED_CLASS = "clicked";

function handleClick() {
    const hasClass = title.classList.contains(CLICKED_CLASS);
    if(hasClass) {
        title.classList.remove(CLICKED_CLASS);
    } else {
        title.classList.add(CLICKED_CLASS);
    }
}


function init(){
    title.addEventListener("click", handleClick);
}
init();

// class 추가 제거를 제어해서 css를 제어
```

- 사실 위의 if문을 통한 remove, add기능은 따로 함수가 존재함(원리를 알기위해서 해본 것일 뿐)

``` js

function handleClick() {
    title.classList.toggle(CLICKED_CLASS); // toggle 함수 : class list에서 해당 class 이름이 있는지 없는지 체크해서 없으면 추가하고, 있으면 제거함
    }
function init(){
    title.addEventListener("click", handleClick);
}
init();
```