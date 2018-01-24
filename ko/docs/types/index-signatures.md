# 인덱스 시그니처

JavaScript(및 TypeScript)의 `Object`는 다른 JavaScript **객체**에 대한 참조를 보유하기 위해 **문자열**을 사용하여 액세스 할 수 있습니다.

여기서 빠르게 확인가능한 예제가 있습니다:

```ts
let foo:any = {};
foo['Hello'] = 'World';
console.log(foo['Hello']); // World
```

문자열 `"World"`을 `"Hello"`키로 저장했습니다. 우리는 JavaScript **객체**에는 어떠한 것도 저장할 수 있다고 말해왔고, 그래서 그러한 부분을 보여주기 위해 클래스 인스턴스를 저장하겠습니다:
```ts
class Foo {
  constructor(public message: string){};
  log(){
    console.log(this.message)
  }
}

let foo:any = {};
foo['Hello'] = new Foo('World');
foo['Hello'].log(); // World
```

또한 **문자열**을 사용하여 액세스 할 수 있다고 말한 것을 기억하십시오. 다른 객체를 인덱스 시그니처에 전달하면 JavaScript 런타임은 실제로 결과를 얻기 전에 `.toString`을 호출합니다. 이러한 내용은 아래에서 설명됩니다:

```ts
let obj = {
  toString(){
    console.log('toString called')
    return 'Hello'
  }
}

let foo:any = {};
foo[obj] = 'World'; // toString called
console.log(foo[obj]); // toString called, World
console.log(foo['Hello']); // World
```

`toString`는 인덱스 위치에서 `obj`가 사용될 때마다 호출됩니다.

배열은 약간 다릅니다. `number`인덱스를 위해 JavaScript VM은 최적화(같은값에 따라 실제로 배열에 일치하는 항목의 구조를 저장합니다.)를 시도합니다. 그래서`number`는 그 자체로 유효한(`string`과 구별되는) 객체 접근자로 간주되어야 합니다. 여기에 간단한 배열 예제가 있습니다:

```ts
let foo = ['World'];
console.log(foo[0]); // World
```

이러한 내용이 JavaScript입니다. 이제 이러한 개념을 TypeScript로 훌륭하게 처리해 보겠습니다.

## TypeScript 인덱스 

첫째, JavaScript는 *암시적*으로 모든 객체 인덱스 시그니처에서 `toString`을 호출하기 때문에, TypeScript는 초보자가 잘못된 코드를 막위해서 오류를 줄 것입니다.(stackoverflow에서 항상 JavaScript의 사용자중 이러한 경우를 많이 보았습니다.):

```ts
let obj = {
  toString(){
    return 'Hello'
  }
}

let foo:any = {};

// ERROR: the index signature must be string, number ...
foo[obj] = 'World';

// FIX: TypeScript forces you to be explicit
foo[obj.toString()] = 'World';
```

사용자에게 명시적으로 강요하는 이유는 객체의 기본 `toString` 구현이 상당히 끔찍하기 때문입니다. 예를들어 v8에서는 항상 `[object Object]`을 반환합니다:

```ts
let obj = {message:'Hello'}
let foo:any = {};

// ERROR: the index signature must be string, number ...
foo[obj] = 'World';

// Here is what you actually stored!
console.log(foo["[object Object]"]); // World
```

물론 `number`은 지원가는합니다. 아래 이유 때문에 필요합니다.

1. Array/Tuple의 지원을 위해서 필요하니다.
1. 당신이 `obj`를 위해 `number`을 사용한다고 하더라도 디폴트 `toString` 구현은 필요(`[object Object]`는 아닙니다.)합니다.

두번째 내용은 아래에서 예제를 보세요:

```ts
console.log((1).toString()); // 1
console.log((2).toString()); // 2
```

여기서 첫번째 배울점은:
So lesson 1:

> TypeScript 인덱스 시그니처는 `string`나 `number`이어야합니다. 

메모: `symbols`는 TypeScript에서도 유효하고 지원됩니다. 그러나 걸음마 단계인 아직은 설명하지는 않겠습니다. 

