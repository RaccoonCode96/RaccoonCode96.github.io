---
title : "20210303_React.js02 JSX, Prop, Prop-types, state , Nomadcoder" #제목
category : #카테고리
tag : #태그
    - React.js
    - JSX
    - Prop
    - Prop-types
    - state
    - Nomadcoder
   
   
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
<br>
<br>


># React- JSX & PROPS

>## component에 정보 보내기

- react는 재사용가능한 component를 만들수 있음(즉, 계속 반복하여 사용가능 함)

``` js
import React from 'react';


function Movie() {
  return <h1>I like Movie</h1>;
}

function App() {
  return (
    <div>
      <h1>Hello!!!</h1>
      <Movie />
      <Movie />
      <Movie />
      <Movie />
    </div>
  );
}
export default App;
```

- **component -> component 로 정보 보내기**
  - App -> Food -> 어떻게 활용??
  - `<Food fav="kimchi" />` 는 Food component에 kimchi라는 value로 fav이라는 prop(property)을 줬음
  - Array, bulean, stirng, num 상관 없이 모두 prop 에 넣어 다른 컴포넌트에 줄수 있음
  - 기본적으로 component는 정보를 받아올때 Object형식으로 받아오고 Object안에 props들이 존재하는 것임
  - 그래서 compenent에서는 parameter로 바로 Object.prop까지 해서 특정 prop를 받아오는 것도 가능하고, Object로 받아서 prop를 달아 사용도 된다.
  - 그리고 `{ prop }` 표현으로 해도 prop를 받아 올수도 있고 사용도 가능함.

``` js
function App() {
  return (
    <div>
      <h1>Hello!!!</h1>
      <Food fav="kimchi" />
      <Food fav="ramen" />
      <Food fav="samgiopsal" />
      <Food fav="jjukumi" />
    </div>
  );
}
```

<br>
<br>

**주의) 컴포넌트 이름은 첫글자 대문자이고 컴포넌트 연결할 때 prop의 이름은 동일해야 함**

<br>
<br>

**즉, component로 특정 prop를 포함하는 의도된 형태의 template를 만들어서 간편하게 필요한 정보를 prop에 넣어 정보를 보내 바꾸어 표현할 수 있다.**  

<br>

이처럼 html + javascript를 jsx라고 함

<br>

하지만, 계속 새로운 음식추가할 때 마다 복붙은 효율적이지 않음


<br>
<br>
<br>
<br>
<br>

>## dynamic Component Generation

데이터가 이미 API에 왔다고 상상하고 변수를 지정해서 데이터 Object를 가지는 Array인것으로 대체해 보자

그러면 아래처럼 긴 데이터를 가지게 됨

``` js
const foodILike = [
  {
    name: "kimchi",
    image: 
    "https://www.maangchi.com/wp-content/uploads/2019/11/vegankimchi-insta.jpg"
  },
  {
    name: "kimcbap",
    image: 
    "https://mykoreankitchen.com/wp-content/uploads/2006/10/1.-Easy-Kimbap.jpg"
  },
  {
    name: "Doncasu",
    image: 
    "https://pds.joins.com/news/component/htmlphoto_mmdata/201508/05/htm_20150805071153241.jpg"
  },
  {
    name: "Samgyepsal",
    image: 
    "https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile30.uf.tistory.com%2Fimage%2F9942B3395A3501C304B135"
  },
  {
    name: "Bibimbap",
    image: 
    "https://img.taste.com.au/89rfrNZj/w720-h480-cfill-q80/taste/2016/11/bibimbap-92850-1.jpeg"
  }
];
```

- 이때 javascript의 Array 함수중 map을 이용할수 있음
- `map`은 Array안의 item을 차례대로 callback함수에 대입하여 나온 결과를 가지고 Array를 구성해서 return함 기존 Array에는 영향 X

<br>

- **주의!!! component 안에서는 기본적으로 html이기 때문에 `{}`를 씌워주어야 javascript가 됨**

<br>

