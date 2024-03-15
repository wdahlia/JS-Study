## 🔖 개요
**모듈**은 애플리케이션을 구성하는 개별적 요소로서, 재사용 가능한 코드 조각을 말한다. 

모듈을 구성하고, 이를 재사용하는 방법에 대해 알아보자.

## 🔖 모듈의 일반적 의미
모듈이 성립하기 위해서는 자신만의 **파일 스코프(모듈 스코프)** 를 가질 수 잇어야 한다.

모듈은 캡슐화되어 다른 모듈에서 접근할 수 있고, 각 모듈은 개별적 존재로서 애플리케이션과 분리되어 존재하게 된다.

이렇게 구성된 모듈을 다른 모듈이나 애플리케이션에 의해 재사용해야 한다.

### 📍 import와 export
- **export** : 모듈이 공개가 필요한 자산에 한정하여 명시적으로 선택적 공개를 하는 것
- **import** : 모듈 사용자가 모듈이 공개한 자산 중 일부 또는 전체를 선택해 자신의 스코프 내로 불러들여 재사용하는 것

import와 export를 통해 애플리케이션 내 코드의 단위를 명확히 분리할 수 있고, **재사용성**이 좋아지기에 **개발 효율성**과 **유지보수성**을 높일 수 있다.

## 🔖 자바스크립트와 모듈
자바스크립트는 C언어처럼 모듈을 사용하기 위한 파일 스코프 및 import, export를 지원하지 않는다.

모듈처럼 사용하기 위해 자바스크립트 파일을 script 태그로 로드해도, **하나의 자바스크립트 파일 내에 있는 것처럼 작동**하기 때문에 변수 중복 등의 문제가 발생할 수 있다.

이를 극복하기 위해 제안된 대책으로 CommonJS와 AMD(Asynchronous Module Definition)이 있다. 자바스크립트에서 모듈을 사용하기 위해는 이들을 구현한 **모듈 로더 라이브러리**를 사용해야 했다.

현재의 **Node.js 런타임 환경에서는 CommonJS가 채택**되었고, 모듈 시스템을 지원하기 때문에 파일별로 독립적인 파일 스코프(모듈 스코프)를 갖게 되었다.

## 🔖 ES6 모듈 (ESM)
ES6부터는 클라이언트 자바스크립트에서도 동작하는 모듈 기능이 추가되었다.

ESM의 사용법은 다음과 같다.
```html
<script type="module" src="app.mjs"></script>
```

`type="module"` 속성을 통해 로드된 js 파일은 모듈로 동작한다.

ESM임을 명시하기 위해 `.mjs` 파일 확장자 사용을 권장한다.

### 📍 모듈 스코프
ESM은 독자적인 모듈 스코프를 갖는다.

ESM이 아닌 기존의 js 파일은 여러 파일을 로드해도 하나의 js 파일처럼 동작하기에 독자적인 모듈 스코프를 갖지 않는다. (변수 중복, 재할당 등의 문제 발생)

ESM은 파일 자체의 독자적인 모듈 스코프를 제공하기에, ESM 내에서 선언한 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니게 된다.

## 🔖 export 키워드
모듈 내부에서 선언한 식별자를 외부에 공개하여 다른 모듈들이 재사용할 수 있도록 하려면 export 키워드를 사용한다.

export 방법은 다음과 같이 두 가지 방법이 있다.
```js
export const pi = Math.PI;

export function square(x) {
    return x * x;
}

export class Person {
    /* ... */
}
```

```js
const pi = Math.PI;

function square(x) {
    return x * x;
}

class Person {
    /* ... */
}

export { pi, square, Person };
```

## 🔖 import 키워드
다른 모듈에서 export한 식별자를 로드하기 위해 import 키워드를 사용한다.

💡 ESM 파일은 파일 확장자를 생략할 수 없다.

```js
import { pi, square, Person } from './lib.mjs';

console.log(pi, square(10), new Person('miseon'));
```

```html
<script type="module" src="app.mjs"></script>
```

`app.mjs`는 애플리케이션의 진입점이므로, import문에 의해 로드되는 의존성이 존재하기에 반드시 script 태그로 로드해야 하지만, `lib.mjs`는 그렇지 않다.

### 📍 모듈이 export한 식별자 이름을 변경하여 import 하기
```js
import * as lib from './lib.mjs';

console.log(lib.pi);
```

### 📍 하나의 값만 export 하기
```js
// lib.mjs
export default x => x * x;
```

```js
// app.mjs
import square from './lib.mjs';
```