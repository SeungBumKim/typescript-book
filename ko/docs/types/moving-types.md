TypeScripts type 시스템은 언어 이동/분리/변경 type에서 매우 강력한 기능을 제공하는 점에서 다른 단일 언어로는 불가능한 매우 강력합니다.

이것은 TypeScript가 JavaScript와 같이 *동적인* 언어로 원활하게 작업 할 수 있도록 설계 되었기 때문입니다. 여기에서는 TypeScript에서 type을 이동하는 몇 가지 트릭을 소개합니다.

주요 동기는 다음과 같습니다: 잘 설계된 제약 시스템처럼 한 가지만 변경하면 다른 모든 것은 자동으로 업데이트되고 어떤것이 손상되면 오류가 발생합니다.

#### type + 값 모두 복사

클래스를 옮기고 싶을때 다음과 같의 시도하려고 할 것입니다:

```ts
class Foo { }
var Bar = Foo;
var bar: Bar; // ERROR: "cannot find name 'Bar'"
```
`var`는`Foo`를 *변수* 선언 공간에 복사하기 때문에 오류입니다. 따라서 `Bar`를 type 어노테이션으로 사용할 수 없습니다. 적절한 방법은 `import` 키워드를 사용하는 것입니다. *namespaces* 또는 *modules*를 사용하는 경우에만 `import` 키워드를 사용할 수 있습니다.(자세한 내용은 뒤에 있습니다.):

```ts
namespace importing {
    export class Foo { }
}

import Bar = importing.Foo;
var bar: Bar; // Okay
```

이 `import`트릭은 type과 변수 모두에 대해서 작동합니다.

#### 변수 type 캡처

`typeof` 연산자를 사용하여 타입 어노테이션에서 실제로 변수를 사용할 수 있습니다. 이를통해 컴파일러에게 하나의 변수가 다른 변수와 동일한 유형임을 알릴 수 있습니다. 여기에 이를 설명하는 예제가 있습니다:

```ts
var foo = 123;
var bar: typeof foo; // `bar` has the same type as `foo` (here `number`)
bar = 456; // Okay
bar = '789'; // ERROR: Type `string` is not `assignable` to type `number`
```

#### 클래스 멤버의 type 캡처

변수 type 캡처와 유사하게 type 캡처 목적으로 변수를 선언하면 됩니다:

```ts
class Foo {
  foo: number; // some member whose type we want to capture
}

// Purely to capture type
declare let _foo: Foo;

// Same as before
let bar: typeof _foo.foo;
```

### 매직 문자열의 type 캡처

많은 JavaScript 라이브러리와 프레임워크는 원시 JavaScript 문자열로 작동합니다. `const` 변수를 사용하여 type을 캡처 할 수 있습니다. 예를들면:

```ts
// Capture both the *type* and *value* of magic string:
const foo = "Hello World";

// Use the captured type:
let bar: typeof foo;

// bar can only ever be assigned to `Hello World`
bar = "Hello World"; // Okay!
bar = "anything else "; // Error!
```

예제의 `bar`는 `"Hello World"` 리터럴 type을 갖고 있습니다. 자세한 내용은 앞에서 다루었습니다. [literal type section](https://seungbumkim.gitbooks.io/typescript/ko/docs/types/literal-types.html).

### 키의 이름 캡처

`keyof` 연산자는 type의 키 이름을 캡처합니다. 예를들면, `typeof`를 사용하여 type을 먼저 잡아서 변수의 키 이름을 캡처 할 수 있습니다:

```ts
const colors = {
  red: 'red',
  blue: 'blue'
}
type Colors = keyof typeof colors;

let color: Colors;
color = 'red'; // okay
color = 'blue'; // okay
color = 'anythingElse'; // Error
```

이것은 위의 예제에서 보았듯이 문자열 enums + 상수 같은 것을 아주 쉽게 가질 수 있게 해줍니다.
