## 🔖 개요
자바스크립트에서의 객체지향 프로그래밍을 위해서는, 자신이 속한 객체를 식별하기 위한 `this` 키워드가 필요하다.

`this` 키워드가 무엇인지, 왜 쓰이는지, 어떻게 사용하는지 알아보자.

## 🔖 this 키워드
### 📍 객체란?
**프로퍼티(상태) + 메서드(동작)**를 하나의 논리적 단위로 묶은 복합적인 자료구조를 말한다.

### 📍 this를 쓰는 이유
자신이 속한 객체를 가리키는 식별자를 참조함으로써, 자신이 속한 객체의 프로퍼티를 참조하기 위해서이다.


### 📍 this 사용 방법

```js
const circle = {
  	radius: 5,
  	getDiameter() {
    	return 2 * circle.radius;
    }
};

// output: 10
console.log(circle.getDiameter());
```

위 코드에서는 객체 리터럴 방식으로 생성하고, 리터럴 변수를 사용함으로써 **자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조**할 수 있다.

하지만, 재귀적인 참조 방식은 일반적이지 않다.
따라서 `new` 키워드와 함께 생성자를 이용한 인스턴스 생성을 해보자.

```js
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  return 2 * this.radius;
};

const circle = new Circle(5);
```

생성자 함수를 이용한 `circle` 인스턴스를 생성했다. 리터럴 방식의 객체 생성과는 다르게, 객체 내부 함수에서는 `circle` 변수로 자신이 속한 객체를 가리킬 수 없다.

위 코드에서 `new` 키워드를 이용하여 인스턴스를 생성했고, 생성자 파라미터로는 `5`를 할당했다. 

이 때, **`this` 키워드를 사용하여 자신이 속한 객체를 접근**할 수 있다.
객체 내 함수에서 `this.radius` 를 통해 해당 객체(=자신이 속한 객체)의 `radius` 변수에 접근할 수 있다.

💡 `this` = 자신이 속한 객체를 가리키는, 자신이 생성할 인스턴스를 가리키는 자기 참조 변수

### 📍 this 의 동작 방식
`this` 는 암묵적으로 생성되고, **함수 호출 방식에 의해 동적으로 결정**되기 때문에 **코드 어디서든 참조**하는 것이 가능하다. 이를 **'`this` 바인딩'**이라고 한다.

자바스크립트의 `this` 바인딩은 식별자와 값을 연결하는 과정이다. 이 과정을 통해 `this`가 가리킬 객체가 바인딩된다. (위 코드의 경우, `this`에 `circle` 객체가 바인딩됨)

|객체 리터럴의 메서드 내부의 `this`|생성자 함수 내부의 `this`|
|:-:|:-:|
|메서드를 호출한 객체|생성자 함수가 생성할(`new` 사용) 인스턴스|

자바스크립트는 자바, C++과 같은 Class 기반 언어에서는 
**언제나 `this` 가 클래스가 생성한 인스턴스를 가리킨다.**

하지만, **자바스크립트는 `this` 바인딩을 통해 참조 객체가 동적으로 결정된다는 차이점**이 있다.

💡 코드 어디서든 참조 가능하다면, **전역 및 함수 내부**에서는 무엇을 가리키는가?
- 전역에서의 `this` : 자바스크립트의 전역 객체인 `window` 를 가리킨다.
- 일반 함수 내부에서의 `this` : 자바스크립트의 전역 객체인 `window` 를 가리킨다.
	-> `function` 키워드로 생성된 '일반 함수'에만 해당한다.
    
## 🔖 함수 호출 방식과 this 바인딩
앞서 말했듯, `this` 바인딩은 함수 호출 방식에 따라 동적으로 결정되는 것을 말한다. 

자바스크립트에서는 하나의 동일한 함수도 여러 호출 방식을 가질 수 있다.
다음 함수를 여러 방식으로 호출해보자.
```js
const foo = function () { console.dir(this); }
```

1. 일반 함수 호출 (`function` 키워드)
	- `this` 는 전역 객체인 `window` 를 가리킨다.
```js
foo();
```
2. 메서드 호출 (객체 리터럴 선언 시)
	- `this` 는 메서드를 호출한 객체 `obj` 를 가리킨다.
```js
const obj = { foo };
obj.foo();
```
3. 생성자 함수 호출 (인스턴스 내부)
	- `this` 는 인스턴스를 가리킨다. 
    - 이 경우, `foo {}` 자체를 가리킨다.
```js
new foo();
```
4. `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출
	- `call`, `apply` 는 함수를 호출하는 함수이고, `bind` 는 매개변수에 의해 bound된 함수 또는 객체를 반환한다.
    - 따라서, `call`과 `apply`의 경우 `this`에 `bar` 객체를 바인딩했으므로 `bar` 객체를 가리킨다.
    `bind` 의 경우 `bar` 객체를 바인딩했으므로 bound된 객체인 `bar` 객체를 가리킨다.
```js
const bar = { name: 'bar' };

