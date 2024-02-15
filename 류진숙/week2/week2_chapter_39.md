# DOM
<br>

- `Document Object Model`은 HTML 문서의 계층적 구조와 정보를 표현 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조

<br>

## 노드
<br>

### HTML 요소와 노드 객체
- HTML 문서를 구성하는 개별적인 요소를 의미하는 HTML 요소는 렌더링 엔진에 의해 파싱
- 이후 DOM을 구성하는 요소 노드 객체로 변환되는데, 이 때 어트리뷰트는 어트리뷰트 노드, 텍스트 콘텐츠는 텍스트 노드로 변환
```html
<div class="greeting">HELLO</div>
```
- `div` - 요소 노드
- `class="gretting"` - 어트리뷰트 노드
- `HELLO` - 텍스트 노드
- HTML 문서들 HTML 요소들의 집합으로 이루어지며, 각 요소들은 중첩 관계를 가짐 따라서, 텍스트 뿐만 아니랴 다른 HTML 요소도 포함 가능
- 이러한 중첩 관계에 의해 계층적인 부자관계가 형성되고 이 것들을 객체화한 모든 노드 객체들을 트리 자료로 구성

<br>

#### 트리자료 구조
- 노드들의 계층 구조로 이루어짐
- 즉, 부모 노드와 자식 노드로 구성 되어 노드 간의 계층적 구조를 표현하는 비선형 자료구조
- 최상위 노드는 부모 노드가 없는 가장 상위의 노드로 `루트 노드`라고 함
- 루트 노드는 0개 이상의 자식 노드를 가지는데 이때, 자식 노드가 없는 노드를 `리프 노드`라 함
- 노드 객체들로 구성된 트리 자료구조를 `DOM 트리`라고 함

<br>

### 노드 객체의 타입

<img src="https://poiemaweb.com/img/dom-tree.png" />

- 노드 객체의 타입은 총 12개가 존재, 그 중 4가지에 대해서 공부

<br>

#### 문서 노드 
- DOM 트리의 최상위에 존재하는 루트 노드로 `document 객체`를 가리킴
- HTML 문서 전체를 가리키는 객체 전역 객체 window의 documnet 프로퍼티에 바인딩
- 문서 노드는 window.document 또는 document로 참조
- 노드에 접근하기 위한 진입점 역할을 담당

<br>

#### 요소 노드

- HTML 요소를 가리키는 객체
- 요소 노드는 문서의 구조를 표현

<br>

#### 어트리뷰트 노드
- 어트리뷰트 노드는 부모 노드와 연결되어 있는 것이 아닌 요소 노드에만 연결되어 있음
- 즉, 요소 노드의 형제 노드는 아니다.
- 어트리뷰트 노드에 접근하여 어트리뷰트를 참조하거나 변경하려면 먼저 요소 노드에 접근

<br>

#### 텍스트 노드
- 문서의 정보를 표현
- 텍스트 노드는 요소 노드의 자식 노드이고, 자식 노드를 가질 수 없는 리프 노드
- 텍스트 노드는 DOM 트리의 최종 트리 접근하기 위해서는 부모 노드인 요소 노드에 접근해야함

<br>

