---
title : "20210309_Yalco01 디버그, 컴파일러, 인터프리터, 빌드, 환경변수, REST API, GraphQL, 객체지향,쿠키,세션,캐시, 프로세스, 스레드, 메모리 관리 " #제목
category : #카테고리
tag : #태그
    - Yalco
    - 디버그
    - 컴파일러
    - 인터프리터
    - 빌드
    - 환경변수
    - REST API
    - GraphQL
    - 객체지향
    - 쿠키
    - 캐시
    - 세션
    - 프로세스
    - 스레드
    - 메모리 관리
   
   
toc : true #옆에 목차
---

># Yalco

얄팍한 코딩사전이라는 아주 좋은 코딩 유튜버 분이 계시는데 비전공자의 눈높이에 맞게 여러 용어등을 설명해 주시는데 아주 좋다.
오늘은 이러한 것들을 보고 지금까지 배운 내용들과 비교하여 부족한 부분들을 살펴보며 용어에 대해서 검토하는 시간을 갖고자 한다.

- [Yalco 사이트! 클릭](https://www.yalco.kr/)


>## 디버그(Debug)

  - : 소프트웨어에 발생하는 문제의 원인을 찾는 것 (개발의 대부분의 과정)
  -  개발 중인 상태(배포와 반대되는 개념)
  -  코드를 직접 돌려서 어디부분에서 문제가 생기는지 알아보고 해결 함
  -  에디터 대부분이 중간에 검문소를 두어 이를 통과하는 값들을 살펴 볼수 있도록 하는 기능(디버깅 툴)을 제공하고 있음

<br>
<br>
<br>

>## 컴파일러(Compiler) vs 인터프리터(Interpreter)

- 둘다 모두 우리가 개발할때 쓰는 언어들을 기계어(이진 코드)로 번역해주는 것을 말함
- 단, 미리 번역해주면 `compiler` 그때 그때 번역해주면 `interpreter`
- **compiler** (compile : 코딩을 마치고 완성된 프로그램을 출시하기 전에 코드를 미리 번역) -> 컴파일 언어(C, C++ , Java)
 - 먼저 실행하기 전에 코드를 모두 번역하기 때문에 오류를 실행전에 발견할 수있고 실행시간도 빠르다
 - 메모리가 많이 필요함

<br>

- **interpreter** (코드를 그대로 가져가서 그때그때 번역) -> interpreter 언어 or script 언어 (JS, Python, Ruby) 
 - interpreter 언어는 수정하기는 편하다.
 - bytecode로 번역하고 또 그걸 이진 코드로 번역하기 때문에 실행속도가 compile 언어보다 느리다.
 - 프로그램 코드가 쉽게 누출 될수 있다.
 - 실행하면서 1줄씩 번역하면서 실행하기 때문에 오류 발생전에 오류를 발견하기 힘듦(오류를 만나야 오류가 있음을 알수 있다 보니)
 - 중간 목적 코드가 없어 메모리 효율이 좋음

<br>
<br>
<br>

>## 빌드(Build)

- 개발할 때 작업하는 코드 및 여러 파일, 폴더, 라이브러리 등을 압축하는 것
- 목적 : 
  - 1) 압축해서 전체 크기를 줄이기 위함
  - 2) 바로 실행할수 있는 파일 하나로 제공하기 위함 (exe 파일처럼)
  - 3) 다른 사람이 못 알아보게 난독화 하기 위함
- 컴파일 언어는 빌드에 컴파일 작업이 포함 되어 있음
- 각 언어 별로 정리되는 형태결과는 다르다. (한 파일, 보기 좋은 코드, 파일 개수를 줄이는 등..) 
- 출시하기 적합한 형태로 만드는 일(build) -> 배포(deploy)

<br>  
<br>  
<br>  

>## 환경변수

- 환경 : 소프트웨어가 동작하는 공간 (컴퓨터, doker 같은 세분화된 공간 등 ..)
- 환경변수 : 환경 마다(소프트웨어가 깔린 컴퓨터 등 마다) 특정 변수명에 지정된 값
- 목적 : 
  - 1) 한가지 코드로 환경변수를 넣어 개발할 컴퓨터(dev)와 배포될 서버(prod)의 조건을 걸어 분리해서 관리 가능
  - 2) 보안상 민감한 정보들을 코드로 부터 분리 (API key 등)
  - 3) 프로그램이 자신의 환경에 맞게 잘 동작할 수 있게 알려주는 기준이 됨

<br>
<br>
<br>

>## 프레임워크 vs 라이브러리

- 라이브러리 : 특정 기능들이 함수나 클래스 등의 형태로 공개되어서 가져다 내 소프트웨어에 사용가능한 것
  - (즉, 가져다 붙여서 쓸수 있는것 으로 모듈화된 코드 그 묶음, 꼭 남의 것일 필욘 없음)
