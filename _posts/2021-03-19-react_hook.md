---
title : "20210319 React Hooks01, useState, useInput(nico's hooks + validator 커스텀 유효성 검증) nomadcoder " #제목
category : #카테고리
tag : #태그
    - React
    - useState
    - nomadcoder
    - hooks
    - useInput
   
toc : true #옆에 목차
---

># React Hooks

- [노마드코더 : React Hooks](https://nomadcoders.co/)

- 노마드코더의 React Hooks 강의를 듣고 작성 함

<br>
<br>
<br>

># Hooks

- 앱을 리액트 훅으로 만들면 class component, did mount, render ...등이 필요 없어짐
- 모든것은 하나의 function이 됨
- 이제는 functional programming(함수형 프로그래밍)스타일이 됨
- Hooks는 react의 state machine에 연결하는 기본적인 방법임

<br>

- **Hook의 시작**
  - recompose라는 라이브러리에서 시작되어 리액트 팀이 인수함
  - 그리고 react에서 hook으로 릴리스 함

<br>
<br>
<br>

>## useState (React hook)

- `useState`는 항상 2개의 value를 return함
  - 첫번째value는 이름을 주고(useState의 값을 불러옴), 두번째 value는 값을 변경하게 함(callback함수처럼 useState값을 가져와서 가공할 수 있음 callback은 아니고 수정모드 느낌? 으로 선언해서 셋팅해주고 따로 함수에 넣어서 JSX에서 사용) 
  - (참고, 대괄호 안에 두개의 value 이름은 바꿀수 있음 원하는 대로)
  - useState는 초기에 state를 InitialState를 세팅할 수 있는 옵션을 제공함
  - 예시) useState는 Array를 반환해야함 첫번째 요소는 item, 두번째 요소는 setItem이 됨
  - 한개 요소만 사용하고 싶으면 `const item = useState(1)[0];` 쓰고 function을 수정하고 싶으면 `const item = useState(1)[1]`
- 지정해 놓으면 useState를 부르곳에 값을 return 해줄수 있음

``` js
import {useState} from "react";
import logo from './logo.svg';
import './App.css';

function App() {
  const [item, setItem] = useState(1);
  // const item = useState(1)[0]; // 대괄호 표시의 인덱스가 단순 값 불러오기 인지 callback 호출인지 알려주는 것 같음
  // const item = useState(1)[1];
  const incrementItem = () => setItem(item + 1);
  const decrementItem = () => setItem(item - 1);
  
  return (
    <div className="App">
      <h1>Hello! {item}</h1>
      <h2>Learning hooks in React </h2>
      <button onClick={incrementItem}>Increment</button>
      <button onClick={decrementItem}>Decrement</button>
    </div>
  );
}

export default App;
```

<br>
<br>
<br>

- **예전 class component 표현식**

``` js
class AppUgly extends React.Component{
  state = {
    item: 1 
  }
  render() {
    const {item} = this.state;
    return (
      <div className="App">
        <h1>Hello! {item}</h1>
        <h2>Learning hooks in React </h2>
        <button onClick={this.incrementItem}>Increment</button>
        <button onClick={this.decrementItem}>Decrement</button>
      </div>
    );
  }
  incrementItem= () => {
    this.setState(state => {
      return {
        item:state.item +1
      };
    });
  };
  decrementItem= () => {
    this.setState(state => {
      return {
        item:state.item -1
      }
    })
  }
}
```

- 한눈에 안들어오고 수식어구가 너무 많음

<br>
<br>
<br>

- **useState 정의 된곳 가보기 &  React reference**

- [React Hooks : useState](https://reactjs.org/docs/hooks-reference.html#usestate)

``` js
/**
     * Returns a stateful value, and a function to update it.
     *
     * @version 16.8.0
     * @see https://reactjs.org/docs/hooks-reference.html#usestate
     */
    function useState<S>(initialState: S | (() => S)): [S, Dispatch<SetStateAction<S>>];
    // convenience overload when first argument is omitted
    /**
     * Returns a stateful value, and a function to update it.
     *
     * @version 16.8.0
     * @see https://reactjs.org/docs/hooks-reference.html#usestate
     */
    function useState<S = undefined>(): [S | undefined, Dispatch<SetStateAction<S | undefined>>];
    /**
```

<br>
<br>
<br>
<br>

>## useInput (nico's hook)


- `useInput`
  - useInput은 initialValue를 넣어서 useState를 활용하여 initialValue를 가진 객체로 만들어서 활용하는데 이것을 onChange(input 안이 변하면 함수호출하는 evnet 속성)를 사용하여 event 발생시 target객체의 value 객체를 event로 받아 지정해 놓고 나중에 setValue로 지정되면 그때서야 확정되어 browser에 반영

  - input event를 받아서 다른 function에서 event를 처리 할 수 있음
  - 유효성 검증 기능(validator) : 에러 방지를 위해 특정 문자 못쓰게
    - useInput의 두번째 인자로 validator는 매개변수 이름일 뿐이고 이러한 위치에 callback 함수를 가져오게 함
    - willUpdate 라는 유효성 검증 결과를 담을 변수를 지정한다. (변하기 때문에 let으로 선언)
    - `typeof`를 통해서 validator 매개변수가 함수만 올수 있게 제한하고
    - 통과하면 willUpdate 변수에 validator Callback함수를 통해 value를 검증한 불린 값을 저장한다.
    - 마지막으로 useInput 함수는 객체 value, onChange를 담은 객체를 return 한다.

  - APP에서 검증할 함수를 custom하여 지정하고 useInput을 사용하면 유효성이 검증 된 값만 browser에 반영될 수 있다.

<br>

``` js
import React, {useState} from "react";
// import logo from './logo.svg';
import './App.css';

const useInput = (initialValue, validator) => {
  const [value, setValue] = useState(initialValue); // initialValue -> value -> name
  const onChange = (event) => {
    const {
      target: { value }
    } = event;
    let willUpdate = true; // callback 함수의 조건에 맞게 떨어졌는지 확인하는 변수
    if (typeof validator === "function") {
      willUpdate = validator(value); // validator callback 함수가 들어 올거니까 이름을 맞춘거임
    }
    if (willUpdate) { // 검증이 되면 그때서야 value가 수정됨
      setValue(value);
    }
  };
  return { value, onChange };
};

function App() {
  const maxLen = (value) => value.length <= 10;
  const name = useInput("Mr.", maxLen);
  return (
    <div className="App">
      <h1>Hello!</h1>
      {/* <input placeholder="Name" value={name.value} /> */}
      <input placeholder="Name" value={name.value} onChange={name.onChange} /> 
      {/* <input placeholder="Name" {...name} />  */}
      {/* 위처럼 사용하면 name안에 있는 모든 것을 풀어줌 */}
    </div>
  );
}


export default App;

```

  




