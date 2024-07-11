
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

```js
// webpack.config.js

module.exports = {

  // 파일을 읽어들이기 시작하는 진입점 설정

  entry: './src/main.js',  // src 폴더 만들었으므로 경로 수

// 모듈 처리 방식을 설정
  module: {
    rules: [
      {
        test: /\.vue$/,    // .vue 로 시작하는경우 
        use: 'vue-loader'  // vue-loader에 의해 처리
      },
      {
        test: /\.s?css$/,
        use: [
          // 순서 중요!
          'vue-style-loader',  // style 태그 내의 css 를 해석해서 동작하게 해줌 맨위가 가장 마지막에 실 
          'style-loader',
          'css-loader',
          'postcss-loader',
          'sass-loader'
        ]
      },
```

#### module :
웹팩 설정에서 module 속성은 로더(Loader)와 관련된 규칙을 정의한다. 로더는 웹팩이 프로젝트 파일을 처리하는 방법을 지정하는데 사용된다. 

#### rules : 
rules 속성은 각 파일 유형에 따라 사용할 로더의 배열을 정의한다. 각 규칙은 파일 확장자 또는 파일 경로에 따라 특정 로더를 적용한다. 

#### test :
test 속성은 정규 표현식을 사용하여 로더가 적용될 파일 유형을 지정한다. 여기서 `test: /\.vue$/` .vue 확장자로 끝나는 모든 파일을 의미한다.

#### use : 
use 속성은 해당 파일 유형에 사용할 로더를 지정한다. 
use : 'vue-loader'는 .vue 파일을 처리하기 위해 vue-loader를 사용하겠다는 의미이다. 

```js
// webpack.config.js
// path: NodeJS에서 파일 및 디렉토리 경로 작업을 위한 전역 모듈

const path = require('path')
const HtmlPlugin = require('html-webpack-plugin')
const CopyPlugin = require('copy-webpack-plugin')
const { VueLoaderPlugin } = require('vue-loader') // vue-loader에서 가져옴 

  // 번들링 후 결과물의 처리 방식 등 다양한 플러그인들을 설정
  plugins: [
    new HtmlPlugin({
      template: './index.html',
    }),
    new CopyPlugin({
      patterns: [
        { from: 'static' }
      ]
    }),
    new VueLoaderPlugin()  // 추가

  ],

```


```js
// APP.vue
<template>
    <h1>{{ message }}</h1>
</template>

<script>

import { defineComponent } from '@vue/composition-api'

export default {
    data() {
        return {
            message: 'Hello Vue!!!'
        }
    }
}

</script>

// main.js

import Vue from 'vue'        // 1. vue를 가져와서
import App from './APP.vue'  // 2. App.vue 에서 정의한 

Vue.createApp(App).mount('#app') // 3. export 데이터를 가져와서 app 태그에 마운트한다. 


```

#### 객체 구조 분해 할당(Object Destructuring)
```js
//main.js

import { createApp } from 'vue' // 1. vue를 가져와서
import App from './APP.vue'     // 2. App.vue 에서 정의한 

createApp(App).mount('#app')    // 3. export 데이터를 가져와서 app 태그에 마운트한다. 

```

객체의 속성을 추출하여 변수에 할당하여 'vue' 전체를 가져오지 않고 vue 모듈에서 createApp 함수만을 직접 추출한다. 코드가 더 간결해지고 효율적이다. 

#### 결과 확인 
`npm run dev`

방법 2: 환경 변수 설정

Node.js 버전 17 이상에서 OpenSSL 3.0이 기본으로 사용되면서 발생하는 문제입니다. OpenSSL 3.0은 일부 해시 알고리즘을 기본적으로 지원하지 않기 때문에, Webpack이나 기타 도구들에서 `crypto` 모듈을 사용할 때 문제가 발생할 수 있습니다.

`openssl-legacy-provider`는 OpenSSL 3.0에서 지원되지 않는 일부 알고리즘을 사용할 수 있도록 하는 레거시 지원 옵션입니다. Node.js 17 이상 버전에서는 OpenSSL 3.0이 기본적으로 사용되며, 이는 이전 버전의 OpenSSL에서 사용 가능했던 일부 해시 알고리즘 등을 기본적으로 지원하지 않습니다. 이로 인해 기존 프로젝트에서 `crypto` 모듈을 사용할 때 문제가 발생할 수 있습니다.

### OpenSSL 3.0과 레거시 지원

OpenSSL 3.0은 보안 및 성능 개선을 위해 많은 변경사항을 포함하고 있습니다. 그러나 이로 인해 일부 알고리즘이 기본적으로 비활성화되었거나 지원되지 않게 되었습니다. `openssl-legacy-provider` 옵션은 이러한 변경사항을 우회하여 이전 버전의 OpenSSL에서 사용하던 알고리즘을 계속 사용할 수 있도록 해줍니다.


cmd
set NODE_OPTIONS=--openssl-legacy-provider 

powershell
$env:NODE_OPTIONS="--openssl-legacy-provider"

해당 오류는 Node.js 버전 17 이상에서 OpenSSL 3.0이 기본으로 사용되면서 발생하는 문제입니다. OpenSSL 3.0은 일부 해시 알고리즘을 기본적으로 지원하지 않기 때문에, Webpack이나 기타 도구들에서 crypto 모듈을 사용할 때 문제가 발생할 수 있습니다.


vue3에서는 필요없어서 삭제 
npm uninstall @vue/composition-api