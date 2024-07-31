
assets 폴더 생성 
![[Pasted image 20240722235238.png]]


`npm i file-loader `

file-loader의 처리 방식을 정의
```js
// webpack.config.js
// 모듈 처리 방식을 설정
  module: {
    rules: [
		   ...
         {
        test: /\.js$/,
        exclude: /node_modules/, // 제외할 경로
        use: [
          'babel-loader'
        ]
      },
      {
        test: /\.(png|jpe?g|gif|webp)$/,
        use: 'file-loader'
      }
    ]
  },

```


파일경로에 대한 alias 설정 
```js
// webconfig.js
module.exports = {
  // Vue라는 확장자를 가지고 있는 vue 파일을 경로에서 확장자를 따로 명시하지 않아도 됨.
  resolve: {
    extensions : ['.js','.vue'],
    // 경로 별칭
    alias: {
      '~': path.resolve(__dirname, 'src'),
      'assets' : path.resolve(__dirname, 'src/assets')
    }
  },

```

App.vue
```js
import HelloWorld from '~/components/HelloWorld'
```

`~는 webpack.config.js 에서 정의한 





``



