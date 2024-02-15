## 🔖 개요
비동기 처리를 위해 ES6에서는 프로미스 패턴을 도입했다. 콜백 지옥의 단점을 보완하기 위한 방안으로, 비동기 처리 시점을 명확하게 표현할 수 있는 장점이 있다.

프로미스 생성 방법, 프로미스를 이용한 에러 처리 등...

프로미스로 할 수 있는 여러 패턴을 알아보자.

## 🔖 비동기 처리를 위한 콜백 패턴의 단점
```js
let g = 0;

// setTimeout 함수는 비동기 코드를 포함한 비동기 함수
setTimeout(() => { g = 100; }, 0);
console.log(g); // output: 0
```
비동기 함수 호출 시, 함수 내부의 비동기 코드가 완료되지 않았다 해도 즉시 종료된다. 

그렇기에 비동기 함수 내부의 비동기 코드는 비동기 함수 종료 이후에 실행 된다. (ex: `onload` 이벤트 핸들러)

따라서 비동기 함수 내부 비동기 코드에서 처리 결과를 반환하거나, 상위 스코프의 변수에 할당하는 것은 동작하지 않는다.

*그렇다면, 비동기 함수의 처리 결과(서버 응답 등)에 대한 후속 처리는 어떻게 해야할까?*

**비동기 함수 내부**에서만 수행할 수 있다. 

이 때, 비동기 함수를 범용적으로 사용하기 위해 비동기 함수에 **비동기 처리 결과에 대한 후속 처리를 수행하는 콜백 함수**를 전달하는 방법이 일반적이다. 비동기 처리가 성공할 때 호출되는 콜백 함수와 실패할 때 호출되는 콜백 함수를 전달하는 예외 처리를 할 수 있다.

### 📍 콜백 헬
이렇게 계속 중첩되는 콜백 함수에 의해 발생하는 현상이 **콜백 헬**이다. 콜백 헬에 의해 코드 가독성이 나빠지고, 실수를 유발하게 된다. 

무엇보다 에러 처리가 아주 힘들어지기에 유지보수 측면에서 매우 곤란해진다. 

이를 극복하기 위해 ES6에서 프로미스가 도입되었다.

## 🔖 프로미스의 생성
### 📍 프로미스 생성
Promise 생성자 함수를 `new` 연산자와 함께 호출하면 프로미스 객체를 생성하고, 비동기 처리를 수행할 콜백 함수를 인수로 전달 받는다. 이 콜백 함수는 `resolve`와 `reject` 함수를 인수로 전달 받는다.

```js
const promise = new Promise(((resolve, reject) => {
    if (/* 비동기 처리 성공 */) {
        resolve('result');
    }
    /* 비동기 처리 실패 */
    else {
        reject('fail');
    }
}));
```

인수로 전달 받은 두 함수를 통해 콜백 함수 내부에서 비동기 처리를 수행할 수 있다. 성공 여부에 따라 예외를 처리할 수도 있다.

예시 get 함수를 프로미스를 사용해 구현해 보자.

```js
const promiseGet = (url) => {
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();
        xhr.open('GET', url);
        xhr.send();

        xhr.onload = () => {
            if (xhr.status === 200) {
                resolve(JSON.parse(xhr.response));
            }
            else {
                reject(new Error(xhr.status));
            }
        }
    })
}

promiseGet(url);
```

### 📍 프로미스 상태
반환되는 프로미스는 현재 비동기 처리의 진행 상태 정보를 갖는다.
`fulfilled`, `rejected` 상태는 `settled` 상태라고 한다.

|프로미스 상태 정보|의미|상태 변경 조건|
|--|--|--|
|`pending`|비동기 처리가 아직 수행되지 않은 상태|프로미스 생성 직후 기본 상태|
|`fulfilled`|비동기 처리 수행 완료(성공)|`resolve` 함수 호출|
|`rejected`|비동기 처리 수행 완료(실패)|`reject` 함수 호출|