- 프레임워크 : 소프트웨어의 기초 설계가 프로그래밍 되어 있어 뼈대를 가지고 쉽게 개발 할 수 있는 것
  - Spring, Django, .NET, Laravel, Express, Vue

<br>
<br>
<br>


>## Rest API

- 개발자 끼리 정보를 주고받는 일종의 형식 (프로그램이 아니고 형식임, 어떤 언어든지 해당 형식에 맞게 만들면 됨)
- 인터페이스 : 기계와 인간 간의 소통 창구 (예. 리모컨, 마우스, 키보드, 스크린, 모니터 등..)
- API(Application Programming Interface) : 기기와 기기간의 정보를 주고 받는 인터페이스로서 그것을 통해 요청하고 응답하여 받는 것
- 네트워크 상 뿐만 아니라, 
  - 로컬프로그램인 브라우저 -> Web API로 자바스크립트를 통해 특정 동작을 지시 받음
  - 윈도우 프로그램에서는 windows API

<br>
<br>

- **REST API**
  -  각 요청이 어떤 동작이나 정보를 위한 것인지를 그 요청(주소)모습 자체로 추론이 가능함
     -  URI를 사용하여 데이터의 대략적인 구조를 유추 할 수 있음 (`http://domain/classes/2/students?page=2&count=10`)
  -  URI : 자원의 구조와 함께 나타내는 형태의 구분자
  -  조회 작업 및 정보를 넣거나 수정삭제하는 CRUD 작업도 가능
  -  REST API에서는 http 규약을 사용하면서 요청시 http method인 `GET, POST, DELETE, PUT, PATCH`를 사용하고
  -  `POST, PUT, PATCH`에는 `Body`가 존재해서 GET, DELETE 보다많이 정보를 다룰 수 있고 감추어서 안전하게 보낼 수 있음
  -  POST 하나로 CRUD가 모두 가능한데 모두 알기 쉽게 RESTful하게 API를 만들기 위해서 목적에 따라 구분해서 사용함
  -  `GET` : 데이터 조회용(read)
  -  `POST` : 새로운 정보를 추가하는데 사용(create) body에 추가할 정보를 담아 제공(URI : index 번호 필요 X)
  -  `PUT`, `PATCH` : 정보를 수정하는 경우 변경할 정보 body에 담아 보냄 (URI: index 번호 필요 대상이 필요하니까)
     - `PUT` : 정보를 통째로 갈아 끼울 때
     - `PATCH` : 정보 중 일부를 특정 방식으로 변경시
  - `DELETE` : 정보 삭제시 (URI : index 번호 필요)  
- 한가지 method로만 사용하면 URI가 CRUD 표현으로 지저분 해지기 때문에 각 method 용도별로 분리해둔 것임 
- 그래서 URI는 동사가 아닌 명사들로 이뤄져야 함
- REST API의 대안인 `GraphQL`이 있음


<br>
<br>
<br>

>## GraphQL

- 자기가 원하는 데이터만 적어서 CRUD 할 수 있음(REST API는 쓸데 없는 정보들 까지 모두 불러오기 때문에 데이터 효율이 떨어짐)
- GraphQL은 응답 복잡도는 적고 요청 복잡도가 크고
- REST API는 응답 복잡도는 크지만 요청 복잡도는 작다
- 둘중의 어떤 것을 사용해야 할지는 개발자가 상황에 맞게 구현하기 나름이다. (둘다 구현해 놓고 상황에 따라 다르게 요청하게 끔 할수도 있고)
- GraphQL은 환경마다 구현 방식이 조금씩 다름 
- [GraphQL Official site](https://graphql.org/code/#python)

<br>
<br>
<br>


>## 객체 지향
  - `객체` : 데이터와 기능이 클래스로 `캡슐화` 된 컴퓨터 자원 묶음
  - 하나하나 구구절절 명령해 줄 필요 없이 규칙(class)을 만들어 대상에게 암기(constructor) 시킨 객체(Object)로 만들어 간단한 명령(method)으로 부릴수 있음
  - 근데 그 규칙(class)안에 외부에서 명령할 수 있는 규칙, 내부적 규칙으로 나누어서 정의하여 내부적 규칙(private)은 밖에서 상관할수 없게 하고(간섭 오류 예방) 외부 명령 규칙(public)을 통해서 명령가능
  - 근데, 세부적으로 규칙(class)들이 많아지면 또 계속 그 규칙들(class)을 하나하나 간단한 명령(method)들을 써주어야 하는데 그러지 말고 암기된 상태의 객체(Object)의 명령의 공통점을 찾아 Object를 카테고리(interface)로 묶어서 한번에 카테고리(interface)의 명령(method) 하나로 명령하면 한번에 각자 맡은 일을 하게 됨
  - 더 나아가 카테고리(interface)를 가진 객체(object)들을 list(Array)로 묶어서 만들면 list 이름과 카테고리 명령 하나만 있으면 전체를 한번에 주어진 일을 하라고 명령할 수 있음
  - 또한 개인적인 규칙(class)를 암기한 객체들을(object) 유동적으로 모아서 팀으로 운영하는 규칙(class2)를 만들어 일을 시행할 수 도 있음    

<br>

- **객체지향 특성**
  - `은닉성` : 내부구조는 private로 감추고 외부 조작만 public으로 공개
  - `상속` : super의 protected, public으로 지정한 변수들과 메소드들을 그대로 받는 자식을 구성할 수 있음 (다르 부분만 추가하거나 override 해서 입혀주면 되기 때문에 효율적임)
    - 추상적인 super class로는 객체가 안되고 구체적인 자식 class를 통해서 객체를 만들 수 있음
  - `다형성` : override
  - `인터페이스` : 부모가 다른 클래스의 공통점을 묶어 장착 가능한 것을 말함
    - 상속은 물려 받는것, 인터페이스는 장착하는 것 
    - 예) 개발자 -> super : IT전문직 , interface : 운전면허, 공인중개사자격증, 조리사자격증 등 ...

