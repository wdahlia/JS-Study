# 프로미스

<br>

## 비동기 처리를 위한 콜백 패턴의 단점

- 비동기 함수는 내부에 비동기로 동작하는 코드를 포함한 함수를 일컬음
- 비동기 함수를 호출하면, 함수 내부의 비동기로 동작하는 코드가 완료되지 않았다 해도 기다리지 않고 즉시 종료
- 즉, 비동기 함수 내부의 비동기로 작동하는 코드는 비동기 함수가 종료된 이후 완료
- 따라서, 비동기 함수 내부의 비동기로 동작하는 코드에서 처리결과를 외부로 반환하거나 상위 스코프의 변수에 할당하면 기대대로 동작하지 않음

```javascript
// case 1 
let g = 0;

setTimeout(() => { g = 100; }, 0);
// 비동기 함수 내부에 콜백함수는 이미 선언된 변수인 g에 100을 재할당하는 함수 존재

console.log(g);
// 실제 결과는 0
// setTimeout내부의 콜백 함수는 setTimeout 함수가 종료된 이후에 호출되므로
// 함수 내부의 콜백 함수에서 처리 결과를 외부로 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 아니함


// case 2
const get = url => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      return JSON.parse(xhr.response);
    }
    console.error(`${xhr.status} ${xhr.statusText}`);
  };
};

const response = get('https;//jsonplaceholder.typicode.com/posts/1');
// get함수가 호출되면, XMLHttpRequest 객체 생성하고 http 요청 초기화 후, 요청 전송
// xhr.onload 이벤트 핸들러 프로퍼티에 이벤트 핸들러를 바인딩하고 종료 get 함수에 명시적인 반환문이 없어서 undefined 반환
// xhr.onload에 return문이 존재하나 이는 get 함수의 반환문이 아님 onload 이벤트를 get 함수가 호출할 수 있다면 반환값이 있을 수도 있으나 onload 이벤트 핸들러는 get 함수가 호출하지 않기 떄문에 캐치할 수 없음
console.log(response); // undefined


// case 3
let todos;

const get = url => {
  ....
  xhr.onload = () -> {
    if (xhr.status === 200) { 
      todos = JSON.parse(xhr.response);
      ...
    }
  }
}
....
console.log(todos); 
// 상위스코프에 변수를 선언하고 onload 내부에서 todos에 재할당을 해줘도 똑같이 todos는 undefined 반환
// onload 이벤트 핸들러 프로퍼티에 바인딩한 이벤트 핸들러는 언제나 console.log가 종료된 이후에 호출
// 즉, console.log(todos);가 실행 될 때는 응답결과가 반환되기 이전이기 때문에 값이 할당되지 못하고 undefined 반환하는 것
// get 함수가 실행된 후 콜 스택에서 팝되고, console.log()가 실행되고
// 그 이후 서버로부터 응답이 도착하면 xhr객체에서 load 이벤트가 발생해서 태스크큐에 저장되어 대기하다 콜 스택이 비면 이벤트 루프에 의해 콜 스택으로 푸시되어 실행
``` 

- 비동기 함수는 비동기 처리 결과를 **외부에 반환할 수도, 상위 스코프의 변수에 할당할 수도 없다**.
- 따라서, 비동기 함수의 처리 결과(서버의 응답)에 대한 후속 처리는 비동기 함수 내부에서 수행해야 함

```javascript
const get = (url, successCallback, failureCallback) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      successCallback(JSON.parse(xhr.response));
    } else {
      failureCallbarck(xhr.status);
    }
  }

  get('https://jsonplaceholder.typicode.com/posts/1', console.log, console.error);
}
```

- 이처럼, 콜백 함수를 통해 비동기 처리 결과에 대한 후속 처리를 수행하는 비동기 함수가 비동기 처리 결과를 가지고 또다시 비동기 함수를 호출해야 한다면, 콜백 함수 호출이 중첩되어 복잡도가 높아지는 `콜백 헬`이라 함

<br>

## 에러 처리의 한계

```javascript
try {
  setTimeout(() => { throw new Error('Error!'); }, 1000);
} catch (e) {
  console.error('캐치한 에러', e)
}
```
- 1초 후에 콜백 함수가 실행되도록 타이머가 설정되고 이후 콜백 함수는 에러를 발생시키는데 catch 코드 블록에서 캐치되지 음
- setTimeout 함수의 콜백 함수가 실행될 때 setTimeout 함수는 이미 콜 스택에서 제거가 된 상태이기 때문에
- setTimeout 함수의 콜백 함수를 호출한 것이 setTimeout 함수가 아니라는 것을 의미 *에러는 호출자 방향으로 전파*됨
- 즉, 콜백 함수가 발행시킨 에러는 catch 블록에 캐치되지 아니함
- 이처럼 에러 처리가 곤란하기 때문에 **프로미스가 도입**

