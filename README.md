# 웹팩(Webpack) 실습

- 2023.01.02
- 사전학습으로 [번들러](https://github.com/jiheon788/tech-note/blob/master/dev/bundler.md)에 대하여 알아본 후, [웹팩 핸드북](https://joshua1988.github.io/webpack-guide/getting-started.html#%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%84%B1)을 참고해 웹팩 실습 진행

## 웹팩이란?

- 웹팩이란 최신 프런트엔드 프레임워크에서 가장 많이 사용되는 `모듈 번들러(Module Bundler)`이다.

- 모듈 번들러란 웹 애플리케이션을 구성하는 자원(HTML, CSS, Javscript, Images 등)을 모두 각각의 모듈로 보고 이를 조합해서 병합된 하나의 결과물을 만드는 도구를 의미

### 모듈이란?

- 모듈이란 프로그래밍 관점에서 특정 기능을 가지는 작은 코드 단위를 의미
- 예시: 아래 코드(math.js)는 3가지 기능을 갖고 있는 모듈
- 성격이 비슷한 기능들을 하나의 의미 있는 파일로 관리하면 모듈이 된다.
- 웹팩에서 지칭하는 모듈이라는 개념은 위와 같이 자바스크립트 모듈에만 국한되지 않고 웹 애플리케이션을 구성하는 모든 자원을 의미 (HTML, CSS, Javascript, Images, Font 등)

```JavaScript
// math.js
function sum(a, b) {
  return a + b;
}

function substract(a, b) {
  return a - b;
}

const pi = 3.14;

export { sum, substract, pi }

```

### 모듈 번들링이란?

- 웹 애플리케이션을 구성하는 몇십, 몇백개의 자원들을 하나의 파일로 병합 및 압축 해주는 동작을 모듈 번들링이라고 한다.

- `빌드`, `번들링`, `변환` 모두 같은 의미

![w](./images/webpack-bundling.png)

## 웹팩이 등장한 이유

1. 파일 단위의 자바스크립트 모듈 관리

2. 웹 개발 작업 자동화 도구 (Web Task Manager)

3. 웹 애플리케이션의 빠른 로딩 속도와 높은 성능

## 웹팩으로 해결하려는 문제?

1. **자바스크립트 변수 유효 범위**

   - 웹팩은 변수 유효 범위의 문제점을 ES6의 Modules 문법과 웹팩의 모듈 번들링으로 해결

2. **브라우저별 HTTP 요청 숫자의 제약**

   - 클라이언트에서 서버에 HTTP 요청을 보내기 위해서는 먼저 TCP/IP가 연결되어야 한다.

   - TCP 스펙에 따라 브라우저에서 한 번에 서버로 보낼 수 있는 HTTP 요청 숫자는 제약되어 있다.

3. **사용하지 않는 코드의 관리**
4. **Dynamic Loading & Lazy Loading 미지원**

   - 웹팩의 Code Splitting 기능을 이용하여 원하는 모듈을 원하는 타이밍에 로딩할 수 있다.

## 웹팩의 4가지 주요 속성

![d](./images/diagram.png)

- Entry 속성은 웹팩을 실행할 대상 파일. 진입점
- Output 속성은 웹팩의 결과물에 대한 정보를 입력하는 속성. 일반적으로 filename과 path를 정의
- Loader 속성은 CSS, 이미지와 같은 비 자바스크립트 파일을 웹팩이 인식할 수 있게 추가하는 속성. 로더는 오른쪽에서 왼쪽 순으로 적용
- Plugin 속성은 웹팩으로 변환한 파일에 추가적인 기능을 더하고 싶을 때 사용하는 속성. 웹팩 변환 과정 전반에 대한 제어권을 갖고 있음

> 위 속성 이외에도 resolve, devServer, devtool 속성이 있다.

1. **entry**

- `entry` 속성은 웹팩에서 웹 자원을 변환하기 위해 필요한 최초 진입점이자 자바스크립트 파일 경로
- 웹팩을 실행했을 때 src 폴더 밑의 index.js 을 대상으로 웹팩이 빌드를 수행
- entry 속성에 지정된 파일에는 웹 애플리케이션의 `전반적인 구조`와 `내용`이 담겨져 있어야 함
- 웹팩이 해당 파일을 가지고 웹 애플리케이션에서 사용되는 모듈들의 연관 관계를 이해하고 분석
- 예시: 블로그 서비스를 웹팩으로 빌드

```javascript
// index.js
import LoginView from "./LoginView.js";
import HomeView from "./HomeView.js";
import PostView from "./PostView.js";

function initApp() {
  LoginView.init();
  HomeView.init();
  PostView.init();
}

initApp();
```

![w](./images/webpack-entry.png)

- 위와 같이 모듈 간의 의존 관계가 생기는 구조를 `디펜던시 그래프(Dependency Graph)`라고 함

- 엔트리 포인트는 여러 개가 될 수도 있다.
- 엔트리 포인트를 분리하는 경우는 `싱글 페이지 애플리케이션`이 아닌, 특정 페이지로 진입했을 때 서버에서 해당 정보를 내려주는 형태의 `멀티 페이지 애플리케이션`에 적합

```javascript
// webpack.config.js

// 1개
entry: "./src/index.js";

// 여러개
entry: {
login: './src/LoginView.js',
main: './src/MainView.js'
}
```

2. **output**

- `output` 속성은 웹팩을 돌리고 난 결과물의 파일 경로를 의미
- `entry` 속성과는 다르게 객체 형태로 옵션들을 추가
- 최소한 `filename`은 지정해줘야 하며 일반적으로 `path` 속성을 함께 정의

  2.1. Output 파일 이름 옵션

  - filename 속성에 여러 가지 옵션을 넣을 수 있다.
  - 생성된 결과 파일의 이름에는 각각 엔트리 이름, 모듈 아이디, 해시 값 등이 포함

  ```javascript
  // 결과 파일 이름에 entry 속성을 포함하는 옵션
  module.exports = {
    output: {
      filename: "[name].bundle.js",
    },
  };

  // 결과 파일 이름에 웹팩 내부적으로 사용하는 모듈 ID를 포함하는 옵션
  module.exports = {
    output: {
      filename: "[id].bundle.js",
    },
  };

  // 매 빌드시 마다 고유 해시 값을 붙이는 옵션
  module.exports = {
    output: {
      filename: "[name].[hash].bundle.js",
    },
  };

  // 웹팩의 각 모듈 내용을 기준으로 생생된 해시 값을 붙이는 옵션
  module.exports = {
    output: {
      filename: "[chunkhash].bundle.js",
    },
  };
  ```

3. **loader**

- `로더(Loader)`는 웹팩이 웹 애플리케이션을 해석할 때, 자바스크립트 파일이 아닌 웹 자원(HTML, CSS, Images, 폰트 등)들을 변환할 수 있도록 도와주는 속성
- `module` 이름을 사용

- app.js 파일에서 임포트한 common.css 파일을 해석하기 위해 적절한 로더를 추가해야함
- NPM 명령어로 CSS 로더를 설치하고 웹팩 설정 파일 변경
- module 쪽 코드를 보면 rules 배열에 객체에는 2개의 속성이 들어가 있다.
  - test: 로더를 적용할 파일 유형 (일반적으로 정규 표현식 사용)
  - use: 해당 파일에 적용할 로더의 이름

```javascript
// app.js
import "./common.css";

console.log("css loaded");
```

```
npm i css-loader -D
```

```javascript
// webpack.config.js
module.exports = {
  entry: "./app.js",
  output: {
    filename: "bundle.js",
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["css-loader"],
      },
    ],
  },
};
```

- 위 코드는 해당 프로젝트의 모든 CSS 파일에 대해서 CSS 로더를 적용하겠다는 의미

- 자주 사용되는 로더 종류
  - Babel Loader
  - Sass Loader
  - File Loader
  - Vue Loader
  - TS Loader

```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      // 로더는 기본적으로 오른쪽에서 왼쪽 순으로 적용
      // scss 파일에 대해 먼저 Sass 로더로 전처리(scss 파일을 css 파일로 변환)를 한 다음 웹팩에서 CSS 파일을 인식할 수 있게 CSS 로더를 적용
      {
        test: /\.scss$/,
        use: ["css-loader", "sass-loader"],
      },
      {
        test: /\.ts$/,
        use: "ts-loader",
      },
      // ...
    ],
  },
};
```

4. **plugin**

- `플러그인(plugin)`은 웹팩의 기본적인 동작에 추가적인 기능을 제공하는 속성
- `로더`는 파일을 해석하고 변환하는 과정에 관여하는 반면, `플러그인`은 해당 결과물의 형태를 바꾸는 역할
- 플러그인의 배열에는 생성자 함수로 생성한 객체 인스턴스만 추가
- 자주 사용하는 플러그인
  - split-chunks-plugin
  - clean-webpack-plugin
  - image-webpack-loader
  - webpack-bundle-analyzer-plugin

```javascript
// webpack.config.js
var webpack = require("webpack");
var HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  plugins: [
    // 웹팩으로 빌드한 결과물로 HTML 파일을 생성해주는 플러그인
    new HtmlWebpackPlugin(),
    // 웹팩의 빌드 진행율을 표시해주는 플러그인
    new webpack.ProgressPlugin(),
  ],
};
```
