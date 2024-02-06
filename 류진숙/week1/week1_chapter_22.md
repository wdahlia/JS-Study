# this
<br>

## this 키워드
<br>

객체는 *상태*를 나타내는 **프로퍼티**와 *동작*을 나타내는 **메서드**를 하나의 논리적 단위로 묵은 복합적인 자료구조

`객체 리터럴 형식`

```javascript

// circle은 객체를 가리키는 식별자
const circle = {
  radius : 5,
  // radius : 5는 프로퍼티 => 객체 고유의 상태 데이터
  getDiameter() {
    return 2 * circle.radius;
    // 메서드가 자신이 속한 객체의 프로퍼티나 다른 메서드를 참조하려면
    // 자신이 속한 객체인 식별자 circle을 참조할 수 있어야 함
    // 이 참조 표현식이 평가되는 시점은 getDiameter()가 호출되어 함수가 실행되는 시점
    // 하지만, 이런식으로 자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 일반적이지도 바람직하지도 않음
  } 
  // getDiameter()는 메서드 => 상태 데이터를 참조하고 조작하는 동작
}

console.log(circle.getDiameter()); // 10

```

<br>

`생성자 함수 방식`

```javascript
function Circle(radius) {
  ????.radius = radius;
}

Circle.prototype.getDiameter = function () {
  return 2 * ????.radius;
}

const circle = new Circle(5);

```
- 생성자 함수 내부에 프로퍼티나 메서드를 추가하기 위해서는 자신이 생성할 인스턴트를 참조해야 하나,
- 먼저 생성자 함수를 정의하고 new 연산자와 함꼐 생성자 함수를 호출하는 단계가 우선적으로 필요
- 그런데 위의 *function Circle(radius)* 라던지 *Circle.prototype.getDiameter = function ()*는 인스턴트 생성하기 이전이브로 식별자를 알 수 없음
- 즉, 특수한 식별자가 필요한데 이때의 특별한 식별자가 바로 `this`


### this
- 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수
- 인스턴스의 프로퍼티나 메서드를 참조할 수 있음
- 자바스크립트 엔진에 의해 암묵적으로 생성되며, 코드 어디서든 참조 가능하지만
- **this가 가리키는 값**은 함수 호출 방식에 의해 **동적으로 결정**

<br>

**객체 리터럴 형식과, 생성자 함수 예제를 this 사용해 수정**
- 객체 리터럴 방식의 this는 *메서드를 호출한 객체 즉, circle*을 가리키게 되고
- 생성자 함수의 this는 *생성자 함수가 생성할 인스턴스*가 된다.

**동적으로 결정되는 this**

- 전역의 this는 전역 객체 **window**
- 일반 함수 내부에서의 this는 전역 객체**window**, strict mode에서는 **undefined**
- 객체 리터럴 메서드 내부에서의 this는 메서드를 호출한 객체 밑의 예제에서는 **{ name : 'Lee', getName: f }**
```javascript
const person = {
  name : 'Lee',
  getName() {
    console.log(this);
    return this.name;
  }
}
```
- 생성자 함수 내부에서의 this는 생성자 함수가 생성할 인스턴스 밑의 예제에서는 **Person { name : 'Lee' }**
```javascript
function Person(name) {
  this.name = name;
  console.log(this);
}

const me = new Person('Lee');
```

<br>

## 함수 호출 방식과 this 바인딩

### 함수 호출 방식

```javascript
const foo = function () {
  console.dir(this); // console.dir() 전체 요소 출력
}
```
<br>

1. 일반 함수 호출
```javascript
// case 1 
foo(); 

// case 2
var value = 1;

const obj = {
  value : 100,
  foo() {
    console.log(this.value); // 100
    
    // 중첩 일반 함수
    function bar() {
      console.log(this.value); // 1
    }

    // 콜백 함수
    setTimeout(function () {
      console.log(this.value); // 1
    })

  }
}

obj.foo();
```
`case 1`

- 일반 함수 내부의 경우 전역 객체인 **window** 출력