<br>
<br>
<br>

>## 객체지향 디자인 패턴

>## 쿠키(Cookie) vs 세션(Session) vs 캐시(Cache)

- **쿠키**: 개인이 가지고 다님 (브라우저에 정보를 저장함)
  - 사용자 편의를 위한 기능에 쓰이지만 지워지거나, 조작되어도 큰일이 없는 정보를 담음 
  - 로그인창의 아이디 자동완성
  - 공지 메세지 하루 안보기
  - 쇼핑몰 로그인 없이 장바구니

<br> 

- **세션** : 서비스 제공자가 관리 (서버에 정보를 저장해서 관리)
- 서비스 제공자가 관리하고 누구것인지는 기한이 짧은 인식키를 브라우저에 보내 쿠키에 저장함
- http 요청에 키를 실어서 전송함으로서 서버가 이를 인식하여 정보를 보내줌
- 사용자나 다른 누군가에게 노출되어서는 안되는 정보, 서비스 제공자가 직접 관리해야하는 정보들을 서버안에서 세션으로 다뤄짐
- 세션 남발하면 접속자가 많으면 서버에 부하가 걸림

<br>

- **캐시** : 가져오는데 비용이 드는 데이터를 한번 가져온 뒤 잠시 임시로 보관하는 것(웹, 컴퓨터 메모리, 안드로이드 등.. 에서 사용됨)
- 웹캐시 : 이미지 등의 정보를 불러올때 데이터 사용량, 시간이 들어서 여러번 방문할 것 같은 사이트는 `사용자 컴퓨터 또는 중간 역할을 하는 서버`에 저장해 두는 것

<br>
<br>
<br>


>## 프로세스, 스레드

- 프로세서(요리사) - 프로세스(요리 메뉴와 해당 조리대) - 스레드(손의 개수)  
- 프로그램(exe)이 실행되어 일하고 있는 상태를 `프로세스`라고 함
- 일하는 방식 -> 
  - `동시적` : 프로세서 하나가 여러 프로그램을 돌아다니면서 조금씩 일을 진행
  - `병렬적` : 프로세서 하나에 코어가 여러개 달려서 각각 동시에 작업 수행

- `스레드` : 하나의 프로세스 내에서도 여러 갈래의 작업들이 동시에 진행될 필요가 있는데 이 갈래를 스레드라고 함
  - 스레드들은 해당 프로세스 하나 안에서만 진행 됨

- **프로세스는 컴퓨터 자원을 분할해서 쓰지만, 스레드는 프로세스마다 주어진 전체 자원을 함께 사용함**
- 프로세스 안에서 공유되는 변수에 스레드 두 개가 동시에 손을 대면 error가 발생함(시간문제로 발생하는 문제)
- 스레드를 사용하는 프로그래밍은 코드짜기도 힘들고 디버깅 하기도 힘들어서 -> Closure, lambda , Functional programming, Actor등을 통해서 도와주고 있음

<br>
<br>
<br>


>## 가비지 컬렉터, 메모리 관리

- 메모리 누수 : 컴퓨터 과학에서 메모리 누수 현상은 컴퓨터 프로그램이 필요하지 않은 메모리를 계속 점유하고 있는 현상이다. 할당된 메모리를 사용한 다음 반환하지 않는 것이 누적되면 메모리가 낭비된다. 즉, 더 이상 불필요한 메모리가 해제되지 않으면서 메모리 할당을 잘못 관리할 때 발생한다.
- 가비지 컬렉터 : 필요 없는 정보들을 쓸어 담음 (메모리를 차지 하므로 동시성이 떨어지니까)
  - Mark-and-sweep : 필요한 것만 마크하고 나머지는 버림 (root에서 닿지 않는 것을 버리는 것임)
  - Reference Counting : 한요소가 다른 요소에게 몇번이나 참조가 되는지 세어서 그수가 0이 되면 지우는 것
- managed language : 메모리를 알아서 관리해주는 언어