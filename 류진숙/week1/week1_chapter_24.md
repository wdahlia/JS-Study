# 클로저
<br>

- 함수와 그 함수가 선언된 렉시컬 환경과의 조합

### 함수가 선언된 렉시컬 환경

```javascript
// case 1
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }

  innerFunc();
}

outerFunc();

// case 2
const x = 1;

function outerFunc() {
  const x = 10;
  innerFunc();
}

function innerFunc() {
  console.log(x); // 1
}

outerFunc();
```
- *outerFunc* 함수 내부에서 중첩 함수 *innerFunc*가 정의 호출
- *innerFunc*의 상위 스코프는 외부 함수 *outerFunc*의 스코프
- 그리하여, 중첩 함수 *innerFunc* 내부에서 외부 함수 *outerFunc*의 x 변수에 접근 가능
- case2처럼 중첩 함수가 아니였다면 *outerFunc* 내부에서 호출 한다고 하더라도 변수에 접근 불가능

<br>

## 렉시컬 스코프

- 자바스크립트 엔진은 함수를 어디에서 호출했는지가 아닌, 함수를 어디에 정의했는지에 따라 상위 스코프를 결정하는데 이를 `렉시컬 스코프(정적 스코프)`라고 함

```javascript
const x=1;

function foo(x) {
  const x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo();
bar();
```
- foo, bar 모두 전역에서 정의된 함수 이므로, 상위 스코프는 전역
- 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조값, 즉 상위 스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 환경(위치)에 의해 결정 이것이 **렉시컬 스코프**
<br>

## 함수 객체의 내부 슬롯 [[Environment]]
- 함수가 정의된 환경(위치)와 호출되는 환경(위치)는 다를 수 있음
- 렉시컬 스코프가 가능하려면 자신이 정의된 환경, 즉 상위 스코프를 기억해야함
- 이를 위해 함수는 자신의 내부 슬롯 [[Environment]]에 자신이 정의된 환경, 상위 스코프의 참조를 저장
- 위의 예시에서 살펴보면 함수 bar는 호출 위치와 상관 없이 함수 정의 환경에 따라 상위 스코프가 결정되므로, 전역 렉시컬 환경을 [[Enviroment]]에 저장, 기억함
- *foo()*, *bar()* 모두 전역에서 함수 선언문으로 정의되었으므로, 전역 객체 window의 메서드가 됨 또한 내부 슬롯 [[Enviroment]]에는 함수 정의가 평가된 시점, 즉 전역 코드 평가 시점에 실행 중인 실행 컨텍스트의 렉시컬 환경인 전역 렉시컬 환경의 참조 저장
- 함수가 호출 되면 함수 내부로 코드 제어권이 이동하고 함수 코드를 평가
- 외부 렉시컬 환경에 대한 참조에는 함수 객체의 내부 슬롯 [[Enviroment]]에 저장된 렉시컬 환경의 참조가 할당

<br>

## 클로저와 렉시컬 환경

```javascript
const x = 1;

// 1
function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  }; // 2
  return inner;
}

const innerFunc = outer(); // 3
innerFunc(); // 4
```
- 3번의 outer를 호출 하면 중첩함수 inner을 반환하고 생명 주기 마감 ➡️ 실행 컨텍스트 스택에서 팝되어 제거
- outer의 지역 변수 x와 변수 값 10을 저장하고 있던 outer 함수의 실행 컨텍스트가 제거되었으므로 지역 변수 x도 생명 주기 마감
- 지역 변수 x는 즉 유효하지 않은 값이 되므로 console.log(x); 를 담고 있는 inner 함수는 뱉어 줄 값이 없어보이지만
- 결과는 4번에서 10으로 나오게 된다. 즉 x가 부활이라도 한 듯 동작함
- 이처럼 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조 할 수 있고 이를 **클로저**라고 함
- 즉 상위 스코프는 자신이 정의된 위치의 상위 스코프를 [[Enviroment]] 내부 슬롯에 저장되고 이 스코프는 함수가 존재하는 한 유지됨 
- outer가 실행 컨텍스트의 스택에서는 제거 되었어도 즉, outer 함수의 렉시컬 환경이 소멸한 것은 아님
- outer는 inner의 [[Enviroment]] 내부 슬롯에 의해 참조되고 있고 전역 변수 innerFunc에 의해 참조되고 있어 가비지 컬렉션에 저장되지 아니함
- 그렇기에 중첩함수 inner가 outer보다 오래 생존하게 됨 그래서 상위 스코프를 기억하고 참조할 수 있게됨

