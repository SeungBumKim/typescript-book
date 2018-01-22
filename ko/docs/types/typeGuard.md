## Type 가드
type 가드를 사용하면 객체 type의 범위를 조건 블록 내로 좁힐 수 있습니다.


### typeof

TypeScript는 JavaScript `instanceof`와`typeof` 연산자의 사용법을 알고 있습니다. 조건부 블록에서 이들을 사용하면 TypeScript는 해당 조건부 블록 내에서 변수의 type이 다름을 인식합니다. 다음은 TypeScript가 특정 함수에서 `string`에 존재하지 않는다는 것을 깨닫고 아마도 사용자의 오자였던 것을 보여주는 빠른 예제입니다:

```ts
function doSomething(x: number | string) {
    if (typeof x === 'string') { // Within the block TypeScript knows that `x` must be a string
        console.log(x.subtr(1)); // Error, 'subtr' does not exist on `string`
        console.log(x.substr(1)); // OK
    }
    x.substr(1); // Error: There is no guarantee that `x` is a `string`
}
```

### instanceof

여기서는 클래스와 `instanceof`의 예제입니다:

```ts
class Foo {
    foo = 123;
    common = '123';
}

class Bar {
    bar = 123;
    common = '123';
}

function doStuff(arg: Foo | Bar) {
    if (arg instanceof Foo) {
        console.log(arg.foo); // OK
        console.log(arg.bar); // Error!
    }
    if (arg instanceof Bar) {
        console.log(arg.foo); // Error!
        console.log(arg.bar); // OK
    }

    console.log(arg.common); // OK
    console.log(arg.foo); // Error!
    console.log(arg.bar); // Error!
}

doStuff(new Foo());
doStuff(new Bar());
```

TypeScript는 `else`를 이해하기 때문에 `if`가 하나의 타입을 좁히면 else 안에 *타입을 확실히 알 수 있습니다*. 여기 예제가 있습니다:

```ts
class Foo {
    foo = 123;
}

class Bar {
    bar = 123;
}

function doStuff(arg: Foo | Bar) {
    if (arg instanceof Foo) {
        console.log(arg.foo); // OK
        console.log(arg.bar); // Error!
    }
    else {  // MUST BE Bar!
        console.log(arg.foo); // Error!
        console.log(arg.bar); // OK
    }
}

doStuff(new Foo());
doStuff(new Bar());
```

### 구문 Type 가드

여러 구문type을 가지고있을 때 그들을 차별 할 수 있는지 확인 할 수 있습니다. 예를들면:

```ts
type Foo = {
  kind: 'foo', // Literal type 
  foo: number
}
type Bar = {
  kind: 'bar', // Literal type 
  bar: number
}

function doStuff(arg: Foo | Bar) {
    if (arg.kind === 'foo') {
        console.log(arg.foo); // OK
        console.log(arg.bar); // Error!
    }
    else {  // MUST BE Bar!
        console.log(arg.foo); // Error!
        console.log(arg.bar); // OK
    }
}
```

### 사용자 정의 type 가드
JavaScript에는 다양한 런타임 검사 지원 기능이 내장되어 있지 않습니다. 일반적인 JavaScript 객체 만 사용하는 경우(개발 편의를 위한 구조적 type사용), `instanceof`나 `typeof`에 접근해서 사용할 수 없습니다. 이러한 경우에는 *사용자 정의형 type 가드 함수*를 생성해야 합니다. `someArgumentName is SomeType`를 반환하는 함수입니다. 여기에 예제가 있습니다:

```ts
/**
 * Just some interfaces
 */
interface Foo {
    foo: number;
    common: string;
}

interface Bar {
    bar: number;
    common: string;
}

/**
 * User Defined Type Guard!
 */
function isFoo(arg: any): arg is Foo {
    return arg.foo !== undefined;
}

/**
 * Sample usage of the User Defined Type Guard
 */
function doStuff(arg: Foo | Bar) {
    if (isFoo(arg)) {
        console.log(arg.foo); // OK
        console.log(arg.bar); // Error!
    }
    else {
        console.log(arg.foo); // Error!
        console.log(arg.bar); // OK
    }
}

doStuff({ foo: 123, common: '123' });
doStuff({ bar: 123, common: '123' });
```
