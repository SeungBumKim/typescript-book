## 생성기

> 참고: TypeScript 생성기는 의미있게 사용하기는 사용하기는 힘들 것 입니다. 그러나 조만간 바뀔 것 이라 해당 챕터는 계속 유지 할 것입니다.

`function *`의 구문은 *생성기 함수*를 만들 때 사용합니다. 생성기 함수를 호출하면 *생성기 오브젝트*를 리턴합니다. 생성기 오브젝트는 [iterator][iterator] 인터페이스(예를들면,`next`, `return`, `throw` 함수들) 를 따릅니다.

생성기 함수에는 두가지 주요한 목적이 있습니다:

### Lazy Iterators

생성기 함수는 **무한반복**으로 요청시 만들어지는 정수형 리스트를 리턴하는 아래와 같이 lazy iterators을 만들어 사용할 수 있다:

```ts
function* infiniteSequence() {
    var i = 0;
    while(true) {
        yield i++;
    }
}

var iterator = infiniteSequence();
while (true) {
    console.log(iterator.next()); // { value: xxxx, done: false } forever and ever
}
```

물론, 반복이 끝난다면 아래 설명된 것과 같이 `{ done: true }`의 결과를 얻을 수 있다:

```ts
function* idMaker(){
  let index = 0;
  while(index < 3)
    yield index++;
}

let gen = idMaker();

console.log(gen.next()); // { value: 0, done: false }
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { done: true }
```

### Externally Controlled Execution
이번에 설명할 생성기의 내용은 매우 유동적입니다. 함수가 멈추거나 콜러에서 남아있는 다음단계의 함수(fate)로 넘어갈 때 필요한 기능입니다. 

생성기 함수는 함수가 호출되었을 때는 실행되지 않습니다. 단지 생성기 오브젝트만 생성할 뿐이다. 다음 예로든 간단한 실행 동작을 살펴보겠습니다:

```ts
function* generator(){
    console.log('Execution started');
    yield 0;
    console.log('Execution resumed');
    yield 1;
    console.log('Execution resumed');
}

var iterator = generator();
console.log('Starting iteration'); // This will execute before anything in the generator function body executes
console.log(iterator.next()); // { value: 0, done: false }
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

실행시키면 다음과 같은 출력이 나올것입니다:

```
$ node outside.js
Starting iteration
Execution started
{ value: 0, done: false }
Execution resumed
{ value: 1, done: false }
Execution resumed
{ value: undefined, done: true }
```

* 함수는 시작해서 생성기 오브젝트의 `next` 함수를 한번 호출합니다.
* 함수가 *pauses* 되면 바로 `yield`의 상태값이 증가합니다.
* 함수는 `next` 호출되면 *resumes*합니다.

> 따라서 본질적으로 생성기 함수의 실행은 생성기 오브젝트에 의해 제어 가능합니다.

생성기를 사용하는 방식은 대부분 iterator에 대한 값을 반환하는 생성자가 있는 한 가지 방법이었습니다. JavaScript의 생성기의 강력한 기능중 한가지는 양방향을 허용한다는 것입니다!

* `iterator.next(valueToInject)`을 통해 `yield`표현식의 결과값을 컨트롤 할 수 있습니다.
* `iterator.throw(error)`통해서 `yield`표현식의 지점에서 예외상황을 발생시킬 수 있습니다.

다음 예제는 `iterator.next(valueToInject)`을 설명합니다:

```ts
function* generator() {
    var bar = yield 'foo';
    console.log(bar); // bar!
}

const iterator = generator();
// Start execution till we get first yield value
const foo = iterator.next();
console.log(foo.value); // foo
// Resume execution injecting bar
const nextThing = iterator.next('bar');
```

다음 예제는 `iterator.throw(error)`을 설명합니다: 

```ts
function* generator() {
    try {
        yield 'foo';
    }
    catch(err) {
        console.log(err.message); // bar!
    }
}

var iterator = generator();
// Start execution till we get first yield value
var foo = iterator.next();
console.log(foo.value); // foo
// Resume execution throwing an exception 'bar'
var nextThing = iterator.throw(new Error('bar'));
```

이제 여기서 다시 정리해보면:
So here is the summary:
* `yield`는 생성기 함수의 처리과정을 멈추게하고 외부 시스템에서 컨트롤 할 수 있게 해줍니다.
* 외부 시스템에서 생성기 함수 내부에 값을 넣을 수 있습니다.
* 외부 시스템에서 생성기 함수 내부에서  예외처리를 발생 시킬 수 있습니다.

도움이 되었나요? 다음 [**async/await**][async-await]세션으로 이동해보세요.

[iterator]:./iterators.md
[async-await]:./async-await.md
