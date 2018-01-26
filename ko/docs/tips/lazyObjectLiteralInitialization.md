## 늦게하는 객체 리터럴 초기화

JavaScript 코드에서는 일반적으로 다음과 같은 방법으로 초기화하고 리터럴을 처리합니다:

```ts
let foo = {};
foo.bar = 123;
foo.bas = "Hello World";
```

코드를 TypeScript로 옮기면 다음과 같은 오류가 발생합니다:

```ts
let foo = {};
foo.bar = 123; // Error: Property 'bar' does not exist on type '{}'
foo.bas = "Hello World"; // Error: Property 'bas' does not exist on type '{}'
```


이것은 `let foo = {}` 상태 때문입니다. TypeScript는 `foo`(왼쪽방향으로 할당을 초기화)의 타입을 오른쪽의 `{}`(예, 오브젝트에는 프로퍼티가 없습니다.)의 타입으로 *유추*합니다. 따라서 알지 못하는 속성에 할당 하려고해서 오류가 발생한 것입니다.

### 이상적인 수정방법

TypeScript에서 객체를 초기화하는 *적절한*방법은 할당할 때 수행하는 것입니다:

```ts
let foo = {
    bar: 123,
    bas: "Hello World",
};
```

이는 코드 검토 및 코드 유지관리 목적으로도 좋습니다.

### 빠르게하는 수정방법

TypeScript로 마이그레이션하는 JavaScript 코드가 많다면 이상적인 해결책이 실행 가능한 솔루션이 아닐 수도 있습니다. 이 경우 *type의 주장*을 사용하여 컴파일러를 통과 할 수 있습니다:

```ts
let foo = {} as any;
foo.bar = 123;
foo.bas = "Hello World";
```

### 중간방식의 수정방법

물론 `any`를 사용하는 것은 TypeScript의 안전을 무효화하여 매우 나쁠 수 있습니다. 그래서 중간방식의 수정은 '인터페이스'를 만들면 
* 좋은 코드가 됩니다.
* 안전하게 할당할 수 있습니다.

아래 내용을 보세요:

```ts
interface Foo {
    bar: number
    bas: string
}

let foo = {} as Foo;
foo.bar = 123;
foo.bas = "Hello World";
```


인터페이스를 사용하면 도움이 된다는 사실을 보여주는 간단한 예가 있습니다:

```ts
interface Foo {
    bar: number
    bas: string
}

let foo = {} as Foo;
foo.bar = 123;
foo.bas = "Hello World";

// later in the codebase:
foo.bar = 'Hello Stranger'; // Error: You probably misspelled `bas` as `bar`, cannot assign string to number
}
```