### 노드 객체의 상속 구조
- DOM API를 사용하여 자신의 부모, 형제, 자식 탐색 가능
- 모든 노드 객체는 Object, EventTarget, Node 인터페이스를 상속
- 노드는 Document, HTMLDocument 인터페이스를 상속
- 어트리트뷰트 노드는 Attr, 텍스트 노드는 CharacterData 인터페이스 각각 상속
- 요소 노드는 Element 인터페이스 상속 받고 추가적으로 태그의 종류별로 세분화된 HTMLHtmlElement, HTMLHeadElementm HTMLBodyElement, HTMLUListElemetn 등의 인터페이스 상속 받음
- 예를 들어, input 요소 노드 객체는 Object, EventTarget, Node, Element, HTMLElement, HTMLInputElement의 prototype에 바인딩되어 있는 프로토타입 객체를 상속바독, 이 프로토타입의 모든 프로퍼티와 메서드를 상속받아 사용 가능
- 노드 객체에는 노드 타입에 상관없이 모든 노드 객체가 공통으로 갖는 기능이 있고, 고유의 기능도 존재
- 이벤트 관련 기능(EventTarget.addEventListener 등), 트리 탐색 기능(Node.parentNode 등), 노드 정보 제공 기능(Node.nodeType 등) **Node 인터페이스에서 제공하는 기능은 공통 기능**
- 또한 요소 노드 객체에서 style 프로퍼티의 경우 공통적인 기능이고 이는 **HTMLElement 인터페이스가 제공**
- 하지만 input의 경우에는 value 프로퍼티가 필요하나 div에서는 value 프로퍼티가 필요하지 않으므로 이들은 HTMLElement에서 세분화 된 **HTMLInputElement, HTMLDivElement에서 차별화 된 기능 제공**
- 즉, 공통의 기능일 수록 프로토타입 체인의 상위에, 개별적인 고유 기능일수록 프로토타입의 체인의 하위에 구축
- DOM은 문서의 계층적 구조와 정보를 표현하는 것은 물론 노드 타입에 따라 필요한 기능을 프로퍼티와 메서드의 집합인 DOM API로 재공하여 HTML의 구조나 내용 또는 스타일 등을 동적으로 조작 가능

<br>

## 요소 노드 취득
<br>

### id를 이용한 요소 노드 취득
- `Document.prototype.getElementById`메서드는 인수로 전달한 id 어트리뷰트 값을 갖는 하나의 요소 노드를 탐색하여 반환
- **getElementById** 메서드는 Document.prototype의 프로퍼티이기 때문에, 문서 노드인 document를 통해 호출

```html
<!DOCTYPE html>
  <html lang="en">
  <body>
    <ul>
      <li id="apple">APPLE</li>
      <li id="banana">BANANA</li>
      <li id="orange">ORANGE</li>
    </ul>
    <script>
      const $elem = document.getElementById('banana');
      const $anotherelem = document.getElementById('grape'); // null
      // banana라는 id를 가진 요소노드를 탐색 후 반환
      // id는 무조건 유일한 값
      // 중복된 id 값을 갖는 경우 첫 번째 요소 노드만 반환
      // id 값을 갖는 HTML 요소가 존재하지 않는 경우 null을 반환
      $elem.style.color = 'red';
      // 해당 요소노드의 색깔을 빨간색으로 변경
      $anotherelem.style.color = 'red';
      // TypeError : Cannot read property 'style' of null
    </script>
  </body>
</html>
```
<br>

### 태그 이름을 이용한 요소 노드 취득
- `Document.prototype/Element.prototype.getElementsByTagName`메서드는 인수로 전달한 태그 이름을 갖는 모든 요소 노드들을 탐색하여 반환
- **getElementsByTagName** 메서드는 Elements가 복수형인 것에서 알 수 있듯이 여러 개의 요소 노드 객체를 갖는 DOM 컬렉션 객체인 HTMLCollection 객체를 반환
- Element.prototype.getElementsByTagName은 특정 요소 노드를 통해 호출하는 것으로 이 노드의 자손 노드 중에서 요소 노드 탐색하여 반환, 존재하지 않는 경우 빈 HTMLCollection 객체 반환

```html
<!DOCTYPE html>
  <html lang="en">
  <body>
    <ul>
      <li id="apple">APPLE</li>
      <li id="banana">BANANA</li>
      <li id="orange">ORANGE</li>
    </ul>
    <script>
      const $elems = document.getElementsByTagName('li');
      // li인 모든 요소를 탐색하여 출력
      // HTMLCollection 객체에 담겨 반환
      // 유사 배열 객체이면서 이터러블하기 때문에 순회하면서 프로퍼티 값 변경 가능 
      // HTMLCollection(3) [li#apple, li#banana, li#orange ... ];
      const $all = document.getElementsByTagName('*');
      // 문서의 모든 요소 노드 취득하려면 인수로 *를 전달하면 됨
      // HTMLCollection(8) [html, head, body, ul, li#apple, li#banana, li#orange, script, ... ]
      [...$elems].forEach(elem => { elem.style.color = 'red' });
      // 각 li 요소의 색깔이 빨간색으로 변경
    </script>
  </body>
</html>
```
<br>