<br>

## 프로미스의 생성

- Promise 생성자 함수를 new 연산자와 함께 호출하면 프로미스를 생성
- 비동기 처리를 수행할 콜백 함수를 인수로 전달 받는데, 이 콜백 함수는 resolve와 reject 함수를 인수로 전달받음

```javascript
const promise = new Promise((resolve, reject) => {
  // 콜백 함수 내부에서 비동기 처리를 수행
  if () {
    resolve('result');
    // 비동기 처리 성공
  } else {
    reject('failure reason');
    // 비동기 처리 실패
  }
});
```
- 프로미스는 비동기 처리가 어떻게 진행되고 있는지를 나타내는 상태 정보를 가짐
- `pending` : 비동기 처리가 아직 수행되지 않은 상태, 프로미스가 생성된 직후 기본 상태 
- `fulfilled` : 비동기 처리가 수행된 상태(성공), resolve 함수 호출
- `rejected` : 비동기 처리가 수행된 상태(실패), reject 함수 호출
- fulfilled나 rejected 상태를 settled 상태라고 함 - 비동기 처리가 수행된 상태
- 프로미스는 즉, 비동기 처리 상태와 처리 결과를 관리하는 객체

<br>

## 프로미스의 후속 처리 메서드

### Promise.prototype.then
- 두 개의 콜백 함수를 인수로 전달 받음
- 비동기 처리가 성공/실패했을때 호출되는 성공/실패 처리 콜백 함수
- then 메서드의 콜백함수가 프로미스를 반환하면 그대로 그 값을 반환, 아니라면 그 값을 암묵적으로 resolve,reject하여 프로미스 생성해 반환

<br>

### Promise.prototype.catch
- 한 개의 콜백 함수를 인수로 전달 받음
- 프로미스가 rejected 상태인 경우에만 호출
- 언제나 프로미스 반환 .then(undefined, onRejected)와 동일하게 동작

<br>

### Promise.prototype.finally
- 한 개의 콜백 함수를 인수로 전달 받음
- 프로미스의 성공, 실패와 상관없이 무조건 한 번 호출
- 상태와 상관없이 공통적으로 수행해야 할 처리 내용이 있을 때 유용 
- 언제나 프로미스를 반환


<br>

## 프로미스 체이닝
- then, catch, finally 후속 처리 메서드는 언제나 프로미스를 반환하므로 연속적으로 호출할 수 있음
- 이를 `프로미스 체이닝`이라 함
- 비동기 처리 결과를 전달받아 후속 처리를 하므로 비동기 처리를 위한 콜백 패턴에서 발생하던 콜백 헬이 발생하지 아니함
- 다만, 프로미스도 콜백 패턴을 사용하므로 콜백 함수를 사용하지 않는 것은 아님
- 콜백 패턴은 가독성이 좋지 않기에 이는 async/await을 통해 해결할 수 있으며, 동기 처리 처럼 프로미스가 처리 결과를 반환하도록 구현 가능

<br>

## 프로미스의 정적 메서드

- 프로미스는 주로 생성자 함수로 사용되나 함수도 객체이므로 메서드를 가질 수 있음
<br>

### `Promise.resolve/Promise.reject`
```javascript
const resolvedPromise = Promise.resolve([1, 2, 3]);
resolvedPromise.then(console.log); // [1, 2, 3]

const rejectPromise = Promise.reject(new Error('ERROR'));
rejectedPromise.catch(console.log); // Error: ERROR

const resolvedPromise = new Promise(resolve => resolve([1, 2, 3]));
resolvedPromise.then(console.log) // [1, 2, 3]

const rejectPromise = Promise.reject((_, reject) => reject(new Error('ERROR')));
rejectedPromise.catch(console.log); // Error: ERROR
```

<br>

### Promise.all

- 여러 개의 비동기 처리를 병렬처리할 때 사용

