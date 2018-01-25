# 예외처리

JavaScript는 예외에서 사용하기 위한 `Error`클래스가 있습니다. 에러는 `throw`키워드를 통해서 발생시킬 수 있습니다. `try`/`catch`의 블럭 페어로 에러를 잡을 수 있습니다. 예를들면:

```js
try {
  throw new Error('Something bad happened');
}
catch(e) {
  console.log(e);
}
```

## 에러의 서브 타입
내장된 `Error` 클래스 외에도 JavaScript 런타임이 던질 수 있는 `Error`를 상속받은 몇 가지 추가 내장된 에러 클래스가 있습니다:

### 범위에러
숫자변수 또는 매개변수가 유효한 범위를 벗어날 때 발생하는 오류를 나타내는 인스턴스를 만듭니다.

```js
// Call console with too many arguments
console.log.apply(console, new Array(1000000000)); // RangeError: Invalid array length
```

### 참조에러
잘못된 참조를 참조해제 할 때 발생하는 오류를 나타내는 인스턴스를 만듭니다. 예:

```js
'use strict';
console.log(notValidVar); // ReferenceError: notValidVar is not defined
```
### 구문에러
유효한 JavaScript가 아닌 코드를 구문 분석하는 동안 발생하는 구문 오류를 나타내는 인스턴스를 만듭니다.

```js
1***3; // SyntaxError: Unexpectd token *
```
### Type에러
변수 또는 매개 변수가 유효한 type이 아닌 경우 발생하는 오류를 나타내는 인스턴스를 만듭니다.
```js
('1.2').toPrecision(1); // TypeError: '1.2'.toPrecision is not a function
```

### URI에러
`encodeURI()` 또는 `decodeURI()`에 유효하지 않은 파라미터가 건네 받았을 때에 발생하는 에러를 나타내는 인스턴스를 생성합니다.
```js
decodeURI('%'); // URIError: URI malformed
```

## 항상 사용되는 `Error`
초보 JavaScript 개발자는 때로는 그냥 원시 문자열 그래로를 던질 수도 있습니다. 예:
Beginner JavaScript developers sometimes just throw raw strings e.g. 
```js
try {
  throw 'Something bad happened';
}
catch(e) {
  console.log(e);
}
```
*이렇게 사용하지 마세요.* `Error` 객체의 근본적인 장점은 그것들이 어디서 만들어지고 생성되었는지를 `stack` 속성으로 자동 추적한다는 것입니다.

원시 문자열은 매우 고통스러운 디버깅 경험을 가져오고 로그에서 오류 분석을 복잡하게 만듭니다.

## 에러를 `throw` 할 필요는 없습니다.
`Error` 객체를 전달하는 것은 괜찮습니다. 이는 Node.js 콜백 스타일 코드에서 일반적인 것으로, 첫 번째 인수를 오류 객체로 사용하여 콜백을 수행합니다.

```js
function myFunction (callback: (e?: Error)) {
  doSomethingAsync(function () {
    if (somethingWrong) {
      callback(new Error('This is my error'))
    } else {
      callback();
    }
  });
}
```

## 예외 케이스
`예외는 예외적 이어야한다`는 컴퓨터 과학의 일반적인 말이있습니다. JavaScript(및 TypeScript)에 대해서도 마찬가지입니다.

### 어디서 발생한 것인지 불분명하다.
다음 코드를 고려해보겠습니다:
```js
try {
  const foo = runTask1();
  const bar = runTask2();
}
catch(e) {
  console.log('Error:', e);
}
```
다음 개발자는 어떤 기능이 오류를 던질 지 알 수 없습니다. 코드를 검토하는 사람은 task1/task2 및 호출 할 수 있는 다른 기능에 대한 코드를 읽지 않고는 알 수 없습니다.

### 핸들링 하기 어렵게 한다.
당신은 throw 될 수 있는 각각의 것들에 대한 명시적인 포착으로 잘 처리하려고 할 수 있습니다:

```js
try {
  const foo = runTask1();
}
catch(e) {
  console.log('Error:', e);
}
try {
  const bar = runTask2();
}
catch(e) {
  console.log('Error:', e);
}
```
하지만 이제 첫번째 작업에서 두번째 작업으로 물건을 전달해야하는 경우 코드가 지저분해집니다(`let`을 필요로하는 `foo`돌연변이와 `runTask1`의 리턴으로부터 유추될 수 없으므로 주석을 달아야한다는 명백한 필요성에 주의하십시오.):

```js
let foo: number; // Notice use of `let` and explicit type annotation
try {
  foo = runTask1();
}
catch(e) {
  console.log('Error:', e);
}
try {
  const bar = runTask2(foo);
}
catch(e) {
  console.log('Error:', e);
}
```

### type 시스템에서 잘 표현되지 않음

다음 함수를 고려해보세요:

```js
function validate(value: number) {
  if (value < 0 || value > 100) throw new Error('Invalid value');
}
```
이런 경우에 `Error`를 사용하는 것은 validate함수(`(value:number) => void`부분)의 type 정의에서 표현되지 않기 때문에 나쁜 생각입니다. 
대신 validate 메소드를 만드는 더 좋은 방법은:

```js
function validate(value: number): {error?: string} {
  if (value < 0 || value > 100) return {error:'Invalid value'};
}
```
그리고 지금은 type 시스템에서 표현됩니다.

> 매우 일반적인(단순한/catch-all 등) 방식으로 오류를 처리하고 싶지 않으면 오류를 throw하지 마십시오.