<br>

`클로저가 아닌 경우`

- 자바스크립트의 모든 함수는 상위 스코프를 기억하므로, 모든 함수는 클로저
- 다만 상위 스코프의 어떤 식별자도 참조하지 않는 함수는 클로저라고 하지 아니함
- 또한 중첩 함수가 상위 스코프의 식별자를 참조하더라도 외부 함수보다 생명주기가 짧다면 클로저라 하지 않음
- 또한 상위 스코프의 식별자 중에 일부의 식별자만 참조한다면, 그 일부만 기억하므로 메모리 낭비의 문제는 신경쓰지 않아도 됨


<br>

## 클로저의 활용

- 클로저는 상태를 안전하게 변경, 유지하기 위해 사용
- 상태가 의도치 않게 변경되지 않도록 상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하기 위해 사용

```javascript
// case 1
// 함수를 실행할 떄마다 1씩 증가 되나 전역에서 관리가 되고 있기에 전역에서 다른 개입이 있을 경우 num 값이 변경될 가능성 존재
let num = 0;

const increase = function () {
  return ++num;
}

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3

// case 2
// increase 함수 내부에서만 num을 조작할 수 있게 변경 했으나, increase가 호출될때마다 변수가 초기화 되서 값이 증가하지 않음
const increase = function () {
  let num = 0;
  return ++num;
}

console.log(increase()); // 1
console.log(increase()); // 1
console.log(increase()); // 1

// case 3
const increase = (function () {
  let num = 0;

  return {
    increase() {
      return ++num;
    },
    decrease() {
      return num > 0 ? --num : 0;
    }
  };
}());

// case 3 생성자 함수 방식

const Counter = (function () {
  // Counter가 생성할 인스턴스의 프로퍼티가 아니므로 은닉된 변수
  let num = 0;

  function Counter() {

  }

  Counter.prototype.increase = function () {
    return ++num;
  };

  Counter.prototype.decrease = function () {
    return num > 0 ? --num : 0;
  }

  return Counter
}());

const counter = new Counter();

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
console.log(decrease()); // 2
console.log(decrease()); // 1
```
- case3의 경우 즉시 실행 함수가 호출되고 실행 함수가 반환한 함수가 increase 변수에 할당
- increase 변수에 할당된 함수는 클로저 왜냐하면 상위 스코프의 num 식별자를 참조하고 있기 때문
- 실행 함수는 한 번만 실행되므로 num 변수가 초기화 될 일이 존재하지 않음
- 고차함수 형태로 클로저를 별도로 뺸다음 increase나 decrease를 별도 함수로 전역에 선언한 후 참조하는 방식도 존재
- 다만, 이 경우는 increase/decrease가 별도의 함수이기에 별도로 num이 초기화 된다.
- 증감 함수를 만들기 위해서는 function()으로 한번 더 묶어서 생성해주면 증감이 동시에 이루어질 수 있음

<br>

## 캡슐화와 정보 은닉
- `캡슐화` : 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것 보통은 특정 프로퍼티와 메서드를 감출 목적으로 사용되고 이를 **정보 은닉**이라 함
- **정보 은닉**은 적절치 못한 접근으로부터 객체의 상태가 변경되는 것을 방지해 정보를 보호, 객체 간의 상호 의존성, 결함도를 낮추는 효과 있음

<br>