`case 2`
- `예외` 메서드 내부에서 **중첩 일반 함수/콜백 함수** 호출 시, this는 전역 객체 window가 바인딩 만약 이때 전역에도 객체 내부의 프로퍼티랑 같은 이름의 변수가 존재한다면 this.변수의 값은 프로퍼티 값이 아닌 전역 변수의 값을 출력하게 됨
- 하지만 보통 중첩 일반 함수라던지 콜백 함수를 객체 내부에 넣어 놨다는 것은 헬퍼 함수로 동작하기 위함인데
- 이들 내부에서는 this가 window 객체가 바인딩 되면서 문제가 생김
- 메서드의 this 바인딩과 일치시키기 위해서는 **this를 변수에 할당 하고 그 변수.value를 참조**하는 방법 존재
- 또는 **apply/call/bind 간접 호출 방식** 마지막으로 **콜백 함수를 이용한 방법** 존재
- `콜백 함수 내부의 this`는 *상위 스코프의 this*를 나타내기 때문

<br>

2. 메서드 호출
```javascript
const obj = { foo };
obj.foo();
```
- 객체 리터럴 방식으로 obj에 foo 함수를 넣어 메서드 형식으로 호출을 하면,
- this는 메서드를 호출한 객체 obj를 가리키게 되므로
- **obj** 출력
<br>

```javascript
const anotherPerson = {
  name : 'Kim',
}

// anotherPerson의 객체에 getName 메서드 할당
anotherPerson.getName = person.getName;
console.log(anotherPerson.getName()); // Kim

const getName = person.getName;
console.log(getName()); // 이 경우는 일반 함수 호출 즉 window.name과 같음
```
- 메서드는 obj 객체에 포함된 것이 아닌 독립적으로 존재하는 별도의 객체
- 즉, 다른 객체의 메서드가 될 수도 있으며, 일반 변수에 할당하여 일반 함수로 호출 될 수도 있음
- 프로토 타입 내부에서 사용된 this 역시도 해당 메서드를 호출한 객체에 바인딩

<br>

3. 생성자 함수 호출
```javascript
new foo();
```
- 생성자 함수의 경우 생성자 함수가 (미래에) 생성할 인스턴스를 가리키므로
- **foo {}** 출력
- new 연산자와 함께 생성자 함수 호출하지 않으면 생성자 함수가 아닌 일반 함수로 동작
```javascript
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  }

  const circle1 = new Circle(5);

  console.log(circle1); // 반환문 없음 즉, undefined
  console.log(radius); // 전역 참조하게 되므로 radius에 할당된 값인 5가 출력
}
```
<br>

4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출
```javascript
const bar = { name : 'bar' };

foo.call(bar); // bar
foo.apply(bar); // bar
foo.bind(bar)(); // bar
```
- apply, call, bind 메서드는 Function.prototype의 메서드 모든 함수가 상속받아 사용 가능
- 이 경우 this는 인수에 의해 결정

```javascript
function getThisBinding() {
  console.log(arguments);
  return this;
}

const thisArg = { a : 1 };

console.log(getThisBinding()); // 이경우는 일반 함수 호출로 window
console.log(getThisBinding.apply(thisArg)); // { a : 1 }
console.log(getThisBinding.call(thisArg)); // { a : 1 }
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: f, Symbol(Symbol.interator): f]
// { a : 1 }
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: f, Symbol(Symbol.interator): f]
// { a : 1 }
console.log(getThisBinding.bind(thisArg)); // getThisBinding
console.log(getThisBinding.bind(thisArg)()); // { a : 1 }
```
- `apply, call 메서드`의 본질적인 기능은 **함수를 호출**
- 첫번째 인수로 전달한 특정 객체를 호출한 함수의 this에 바인딩
- apply는 인수를 배열로 묶어 전달, call은 인수를 쉼표로 구분한 리스트 형식 => 방식만 다름
- `bind 메서드`의 경우 첫번쨰 인수로 전달한 값으로 **this 바인딩이 교체된 함수를 새롭게 생성해 반환**
- bind 메서드의 경우 함수를 호출하지는 않으므로 명시적으로 호출해야함
- 주로, 메서드의 this와 메서드 내부의 중첩 함수, 콜백 함수 this의 불일치 현상을 해소하기 위해 사용됨