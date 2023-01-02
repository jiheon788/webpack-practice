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