``` js
function App() {
  return (
    <div>
      {foodILike.map(dish => (
      <Food name={dish.name} picture={dish.image} />
      ))}
    </div>
  );
}

function Food({ name, picture }) {
  return <div>
    <h2>I like {name}</h2>
    <img src={picture} />
    </div>;
}
```

<br>

- 위처럼 foodILike라는 Array를 map을 통해 가공하여 각 item인 dish의 prop들을 불러 Food component에 name, picture라는 prop이름으로 정보를 보냄

- Food component에서 정보를 받아 표시할 html을 작성하여 안에 name, piture prop을 구성하여 사용 


``` js
function renderFood(dish) {
  console.log(dish);
  return <Food name={dish.name} picture={dish.image} />
}

function App() {
  return (
    <div>
      {foodILike.map(renderFood)}
    </div>
  );
}
```

- 그리고 map사용시 보기 좋게 하기 위해서 callback함수를 따로 빼놓음 근데 그냥 해도 상관은 없음, 또따로 함수만드는 것이 귀찮으니까

<br>
<br>
<br>

- **img tag 사용시 alt 속성 꼭 넣어주기(react에서는 이것도 잡아서 console로 권고함)**
  - 시각 장애우를 위한 alt prop임 (설명)

<br>
<br>

- **Warning: Each child in a list should have a unique "key" prop.**
  - react의 모든 element들은 다르게 보여야함
  - 고유의 id값을 주고 표현할때는 key라는 이름의 prop로 할당해 주면됨 
  - 물론 외부적으로 표편할때는 key가 사용될 필요 없지만, react 내부적으로는 필요함으로 component에 정보 전달시에만 사용해주면 됨 (Food component 자체에 key를 받아서 쓸필요는 없음 내부적으로 react가 구분하기 위해서 일뿐임)


<br>
<br>
<br>
<br>
<br>

># Prop Types

- prop types 설치 (`npm i prop-types`)
- 설치 확인은 pakage.json 파일에서 가능
- **내가 전달받은 props가 내가 원하는 type의 props인지 확인해 줌**
- 무언가 실수해서 prop이름이 안맞던지 등...
- 사용은 component 파일 상단에 `import PropTypes from 'prop-types'`
- **미리 표현하는 해당 component의 propType에 prop의 예상 설정값을 적용해 놓으면 나중에 실수 했을때 browser의 console에서 error로 알려줌**

``` js
import PropTypes from 'prop-types';
Food.propTypes = {
  name: PropTypes.string.isRequired,
  picture: PropTypes.string.isRequired,
  rating: PropTypes.string.isRequired // 물론 원래는 num이지만 string으로 기대했을 경우 num이니까 Warning(Error)을 뿜음
};
function App() {
  return (
    <div>
      {foodILike.map(dish => (
      <Food key={dish.id}
        name={dish.name}
        image={dish.image}  // 이렇게 Food에서는 picture로 받는데 image라고 되어 있을 경우 error를 뿜어서 picture가 undifined되어 있다고 알려줌
        rating={dish.rating} />
      ))}
    </div>
  );
}
```

