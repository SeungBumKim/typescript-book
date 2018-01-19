## Type 주장
TypeScript를 사용하면 원하는 방식으로 type의 유추되고 분석된 뷰를 재정의 할 수 있습니다. 이것은 "type 주장"라는 메커니즘에 의해 수행됩니다. TypeScript의 type 주장는 순전히 컴파일러에게 당신이 타입보다 더 잘 알고 있고, 그것을 추측해서는 안된다는 것을 알려주는 것입니다.

type주장의 일반적인 사용 사례는 JavaScript에서 TypeScript로 코드를 변환 할 때입니다. 예로 다음 패턴을 고려해보세요:

```ts
var foo = {};
foo.bar = 123; // Error: property 'bar' does not exist on `{}`
foo.bas = 'hello'; // Error: property 'bas' does not exist on `{}`
```

`foo`는 `{}`(예를들면 프로퍼티가 없는 오브젝트)로 *추론*되어서 에러가 발생했습니다. 그래서 `bar`나 `bas`을 추가할 수 없었던 것입니다. `as Foo` type 주장으로 간단하게 고칠 수 있습니다:

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo = {} as Foo;
foo.bar = 123;
foo.bas = 'hello';
```

### `as foo` vs `<foo>`
원래는 `<foo>`구문을 사용했었습니다. 아래와 같이 설명됩니다: 

```ts
var foo: any;
var bar = <string> foo; // bar is now of type "string"
```

그러나 JSX에서 `<foo>`스타일 주장을 사용할 때 언어 문법에 모호함이 있습니다:

```ts
var foo = <string>bar;
</string>
```

그래서 일관성 측면서에도 요즘은 `as foo`의 사용을 추천합니다.

### Type 주장 vs 캐스팅
"type 캐스팅"이라고 불리는 않는 이유는 *캐스팅*은 일반적으로 일종의 런타임 지원을 의미하기 때문입니다. 그러나 *type 주장*은 순전히 컴파일 타임을 위한 구조이며 코드 분석 방법에 대한 힌트를 컴파일러에 제공합니다.

### Assertion 사용의 위험성.
대부분의 경우 assertion(주장)을 통해 기존 코드를 쉽게 마이그레이션 할 수 있습니다.(코드베이스에 다른 코드 샘플을 붙여 넣기 복사하기). 그러나 사용함에 있어 주의가 필요합니다. 기존 코드를 샘플로 사용하면 컴파일러는 *약속한 속성을 실제로 추가하는 것을 잊지 않도록 보호하지 않습니다*:

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo = {} as Foo;
// ahhhh .... forget something?
```

또한 또다른 일반적인 생각은 *자동 완성*을 제공하는 수단으로 assertion(주장)을 사용하는 것입니다. 예를들면:

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo = <Foo>{
    // the compiler will provide autocomplete for properties of Foo
    // But it is easy for the developer to forget adding all the properties
    // Also this code is likely to break if Foo gets refactored (e.g. a new property added)
};
```

하지만 컴파일러가 요청하지 않는 속성을 잊어 버린 경우, 여기서도 동일한 위험요소가 있습니다. 다음과 같이 사용하는 것이 더 나을 것입니다:

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo:Foo = {
    // the compiler will provide autocomplete for properties of Foo
};
```

어떤 경우에는 임시 변수의 생성이 필요할 수도있습니다. 하지만 적어도 당신은 (아마도 거짓)promise를 만들지않아도 될 것입니다. 대신 type 추론에 의존하여  검사 할 것입니다.

### 중복 주장
type 주장은 우리가 보여준 것처럼 조금 안전하지는 않지만 *완전히 그런 것만*은 아닙니다. 예를들면, 다음은 매우 유용한 사용일 것이고(예, 사용자는 전달된 이벤트가 이벤트의 보다 구체적인 경우라고 생각합니다.), type 주장은 기대하는바와 동작할 것입니다:

```ts
function handler (event: Event) {
    let mouseEvent = event as MouseEvent;
}
```

그러나 다음은 오류일 가능성이 높으며 TypeScript는 사용자의 type 주장에도 불구하고 표시된대로 경고을 제기합니다:

```ts
function handler(event: Event) {
    let element = event as HTMLElement; // Error: Neither 'Event' not type 'HTMLElement' is assignable to the other
}
```

그래도 여전히 사용하기를 원하면, 중첩된 assertion(주장)을 사용하세요. 첫번째 assertion `any`은 모든 type 호환되고 그래서 컴파일러는 경고하지  것입니다:

```ts
function handler(event: Event) {
    let element = event as any as HTMLElement; // Okay!
}
```

#### typescript 단일 assertion이 어떻게 충분하지 않은지 결정하는가?
기본적으로 `S`가 `T`의 하위 type이거나 `T`가 `S`의 하위 type인 경우 `S`type에서 `T`type의 assertion(주장)이 성공할 수 있습니다. 이것은 type주장를 할 때 특별한 안전을 제공하기 위한 것 입니다. 완전히 관계없는 assertion(주장)은 매우 안전하지 않을 수 있으며 안전하지 않은것을 사용하려면 `any`를 사용해야합니다.
