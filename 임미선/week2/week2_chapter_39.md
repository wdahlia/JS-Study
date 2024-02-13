## 🔖 개요

브라우저 렌더링 엔진의 HTML 문서 파싱을 통해 생성된 DOM은 HTML 문서의 계층적 구조와 정보를 표현하며 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조이다. 모든 함수를 외울 필요는 없지만, DOM에 대해 한 번 자세히 알아보자.

## 🔖 노드
DOM은 요소 노드 객체에 의해 이루어져있다. 각 요소의 어트리뷰트는 어트리뷰트 노드로, HTML 요소의 텍스트 컨텐츠는 텍스트 노드로 변환된다.

```html
<div class="greeting">Hello</div>
```
- `<div>` : 시작 태그 (요소 노드)
- `class` : 어트리뷰트 이름 (어트리뷰트 노드)
- `"greeting"` : 어트리뷰트 값 (어트리뷰트 노드)
- `Hello` : 컨텐츠 (텍스트 노드)
- `</div>` : 종료 태그 (요소 노드)

각 HTML 요소는 중첩 관계를 갖는다. 요소 내에 요소를 포함할 수 있다.

### 📍 문서 노드
DOM 최상위에 존재하는 루트 노드로서, `document` 객체를 가리킨다. 문서 노드를 통해 하위 노드들인 요소, 어트리뷰트, 텍스트 노드에 접근할 수 있다.

### 📍 요소 노드
HTML 요소를 가리키는 객체를 말한다. 각 요소 노드 간 중첩을 통해 문서의 구조를 표현할 수 있다.

### 📍 어트리뷰트 노드
어트리뷰트가 지정된 요소 노드와 연결되어 있다. 하지만, 요소 노드는 부모 노드와 연결되어 있는 반면 어트리뷰트 노드는 부모 노드와 연결되어 있지 않고 요소 노드에만 연결되어 있다. 

따라서 어트리뷰트 참조 및 변경을 위해서는 요소 노드에 먼저 접근해야 한다.

### 📍 텍스트 노드
요소 노드의 자식 노드로서, 자식 노드를 가질 수 없는 리프 노드이다. 

따라서 텍스트 노드에 접근하기 위해서는 부모 노드인 요소 노드에 접근해야 한다.

### 📍 노드 객체의 상속 구조
각 노드가 가지는 기능들은 특정 인터페이스를 상속받아 구성된다. 이는 프로토타입 체인 관점에서 살펴볼 수 있다. 예를 들어, input 요소 노드 객체는 다음과 같은 기능들을 상속을 통해 제공받는다.

|input 요소 노드 객체의 특성|프로토타입을 제공하는 객체|
|--|--|
|객체|Object|
|이벤트를 발생시키는 객체|EventTarget|
|트리 자료구조의 노드 객체|Node|
|브라우저가 렌더링할 수 있는 웹 문서의 요소(HTML, XML, SVG)를 표현하는 객체|Element|
|웹 문서의 요소 중에서 HTML 요소를 표현하는 객체|HTMLElement|
|HTML 요소 중에서 input 요소를 표현하는 객체|HTMLInputElement|

이러한 상속 구조를 자세히 알 필요는 없지만, 노드 객체는 상속을 통해 DOM API를 사용하여 동적으로 변경시킬 수 있다는 점을 숙지해야 한다.

## 🔖 요소 노드 취득
### 📍 요소 노드 취득 함수
|함수명|설명|
|--|--|
|`Document.prototype.getElementById`|id를 이용한 요소 노드 취득|
|`Document.prototype.getElementsByTagName`|인수로 전달한 태그 이름을 갖는 모든 요소 노드들을 탐색하여 반환|
|`Document.prototype.getElementsByClassName`|인수로 전달한 class 어트리뷰트 값을 갖는 모든 요소들을 탐색하여 반환|

여러 노드를 요소로 갖는 배열을 반환하는 함수의 경우, 반환 배열을 순회하며 DOM을 조작할 수 있다. 이러한 노드 배열은 HTMLCollection, NodeList로써 여러 개의 결과값을 반환하기 위한 DOM 컬렉션 객체에 의해 생성된다. 

위 함수는 `getElementBy...` 메서드들을 가지는 HTMLCollection에 의한 함수이고, NodeList 객체를 반환하기 위해서는 `querySelectorAll` 과 같은 메서드를 사용하면 된다.

HTMLCollection 객체에 의해 생성된 노드 배열들은 현재 노드 객체를 실시간으로 반영할 수 있지만(노드 객체가 살아있기 때문), NodeList 객체는 실시간 변경 사항 반영을 하지 않는다. 상태 변경 여부에 따라 적절히 객체를 사용하는 것이 중요하다.

### 📍 CSS 선택자를 이용한 요소 노드 취득
```css
* { ... } /* 전체 선택자 : 모든 요소를 선택 */
p { ... } /* 태그 선택자 : 모든 p 태그 요소를 모두 선택 */
#foo { ... } /* id 값이 'foo'인 요소를 모두 선택 */
input[type=text] { ... } /* 어트리뷰트 선택자 : input 요소 중에 type 어트리뷰트 값이 'text'인 요소를 모두 선택 */
p::hover { ... } /* 가상 요소 선택자 : p 요소의 콘텐츠 앞에 위치하는 공간을 선택함. 일반적으로 content 프로퍼티와 함께 사용됨 */
```

