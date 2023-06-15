# 이벤트 버블링 & 캡쳐링 

![Desktop - 8](https://github.com/chaedev3/algorithm/assets/109324466/74f2dd9e-60af-48e0-97f2-8160851feee9)



## ⭐ 이벤트 버블링(Event Bubbling) 

> **이벤트 버블링**이란 한 요소에 이벤트가 발생하면 이 요소에 할당된 핸들러가 동작하고, 이어서 부모 요소의 핸들러가 동작함. **최상단의 부모 요소를 만날 때까지 반복**되면서 핸들러가 동작하는 현상을 말함  

![image](https://github.com/chaedev3/algorithm/assets/109324466/947a4729-12ed-47c3-9656-16222df00c02)

<br>

#### 💗 이벤트 버블링의 예시

```javascript
<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>

```
이 코드는 form > div > p 형태로 중첩된 구조를 가지고 있음
이 때 가장 안쪽의 p 를 클릭하면 
  1. p에 할당된 onClick handler 동작 

  2. div에 할당된 onClick handler 동작 

  3. form에 할당된 onClick handler 동작 
      **p -> div -> form** 순으로 alert 창이 뜨게 됨! 
       div 를 클릭하면 div -> form, form 을 클릭하면 form 순으로 alert 창이 뜨게 됨
       즉, **document 객체를 만날 때까지** 핸들러가 모두 호출되는 것임  

      <br>

      ![image](https://github.com/chaedev3/algorithm/assets/109324466/6eca3748-20c4-454c-8b1a-864d0cf2684f)

> 이런 흐름을 **'이벤트 버블링'** 이라 부름. 이벤트가 **제일 깊은 곳에 있는 요소**에서 **시작**해 **부모 요소를 거슬러 올라가며** 발생하는 모양이 마치 물속 거품과 닮았기 때문임     

> 단, 모든 이벤트가 버블링 되는 것은 아님. 예를 들면, focus 는 버블링되지 않는 대표적인 이벤트임 

<br>

## ⭐  이벤트 캡쳐링 (Event Capturing)  
> 이벤트 버블링과 반대 방향으로 진행되는 이벤트 전파 방식임

![image (1)](https://github.com/chaedev3/algorithm/assets/109324466/e8b87f16-64d1-43da-a5d1-d4daadcdcd78)



addEventListener 의 옵션 객체에 **{ capture: true }** or true 를 설정해주면 캡처링을 구현할 수 있음 
capture 옵션이 **false**면 핸들러는 **버블링** 단계에서 동작하고 옵션이 **true**면 핸들러는 **캡처링** 단계에서 동작함 

<br>

#### 💗 이벤트 캡쳐링의 예시

```javascript 
<body>
	<div class="one">
		<div class="two">
			<div class="three">
			</div>
		</div>
	</div>
</body>
```
```javascript
let divs = document.querySelectorAll('div');
divs.forEach(function(div) {
	div.addEventListener('click', logEvent, {
		capture: true // default 값은 false입니다.
	});
});

function logEvent(event) {
	console.log(event.currentTarget.className);
}
```
만약 이 코드를 { capture: true } 설정하지 않고 class='three'인 div를 클릭하면 이벤트 버블링이 일어나 three - two - one 순으로 출력이 됐을 것. 하지만 { capture : true } 를 설정하면 이벤트 캡쳐링이 일어나 one - two -three 순으로 출력이 됨 

<br>

## ⭐  이벤트 전파
> 이벤트 전파는 3가지 단계를 거침 1) **캡쳐링(Capturing)** 단계 2) **타깃(Target)** 단계 3) **버블링(Bubbling)** 단계 

1) 캡처링 단계 - 이벤트가 하위 요소로 전파되는 단계 
2) 타깃 단계 - 이벤트가 실제 타깃 요소로 전달되는 단계 
3) 버블링 단계 - 이벤트가 상위 요소로 전파되는 단계 

