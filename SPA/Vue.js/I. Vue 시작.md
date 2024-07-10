
1. node.js 설치(v20.15.1)

2. npm(Node Package Manager)
	Node.js 의 기본 패키지 관리자. 패키지 설치 및 업데이트, 의존성관리, 스크립트 실행, 등의 기능을 수행할 수 있게 해준다.

```
npm i -g @vue/cli // -g 전체 영역에서 설치

```


>[!question] scaffolding?
>비계 : 건축공사에서 높은곳에서 일할 수 있도록 설치하는 임시 가설물을 말하며,  프로젝트를 시작할 때 기본적인 파일 구조와 설정을 자동으로 생성해주는 과정을 말합니다. 이를 통해 개발자는 초기 설정 작업을 줄이고, 바로 코딩에 집중할 수 있습니다.
  Vue CLI (Command Line Interface)를 사용하면 스캐폴딩을 쉽게 할 수 있다
  

# I-I. Vue 프로젝트 생성 

CDN, vue CLI, 미리 만들어놓은 webpack 을 사용하여 만들 수 있다.

#### vue cli 사용시 
vscode 터미널에서 
vue cli 설치 후  
vue create 프로젝트명(폴더명)
vue3 로 설치 

cd 생성한 프로젝트
npm run serve

```js
// package.json 에서 
  "scripts": {

    "serve": "vue-cli-service serve", // serve 개발서버

    "build": "vue-cli-service build", // 배포가능한 상태로 빌드

    "lint": "vue-cli-service lint"   // lint 코드 린팅 

  },
  "eslintConfig": { // lint 에 대한 규칙 명세 

    "root": true,

    "env": {

      "node": true

    },

    "extends": [

      "plugin:vue/vue3-essential",

      "eslint:recommended"

    ],

    "parserOptions": {

      "parser": "@babel/eslint-parser"

    },

    "rules": {}

  },  "browserslist": [  // 프로젝트가 지원할 브라우저의 종류들 

    "> 1%",

    "last 2 versions",

    "not dead",

    "not ie 11"

  ]


```

>[!question] linting?
>lint 의복이나 섬유에서 나오는 잔털이나 먼지를 의미한다. 
>즉, 코드에서 lint는 작은 결함이나 불순물을 뜻하며 이를 제거하여 코드의 품질을 높이는 과정을 의미한다. 


```js
// main.js
import { createApp } from 'vue'  // vue 라는 패키지에서 createApp 이라는 메소드를 가져옴

import App from './App.vue'      // .vue 라는 확장자를 가진 파일을 뷰로 사용 
createApp(App).mount('#app')     // Vue.createApp() 에서 Vue 가 생략됨. 

```

# Vetur(베터)
vue 확장자 파일의 코드 하이라이팅을 위한 확장 프로그램

```vue
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/> // 3. tag를 사용한다. 
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'   // 1. vue 파일을 가져온다

export default { // 2. 객체 데이터를 내보낸다.  
  name: 'App',
  components: {  
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
```

#### vue 파일 구조
1. `<template>`  : HTML
2. `<script>`     : JS
3. `<style>`       : CSS

#### Webpack 사용하여 vue 프로젝트 생성
`npx degit gptjd345/webpack-template-basic vue3-webpack-template` : `npx degit` 명령어는 특정 GitHub 리포지토리의 특정 브랜치나 커밋을 로컬 디렉토리로 복사하는 데 사용된다. `npx degit`가 `git clone`  과 다른점은 `.git` 디렉토리를 포함하지 않는다는 것이다. 이전 이력없이 새로운 프로젝트를 시작할 때 유용합니다.

`code . -r`  현재 위치에서 프로젝트 열기 

기존 js 폴더 삭제, src 폴더 생성 후 
main.js, App.vue 파일 생성 
터미널에서 `npm install vue@3` (vue3버전으로 설치)
-> -D : (dev)개발 의존성으로 설치한다는 뜻 : vue.js 는 일반의존성으로 설치해야한다. 개발뿐만아니라 브라우저에서도 사용하기 때문.

>[!question] 의존성 종류
>1. 일반 의존성(dependencies) : 애플리케이션이 실행될 떄 필요한 패키지들을 말함. 애플리케이션이 실제로 배포되고 사용될 때 필요한 모든 패키지를 포함한다. 
>2. 개발 의존성(devDependencies) : 애플리케이션을 개발하는 동안에만 필요한 패키지들을 말함. ex) 테스트 라이브러리나 빌드도구 등
>

#### 개발의존성 패키지 설치
vue-loader
vue-style-loader
@vue/compiler-sfc : vue 파일을 변환에서 브라우저에서 동작할수있는 형태로 바꿔준다.

`npm i -D vue-loader@next vue-style-loader @vue/compiler-sfc`
