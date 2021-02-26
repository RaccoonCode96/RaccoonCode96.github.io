---
title : "20210223_JavaScript12 Nomadcoder-ToDoList project 날씨 기능 구현하기, 프로젝트 완료!  " #제목
category : #카테고리
    - JavaScript
tag : #태그
    - JavaScript
    - 날씨기능
    - Nomadcoder
    - project
   
   
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

># 실시간 날씨 가져오기(weather.js)

- 현재의 경도 위도를 가져와서 경도위도의 좌표를 통해서 날씨open API를 이용해서 현재 위치의 날씨 정보를 불러와서 나타낼 것이다.


>## 초기화 함수 (init)

- `Coords`는 좌표 뜻을 가진 단어임

``` js
function init() {
    loadCoords();
}

init();
```

<br>
<br>

>## loadCoords 함수(스토리지 정보 연결)

- storage key이름 coords로 설정하기 위해서 변수 설정
- getItem을 통해서 storage의 값을 받아옴
- 받아온 값이 존재하지 않으면 좌료를 뽑아오는 함수(`askForCoords`)를 실행
- 값이 존재하면 해당 값을 가져와서 `getWeather` 날씨 정보 가져오는 함수에 넣어 실행



``` js
const COORDS = 'coords';
function loadCoords() {
    const loadedCoords = localStorage.getItem(COORDS);
    if (loadedCoords === null) {
        askForCoords();
    } else {
        const parseCoords = JSON.parse(loadedCoords);
        getWeather(parseCoords.latitude, parseCoords.longitude);
    }
}
```

<br>
<br>

>## askForCoords 함수(현재 위치 좌표 뽑아서 콜백함수 실행)

- navigator 객체는 사용기기 및 사용자 정보에 관한 것들을 가져옴
- [MDN - Navigator](https://developer.mozilla.org/ko/docs/Web/API/Navigator)
- navigator의 geolocation 속성은 사용자의 좌표를 구해줌
- geolocation 속성의 `getCurrentPosition` 메서드를 통해 장치의 현재 위치 조사하여 geolocationPosition 객체로 반환하는데
- getCurrentPosition 메서드 param으로 콜백을 받기에 콜백함수를 통해 값을 제어할 수 있음

``` js
function askForCoords() {
    navigator.geolocation.getCurrentPosition(handleGeoSucces, handleGeoError)
}
function handleGeoError() {
    console.log("Can't access geo location!");
}
```
<br>
<br>

>## handleGeoSucces 함수 (오브젝트를 받아 표시할 데이터를 제어하여 보냄)

- askForCoords에서 받아온 position 객체를 위도 경도로 나누어 변수에 할당
- 위도 경도를 가진 좌표 object를 만듦
- saveCoords를 호출해서 Obj를 스토리지에 저장
- getWeather를 호출해서 위도 경도를 화면에 표시하게 함

``` js
function handleGeoSucces(position) {
    const latitude = position.coords.latitude;
    const longitude = position.coords.longitude;
    const coordsObj = {
        latitude: latitude,
        longitude: longitude
    };
    saveCoords(coordsObj);
    getWeather(latitude, longitude);
}
```

<br>
<br>

>## saveCoords 함수 (좌표를 스토리지에 저장)

- 역시나 obj이기 때문에 스토리지에서 표현이 안보임
- 그래서 JSON으로 바꾸어서 저장함

``` js
function saveCoords(coordsObj) {
    localStorage.setItem(COORDS, JSON.stringify(coordsObj));
}
```

<br>
<br>

>## getWeather 함수 (좌표를 통해 날씨 데이터를 받아와서 화면에 표시)

- web API : 다른 서버로부터 손쉽게 데이터를 가져올 수 있는 수단임(오직 데이터만 가져옴)
  - 특정 웹사이트로 부터 데이터를 얻거나 컴퓨터 끼리 소통하기 위해서 고안됨

<br>

- [무료 Wether API 사이트](https://home.openweathermap.org/api_keys)
- [current weather data API Docs](https://openweathermap.org/current#data)

<br>

- 가입해서, API를 사용할 수 있는 개인 API key를 받아야함
- API의 정보를 가져올 때 쓰는 함수는 `fetch`로 fetch  안에 해당 사이트 주소를 가져와 넣고 여러 변수들을 string 포맷팅하여 넣으면 됨

<br>

- fetch를 통해서 잘 받아오는지 확인하기 위해서 브라우저 개발자 툴의 network 패널을 본다.
- network 패널(브라우저 개발툴 안에) : API를 통해서 우리가 request한 내용을 보여줌 (preview 또는 response 탭 확인)

<br>

- API의 경우는 서버에서 데이터를 가져오는 작업이기 때문에 fetch를 통해 가져오면 response 객체는  promise 객체의 형태구성을 띄며 리턴이 된다. 그래서 then 함수를 통해서 받을 수 있다.
- console로 해당 response를 찍어보면 접근해야할 property나 method를 알수 있다.
  - 여기에서는 then함수로 받아서 json으로 반환하게 할 콜백함수를 통해서 접근해 return받고
  - 사실 실제로는 json이 아니지만 다음 then에서는 해당 json이름으로 받아와서 필요한 자료에 접근하여 데이터를 변수에 할당시킴
- html의 js-weaehr 클래스 dom을 받아와서 받았던 데이터를 넣어(innerText) 포맷팅 시킨다.

``` html
<span class="js-weather"></span>
```

``` js
const API_KEY = "asdfasiejvosiejvosijeosiej"; // 실제 key아님 보여주기식 임의 코드임
const weather = document.querySelector(".js-weather")
function getWeather(lat, lon) {
    fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${API_KEY}&units=metric`) // 백틱 조심
    .then((response) => {
    return response.json();
    })
    .then((json) => {
        const temperature = json.main.temp;
        const place = json.name;
        weather.innerText = `${temperature} @ ${place}`;
    })
}
```

<br>
<br>
<br>
<br>

오늘은 여기까지 하고,,, 사실상 기능적인 면에서 프로젝트는 끝났고 추가적으로 CSS만 만져주면 이쁘게 나오게 될 것이다. 내일은 CSS를 만져 똑같이 만들어 보고 CSS를 다시한번 익혀보자


