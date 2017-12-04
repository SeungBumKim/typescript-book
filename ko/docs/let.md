### let

`var`는 JavaScript의 *함수 범위*의 변수입니다. 이런한 범위는 다른 많은 언어(C# / Java 등)의 변수의 *블럭 범위*는 다릅니다. 만약 *블럭 범위* 기준을 JavaScript에 적용하면, `123`이 출력되는 것을 기대할 것이나 `456`이 출력됩니다:

```ts
var foo = 123;
if (true) {
    var foo = 456;
}
console.log(foo); // 456
```
이는 `{`이 *변수의 범위* 생성하지 않기 때문입니다. 변수 `foo`는 *블럭*안의 것과 블럭밖의 것이 동일합니다. 이는 JavaScript 프로그래밍에서 대게 잘못사용는 소스코입니다. 그래서 TypeScript(그리고 ES6)에서는 *블럭 범위*의 변수를 정의하는 키워드 `let`을 추가하였습니다. `var`대신에 `let`을 사용하면 범위밖에서 정의한 것과 분리된 진정한 고유 요소를 얻게됩니다. 아래 동일한 예제로 `let`을 설명할 수 있습니다:

```ts
let foo = 123;
if (true) {
    let foo = 456;
}
console.log(foo); // 123
```

`let`이 에러를 방지 할 수있는 또 다른 장소는 루프입니다.
```ts
var index = 0;
var array = [1, 2, 3];
for (let index = 0; index < array.length; index++) {
    console.log(array[index]);
}
console.log(index); // 0
```
신규 및 기존 다른언어 개발자들에게는 덜놀랍겠지만 가능하면 언제든지 `let`을 사용하는 것이 더 낫다는 것을 발견 할 수 있습니다.

#### 함수의 새로운 범위 생성
언급 했듯이 함수가 JavaScript에서 새로운 변수 범위를 생성한다는 것을 보여드리고 싶습니다. 다음 예를 참고하세요:

```ts
var foo = 123;
function test() {
    var foo = 456;
}
test();
console.log(foo); // 123
```
예측했던데로 동작합니다. 이러한 지식없이 JavaScript로 코드를 작성하는 것이 매우 어려울 것입니다.

#### JS 생성
TypeScript에서 JS를 생성할 때 유사한 이름이 이미 주변 범위에 존재하면 `let`변수의 이름을 간단하게 변경만하면 됩니다. 예를들면, 다음 코드는 간단하게 `let`을 `var` 교체하면서 생성될 것입니다.

```ts
if (true) {
    let foo = 123;
}

// becomes //

if (true) {
    var foo = 123;
}
```
그러나 이미 주위범위에 이미 존재한다면 새로운 변수이름을 다음과 같이(`_foo`) 생성합니다:

```ts
var foo = '123';
if (true) {
    let foo = 123;
}

// becomes //

var foo = '123';
if (true) {
    var _foo = 123; // Renamed
}
```

#### 클로저 안의 let
JavaScript 개발자를위한 일반적인 프로그래밍 인터뷰 질문은 아래 내용의 간단한 파일의 로그란 무엇인지 물어보는 것이다:

```ts
var funcs = [];
// create a bunch of functions
for (var i = 0; i < 3; i++) {
    funcs.push(function() {
        console.log(i);
    })
}
// call them
for (var j = 0; j < 3; j++) {
    funcs[j]();
}
```
우리는 `0,1,2`가 나올것으로 기대할 것이다. 그러나 놀랍게도 세개의 함수는 모두 `3`을 출력한다.그이유는 세개의 함수 모두 범위밖의 `i`를 사용했고, 함수가 실행되는 시점(두번째루프)에는 `i`의 값이 `3`이기 때문이다.(첫번째 함수가 종료되는 종료되는 시점에)

고치기위해서는 각루프안에 루프별 변수를 새롭게 만들어 주면된다. 우리가 전에 배웠던 것처럼, 새로운 함수를 생성하고 그것을 즉시 실행함으로써 새로운 변수 범위를 생성 할 수 있습니다(예를들면, `(function() { /* body */ })();` 클래스의 IIFE 패턴이 적용된 방식):
```ts
var funcs = [];
// create a bunch of functions
for (var i = 0; i < 3; i++) {
    (function() {
        var local = i;
        funcs.push(function() {
            console.log(local);
        })
    })();
}
// call them
for (var j = 0; j < 3; j++) {
    funcs[j]();
}
```
여기있는 함수의 *local*변수는 닫혀있고(`closure`라고 불림) 변수 `i`대신으로 사용됩니다.

> 클로저는 성능에 영향을 끼친다는 것에 주의하세요. (주변 여러 상태를 저장해야 함).

루프안의 ES6의 `let`키워더는 앞의 예와 동일한 동작을 수행합다:

```ts
var funcs = [];
// create a bunch of functions
for (let i = 0; i < 3; i++) { // Note the use of let
    funcs.push(function() {
        console.log(i);
    })
}
// call them
for (var j = 0; j < 3; j++) {
    funcs[j]();
}
```

`var` 대신 `let`을 사용한 변수 `i`는 각 반복루프에서 유니크하게 생성된다.

#### 요약
`let`은 대다수의 코드에 매우 유용합니다. 코드 가독성을 크게 향상시키고 프로그래밍 오류의 가능성을 줄입니다.



[](https://github.com/olov/defs/blob/master/loop-closures.md)
