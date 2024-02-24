
## 🔖 개요
대부분의 브라우저에서 ES6 지원율은 98% 정도이다. 그러나 ES6 이상의 버전들인 ES6+, ES.NEXT는 브라우저에 따라 지원율이 제각각이다.

구형 브라우저에서도 문제 없이 동작시키기 위한 개발 환경을 구축하는 것이 필요한데, 대부분의 프로젝트가 모듈을 사용하므로 모듈 로더도 필요하다. 

ESM은 대부분의 모던 브라우저에서 사용 가능하지만, IE를 포함한 구형 브라우저에서는 지원하지 않고 **트랜스파일링 및 번들링이 필요한 것은 변함이 없기 때문에** 별도의 모듈 로더를 사용하는 것이 일반적이다.

트랜스파일러인 **Babel**과 모듈 번들러인 **Webpack**을 이용하여 ES6+/ES.NEXT 개발 환경을 구축해 보자.

💡 **Babel**이란?
- 자바스크립트 컴파일러
- ES6 등 최신 사양의 코드를 ES5 등 아래 사양의 자바스크립트 소스코드로 변환함


## 🔖 Babel
### 📍 설치 방법
npm을 이용하여 Babel을 설치할 수 있다.
```
$ npm install --save-dev @babel/core @babel/cli
```

### 📍 Bable 프리셋 설치/babel.config.json 설정 파일 작성
Bable을 사용하기 위해 `@babel/preset-env`를 설치해야 한다. 이는 함께 사용되어야 하는 Babel 플러그인을 모아둔 것으로, **Babel 프리셋**이라고 부른다. 필요한 플러그인들을 프로젝트 지원 환경에 맞춰 동적으로 결정해 준다.

```
$ npm install --save-dev @babel/preset-env
```

```js
// babel.config.json
```

공식적인 preset은 `preset-env`, `preset-flow`, `preset-react`, `preset-typescript`가 있다.

### 📍 트랜스파일링
Babel을 이용하여 ES6+/ES.NEXT 사양의 소스코드를 ES5 사양의 소스코드 트랜스파일링 해보자.

💡 **트랜스파일러**
- 같은 언어 혹은 비슷한 레벨의 언어일 때, 문법적으로 변환해 주는 도구 (문법 기준 변환 도구)

트랜스파일링을 위한 Babel CLI 명령어를 등록할 수 있다.
```js
// package.json
{
    ...
    "scripts": {
        // 'src/js'에 있는 모든 js 파일을 트랜스파일링 후, 'dist/js'에 저장
        "build": "babel src/js -w -d dist/js"
    }
}
```
ES6+/ES.NEXT 사양의 자바스크립트 파일을 `src/js` 폴더의 `lib.js`로 가정하자.
`src.js` 폴더의 `main.js`에서 `lib.js`의 소스를 사용하는 상황이라면, 위 명령어를 이용하여 트랜스파일링이 필요하다.

다양한 제안 단계에 있는 사양을 트랜스파일링하려면 별도의 플러그인 설치가 필요할 수도 있다.

### 📍 Babel 플러그인 설치
Babel 홈페이지에서 다양한 플러그인을 검색할 수 있다.

https://babeljs.io/

ES+/ES.NEXT 사양의 class 내부 private 필드 선언 시, 빌드 에러가 발생한다.

이를 해결하기 위해 "class field"를 검색하여 플러그인을 찾아낼 수 있다.
해당하는 플러그인은 `public/private` 클래스 필드를 지원하는 `@babel/plugin-proposal-class-properties`이다.

https://babeljs.io/docs/v7-migration#babelplugin-proposal-class-properties

npm을 이용해 플러그인을 설치할 수 있다.
```
$ npm install --save-dev @babel/plugin-proposal-class-properties
```

설치한 플러그인은 `babel.config.json` 설정 파일에 추가해야 한다.

```js
// babel.config.json
{
    "presets": ["@babel/preset-env"],
    "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

이후 트랜스파일링을 다시 시도하면 정상 동작하는 것을 확인할 수 있다.

### 📍 브라우저에서의 모듈 로딩 테스트
위 예제의 모듈 기능은 Node.js 환경에서 동작한 것이고, Babel이 모듈을 트랜스파일링한 것도 Node.js가 기본 지원하는 CommonJS 방식의 모듈 로딩 시스템에 따른 것이다.

그렇기에 브라우저에서는 CommonJS 방식의 require 함수를 지원하지 않으므로 에러가 발생한다.

브라우저의 ESM 모듈을 사용하도록 Babel을 설정할 수도 있지만, **ESM을 사용하는 것 자체에서 크로스 브라우징 등의 문제가 발생하는 것도 여전하다.**

이를 **Webpack**을 통해 해결할 수 있다.

## 🔖 Webpack
**Webpack**은 의존 관계에 있는 js, CSS, 이미지 등의 리소스들을 하나 혹은 여러 개의 파일로 번들링하는 **모듈 번들러**이다. 이를 통해 의존 모듈이 하나의 파일로 번들링되므로, **별도의 모듈 로더가 필요 없다.** 여러 개의 js 파일을 로드해야 하는 번거로움도 사라진다.

### 📍 Webpack 설치
```
$ npm install --save-dev webpack webpack-cli
```

### 📍 babel-loader 설치
Webpack이 모듈을 번들링할 때, Babel을 사용하여 트랜스파일링하도록 `babel-loader` 설치가 필요하다.

```
$ npm install --save-dev babel-loader
```

```js
// package.json
{
    ...
    "scripts": {
        "build": "webpack -w"
    }
}
```

### 📍 webpack.config.js 설정 파일 작성
`webpack.config.js` 설정 파일을 작성한 후, 앞서 설명한 Babel을 이용한 트랜스파일링처럼 Webpack을 실행함으로써 트랜스파일링 및 번들링을 할 수 있다. 이 때, **트랜스파일링은 Babel이 수행하고 번들링은 Webpack이 수행한다.**

결과적으로, 여러 js 모듈이 하나로 번들링된 `bundle.js`를 브라우저에서 실행시킬 수 있다.

### 📍 babel-polyfill 설치
Babel을 사용하여 트랜스파일링을 해도 브라우저가 지원하지 않는 소스코드가 남아있을 수 있다.

`Promise`, `Object.assign`, `Array.from` 등과 같이 ES5 사양으로 대체할 수 없는 기능은 트랜스파일링할 수 없다. 이를 해결하기 위해서 `@babel/polyfill`을 설치해야 한다.

💡 실제 운영 환경에서도 사용되기 때문에 dev 옵션을 지정하지 않는다.

```
$ npm install @babel.polyfill
```

```js
// ES6
import "@babel/polyfill";

// Webpack: webpack.config.js
module.export = {
    // entry file
    entry: ['@babel/polyfill', './src/js/main.js'],
    ...
}
```