![image (2)](https://github.com/chaedev3/algorithm/assets/109324466/ade23229-5579-491e-891f-338dcae4f478)

td 를 클릭하면 이벤트가 최상위 조상에서 시작해 아래로 전파되고(**캡쳐링** 단계), 이벤트가 타깃 요소에 도착해 실행된 후(**타깃** 단계), 다시 위로 전파됨(**버블링** 단계)

<br>

```javascript
<form>FORM
  <div>DIV
    <p>P</p>
  </div>
</form>

<script>
  for(let elem of document.querySelectorAll('*')) {
    elem.addEventListener("click", e => alert(`캡쳐링: ${elem.tagName}`), true);
    elem.addEventListener("click", e => alert(`버블링: ${elem.tagName}`));
  }
</script>
```

즉, 이런 코드가 있을 때 p tag를 클릭했을 때 다음과 같으니 순서로 이벤트가 전달되게 됨
1. HTML -> BODY -> FORM -> DIV (**캡처링** 단계) 
2. P (**타깃** 단계, 캡처링과 버블링 둘 다에 리스너를 설정했기 때문에 두 번 호출됨) 
3. DIV -> FORM -> BODY -> HTML (**버블링** 단계) 
  즉, 캡쳐링: HTML - 캡쳐링: BODY - 캡쳐링: FORM - 캡쳐링: DIV - 캡쳐링: P - 버블링: P - 버블링: DIV - 버블링: FORM - 버블링: BODY - 버블링: HTML 순으로 출력  

<br>

## ⭐ 이벤트 전파 막기 
그렇다면 원하는 타깃에서만 이벤트를 발생하게 하고 싶으면 어떻게 해야할까?
**event.stopPropagation()** 을 이용하면 이벤트 캡처링 / 버블링 단계에서 더 이상 전파되지 않도록 방지할 수 있음

<br>

#### 💗 event.stopPropagation 예시

```javascript
<body onclick="alert(`버블링은 여기까지 도달하지 못합니다.`)">
  <button onclick="event.stopPropagation()">클릭해 주세요.</button>
</body>
```
이렇게 되면 button을 클릭해도 body.onClick은 동작하지 않게됨


>단, stopPropagation으로 이벤트 전파를 막아놓은 영역에서는 분석 시스템의 코드가 제대로 동작하지 않음 (예를 들면, document.addEventListener('click') 가 작동하지 않음) 따라서 꼭 필요한 경우를 제외하고는 버블링을 막는 건 좋지 않다. 



<br>

<br>

이러한 이벤트 버블링과 캡쳐링은 **이벤트 위임(event delegation)**의 토대가 됨 

## ⭐ 이벤트 위임 (Event Delegation) 
> 각 노드에 대해 이벤트를 추가하지 않고, **상위 노드**에서 하위 노드의 이벤트들을 제어하는 방식으로 **공통의 부모 요소**에 하나의 이벤트 핸들러를 바인딩함으로써 성능과 사용성을 개선하는 패턴을 말함 

<br>

#### 💗 이벤트 위임 예시

```javascript
<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
const list = document.querySelector('ul');

list.addEventListener("click", (e) => {
  if (e.target.tagName === "LI") {
    console.log(e.target.innerText);
  }
});
```
- li tag에 일일이 할당하지 않고, 공통 부모 요소인 ul tag 에 이벤트 핸들러를 할당. 이벤트 버블링에 의해 li에서 발생한 이벤트가 ul로 전파되기 때문에, li에서 발생한 클릭 이벤트를 상위 요소 ul에서 감지하고 처리할 수 있음 

<br>

그렇다면 React 에서 이벤트 위임은 어떤 식으로 해야할까 ..? 
- 정답은 React의 경우 애초에 모든 이벤트가 이벤트 위임으로 처리되고 있다는 것이다. 이벤트 리스너를 루트 요소에 붙이고 있어 root 레벨에서 모든 이벤트가 처리되고 있었던 것이다..   
![image (3)](https://github.com/chaedev3/algorithm/assets/109324466/b8c4c18e-3633-4202-9b12-dda610594b0b)

<br>

#### **📒 참고** 

https://ko.javascript.info/bubbling-and-capturing 
https://ko.javascript.info/event-delegation 
https://developer.mozilla.org/ko/docs/Web/API/Event/stopPropagation
https://developer.mozilla.org/ko/docs/Web/API/Event/stopImmediatePropagation 
https://joshua1988.github.io/web-development/javascript/event-propagation-delegation/ 