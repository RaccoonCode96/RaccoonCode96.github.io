---
title: "20210907 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit39 :CRA + gh-pages를 통한 프로젝트 배포, Firebase 보안 설정, 프로젝트를 마무리 하며" #제목
category: #카테고리
tag: #태그
  - project
toc: true #옆에 목차
---

> # 리팩토링 Instagram 클론 프로젝트 by Redux-toolkit39

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

# 2021.09.07 사항

<br/>

## CRA + gh-pages 배포

드디어 인스타그램 클론을 배포하였습니다.

배포는 gh-pages를 통해서 배포하였습니다. (gh-pages가 제일 간단하고, 그래도 연관성 있는 도메인과 path를 가질수 있기 때문입니다.)

gh-pages의 경우 배포하면, repo 이름이 들어간 도메인, Path로 배포가 됩니다. 하지만, React-router-dom에서 다른 설정을 하지 않으면 `사용자.github.io/`로 path를 나타내기 때문에 주소에 repo 이름이 기본적으로 깔리게 `basename`을 설정하였습니다.

그럼, gh-pages를 npm으로 설치하고 pakage.json에 script를 작성합니다.

- `npm i gh-pages`
- build 명령어로 CRA의 build를 지정하고, predeploy를 지정하여 항상 deploy 전에 build를 할 수 있도록 합니다. 그리고 deploy 명령어에 `gh-pages -d 빌드폴더명` 으로 지정합니다. (어떤 빌더를 쓰느냐에 따라 build 폴더가 다르기 때문이죠, parcel의 경우 dist 입니다.)
- hompage 속성에 배포할 주소를 넣어줍니다.

```json
// pakage.json
{
  "scripts": {
    "start": "react-scripts start",
    "eject": "react-scripts eject",
    "build": "react-scripts build",
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
  },
  "homepage": "https://RaccoonCode96.github.io/redux_racstagram" // 배포 주소
}
```

빌드가 완료되면, build 폴더가 생성되며 staic폴더와 함께 필요한 파일들이 자동으로 build 됩니다.

- `meta 태그 수정` : 빌드전에 public 폴더의 index.html의 meta tag와 manifest.json을 앱에 맞게 수정해 주세요. (더 멋진 앱을 만들고 싶다면, 당연히 title, favicon은 기본으로 넣어주어야 겠죠.)
- `이미지 관리` : 앱에 사용되는 이미지들은 src의 images 폴더에 넣어 관리하고 build를 하면 build 폴더의 static 폴더의 media 폴더명으로 들어가게 됩니다.
- `SCSS` : SCSS를 사용하면, build를 위해 sass를 설치해야 합니다. (node-sass의 경우 곧 dart-sass 위주로 변경된다고 하니 기본적으로 제공하는 sass를 설치하여 사용합시다. sass-loader의 경우 CRA에서 기본적으로 제공하기 때문에 eject할 필요가 없습니다.)

npm run deploy 명령어를 통해 build 부터 gh-pages의 publish까지 모두 해결 할 수 있습니다.

<br/>
<br/>
<br/>

## firebase 보안 규칙 수정

<br/>

firebase의 경우 강력한 보안 규칙을 제공합니다. firebase 콘솔에 들어가 Auth, firestore, storage 서비스의 접근에 대한 규칙을 설정 할수 있습니다. 데이터 베이스의 CRUD에 대한 조건을 걸을수 있습니다.

- Auth의 경우 domain에 사용할 배포된 도메인을 추가시켜서 사용할 수 있게 합니다.
- firestore 설정에서는 기본적으로 CRUD의 경우 로그인 사용자만 할 수있도록 하였고, 예외적으로 회원가입시 이름 중복체크 때문에 이름만 Read 할 수 있도록 설정 했습니다.
- storage 서비스의 경우 로그인 사용자만 CRUD 할수 있도록 하였습니다.

<br/>

### API 키(Browser key) 제한

구글에서는 API를 사용할 수 있는 도메인을 제한하는 기능을 제공합니다. 이를 통해서 API 접근 key를 알더라도 다른 사이트에서 사용할 수 없습니다.

https://console.cloud.google.com/apis 에 접속하여 Credentials (사용자 인증 정보) 탭을 클릭하여 API 키의 해당 프로젝트의 API 키를 클릭하여 들어갑니다.

애플리케이션 제한사항으로 적절한 것을 선택합니다(프로젝트에 따라 다릅니다.) 저는 웹사이트 이름으로 제한을 걸었고, 아래 API를 사용할 수 있는 도메인을 넣어 설정해 줍니다. `배포할 사이트, localhost, firebase 프로젝트 서버 주소` (주소 도메인 뒤에 path로 애스터리스크를 넣어 유연하게 적용시킬 수 있습니다.)

<br/>
<br/>
<br/>

## 프로젝트를 마치며

<br/>

완벽히 인스타그램의 모든 기능을 클론하지는 못하였지만, 부분적으로 클론을 실시 하였습니다.

일반적인 클론과는 다르게 모바일 환경에서도 사용할 수 있도록 반응형 스타일을 적용하였고, 최대한 모바일 환경의 UI를 참고하여 만들었습니다.

개발을 진행하면서, 그래도 끝을 내야되진 않을 까? 라는 생각이 많이 들었습니다. 물론, 계속해서 기능들을 구현하는 것도 좋지만 끝을 맺는 것도 중요하다고 생각합니다. 끝을 내야 결과물이 나오기 때문이죠.

일단은 현재 프로젝트에서 주요적으로 다룬 게시글CRUD, 프로필 수정, 무한스크롤, 좋아요, 댓글으로 마무리를 하고자 합니다.

프로젝트를 진행 중 생각하는 시간을 가지고, 기능을 구현하면서 정말 많이 배웠습니다. Redux를 상태관리로 하는 앱의 전체적인 동작 방식에 친숙해졌습니다.

<br/>

## 아쉬운점

- 테스트 코드를 작성하지 않아서, 지속적인 테스트 환경이 없다는 게 아쉽습니다.
- Type을 아직 propType이나 TypeScript를 통해 제한을 두지도 않았기 때문에 아쉽습니다.
- 인스타그램의 여러가지 요소들을 더 클론하고 싶었지만, 너무 이 프로젝트 자체에 매달릴수는 없기에 너무 아쉬웠습니다.

<br/>

> ### 9월 1일 ~ 9월 6일 까지 포트폴리오 웹사이트를 제작하고 있었습니다. <br/> 다음 시간에 완성된 포트폴리오에 대해서 포스팅 해보려고 합니다.
