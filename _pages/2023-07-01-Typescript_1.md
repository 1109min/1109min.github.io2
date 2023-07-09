---
title: Typescript 의 함수
author: Lee SeungMin
date: 2023-06-24
categories: [Typescript]
tags: [Typescript]
layout: post
comment: true
published: true
---

<br>

# Typescript에서의 함수

웹 애플리케이션을 구현할 때 자주 사용되는 함수는 타입스크립트로 다음 3가지 타입으로 정의할 수 있다.

+ 함수의 파라미터 타입
+ 함수의 반환 타입
+ 함수의 구조 타입

# 함수의 기본적인 타입 선언

타입스크립의 함수 선언 방법을 이해하기 위해 먼저 간단한 자바스크립트 함수를 보자.
```javascript
function sum(a, b) {
    return a + b;
}
```
위 자바스크립트 함수에 타입을 부여하면 다음과 같다.
```typescript
function sum(a: number, b: number): number {
    return a + b;
}
```
> >
<br> <b>TIP</b>
<br>
함수의 반환 갑에 타입을 정하지 않을 때는 `void`라도 사용
<br>
<br>

# 함수의 인자

+ 타입스크립트에서는 함수의 인자를 모두 필수 값으로 간주
+ 함수의 매개변수를 설ㅈㅇ하면 `undefind`나 `null`이라도 인자로 넘겨야하며 컴파일러에서 정의된 매개변수 값이 넘어왔는지 확인해야함
+ 정의된 매개변수 값만 받을 수 있고 추가로 인자를 받을 수 없음

```typescript
function sum(a: number, b: number): number {
    return a + b;
}
sum(10, 20); // 30
sum(10, 20, 30); // error, too many parameters
sum(10); // error, too few parameters
```
정의된 매개변수의 개수만큼 인자를 넘기지 않아도 되는 JS와 다른 것을 볼 수 있다.
만약 JS와 같은 특성을 살리고 싶다면 `?`를 이용해서 아래와 같이 정의할 수 있다.
```typescript
function sum(a: number, b?: number): number {
    return a + b;
}
sum(10, 20); // 30
sum(10, 20, 30); // error, too many parameters
sum(10); // 110
```
매개변수 초기화는 ES6와 문법이 같다
```typescript
function sum(a: number, b = '100'): number {
    return a + b;
}
sum(10, undefined); // 110
sum(10, 20, 30) // error, too many parameters
sum(10); // 110
```

# REST 문법이 적용된 매개변수
ES6 문법에서 지원하는 [REST 문법](https://babeljs.io/docs/learn#default-rest-spread)은 타입스크립트에서 다음과 같이 사용할 수 있다.
```typescript
function sum(a: number, ...nums: number[]): number {
    const totalOfNums = 0;
    for ( let jey in nums ) {
        totalOfNums += nums[key];
    }
    return a + totalOfNums;
}
```

# this
타입스크립트에서 자바스크립트의 `this`가 잘못 사용되었을 때 감지할 수 있다. 만약 `this`가 뭔지 모른다면 [Understanding Javascript Function and this](https://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)를 확인하면 된다.

타입스크립트에서 `this`가 가리키는 것을 명시하려면 아래와 같은 문법을 사용한다.
```typescript
function 함수명(this: 타입){
    /// ...
}
```
해당 문법을 적용한 예시이다.
```typescript
interface Vue {
    el: string;
    count: number;
    init(this: Vue): () => {};
}

let vm: Vue = {
    el: '#app',
    count: 10,
    init: function(this: Vue) {
        return () => {
            return this.count;
        }
    }
}

let getCount = vm.init();
let count = getCount();
console.log(count); // 10
```
위의 코드를 타입스크립트로 컴파일 했을 때 만일 `--noImplicitThis` 옵션이 있더라도 에러가 발생하지 않는다.

# 콜백에서의 this
앞에서 살펴본 일반적인 상황에서의 `this`와는 다르게 콜백으로 함수가 전달되었을 때의 `this`를 구분해줘야할 때가 있다. 그럴 때는 아래와 같이 강제할 수 있다.
```typescript
interface UIElement {
  // 아래 함수의 `this: void` 코드는 함수에 `this` 타입을 선언할 필요가 없다는 의미입니다.
  addClickListener(onclick: (this: void, e: Event) => void): void;
}

class Handler {
    info: string;
    onClick(this: Handler, e: Event) {
        // 위의 `UIElement` 인터페이스의 스펙에 `this`가 필요없다고 했지만 사용했기 때문에 에러가 발생합니다.
        this.info = e.message;
    }
}
let handler = new Handler();
uiElement.addClickListener(handler.onClick); // error!
```

만약 `UIElement`인터페이스의 스펙에 맞춰 `Handler`를 구현하려면 아래와 같이 변경한다.
```typescript
class Handler {
    info: string;
    onClick(this: void, e: Event) {
        // `this`의 타입이 void이기 때문에 여기서 `this`를 사용할 수 없습니다.
        console.log('clicked!');
    }
}
let handler = new Handler();
uiElement.addClickListener(handler.onClick);
```

> >
<br> <b>TIP</b>
<br>
클래스의 메서드 방식으로 선언하는 것과 변수에 화살표 함수를 연결하는 것의 차이점
<br>
<br>