### class를 이용한 요소 노드 취득
- `Document.prototype/Element.prototype.getElementsByClassName`메서드는 class 어트리뷰트 값을 갖는 모든 요소 노드들을 탐색하여 반환
- 인수로 전달할 class 값을 공백으로 구분하여 여러 개의 class 지정 가능
- HTMLCollection 객체 반환

```html
<!DOCTYPE html>
  <html lang="en">
  <body>
    <ul>
      <li id="fruit apple">APPLE</li>
      <li id="fruit banana">BANANA</li>
      <li id="fruit orange">ORANGE</li>
    </ul>
    <script>
      const $elems = document.getElementsByClassName('fruit');
      // class 값이 fruit 요소 노드를 모두 탐색하여 HTMLCollection 객체에 담아 반환
      // id인 apple, banana, orange 모두 출력
      const $elems = document.getElementsByClassName('fruit apple');
      // class 값이 fruit apple 인 요소 노드 모두 탐색하여 HTMLCollection 객체에 담아 반환
    </script>
  </body>
</html>
```
<br>

### CSS 선택자를 이용한 요소 노드 취득
- css 선택자는 스타일을 적용하고자 하는 HTML 요소를 특정할 때 사용하는 문법
- `*` : 모든 요소를 선택
- `p` : 모든 p 태그 요소 선택 (HTML 태그 요소)
- `#foo` : id 선택자 - id 값이 foo인 요소를 모두 선택
- `.foo` : class 선택자 - class 값이 foo인 요소를 모두 선택
- `input[type=text]` : input 요소 중에 type 어트리뷰트 값이 text인 요소를 모두 선택
- `div p` : 후손 선택자 - div의 후손 요소 중 p 요소를 모두 선택
- `div > p` : 자식 선택자 - div 요소의 자식 요소 중 p 요소를 모두 선택
- `p + ul` : 인접 형제 선택자 - p 요소의 형제 요소 중에 p 요소 *바로 뒤에* 위치하는 ul 요소를 선택
- `p ~ ul` : 일반 형제 선택자 - p 요소의 형제 요소 중에 p 요소 *뒤에 위치*하는 ul 요소를 모두 선택
- `a:hover` : 가상 클래스 선택자 - hover 상태인 a 요소를 모두 선택
- `p::before` : 가상 요소 선택자 - p 요소의 콘텐츠의 앞에 위치하는 공간을 선택, content 프로퍼티와 함께 사용
<br>

- `Document.prototype/Element.prototype.querySelector`메서드는 인수로 전달한 CSS 선택자를 만족시키는 하나의 요소 노드를 탐색하여 반환
- 요소 노드가 여러 개인 경우 **첫 번째 요소 노드**만 반환
- 요소 노드가 존재하지 않는 경우 **null** 반환
- 문법에 맞지 않는 경우 **DOMException** 에러 발생

<br>

- `Document.prototype/Element.prototype.querySelectorAll`메서드는 인수로 전달한 CSS 선택자를 만족시키는 모든 요소 노드를 탐색하여 반환
- 여러 개의 요소 노드 객체를 갖는 DOM 컬렉션 객체인 NodeList 객체를 반환, 유사 배열 객체이면서 이터러블함
- 만족시키는 요소가 존재하지 않는 경우 빈 **NodeList 객체**를 반환
- 문법에 맞지 않는 경우 **DOMException** 에러 발생

