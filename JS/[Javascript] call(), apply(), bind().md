# [Javascript] call(), apply(), bind() 

![Desktop - 7](https://github.com/chaedev3/algorithm/assets/109324466/55dcdd49-201a-42c6-8109-e994f536cf37)

<br>

```javascript
const say = function () {
	console.log(this); 
	console.log("Hello, my name is" + this.name); 
}; 

say(); 
```

이 코드를 실행하게 되면 this 는 window 객체로 나타나게 된다. 

![binding1](https://github.com/chaedev3/algorithm/assets/109324466/da200051-f87e-4b84-b949-176136d8186d)



> 하지만 this 에 window 객체 대신 다른 값들을 넣어주고 싶을 때 쓰는 함수가 바로 **call, apply, bind** 이다. 

<br> 

## ⭐ call() 

> call() 메소드는 주어진 this 값 및 각각 전달된 인수와 함께 함수를 호출, apply() 와 거의 동일하지만, **call() 은 인수 목록**을, **apply() 는 인수 배열 하나**를 받는다는 점이 가장 큰 차이점 입니다. 

```
func.call(thisArg[, arg1[, arg[, ...]]])
```

- thisArg: func 호출에 제공되는 this의 값

- arg1, arg2: func이 호출되어야 하는 인수 

<br>

#### 💗 call을 사용한 예시 

```javascript
const obj = { name: 'chaelin' }
const say = function (city) {
	console.log(`Hello, my name is ${this.name}, I live in ${city}`); 
}; 

say("seoul")    // Hello, my name is , I live in Seoul  
say.call(obj, "seoul") // Hello, my name is chaelin, I live in Seoul   
```

- say("seoul") 만 한 경우는,  this에 window 객체가 있기 때문에 this.name이 뜨지 않게 됨 
- say.call(obj, "seoul") 인 경우는, this 에 obj, 즉 { name: 'chaelin' } 이 들어갔기 때문에 this.name 을 출력하면 chaelin 이 나오게 됨 

<br>

- 값이 여러개가 들어갈 때 

```javascript
const obj = { name: 'chaelin' }
const say = function (city, age) {
	console.log(`Hello, my name is ${this.name}, I live in ${city}. And I'm ${age} years old`); 
}; 

say("seoul", 26)    // Hello, my name is , I live in seoul. And I'm 20 years old 
say.call(obj, "seoul", 26) // Hello, my name is chaelin, I live in seoul. And I'm 20 years old
```

<br>

## ⭐ apply() 

```javascript
func.apply(thisArg, [argsArray]) 
```

- 앞서 말했듯, call() 과의 차이점은 **단일 배열**을 받는다는 것입니다. 

- thisArg: func 호출에 제공되는 this의 값

- argsArray: func이 호출되어야 하는 인수를 지정하는 유사 배열 객체 

<br>

#### 💗 apply를 사용한 예시 

```javascript
const obj = { name: 'chaelin' }
const say = function (city) {
	console.log(`Hello, my name is ${this.name}, I live in ${city}`); 
};

say.apply(obj, ["seoul"]) // Hello, my name is chaelin, I live in Seoul 
```

<br>

- 값이 여러개가 들어갈 때

```javascript
const obj = { name: 'chaelin' }
const say = function (city, age) {
	console.log(`Hello, my name is ${this.name}, I live in ${city}. And I'm ${age} years old`); 
};  

say.apply(obj, ["seoul", 20]) // Hello, my name is chaelin, I live in seoul. And I'm 20 years old
```

<br>



## ⭐ bind() 

> bind() 함수가 call(), apply() 와 다른 점은 함수를 실행하지 않는다는 점이다. 그 대신 bound 함수를 리턴한다. call(), apply()의 경우 한 번 사용하면 끝이지만 bind를 사용하면 bound 함수는 특정한 this를 가지고 있기 때문에 나중에 사용해도 된다는 장점이 있다. 

```javascript
func.bind(thisArg[, arg1[,arg2[, ...]]]) 
```

- thisArg: 바인딩 함수가 타겟 함수의 this에 전달하는 값
- arg1, arg2, ...: func이 호출되어야 하는 인수  

#### <br> 💗 bind를 사용한 예시 

```javascript
const obj = { name: 'chaelin' }
const say = function (city) {
	console.log(`Hello, my name is ${this.name}, I live in ${city}`); 
};
const boundSay = say.bind(obj)  
boundSay("seoul") // Hello, my name is chaelin, I live in Seoul  
```

<br>

> 즉, call() 은 custom this 를 지정할 수 있었고, apply() 는 call() 과 유사하지만 인자를 배열을 받는다는 점에서 차이가 있다. 마지막으로 bind()는 custom this를 영구히 가지는 함수를 만들 수 있다. 

<br>

<br>

#### 📒 참고 

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call 

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/apply 

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/bind 

https://wooooooak.github.io/javascript/2018/12/08/call,apply,bind/ 