```javascript
const requestData1 = () => {
  new Promise(resolve => setTimeout(() => resolve(1), 3000));
}
const requestData2 = () => {
  new Promise(resolve => setTimeout(() => resolve(2), 2000));
}
const requestData3 = () => {
  new Promise(resolve => setTimeout(() => resolve(3), 1000));
}

const res = [];
requestData1()
  .then(data => {
    res.push(data);
    return requestData2();
  })
  .then(data => {
    res.push(data);
    return requestData3();
  })
  .then(data => {
    res.push(data);
    console.log(res); // [1, 2, 3] => 6초 소요
  })
  .catch(console.error);

// 서로 의존하지 않고 개별적으로 수행되기 때문에, 앞선 비동기 처리를 다음 비동기 처리가 사용하지 않음
// 즉, 처리 순서가 상관없음
// Promise.all을 사용하면 

Promise.all([requestData1(), requestData2(), requestData3()])
  .then(console.log) // [1, 2, 3] => 약 3초 소요
  .catch(console.error)

// Promise.all 메서드는 인수로 전달받은 배열의 모든 프로미스가 모두 fulfilled 상태가 되면 종료
// 메서드가 종료하는데 걸리는 시간은 가장 fulfilled 상태가 되는 프로미스의 처리 시간보다 조금 더 김
// 처리 순서가 보장 됨
// 하나라도 rejected 상태가 되면 나머지 프로미스가 fulfilled 상태가 되는것을 기다리지 않고 즉시 종료
```

<br>

### Promise.race
- 모든 프로미스가 fulfilled 상태가 되는 것을 기다리는 것이 아닌 가장 먼저 fulfilled 상태가 된 프로미스의 처리 결과를 resolve하는 새로운 프로미스를 반환
- 즉, 위의 예시에서는 3을 반환하게 되는 것
- rejected 상태가 되면, Promise.all 메서드와 동일하게 하나라도 rejected되면 에러를 reject하는 새로운 프로미스 즉시 반환


<br>

### Promise.allSettled
- 전달받은 프로미스가 모두 settled 상태 즉, fulfilled 또는 rejected 상태가 되면 처리 결과를 배열로 반환
- reject이 되든 fulfilled가 되든 모든 프로미스의 처리 결과가 담겨 있는 것
- 처리결과 객체는 비동기 처리 상태를 나타내는 status와 처리 결과/에러를 나타내는 value/reason 프로퍼티를 가짐

<br>

## 마이크로태스크 큐

```javascript
setTimeout(() => console.log(1), 0);

Promise.resolve()
  .then(() => console.log(2));
  .then(() => console.log(3));
```
- 1 > 2 > 3 순으로 출력될 것 같지만, 실제로는 2 > 3 > 1의 순서로 출력된다.
- 프로미스 후속 처리 메서드의 콜백 함수는 태스크 큐가 아닌 **마이크로태스크 큐**이기 때문 
- 태스크 큐보다 마이크로태스크 큐의 우선순위가 높기 때문에 콜 스택이 비게 되면 태스크 큐가 아닌 마이크로태스크 큐에 위치하고 있는 함수가 있다면 이를 먼저 실행시킴

<br>

## fetch
- **fetch**는 XMLHttpRequest 객체와 마찬가지로 HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web AP
- fetch 함수에는 HTTP요청을 전송할 url, http 요청 메서드, http 요청 헤더, 페이로드 등을 설정한 객체를 전달
- HTTP 응답을 나타내는 Response 객체를 래핑한 Promise 객체를 반환
- 프로미스 객체를 반환하기 때문에 후속 처리 메서드를 사용할 수 있으며
- 프로미스가 래핑하고 있는 MIME 타입이 application/json인 응답 몸체를 취득하려면 Response.prototype.json 메서드를 사용하면 됨
<br>

`주의할 점`
- fetch 함수 사용시 에러 처리에 주의해야 함

```javascript
const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1';

fetch(wrongUrl) // URL이 부적절하기 때문에 404 Not Found 에러 발생
  .then(() => console.log('ok'))
  .catch(() => console.log('error'));

// 그런데 결과 값은 ok가 출력 됨
```
- fetch 함수가 반환하는 프로미스는 HTTP 에러가 발생해도 에러를 reject하지 않고 불리언 타입의 ok 상태를 false로 설정한 Response 객체를 반환
- **오프라인 등의 네트워크 장애나 CORS 에러에 의해 요청이 완료되지 못한 경우**에만 프로미스를 **reject**
- 즉, fetch 함수 사용 시에는 프로미스가 resolve한 불리언 타입의 ok 상태를 확인해 명시적으로 에러를 처리할 필요성이 존재
- axios는 HTTP 에러를 reject 하는 프로미스를 반환, 모든 에러를 catch에서 처리 할 수 있어 편리

```javascript
...

fetch(wrongUrl)
  .then(response => {
    // response는 HTTP 응답을 나타내는 Response 객체
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then(todo => console.log(todo))
  .catch(err => console.error(err));
```