```html
<!DOCTYPE html>
  <html lang="en">
  <body>
    <ul>
      <li id="apple">APPLE</li>
      <li id="banana">BANANA</li>
      <li id="orange">ORANGE</li>
    </ul>
    <script>
      const $elem = document.querySelector('#banana');
      // banana id를 가진 첫 번째 요소 노드를 탐색하여 반환
      const $elems = document.querySelectorAll('ul > li');
      // ul 요소의 자식 요소인 li 요소를 모두 탐색 반환
      console.log($elems); // NodeList(3) [li.apple, li.banana, li.orange]
    </script>
  </body>
</html>
```
<br>

### 특정 요소 노드를 취득할 수 있는지 확인
- `Element.prototype.matches`메서드는 인수로 전달한 CSS 선택자를 통해 특정 요소 노드를 취득할 수 있는지 확인

```html
<!DOCTYPE html>
  <html lang="en">
  <body>
    <ul id="fruits">
      <li class="apple">APPLE</li>
      <li class="banana">BANANA</li>
      <li class="orange">ORANGE</li>
    </ul>
    <script>
      const $apple = document.querySelector('.apple');
      console.log($apple.matches('#fruits > li.apple')); // true $apple 노드는 #fruits > li.apple로 취득 가능
      console.log($apple.matches('#fruits > li.banana')); // false $apple 노드는 #fruits > li.banana로 취득 가능
    </script>
  </body>
</html>
```
<br>

### HTMLCollection과 NodeList
- HTMLCollection, NodeList의 중요한 특징은 노드 객체의 상태 변화를 실시간으로 반영하는 `살아 있는 객체`
- HTMLCollection은 언제나 live 객체로 동작하지만, NodeList는 대부분의 경우 과거의 정적 상태를 유지하는 non-live 객체로 동작, 하지만 경우에 따라 live 객체로 동작할 때가 있음
<br>

#### HTMLCollection

```html
<!DOCTYPE html>
  <html lang="en">
  <body>
    <ul id="fruits">
      <li class="red">APPLE</li>
      <li class="red">BANANA</li>
      <li class="red">ORANGE</li>
    </ul>
    <script>
      const $elems = document.getElementsByClassName('red');
      console.log($elems);
      // HTMLCollection(3) [li.red, li.red, li.red]
      for (let i=0; i < $elems.length; i++) {
        $elems[i].className = 'blue';
      }
      console.log($elems); // HTMLCollection(1) [li.red]
    </script>
  </body>
</html>
```

- 객체의 요소가 순회하면서 className을 blue로 변경했음에도 HTMLCollection 리스트에 한개가 남아있음을 알 수 있음 
- **왜 ?** live 객체이기 때문에 실시간으로 요소에서 제거가 되면서 i = 0 일때 class를 blue로 변경하게 되면
- $elems가 `[li.red, li.red]` 가 되고 i = 1이 되면 즉 두번째 요소가 사라지게 되고
- HTMLCollection에 `[li.red]`만 남게 됨
- 유사 배열 객체이면서 이터러블인 HTMLCollection 객체를 스프레드 연산자를 사용한 배열로 변환 후 고차함수 사용해서 순회하면서 변경 하는 방식을 사용하는 것이 좋음

<br>

#### NodeList
- HTMLCollection 객체의 부작용 해결 위해 getElementsByTagName, getElementsByClassName메서드 대신 querySelectorAll 메서드를 사용하는 방법이 존재
- querySelectorAll은 NodeList를 반환하고 이 객체는 실시간으로 노드 객체의 상태 변경을 반영하지 않기 때문
- NodeList는 NodeList.prototype을 상속받아 사용할 수 있으며 forEach, item, entries, keys, values 메서드를 제공
- 대부분의 경우 non-live 객체로 동작하기 때문에 HTMLCollection 객체의 부작용 해결할 수 있으나
- childNodes 프로퍼티가 반환하는 NodeList 객체는 live 객체로 동작하므로 주의 필요

<br>

`결론` : HTMLCollection이나 NodeList 객체는 예상과 다르게 동작할 가능성이 존재하기 때문에 안전하게 DOM 컬렉션을 사용하려면 객체를 배열로 변환하여 사용하는 것을 권장(Array.from, 스프레드 연산자 사용)