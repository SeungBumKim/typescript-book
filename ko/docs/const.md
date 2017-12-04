### const

`const`가 ES6 / TypeScript에 추가된 것은 매우 환영받을 일이다. 변수를 사용하여 변경할 수 없게합니다. 이것은 코드뿐아니라 런타임 관점에서도 유용합니다. 사용방식은 `var`위치에 `const`를 사용하면 됩니다:

```ts
const foo = 123;
```

> 이 구문은 사용자가 `let constant foo`와 같이 입력하도록 강요하는 다른 언어(IMHO)보다 훨씬 낫습니다. 예를들면 변수 + 동작 지정자.

`const`는 가독성과 유지보수에 좋고, *매직 상수*를 피할 수 있습니다. 예를들면,

```ts
// Low readability
if (x > 10) {
}

// Better!
const maxRows = 10;
if (x > maxRows) {
}
```

#### const 선언은 반드시 초기화해야한다.
다음은 컴파일 에러가 발생합니다:

```ts
const foo; // ERROR: const declarations must be initialized
```

#### 상수는 좌변으로 사용할 수 없다.
상수는 생성한 후에는 변경할 수 없고, 만약 새로운 값을 할당하려고하면 컴파일 에러가 발생합니다:

```ts
const foo = 123;
foo = 456; // ERROR: Left-hand side of an assignment expression cannot be a constant
```

#### 블럭 범위
`const`는 [`let`](./let.md)과 같은 블럭범위를 갖습니다:

```ts
const foo = 123;
if (true) {
    const foo = 456; // Allowed as its a new variable limited to this `if` block
}
```

#### Deep immutability
`const`는 상수오브젝트로 동작할뿐아니라 *참조*변수에 대해서도 보호합니다:

```ts
const foo = { bar: 123 };
foo = { bar: 456 }; // ERROR : Left hand side of an assignment expression cannot be a constant
```

그러나 오브젝의 하위 프로퍼티에 대해서는 아래와 같이 허용합니다:

```ts
const foo = { bar: 123 };
foo.bar = 456; // Allowed!
console.log(foo); // { bar: 456 }
```

이러한 이유로 기본변수나 변하지 않을 데이터 구조체로 `const`를 사용할 것을 권장합니다.
