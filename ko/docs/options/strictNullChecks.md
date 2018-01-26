## `strictNullChecks`
기본적으로 `null`과 `undefined`는 TypeScript의 모든 타입에 할당 가능합니다. 예:

```ts
let foo: number = 123;
foo = null; // Okay
foo = undefined; // Okay
```

이것은 많은 사람들이 JavaScript를 작성한 방법을 모방 한 것입니다. 그러나 모든것들처럼 TypeScript는 `null` 또는 `undefined`가 될 수 있는 것과 될 수없는 것에 대해 *명시적으로* 할 수 있게합니다.

엄격한 null 검사 모드에서 `null`와 `undefined`는 다릅니다:

```ts
let foo = undefined;
foo = null; // NOT Okay
``` 

`Member` 인터페이스가 있다고 가정해 봅시다:

```ts
interface Member {
  name: string,
  age?: number
}
```

모든 회원이 나이를 제공하는 것은 아니므로 `나이`은 선택사항입니다. `age`의 값은 아마도`undefined`가 될 것입니다.

`undefined`은 잘못된 사용의 근원입니다. 항상 런타임 오류를 발생시킵니다. `Error`를 던지는 코드를 작성하는 것이 나을것 입니다:

```ts
getMember()
  .then(member: Member => {
    const stringifyAge = member.age.toString() // probably throw Cannot read property 'toString' of undefined
  })
```

그러나 엄격한 null 검사 모드에서는 컴파일시 오류가 발생합니다:

```ts
getMember()
  .then(member: Member => {
    const stringifyAge = member.age.toString() // Object is possibly 'undefined'
  })
```

### Non-Null 경고 연산자

새로운`!` 후위 표현 연산자는 type 검사기가 그 사실을 결론 지을 수 없는 문맥에서 그것의 피연산자가 null이 아니고 정의되지 않았음을 주장하는데 사용될 수 있습니다. 예를들면:

```ts
// Compiled with --strictNullChecks
function validateEntity(e?: Entity) {
    // Throw exception if e is null or invalid entity
}

function processEntity(e?: Entity) {
    validateEntity(e);
    let a = e.name;  // TS ERROR: e may be null.
    let b = e!.name;  // Assert that e is non-null. This allows you to access name
}
```
> 이것은 단지 경고이며, type경고와 마찬가지로 값이 null이 아님을 확인해야하는 책임이 있습니다. null 경고는 본질적으로 컴파일러에게 "null이 아니라는 것을 알기 때문에 null이 아닌 것처럼 사용하도록 해달라"라고 말해주는겁니다.
