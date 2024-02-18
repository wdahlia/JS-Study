## 🔖 개요
ES6에서 도입된 제너레이터는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수 함수이다.

제너레이터와 일반 함수의 차이는 무엇일까?

제너레이터를 이용해 비동기 처리를 동기 처리처럼 동작하는 코드를 작성할 수 있지만, 코드가 장황해지고 가독성이 나빠진다. 이를 극복하기 위한 async/await에 대해서도 알아보자.

## 🔖 제너레이터
제너레이터와 일반 함수의 차이는 다음과 같다.

1. 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있음
2. 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있음
3. 제너레이터 함수를 호출하면 제너레이터 객체를 반환 
   
## 🔖 제너레이터 함수의 정의
제너레이터 함수의 선언 방법은 `function*`키워드 `yield` 표현식을 이용한다.

```js
function* genDecFunc() {
    yield 1;
}

class MyClass {
    * genClsMethod() {
        yield 1;
    }
}
```

`*`의 위치는 function 키워드와 함수 이름 사이라면 어디든 상관 없다. 하지만 위치의 일관성을 지키는 것이 좋다.

제너레이터 함수는 **화살표 함수로 정의할 수 없고, `new` 연산자와 함께 생성자 함수로 호출할 수 없다.**

## 🔖 제너레이터 객체
제너레이터 함수를 호출하면, 제너레이터 객체를 생성하여 반환한다. 

반환된 제너레이터 객체는 `Symbol.iterator` 메서드를 상속 받는 **이터러블**이면서, 동시에 `value`, `done` 프로퍼티를 갖는 객체를 반환하는 `next` 메서드를 가진 **이터레이터**다.

```js
function* genFunc() {
    yield 1;
    yield 2;
    yield 3;
}

// 제너레이터 객체가 반환됨
const generator = genFunc();

// 이터러블
console.log(Symbold.iterator in generator); // true

// 이터레이터
console.log('next' in generator); // true
```

제너레이터 객체는 `return`, `throw` 메서드도 갖는다. 제너레이터 객체가 갖는 세 개의 메서드를 호출하면 다음과 같이 동작한다.

1. `next` : 제너레이터 함수의 `yield` 표현식까지 코드 실행, `yield`된 값을 `value` 프로퍼티 값 로, `false`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환
```js
console.log(generator.next()); // { value: 1, done: false }
```
2. `return` : 인수로 전달 받은 값을 `value` 프로퍼티 값으로, `true`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환
```js
console.log(generator.next("End")); // { value: "End", done: true }
```
3. `throw` : 인수로 전달받은 에러를 발생시키고 `undefined`를 프로퍼티 값으로, `true`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환
```js
console.log(generator.throw('error')); // { value: undefined, done: true }
```

## 🔖 제너레이터의 일시 중지와 재개
제너레이터 함수의 특징으로, `yeild` 키워드와 `next` 메서드를 이용해 **실행을 일지 중지했다가 필요한 시점에 다시 재개**할 수 있다.

제너레이터 함수 실행 시, 일반 함수처럼 코드 블록이 실행되는 것이 아니라 제너레이터 객체를 반환하게 된다. 코드 실행은 `yield` 표현식까지만 실행한다. 이 키워드는 제너레이터 함수의 실행을 일시 중지시키거나, 키워드 뒤에 오는 표현식의 평가 결과를 함수 호출자에게 반환한다.

```js
function* genFunc() {
    yield 1;
    const x = yield (x + 1);
    const y = yield (x + 2);

    return x + y;
}
```

위와 같은 제너레이터 함수가 있다.

`yield` 키워드를 기점으로 함수를 일시 중지하고 재개할 수 있다.

```js
const generator = genFunc();

// 반환되는 리절트 객체는 value, done으로 구성됨
console.log(generator.next()); // 1, false 
console.log(generator.next(2)); // 3, false
console.log(generator.next(3)); // 5, false
console.log(generator.next()); // 8, true
```

## 🔖 제너레이터의 활용
### 📍 이터러블의 구현
제너레이터는 이터러블이자 이터레이터이므로, 제너레이터의 이터레이터 함수를 사용하면 이터러블을 구현할 수 있다.

다음은 무한 피보나치 이터러블을 생성하는 함수이다.

```js
const infiniteFibonacci = (function* () {
    le [pre, cur] = [0, 1];

    while (true) {
        [pre, cur] = [cur, pre + cur];
        yield cur;
    }
}());

for (const num of infiniteFibonacci) {
    if (num > 10000) break;
    console.log(num);
}
```

### 📍 비동기 처리
일시 정지 및 재개가 가능한 제너레이터의 특성을 이용하면, `yield` 표현식과 `next` 메서드를 통해 비동기 처리 코드를 구현할 수도 있다.

```js
const fetch = require('node-fetch');

const async = (generatorFunc) => {
    const generator = generatorFunc();

    // 클로저 함수
    const onResolved = (arg) => {
        const result = generator.next(arg);

        return result.done
            ? result.value
            : result.value
                .then((res) => onResolved(res)); // 프로미스 객체를 주고받는 재귀호출
    };

    return onResolved;
};

(async(function* fetchTodo() {
    const url = "url";

    const response = yield fetch(url);
    const todo = yield response.json();
    console.log(todo);
})());
```

## 🔖 async/await
제너레이터를 이용한 비동기 처리 코드는 가독성이 나쁘다.

ES8에서는 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 구현할 수 있는 `async/await`가 도입되었다.

위에서 제너레이터로 구현한 비동기 예제를 다시 구현해보자.

```js
const fetch = require('node-fetch');

async function fetchTodo() {
    const url = "url";

    const response = await fetch(url);
    const todo = await response.json();
    console.log(todo);
}
```

### 📍 async 함수
`await` 키워드는 반드시 `async` 함수 내부에서 사용해야 한다.

`async` 함수는 언제나 암묵적으로 반환값을 resolve하는 프로미스를 반환한다.

class 내부의 constructor 메서드는 인스턴스를 반환해야 하므로 `async` 메서드가 될 수 없다.

```js
async function foo (n) { return n; }
foo(1).then((v) => console.log(v)); // output: 1
```

### 📍 await 함수
`await` 키워드는 프로미스가 settled 상태가 될 때까지 대기한 후, settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환한다. 프로미스를 반환하는 구문 앞에서만 사용할 수 있다.

```js
const res = await fetch ('url');
const { name } = await res.json();
```

### 📍 에러 처리
콜백 패턴을 이용한 비동기 처리는 에러를 처리하기에 아주 힘들었다. 기본적으로 에러 처리는 try-catch문을 이용할 수 있는데, 기존 방식으로는 호출자가 비동기 함수가 아니었기에 사용할 수 없었다.

`async/await`에서는 에러 처리로 try-catch문을 사용할 수 있다.

콜백 함수를 인수로 전달 받는 비동기 함수와 달리, 

프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있으므로 호출자가 명확하기 때문이다.

```js
const foo = async () => {
    try {
        const res = await fetch(url);
        const data = await res.json();
    } catch (e) {
        console.error(e);
    }
}
```