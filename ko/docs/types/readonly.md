## Readonly
TypeScript의 type 시스템을 사용하면 인터페이스의 개별 속성을 `readonly`로 표시 할 수 있습니다. 이것은 기능적인 방법(예기치 않은 변화는 좋지 않은 경우)으로 일할 수 있게합니다:

```ts
function foo(config: {
    readonly bar: number,
    readonly bas: number
}) {
    // ..
}

let config = { bar: 123, bas: 123 };
foo(config);
// You can be sure that `config` isn't changed 🌹
```

물론 `interface`나 `type` 정의에서도 `readonly`을 사용할 수 있습니다. 예를들면:

```ts
type Foo = {
    readonly bar: number;
    readonly bas: number;
}

// Initialization is okay
let foo: Foo = { bar: 123, bas: 456 };

// Mutation is not
foo.bar = 456; // Error: Left-hand side of assignment expression cannot be a constant or a read-only property
```

또한 클래스의 프로퍼티로도 `readonly`을 선언할 수 있습니다. 아래 보는것 같이 선언할 때나 생성자에서 초기화 해줄 수 있습니다:

```ts
class Foo {
    readonly bar = 1; // OK
    readonly baz: string;
    constructor() {
        this.baz = "hello"; // OK
    }
}
```

### 다양한 사용 케이스들

#### ReactJS
불변성을 좋아하는 라이브러리 중 하나는 ReactJS이며 `Props`와 `State`를 불변으로 표시하는 것이 좋습니다. 예를들면:

```ts
interface Props {
    readonly foo: number;
}
interface State {
    readonly bar: number;
}
export class Something extends React.Component<Props,State> {
    // You can rest assured no one is going to do
    // this.props.foo = 123; (props are immutable)
    // this.state.bar = 456; (one should use this.setState)
}
```

#### 지속적인 불변성

순서 표시에도 readonly을 달 수 있습니다:

```ts
/**
 * Declaration
 */
interface Foo {
    readonly[x: number]: number;
}

/**
 * Usage
 */
let foo: Foo = { 0: 123, 2: 345 };
console.log(foo[0]);   // Okay (reading)
foo[0] = 456;          // Error (mutating): Readonly
```

네이티브 JavaScript 배열을 *불변* 방식으로 사용하려는 경우에 유용합니다. 사실 TypeScript는 `ReadonlyArray <T>`인터페이스를 가지고 있습니다.:

```ts
let foo: ReadonlyArray<number> = [1, 2, 3];
console.log(foo[0]);   // Okay
foo.push(4);           // Error: `push` does not exist on ReadonlyArray as it mutates the array
foo = foo.concat([4]); // Okay: create a copy
```

#### 자동적인 추론
경우에 따라 컴파일러는 특정 항목을 readonly로 자동 추론 할 수 있습니다. 예를들면 클래스에서 프로퍼티가 getter만 있고 setter가 없다면 이는 readonly로 가정합니다. 예:

```ts
class Person {
    firstName: string = "John";
    lastName: string = "Doe";
    get fullName() {
        return this.firstName + this.lastName;
    }
}

const person = new Person();
console.log(person.fullName); // John Doe
person.fullName = "Dear Reader"; // Error! fullName is readonly
```

### `const`와의 차이
`const`
1. 는 변수 참조용입니다.
1. 변수를 어떠한경우에도 재할당 할 수 없습니다.

`readonly` is
1. 프로퍼티를 위한 것입니다. 
1. 프로퍼티는 aliasing때문에 수정가능합니다.

샘플 설명 1:

```ts
const foo = 123; // variable reference
var bar: {
    readonly bar: number; // for property
}
```

 샘플 설명 2:

```ts
let foo: {
    readonly bar: number;
} = {
        bar: 123
    };

function iMutateFoo(foo: { bar: number }) {
    foo.bar = 456;
}

iMutateFoo(foo); // The foo argument is aliased by the foo parameter
console.log(foo.bar); // 456!
```

기본적으로 `readonly`는 *나에 의해서 수정하지 못하도록 보장*하나 보증되지 않는 사람에게 그것을 준다면(타입 호환성의 이유로 허용된다.) 그들은 그것을 수정할 수 있습니다. 물론 `iMutateFoo`가 `foo.bar`를 변경하지 않는다고 말하면, 컴파일러는 표시된 것처럼 올바르게 플래그를 지정합니다:

```ts
interface Foo {
    readonly bar: number;
}
let foo: Foo = {
    bar: 123
};

function iTakeFoo(foo: Foo) {
    foo.bar = 456; // Error! bar is readonly
}

iTakeFoo(foo); // The foo argument is aliased by the foo parameter
```

[](https://github.com/Microsoft/TypeScript/pull/6532)