결과적으로, 프로미스는 **비동기 처리 상태와 처리 결과를 관리하는 객체**이다.

## 🔖 프로미스 후속 처리 메서드
비동기 처리 상태 변화 시, 반환되는 프로미스를 이용하여 후속 처리 메서드에 인수로 전달한 콜백 함수가 호출되고, 이에 따른 후속 처리를 해야 한다. 후속 처리 메서드는 언제나 프로미스를 반환한다.

### 📍 Promise.prototype.then
두 개의 콜백 함수를 인수로 전달 받는다.
- 첫 번째 함수 : 프로미스가 `fulfilled` 상태가 되면 호출되고, 프로미스의 비동기 처리 결과를 인수로 전달 받음
- 두 번째 함수 : 프로미스가 `rejected` 상태가 되면 호출되고, 프로미스의 에러를 인수로 전달 받음
```js
// output : fulfilled
new Promise(resolve => resolve('fulfilled'))
    .then((v) => console.log(v), (e) => console.log(e));

// output : rejected
new Promise((_, reject) => reject(new Error('rejected')))
    .then((v) => console.log(v), (e) => console.log(e));
```

### 📍 Promise.prototype.catch
한 개의 콜백 함수를 인수로 전달 받는다.
- 프로미스가 `rejected` 상태인 경우에만 호출됨

```js
// output : rejected
new Promise((_), reject(new Error('rejected')))
    .catch((e) => console.log(e));
```

### 📍 Promise.prototype.finally
한 개의 콜백 함수를 인수로 전달 받는다.
- 프로미스의 상태(성공 여부)에 상관 없이 무조건 한 번 호출됨

```js
// output : finally
new Promise(() => {})
    .finally(() => console.log('finally'));
```

`finally` 메서드를 통해 프로미스의 상태와 상관없이 공통적으로 수행해야 할 처리 내용이 있을 때 유용하다.

## 🔖 프로미스의 에러 처리
콜백 패턴은 에러 처리가 복잡하고 가독성이 낮지만, 프로미스는 에러를 아주 간단히 문제 없이 처리할 수 있다. 앞서 배운 `then`, `catch`, `finally`를 사용하여 수행하면 된다. 참고로, 에러 처리는 `then` 메서드보다 `catch` 메서드를 사용하는 것이 좋고 명확하다.

## 🔖 프로미스 체이닝
프로미스는 후속 처리 메서드를 이용하여 콜백 헬을 해결할 수 있다. 

후속 처리 메서드로는 `then`, `catch`, `finally`가 있는데, 이 메서드들은 언제나 프로미스를 반환하기 때문에 연속적으로 호출하는 것이 가능하다. 이를 **프로미스 체이닝**이라고 한다.

## 🔖 프로미스의 정적 메서드
Promise는 생성자 함수로 사용되지만, 함수는 객체이기 때문에 메서드를 가질 수 있다. Promise 객체는 5가지 정적 메서드를 제공한다.

### 📍 Promise.resolve / Promise.reject
두 메서드는 이미 존재하는 값을 래핑하여 프로미스를 생성하기 위해 사용한다. 앞서 배웠듯이 두 함수는 프로미스를 생성하여 반환한다.

아래 두 예제는 동일하게 동작한다.
```js
// output: Error!
const rejectedPromise = Promise.reject(new Error('Error!'));
rejectedPromise.catch(console.log);
```
```js
// output: Error!
const rejectedPromise = new Promise((_, reject) => reject(new Error('Error!')));
rejectedPromise.catch(console.log);
```

### 📍 Promise.all
여러 개의 비동기 처리를 모두 병렬 처리할 때 사용한다.

여러 개의 비동기 처리를 하는 함수를 하나씩 호출할 경우, 여러 개의 비동기 처리를 순차적으로 처리한다. 하나의 비동기 처리에 3초가 소요되고 3번 호출한다면, 총 3*3=9초의 시간이 소요된다. `Promise.all`은 이를 병렬 처리하여 해결한다.

