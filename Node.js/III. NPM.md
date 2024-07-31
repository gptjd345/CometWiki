#### NPM(Node Package Manager) 이란 ? 
node.js 환경에서 사용할 수 있는 여러가지 패키지들을 설치, 관리 해주는 매니저

npm안에는 여러 패키지가 들어있어 이 패키지 중 일부를 `npm install XXX` 형태로 설치하는 방식으로 이용한다. 

#### NPM 사용법
`npm init -y` : package.json 파일이 생성됨.

##### y 옵션 사용하지 않을 경우

```js
// npm init -y 에서 y 옵션을 사용하지 않을 경우 
// package.json 프로젝트 이름, 버전, 설명, 진입점, 테스트 명령, git 저장소등의
// 정보를 입력해야함 --> 옵션사용 시 기본값으로 알아서 세팅해준다.  

PS D:\toyProject\test> npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help init` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (test)
version: (1.0.0)
description:
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)
About to write to D:\toyProject\test\package.json:

{
  "name": "test",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}


Is this OK? (yes)

```
`
##### 패키지 설치 
`npm install parcel-bundler -D`

package.json
```js
{

  "name": "test",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },

  "author": "",
  "license": "ISC",
  "description": "",
  "devDependencies": {
    "parcel-bundler": "^1.12.5"
  }

} 
```

`devDependencies` 항목에 설치한 parcel-bundler 패키지 내용이 작성된다. 

package-lock.json
```js
{
  "name": "test",
  "version": "1.0.0",
  "lockfileVersion": 3,
  "requires": true,
  "packages": {
    "": {
      "name": "test",
      "version": "1.0.0",
      "license": "ISC",
      "dependencies": {
        "lodash": "^4.17.21"
      },
      "devDependencies": {
        "parcel-bundler": "^1.12.5"
      }
    },
    "node_modules/@ampproject/remapping": {
      "version": "2.3.0",
      "resolved": "https://registry.npmjs.org/@ampproject/remapping/-/remapping-2.3.0.tgz",
      "integrity": "sha512-30iZtAPgz+LTIYoeivqYo853f02jBYSd5uGnGpkFV0M3xOt9aN73erkgYAmZU43x4VfqcnLxW9Kpg3R5LC4YYw==",
      "dev": true,
      "dependencies": {
        "@jridgewell/gen-mapping": "^0.3.5",
        "@jridgewell/trace-mapping": "^0.3.24"
      },
      "engines": {
        "node": ">=6.0.0"
      }
    },

    "node_modules/@babel/code-frame": {
      "version": "7.24.7",
      "resolved": "https://registry.npmjs.org/@babel/code-frame/-/code-frame-7.24.7.tgz",
      "integrity": "sha512-BcYH1CVJBO9tvyIZ2jVeXgSIMvGZ2FDRvDdOIVQyuklNKSsx+eppDEBq/g47Ayw+RqNFE+URvOShmf+f/qwAlA==",
      "dev": true,
      "dependencies": {
        "@babel/highlight": "^7.24.7",
        "picocolors": "^1.0.0"
      },
      "engines": {
        "node": ">=6.9.0"
      }
    },

```
설치한 패키지 수행에 필요한 여러 패키지들을 관리하는 파일이 자동으로 관리된다. 


node_modules
설치한 패키지와 설치한 패키지가 사용하는 여러 패키지들이 저장되는 공간 
삭제하더라도 `npm install` (= `npm i` 로 생략가능) 명령어 수행 시 package.json 파일을 기준으로 패키지들을 재설치가능하다.

#### npm install -D 옵션

-D , --save-dev : 개발용 의존성 패키지 설치
개발용으로만 사용하고 브라우저에서는 사용안함. 

#### npm run build
`package.json`을 기반으로 빌드



