## 🔖 개요
프로그래밍을 하며 에러는 언제든지 발생한다.

에러나 예외적인 상황에 대응하지 않으면 프로그램이 강제 종료될 수 있다. 따라서, 다양한 예외적인 상황에 대응하는 코드를 작성하는 것은 중요하다.

자바스크립트에서의 예외 처리 방법에 대해 알아보자.

## 🔖 try-catch-finally문
에러 처리를 구현하는 방법은 두 가지가 있다.

1. 예외적인 상황이 발생하면 반환하는 값을 단축 평가 또는 옵셔널 체이닝을 통해 확인하여 처리
2. 에러 처리 코드를 미리 등록해두고, 에러가 발생하면 에러 처리 코드로 점프

try-catch-finally문은 두번째 경우에 해당한다. 이 방법을 **'에러 처리'**라고 한다. 이를 통해 에러가 발생하더라도 프로그램이 강제 종료되지 않고, 끝까지 실행될 수 있다.

```js
try {
    func();
} catch (error) {
    console.error(error);
} finally { // 생략 가능
    console.log('finally')
}
```

## 🔖 Error 객체
Error 생성자 함수를 통해 생성할 수 있고, 에러를 상세히 설명하는 에러 메시지를 인수로 전달할 수 있다. 7가지 종류의 에러 객체를 생성할 수 있고, 모두 `Error.prototype`을 상속 받는다.

```js
const error = new Error('invalid');
```

## 🔖 throw문
에러 객체를 통해 에러를 발생시킬 수 있다. 일반적으로 `throw` 뒤에 오는 표현식은 에러 객체여야 한다.

try 문에서 에러를 발생시키면, catch 문이 실행된다.

```js
try {
    throw new Error('something wrong');
} catch (error) {
    console.log(error);
}
```

## 🔖 에러의 전파
에러는 호출자 방향으로 전파 된다.

다음 예제에서, try 문에서의 `baz()` 함수 호출을 통해 `bar()`, `foo()` 함수까지 실행된다. `foo()` 함수에서 에러가 발생하므로 이 에러를 catch 문에서 처리하지 않으면 **에러가 전파**되기 때문에 프로그램은 강제 종료된다.

```js
const foo = () => {
    throw Error('foo error');
}

const bar = () => {
    foo();
}

const baz = () => {
    bar();
}

try {
    baz();
} catch (error) {
    console.log(error);
}
```