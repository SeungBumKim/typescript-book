# Never

> 다음 비디오에서 never type에 대해서 가르켜줄 것입니다.[video](https://egghead.io/lessons/typescript-use-the-never-type-to-avoid-code-with-dead-ends-using-typescript)

프로그래밍 언어 디자인은 *코드 흐름 분석*을 수행하는 즉시 **자연스러운** 결과인 *bottom* 유형의 개념을 가지고 있습니다. TypeSCript도 *코드 흐름 분석*을 하고있고 그래서 결코 일어날 수 없는 것들을 신뢰할 수 있게 나타낼 필요가 있습니다.

`never` type TypeScript안에서 이러한 *bottom*유형을 나타내는데 사용됩니다. 자연스럽게 발생하는 경우로는:
* 함수가 리턴을 하지 않는다.(예, 함수안에 `while(true){}`가 있는 경우)
* 함수가 언제나 throw 시킨다. (예, `function foo(){throw new Error('Not Implemented')}`안에서 `foo` 반환 type은 `never`입니다.)

물론 어노테이션을 사용할 수도 있습니다.

```ts
let foo: never; // Okay
```

그러나 `never`은 *오직 다른 `never`에서만 할당할 수 있습니다*. 예를드면,

```ts
let foo: never = 123; // Error: Type number is not assignable to never

// Okay as the function's return type is `never`
let bar: never = (() => { throw new Error('Throw my hands in the air like I just dont care') })();
```

좋습니다. 이제는 주요 사용 케이스를 살펴보겠습니다 :)

# 사용 케이스: 철저한 검사

never함수를 never컨텍스트 안에서 호출 할 수 있습니다.

```ts
function foo(x: string | number): boolean {
  if (typeof x === "string") {
    return true;
  } else if (typeof x === "number") {
    return false;
  }

  // Without a never type we would error :
  // - Not all code paths return a value (strict null checks)
  // - Or Unreachable code detected
  // But because typescript understands that `fail` function returns `never`
  // It can allow you to call it as you might be using it for runtime safety / exhaustive checks.
  fail("Unexhaustive!");
}

function fail(message: string) { throw new Error(message); }
```

`never`는 오직 다른 `never`에서만 할당 가능하기 때문에 *컴파일 시간*에 철저한 검사에도 사용할 수 있습니다. 이러한 내용은 다음에서 자세히 다룹니다.[*discriminated union* section](./discriminated-unions.md).

# `void`와의 차이점

함수가 종료되지 않을 때 `never`가 반환된다고하자 마자 여러분은 그것을 `void`와 똑같다고 생각할 것입니다. 그러나 `void`는 유닛입니다. `never`는 속임수 입니다.

아무것도 *반환*하지 않는 함수는 유닛 `void`를 반환합니다. 그러나 절대로 결과값을 반환하지 않는(또는 항상 throw 시키는) 함수는 `never`를 반환합니다. 
`void`는 무언가 할당 가능하지만(`strictNullChecking` 없이) `never`는 절대로 다른 `never`외에는 할당하지 못합니다.

<!--
PR: https://github.com/Microsoft/TypeScript/pull/8652
Issue : https://github.com/Microsoft/TypeScript/issues/3076
Concept : https://en.wikipedia.org/wiki/Bottom_type
-->