```js
Promise.all([requestData_1(), requestData_2(), requestData_3()]);
    .then(console.log);
    .catch(console.log);
```

이렇게 여러 개의 비동기 함수를 배열로 만들어 인수로 전달하면, 여러 개의 비동기 함수를 병렬 처리할 수 있다. 하나의 비동기 처리에 3초가 소요된다면, 총 3초의 시간이 소요되므로 단축된다.

함수가 반환하는 모든 프로미스가 `fulfilled` 상태가 되면, `resolve`된 처리 결과를 모두 배열에 저장하여 새로운 프로미스를 반환한다. 해당 배열은 `Promise.all` 함수에 인수로 전달된 순서대로 프로미스가 저장된다. **따라서, 처리 순서가 보장된다.**

### 📍 Promise.race
해당 함수 또한 프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달 받는다.

`Promise.all`과 다른 점으로는, 모든 프로미스가 `fulfilled` 상태가 되는 것을 기다리는 게 아니라 먼저 `fulfilled` 상태가 된 프로미스의 처리 결과를 `resolve`하는 새로운 프로미스를 반환한다. **처리 순서가 보장되지 않는다.**

### 📍 Promise.allSettled
전달 받은 프로미스가 모두 `settled` 상태가 되면 처리 결과를 배열로 반환한다. 그렇기에 각각 해당하는 프로미스가 `fulfilled` 또는 `rejected` 상태와 관련 없이 결과를 담는다.
- `fulfilled`인 경우, 비동기 처리 상태를 나타내는 `status` 프로퍼티와 처리 결과를 나타내는 `value` 프로퍼티를 갖는다.
  - `{ status: "fulfilled", value: 1 }`
- `rejected`인 경우, 비동기 처리 상태를 나타내는 `status` 프로퍼티와 에러를 나타내는 `reason` 프로퍼티를 갖는다.
  - `{ status: "rejected", reason: <Error> }`

## 🔖 마이크로태스크 큐
다음 예제의 출력을 생각해 보자.
```js
setTimeout(() => console.log(1), 0);

Promise.resolve()
    .then(() => console.log(2));
    .then(() => console.log(3));
```
처리 결과는 어떨까?

2 -> 3 -> 1의 순서로 출력된다.

프로미스의 후속 처리 메서드의 콜백 함수는 **태스크 큐가 아닌, 마이크로태스크 큐**에 저장되기 때문이다. 마이크로태스크 큐는 태스크 큐보다 우선순위가 높다.

이벤트 루프는 콜 스택이 비면 먼저 마이크로태스크 큐에서 대기하고 있는 함수를 가져와 실행한다. 이후 마이크로태스크 큐가 비면 태스크 큐에서 대기하고 있는 함수를 가져와 실행하게 된다.

## 🔖 fetch
`XMLHttpRequest` 객체와 마찬가지로 HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web API이다. 프로미스를 제공하기 때문에 콜백 패턴의 단점에서 자유롭다.

### 📍 사용 방법
```js
const promise = fetch(url, [, options]);

fetch(url)
    .then((response) => console.log(response));
```

`fetch` 함수는 HTTP 응답을 나타내는 `Response` 객체를 래핑한 `Promise` 객체를 반환한다. 후속 처리 메서드를 통해 `resolve`한 `Response` 객체를 전달받을 수 있다. `json` 메서드를 통해 HTTP response body를 역직렬화할 수도 있다.

주의해야 할 점으로는, `fetch` 함수는 에러 처리 중 404, 500과 같은 HTTP 에러가 발생하여도 reject하지 않고 `boolean` 타입의 `ok` 상태를 `false`로 설정한 `Response` 객체를 `resolve`한다. 네트워크 장애, CORS 에러에 의해 요청이 완료되지 않은 경우에만 프로미스를 reject한다는 것을 알아두어야 한다.

이를 명시적으로 해결하기 위해 `boolean` 타입의 `ok` 상태를 후속 처리 메서드에서 확인하여 처리해주는 것이 중요하다.