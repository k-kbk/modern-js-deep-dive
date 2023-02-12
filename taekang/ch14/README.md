# Chapter.14 전역 변수의 문제점

---

> 전역 변수의 무분별한 사용은 위험하다. 전역 변수를 반드시 사용해야 할 이유를 찾지 못한다면 지역 변수를 사용해야 한다.

## 변수의 생명 주기

---

### 지역 변수의 생명 주기

---

> 변수는 선언에 의해 생성되고 할당을 통해 값을 갖는다. 그리고 언젠가 소멸한다.
> 즉, 변수는 생물과 유사하게 생성되고 소멸되는 생성 주기(life cycle)가 있다.

**`변수에 생명 주기가 없다면 한번 선언된 변수는 프로그램을 종료하지 않는 한 영원히 메모리 공간을 점유하게 된다.`**

```jsx
function foo() {
  var x = "local";
  console.log(x); // local
  return x;
}

foo();
console.log(x); // ReferenceError: x is not defined
```

> 지역 변수 x는 foo 함수가 호출되기 이전까지는 생성되지 않는다. **foo 함수를 호출하지 않으면 함수 내부의 변수 선언문이 실행되지 않기 때문이다.**

**💡 변수 선언은 선언문이 어디에 있든 상관없이 가장 먼저 실행된다.
다시 말해, 변수 선언은 런타임 이전 단계에서 자바스크립트 엔진에 의해 먼저 실행된다.**

**`그런데 엄밀히 말하자면 위 설명은 전역 변수에 한정된 것이다.`**

**`함수 내부에서 선언한 변수는 함수가 호출된 직후에 함수 몸체의 코드가 한 줄씩 순차적으로 실행되기 이전에 자바스크립트 엔진에 의해 먼저 실행된다.`**

> 따라서 함수 내부에서 선언된 지역 변수 x는 foo 함수가 호출되어 실행되는 동안에만 유효하다.
> **즉, 지역 변수의 생명 주기는 함수의 생명 주기와 일치한다.**

```jsx
var x = "global";

function foo() {
  console.log(x); // 1
  var x = "local";
}

foo();
console.log(x); // global
```

> foo 함수 내부에서 선언된 지역 변수 x는 1의 시점에서 이미 선언되었고 undefined로 초기화되어 있다.

**`즉, 지역 변수는 함수 전체에서 유효하다. 단, 변수 할당문이 실행되기 이전까지는 undefined 값을 갖는다.`**

💡 **이처럼 호이스팅은 스코프를 단위로 동작한다. 즉, 호이스팅은 변수 선언이 스코프의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징을 말한다.**

### 전역 변수의 생명 주기

---

> 함수와 달리 **전역 코드는 명시적인 호출 없이 실행된다**.
> 함수는 함수 몸체의 마지막 문 또는 반환문이 실행되면 종료되지만 전역 코드에는 반환문을 사용할 수 없으므로 마지막 문이 실행되어 더 이상 실행할 문이 없을 때 종료한다.

💡 **var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 된다.
이는 전역 변수의 생명주기가 전역 객체의 생명 주기와 일치한다는 것을 말한다.**

**`전역 객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체다.`**

**`클라이언트 사이드 환경(브라우저)에서는 window, 서버 사이드 환경(node.js)에서는 global 객체를 의미한다.(ES11에서 globalThis로 통일되었다.)`**

## 전역 변수의 문제점

---

### 암묵적 결합

---

> 전역 변수를 선언한 의도는 전역, 즉 코드 어디서든 참조하고 할당할 수 있는 변수를 사용하겠다는 것이며 이는 모든 코드가 전역 변수를 참조하고 변경할 수 있는 **암묵적 결합**(implicit coupling)을 허용하는 것이다.

**`변수의 유효 범위가 크면 클수록 코드의 가독성은 나빠지고 의도치 않게 상태가 변경될 수 있는 위험성도 높아진다.`**

### 긴 생명 주기

---

> **전역 변수는 생명 주기가 길다.**

- 따라서 메모리 리소스도 오랜 기간 소비한다.
- 전역 변수의 상태를 변경할 수 있는 시간도 길고 기회도 많다.

### 스코프 체인 상에서 종점에 존재

---

> 전역 변수는 스코프 체인 상에서 종점에 존재한다.
> 이는 변수를 검색할 때 전역 변수가 가장 마지막에 검색된다는 것을 말한다.
> 즉, **전역 변수의 검색 속도가 가장 느리다.**

### 네임스페이스 오염

---