// 모두 bar 객체를 출력한다.
// output: Object bar ...
foo.call(bar);
foo.apply(bar);
foo.bind(bar)();
```

다음으로는, 앞서 말한 4가지 경우 내 `this` 에 대해 더욱 자세히 알아보자.

### 📍 일반 함수 호출
```js
function foo () {
  console.log(this);
  
  function bar() {
    console.log(this);
  }
  
  bar(); // window
}

foo(); // window
```
위 코드처럼 일반 함수 호출의 경우에는 모든 `this` 에 `window` 객체가 바인딩된다. 중첩해도 결과는 같다.

메서드 내부에 정의된 중첩 함수의 경우에도 마찬가지이다.
```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 메서드 내부 this는 해당 객체(=자신이 속한 객체)를 가리킨다.
    console.log(this, this.value);
    
    function bar() {
      console.log(this, this.value);
    }
    
    // 메서드 내부에 정의된 중첩함수이므로 window를 가리킨다.
    bar(); // window, 1
  }
}

obj.foo(); // obj, 100
```

그러나 `strict mode` 에서는 그렇지 않다. 
아래와 같이 `undefined` 가 바인딩된다.
```js
function foo() {
  'use strict';
  
  console.log(this);
}

foo(); // undefined
```

하지만 중첩함수 내부에서의 `this` 가 갑자기 전역 객체인 `window` 를 가리키는 로직은 지양하는 것이 좋지 않을까?

이를 해결하기 위한 두가지 방법이 존재한다.

1. `this` 명시적 바인딩
2. 화살표 함수 이용

두 방법 중, 더욱 용이한 것은 화살표 함수 방법이라고 생각한다.
화살표 함수의 특성을 아주 잘 이해하는 것이 중요하기 때문이다.

다음과 같이 화살표 함수를 이용해보자.
```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    setTimeout(() => console.log(this.value), 100);
  }
};

obj.foo(); // 
```

💡 **화살표 함수 내부의 `this` 는 상위 스코프의 `this` 를 가리키기 때문**에, 이 특성을 이용하면 더욱 깔끔한 코드 작성이 가능하다.

### 📍 메서드 호출 및 생성자 함수 호출
메서드/프로토타입 메서드 호출의 경우, 
`this` 가 가리키는 객체는 메서드를 소유한 객체가 아니라 
메서드를 호출한 객체에 바인딩 된다.

이는 생성자 함수로 생성된 객체의 `this` 와 차이점이 있다.
생성자 함수 내부의 `this` 는 생성자 함수로 생성될 인스턴스를 가리키기 때문이다.
그렇기에, `new` 키워드로 인스턴스를 생성해야 `this` 가 인스턴스를 가리키게 된다.

```js
// 같은 함수더라도, 호출 방식에 따라 다르게 작동한다.
function Circle(radius) {
  this.radius = radius;
}

// new 키워드를 사용하지 않으면 일반 함수 호출로 작동한다.
const circle1 = Circle(15);

console.log(circle1); // 일반 함수 호출이므로, 반환값이 없으니 undefined
console.log(radius); // 일반 함수 호출이므로, 전역 변수인 15

// new 키워드를 사용하여 인스턴스를 생성한다.
const circle2 = new Circle(20);
console.log(circle2); // Object circle2 ...
console.log(circle2.radius); // 인스턴스로 radius를 접근하므로, 20
```

### 📍 Function.prototype.apply/call/bind 메서드에 의한 간접 호출
`apply`, `call`, `bind` 메서드는 `Function.prototype` 의 메서드이므로 모든 함수가 상속받아 사용할 수 있다.

- `apply`, `call` : 함수를 호출하는 함수
	-> 각 요소가 메서드인 배열을 사용하기 위함
- `bind` : `this` 바인딩이 교체된 함수를 반환 (명시적 호출 필요)
	-> 메서드 내부와 중첩/콜백함수 내부의 `this` 가 일치하지 않는 경우를 해결하기 위함
    
```js
const person = {
  name: 'Lee',
  foo(callback) {
    setTimeout(callback, 100);
  }
};
```
위 객체를 이용하여 `bind` 의 장점을 익혀보자.

```js
person.foo(function () {
  console.log(this.name);
});
```

어떤 값이 출력될까?
아무것도 출력되지 않을 것이다. 
위 코드에서의 `this` 는 전역 객체인 `window` 를 가리키기 때문이다.

어떻게 하면 'Lee'를 출력할 수 있을까?

```js
const person = {
  name: 'Lee',
  foo(callback) {
    setTimeout(callback.bind(this), 100);
  }
};
```

`callback` 함수의 `this` 를 외부 함수의 `this` 함수와 일치시키기 위해 `bind` 함수를 사용하면 해결할 수 있다.

## 🔖 마치며
함수 호출 방식에 따라 `this` 가 가리키는 객체가 달라지게 된다.
각 경우에 대해 `this` 가 가리키는 객체를 정리해보면 다음과 같다.

- 일반 함수 호출 : 전역 객체
- 메서드 호출 : 메서드를 호출한 객체
- 생성자 함수 호출 : 생성자 함수가 생성할 인스턴스
- `apply`, `call`, `bind` 메서드에 의한 간접 호출 : 각 메서드에 첫번째 인수로 전달한 객체
