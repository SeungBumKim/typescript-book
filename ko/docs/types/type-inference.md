# TypeScript에서의 Type추론

TypeScript는 몇 가지 간단한 규칙을 기반으로 변수 type을 유추(및 확인) 할 수 있습니다. 이 규칙은 간단하기 때문에 안전하고 안전하지 않은 코드를 인식하도록 두뇌를 훈련시킬 수 있습니다.(그것은 나에게 일어 났고, 나의 팀 동료는 아주 빨리 일어났다.)

> type의 흐름은 단지 뇌에서 type 정보의 흐름을 상상하는 것입니다.

## 정의

변수의 type은 정의에 의해 추론됩니다.

```ts
let foo = 123; // foo is a `number`
let bar = "Hello"; // bar is a `string`
foo = bar; // Error: cannot assign `string` to a `number`
```

이것은 오른쪽에서 왼쪽으로 흐르는 유형의 예입니다.

## 반환

반환 type은 return 문에 의해 유추됩니다. 예, 다음함수는 `number`가 반환 될 것입니다.

```ts
function add(a: number, b: number) {
    return a + b;
}
```

이것은 맨 아래로 흐르는 유형의 예입니다.

## 할당
함수 매개 변수/반환의 type은  할당에의해 유추될 수 있습니다. 예, 여기서 `foo`는 `Adder`라고 말하고, `a`와`b`의 타입을 `number`로 추론합니다.

```ts
type Adder = (a: number, b: number) => number;
let foo: Adder = (a, b) => a + b;
```
이 사실은 오류를 발생시키는 아래 코드에 의해 증명 될 수 있습니다:

```ts
type Adder = (a: number, b: number) => number;
let foo: Adder = (a, b) => {
    a = "hello"; // Error: cannot assign `string` to a `number`
    return a + b;
}
```
이것은 왼쪽에서 오른쪽으로 흐르는 유형의 예입니다.

콜백 인수에 대한 함수를 만들면 동일한 *할당* 스타일 type 유추가 작동합니다. 결국 `argument -> parameter`는 변수 할당의 다른 형태 일뿐입니다.

```ts
type Adder = (a: number, b: number) => number;
function iTakeAnAdder(adder: Adder) {
    return adder(1, 2);
}
iTakeAnAdder((a, b) => {
    // a = "hello"; // Would Error: cannot assign `string` to a `number`
    return a + b;
})
```

## 구조화
이러한 간단한 규칙은 **구조화** (객체 리터럴 생성)에서도 동작합니다. 예를들어 다음 케이스에서 `foo`의 type은 `{a:number, b:number}`로 추론됩니다.

```ts
let foo = {
    a: 123,
    b: 456
};
// foo.a = "hello"; // Would Error: cannot assign `string` to a `number`
```

배열도 유사합니다:
```ts
const bar = [1,2,3];
// bar[0] = "hello"; // Would error: cannot assign `string` to a `number`
```
물론 안에서도 가능합니다:

```ts
let foo = {
    bar: [1, 3, 4]
};
foo.bar[0] = 'hello'; // Would error: cannot assign `string` to a `number`
```

## 비구조화
그리고 물론, 그들은 또한 비구조화 함께 동작합니다. 두 객체를 보세요:

```ts
let foo = {
    a: 123,
    b: 456
};
let {a} = foo;
// a = "hello"; // Would Error: cannot assign `string` to a `number`
```
그리고 배열에서도:

```ts
const bar = [1, 2];
let [a, b] = bar;
// a = "hello"; // Would Error: cannot assign `string` to a `number`
```

함수 매개 변수를 추론 할 수 있는 경우, 비구조화된 프로퍼티도 할 수있습니다. 예를들어 여기서 우리는 인수를 그것의 `a`/`b` 멤버로 비구화시킵니다.

```ts
type Adder = (numbers: { a: number, b: number }) => number;
function iTakeAnAdder(adder: Adder) {
    return adder({ a: 1, b: 2 });
}
iTakeAnAdder(({a, b}) => { // Types of `a` and `b` are inferred
    // a = "hello"; // Would Error: cannot assign `string` to a `number`
    return a + b;
})
```

## Type 가드
우리는 이미 [Type Guards] (./ typeGuard.md)가 type을 변경하고 범위를 좁히는데 도움이(특히 유니온의 경우) 되는 것을 보았습니다. 
type 가드는 블록의 변수에 대한 type 유추의 또 다른 형식입니다.


## 경고

### Be careful around parameters

할당에서 유추 할 수 없는 경우 type이 함수 매개 변수로 유입되지 않습니다. 예를들어 다음 케이스를 보면 컴파일러는 `foo`의 type을 알지못합니다. 그래서 `a` 와 `b`의 type을 유추 할 수 없습니다.

```t
const foo = (a,b) => { /* do something */ };
```

그러나 `foo`가 입력되면 함수 매개 변수 유형을 유추 할 수 있습니다. (`a`,`b`는 둘 다 아래의 number로 유추됩니다).

```ts
type TwoNumberFunction = (a: number, b: number) => void;
const foo: TwoNumberFunction = (a, b) => { /* do something */ };
```

### Be careful around return

TypeScript는 일반적으로 함수의 반환 type을 유추 할 수 있지만 기대했던 것이 아닐 수도 있다. 예를들면 `foo`의 반환값은 `any` type을 갖고있습니다:
```ts
function foo(a: number, b: number) {
    return a + addOne(b);
}
// Some external function in a library someone wrote in JavaScript
function addOne(a) {
    return a + 1;
}
```

이는 리턴 type이 `addOne`에 대한 빈타입 정의의 영향을 받기 때문입니다(`a`는 `any`이므로 `addOne`의 리턴은 `any`이다. 그래서 `foo`의 리턴은`any`입니다.)

> I find it simplest to always be explicit about function / returns. After all these annotations are a theorem and the function body is the proof.

There are other cases that one can imagine, but the good news is that there is a compiler flag that can help catch such bugs.

## `noImplicitAny`

There is a boolean compiler flag `noImplicitAny` where the compiler will actually raise an error if it cannot infer the type of a variable (and therefore can only have it as an *implicit* `any` type). You can then

* either say that *yes I want it to be an `any`* by *explicitly* adding an `: any` type annotation
* help the compiler out by adding a few more *correct* annotations.