### 📍 특정 요소 노드를 취득할 수 있는지 확인
```js
// true or false
console.log($apple.matches('#fruits > li.apple')); 
```
`Element.prototype.matches` 메서드를 통해 인수로 전달한 CSS 선택자로 특정 요소 노드를 취득할 수 있는지를 확인할 수 있다.

## 🔖 노드 탐색
취득한 노드를 기점으로, 트리의 노드를 옮겨 다니며 탐색해야 할 때가 있다.

### 📍 자식 노트 탐색
```js
const $fruits = document.getElementById('fruits');
console.log($fruits.firstElementChild);
```
|프로퍼티|설명|
|--|--|
|`Node.prototype.childNodes`|자식 노드를 모두 탐색하여 `NodeList`에 담아 반환 (텍스트 노드 포함)|
|`Element.prototype.children`|자식 노드 중 요소 노드만 모두 탐색하여 `HTMLCollection`에 담아 반환 (텍스트 노드 비포함)|
|`Node.prototype.firstChild`|첫번째 자식 노드 반환 (텍스트 노드 포함)|
|`Node.prototype.lastChild`|마지막 자식 노드 반환 (텍스트 노드 포함)|
|`Element.prototype.firstElementChild`|첫번째 자식 노드 반환 (요소 노드만 포함)|
|`Element.prototype.lastElementChild`|마지막 자식 노드 반환 (요소 노드만 포함)|

> 💡 `Node`의 프로퍼티는 텍스트 노드를 포함할 수도 있지만, `Element`의 프로퍼티는 텍스트 노드가 아닌 요소 노드만 포함한다는 것을 알 수 있다.

### 📍 자식 노트 존재 확인
```js
console.log($fruits.hasChildNodes()); // true or false
```

### 📍 부모 노드 탐색
텍스트 노드는 DOM의 최종단 노드인 리프 노드이므로, 부모 노드가 텍스트인 경우는 없다.
```js
console.log($fruits.parentNode);
```

### 📍 형제 노드 탐색
|프로퍼티|설명|
|--|--|
|`Node.prototype.previousSibling`|부모 노드가 같은 형제 노드 중, 자신의 이전 형제 노드를 반환|
|`Node.prototype.nextSibling`|부모 노드가 같은 형제 노드 중, 자신의 다음 형제 노드를 반환|
|`Element.prototype.previousElementSibling`|부모 노드가 같은 형제 노드 중, 자신의 이전 형제 노드를 반환|
|`Element.prototype.nextElementSibling`|부모 노드가 같은 형제 노드 중, 자신의 다음 형제 노드를 반환|

## 🔖 노드 정보 취득
|프로퍼티|설명|
|--|--|
|`Node.prototype.nodeType`|노드 타입을 나타내는 상수 반환(1, 3, 9)|
|`Node.prototype.nodeName`|노드의 이름을 문자열로 반환|
- 1 : 요소 노드 타입
- 3 : 텍스트 노드 타입
- 9 : 문서 노드 타입

## 🔖 요소 노드의 텍스트 조작
|함수|설명|
|--|--|
|`nodeValue`|요소를 취득하여 해당 함수를 통해 텍스트를 할당함. 텍스트 노드가 아닌 경우엔 null을 반환함|
|`textContent`|요소를 취득하여 해당 함수를 통해 텍스트를 할당함. 위 함수에 비해 간단한 텍스트 할당이 가능함.|

## 🔖 DOM 조작
DOM 조작을 이용한 노드 추가 및 삭제는 리플로우와 리페인트를 발생시키므로, 성능 최적화를 위해 조심히 사용해야 한다.

### 📍 innerHTML
```js
$fruits.innerHTML = '<li class="orange">Orange</li>';
```
해당 함수는 XSS 공격에 취약하므로 위험할 수 있다.

### 📍 insertAdjacentHTML 메서드
```js
$foo.insertAdjacentHTML('beforebegin', '<p>beforebegin</p>');
```
해당 함수는 기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를 삽입한다.
- `<p ..` : beforebegin
- `> ...` : afterbegin
- `</...` : beforeend
- `p>` : afterend
  

### 📍 노드 생성과 추가
- `createElement(tag)`
- `createTextNode(text)`
- `appendChild(node)`

### 📍 노드 삽입
- `insertBefore(newNode, childNode)` : 특정 위치에 노드 삽입

### 📍 노드 복사
- `cloneNode([deep: true | false])` : `deep` 인수에 따라 얕은/깊은 복사를 지정할 수 있음

### 📍 노드 교체
- `replaceChild(newChild, oldChild)`

### 📍 노드 삭제
- `removoeChild(child)`

## 🔖 어트리뷰트
### 📍 data 어트리뷰트와 dataset 프로퍼티
```html
<ul class="users">
    <li id="1" data-user-id="7621" data-role="admin">Lee</li>
    ...
</ul>
```

```js
const users = [...document.querySelector('.users').children];

// get
const user = users.find(user => user.dataset.userId === '7621');
console.log(user.dataset.role);

// set
user.dataset.role = 'subscriber';
```