- `rating: PropTypes.number.isRequired` : rating이 number이어야 한다. 그리고 필수적으로 있어야 함
- `rating: PropTypes.string` : rating이 number 이어야 한다. 필수는 아니고 undifined도 됨
- 등등등의 여러 옵션들이 존재함
- [PropTypes with React](https://ko.reactjs.org/docs/typechecking-with-proptypes.html)

<br>
<br>
<br>
<br>
<br>
<br>

># Class Component and State

state
- 동적 데이터와 함께 작업할때 만들어짐, 변하는 데이터, 존재하지 않는 데이터

>## Function Component -> Class Component 바꾸기

- Class Component는 기본적으로 React.Component를 가져와서 확장하는 것임
- 그래서 React.component가 가지는 method들을 기본으로 가짐
- Class Component는 return을 가지고 있지 않음(function이 아니기 때문에)
- 대신, `render` method를 가지고 있음

- function component는 무언가를 return해서 screen에 표시됨
- class component는 class고 react component로 부터 확장되고 screen에 표시됨(보여주고자 하는 것들은 `render` method안에 넣어야 함)
- react는 자동적으로 모든 class component의 render method를 실행하고자 함
- stat를 사용하기 위해서 class component가 필요함 

<br>
<br>
<br>
<br>
<br>


>## state

- `state`는 **object**로서 component의 data를 넣을 공간이 있고 데이터는 변함
- 즉, state는 변하는 데이터 바꾸고 싶은 data를 넣는 것임(python의 클래스 멤버,변수, javascript에서는 static같은 느낌)

- class component 선언시 `extends React.Component`를 꼭 붙여 줘야 함
- stat 선언은 class component안에서 선언
- **주의) render method에서 class component 안에 있는 method 및 state를 사용할 때는 class이므로 `this`를 붙여 사용해야한다.

``` js
class App extends React.Component{
  state = {
    count: 0
  };
  add = () => {
    console.log("add");
  };
  minus = () => {
    console.log("minus");
  };
  render() {
    return (
      <div>
        <h1>The number is : {this.state.count}</h1>
        <button onClick={this.add}>Add</button>
        <button onClick={this.minus}>Minus</button>
      </div>
    );
  }
}
```

- javascript에서는 `onClick` 이나 `eventListener`를 등록해서 post를 감지하여 제출하는데 React에서는 `onClick`을 가져와서 사용함 


``` js
add = () => {
    this.state.count += 1;
  };
minus = () => {
    this.state.count -= 1;
// reder refrash 도 필요하지만, react에서 이렇게 직접적으로 state를 건드리는 것을 막고 있음
// 위처럼 하면 아무일도 안일어남
```

<br>

- state는 직접적으로 변경해서는 안되고 만약 직접적으로 건드리면 render를 다시 호출해서 보여주어야 한다. 하지만 건드리면 안된다고 했으니까

<br>

- **`setState` function을 사용하는데 이를 호출할 때마다 react가 새로운 state와 함께 알아서 state를 변경하고 render frash까지 해줌**
- setState의 경우 **새로운 state를 취해야함**

<br>

``` js
class App extends React.Component{
  state = {
    count: 0
  };
  add = () => {
    this.setState({ count: this.state.count + 1 });
  };
  minus = () => {
    this.setState({ count: this.state.count - 1 });
  };
  render() {
    return (
      <div>
        <h1>The number is : {this.state.count}</h1>
        <button onClick={this.add}>Add</button>
        <button onClick={this.minus}>Minus</button>
      </div>
    );
  }
}
```

<br>


- virtual Dom을 가지고 있기 때문에 react는 매우 빠르게 변경하고 깜박거리지도 않음

<br>

- 하지만 위처럼 state에 의존하면 성능문제가 나중에 생김
- React에서는 그래서 현재 state를 가져와서 function으로 가공할수 있게 해주었음
- `current`라는 이름의 pram으로 현재 state를 받아서 함수를 구성할수 있음
- 이를 통해서 setState시 외부 상태에 의존하지 않는 방법을 제시함

<br>


``` js
class App extends React.Component{
  state = {
    count: 0
  };
  add = () => {
    this.setState(current => ({ count: current.count + 1 }));
  };
  minus = () => {
    this.setState(current => ({ count: current.count - 1 }));
  };
  render() {
    return (
      <div>
        <h1>The number is : {this.state.count}</h1>
        <button onClick={this.add}>Add</button>
        <button onClick={this.minus}>Minus</button>
      </div>
    );
  }
}
```

<br>
<br>


- **Question**
- js는 function return의 중괄호와 객체 표현의 중괄호를 구분하기 어려워 객체 표현은 `({})`을 사용함
- `count: current.count += 1` 과 같은 할당 연산자 사용도 가능하나, index.js에서 strict mode 확인을 해야함 있으면 2번 실행됨 