### 인덱스 시그니처 선언

So we've been using `any` to tell TypeScript to let us do whatever we want. We can actually specify an *index* signature explicitly. E.g. say you want to make sure than anything that is stored in an object using a string conforms to the structure `{message: string}`. This can be done with the declaration `{ [index:string] : {message: string} }`. This is demonstrated below:

```ts
let foo:{ [index:string] : {message: string} } = {};

/**
 * Must store stuff that conforms the structure
 */
/** Ok */
foo['a'] = { message: 'some message' };
/** Error: must contain a `message` or type string. You have a typo in `message` */
foo['a'] = { messages: 'some message' };

/**
 * Stuff that is read is also type checked
 */
/** Ok */
foo['a'].message;
/** Error: messages does not exist. You have a typo in `message` */
foo['a'].messages;
```

> TIP: the name of the index signature e.g. `index` in `{ [index:string] : {message: string} }` has no significance for TypeScript and really for readability. e.g. if its user names you can do `{ [username:string] : {message: string} }` to help the next dev who looks at the code (which just might happen to be you).

Of course `number` indexes are also supported e.g. `{ [count: number] : SomeOtherTypeYouWantToStoreEgRebate }`

### All members must conform to the `string` index signature

As soon as you have a `string` index signature, all explicit members must also conform to that index signature. This is shown below:

```ts
/** Okay */
interface Foo {
  [key:string]: number
  x: number;
  y: number;
}
/** Error */
interface Bar {
  [key:string]: number
  x: number;
  y: string; // Property `y` must of of type number
}
```

This is to provide safety so that any string access gives the same result:

```ts
interface Foo {
  [key:string]: number
  x: number;
}
let foo: Foo = {x:1,y:2};

// Directly
foo['x']; // number

// Indirectly
let x = 'x'
foo[x]; // number
```
### Using a limited set of string literals

An index signature can require that index strings be members of a union of literal strings e.g.:

```ts
type Index = 'a' | 'b' | 'c'
type FromIndex = { [k in Index]?: number }

const good: FromIndex = {b:1, c:2}

// Error:
// Type '{ b: number; c: number; d: number; }' is not assignable to type 'FromIndex'.
//  Object literal may only specify known properties, and 'd' does not exist in type 'FromIndex'.
const bad: FromIndex = {b:1, c:2, d:3};
```
This is often used together with `keyof typeof` to capture vocabulary types, described on the next page.

The specification of the vocabulary can be deferred generically:

```ts
type FromSomeIndex<K extends string> = { [key in K]: number }
```

### Having both `string` and `number` indexers

This is not a common use case, but TypeScript compiler supports it nonetheless.

However it has the restriction that the `string` indexer is more strict than the `number` indexer. This is intentional e.g. to allow typing stuff like:

```ts
interface ArrStr {
  [key: string]: string | number; // Must accomodate all members

  [index: number]: string; // Can be a subset of string indexer

  // Just an example member
  length: number;
}
```

### Design Pattern: Nested index signature

> API consideration when adding index signatures

Quite commonly in the JS community you will see APIs that abuse string indexers. e.g. a common pattern among CSS in JS libraries:

```js
interface NestedCSS {
  color?: string;
  [selector: string]: string | NestedCSS;
}

const example: NestedCSS = {
  color: 'red',
  '.subclass': {
    color: 'blue'
  }
}
```
Try not to mix string indexers with *valid* values this way. E.g. a typo in the padding will remain uncaught:

```js
const failsSilently: NestedCSS = {
  colour: 'red', // No error as `colour` is a valid string selector
}
```

Instead seperate out the nesting into its own property e.g. in a name like `nest` (or `children` or `subnodes` etc.):

```js
interface NestedCSS {
  color?: string;
  nest?: {
    [selector: string]: NestedCSS;
  }
}

const example: NestedCSS = {
  color: 'red',
  nest: {
    '.subclass': {
      color: 'blue'
    }
  }
}

const failsSilently: NestedCSS = {
  colour: 'red', // TS Error: unknown property `colour`
}
```