> 자바스크립트의 가장 큰 문제점 중 하나는 파일이 분리되어 있다 해도 하나의 전역 스코프를 공유한다는 것이다.
> 따라서 다른 파일 내에서 동일한 이름으로 명명된 전역 변수나 전역함수가 같은 스코프 내에 존재할 경우 예상치 못한 결과를 가져올 수 있다.
> 즉, **전역 변수는 전역 스코프라는 같은 네임스페이스에 선언된다는 것이다.**

## 전역 변수의 사용을 억제하는 방법

---

> 전역 변수의 무분별한 사용은 위험하다.

- **전역 변수를 반드시 사용해야 할 이유를 찾지 못한다면 지역 변수를 사용해야 한다.**
- **변수의 스코프는 좁을수록 좋다.**

### 즉시 실행 함수

---

> 함수 정의와 동시에 호출되는 즉시 실행 함수는 단 한 번만 호출된다.
> **모든 코드를 즉시 실행 함수로 감싸면 모든 변수는 즉시 실행 함수의 지역 변수가 된다.**
> 이러한 특성을 이용해 전역 변수의 사용을 제한하는 방법이다.

```jsx
(function () {
  let foo = 10; // 즉시 실행 함수의 지역 변수
  // ...
})();

console.log(foo); // ReferenceError: foo is not defined
```

**`이 방법을 사용하면 전역 변수를 생성하지 않으므로 라이브러리 등에 자주 사용된다.`**

### 네임스페이스 객체

---

> **전역에 네임스페이스(namespace) 역할을 담당할 객체를 생성하고 전역 변수처럼 사용하고 싶은 변수를 프로퍼티로 추가하는 방법이다.**

```jsx
let MYAPP = {}; // 전역 네임스페이스 객체

MYAPP.name = "Kim";

console.log(MYAPP.name); // Kim
```

> 네임스페이스 객체에 또 다른 네임스페이스 객체를 프로퍼티로 추가해서 네임스페이스를 계층적으로 구성할 수도 있다.

```jsx
let MYAPP = {}; // 전역 네임스페이스 객체

MYAPP.person = {
  name: "Kim",
  address: "Seoul",
};

console.log(MYAPP.person.name); // Kim
```

**`네임스페이스를 분리해서 식별자 충돌을 방지하는 효과는 있으나 네임스페이스 객체 자체가 전역 변수에 할당되므로 그다지 유용해 보이지는 않는다.`**

### 모듈 패턴

---

> **모듈 패턴은 클래스를 모방해서 관련이 있는 변수와 함수를 모아 즉시 실행 함수로 감싸 하나의 모듈을 만든다.**
> 모듈 패턴은 자바스크립트의 강력한 기능인 **클로저를 기반으로 동작한다.**
> 모듈 패턴의 특징은 전역 변수의 억제는 물론 캡슐화까지 구현할 수 있다는 것이다.

💡 **모듈 패턴은 전역 네임스페이스의 오염을 막는 기능은 물론 한정적이기는 하지만 정보 은닉을 구현하기 위해 사용한다.**

```jsx
let Counter = (function () {
  // private 변수
  let num = 0;

  // 외부로 공개할 데이터나 메서드를 프로퍼티로 추가한 객체를 반환한다.
  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
  };
})();

// private 변수는 외부로 노출되지 않는다.
console.log(Counter.num); // undefined

console.log(Counter.increase()); // 1
console.log(Counter.increase()); // 2
console.log(Counter.decrease()); // 1
console.log(Counter.decrease()); // 0
```

> 위 예제의 즉시 실행 함수는 객체를 반환한다.
> 이 객체에는 외부에 노출하고 싶은 변수나 함수를 담아 반환한다.

💡 이때 반환되는 객체의 프로퍼티는 **외부에 노출되는** **퍼블릭 멤버**(public member)다.
외부로 노출하고 싶지 않은 변수나 함수는 반환하는 객체에 추가하지 않으면 **외부에서 접근할 수 없는 프라이빗 멤버**(private member)가 된다.

## ES6 모듈

---

> ES6 모듈을 사용하면 더는 전역 변수를 사용할 수 없다.
> **ES6 모듈은 파일 자체의 독자적인 모듈 스코프를 제공한다.**

💡 **ES6 모듈은 IE를 포함한 구형 브라우저에서는 동작하지 않으며, 브라우저의 ES6 모듈 기능을 사용하더라도 트랜스파일링이나 번들링이 필요하기 때문에 아직까지는 브라우저가 지원하는 ES6 모듈 기능보다는 Webpack 등의 모듈 번들러를 사용하는 것이 일반적이다.**