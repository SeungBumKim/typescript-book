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

그래서 우리는 TypeScript에게 우리가 원하는 것을 무엇이든지 하도록 알려주기 위해`any`를 사용했습니다. *인덱스* 시그니처를 명시적으로 지정할 수 있습니다. 예를들어, `{message : string}`구조체를 따르는 문자열을 사용하는 객체에 저장되는 것보다 더 확실하게 만들고 싶다고합시다. 이것은 `{ [index:string] : {message: string} }`선언으로 할 수 있습니다. 이러한 내용은 아래에서 설명됩니다:

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

> TIP: 인덱스 시그니처의 이름 (예를들어 `{ [index:string] : {message: string} }`안의 `index`)는 TypeScript에서는 의미가 없으며 가독성을 위해 사용됩니다. 예를들자면 `{ [username:string] : {message: string} }`와 같이 사용자 이름을 사용한다면, 다음 개발자(그 개발자가 당신 일지라도)가 코드를 볼 때 도움이 될 것 입니다.

물론 `number`인덱스는 `{ [count: number] : SomeOtherTypeYouWantToStoreEgRebate }`와 같이 지원됩니다.

### 모든 멤버는`string` 인덱스 시그니처를 준수해야 합니다.

`string`을 인덱스 시그니처를 갖으면, 모든 명시적인 멤버도 해당 인덱스 시그니처를 준수해야합니다. 아래에 관련내용이 있습니다:

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

안정성을 제공하기 위해서고 그래서 어떤 문자열 액세스에도 동일한 결과를 제공합니다:

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
### 제한된 문자열 리터럴 세트 사용

인덱스 시그니처는 해당 인덱스 문자열을 리터럴 문자열의 조합의 멤버로 요구할 수 있습니다. 예를들면:

```ts
type Index = 'a' | 'b' | 'c'
type FromIndex = { [k in Index]?: number }

const good: FromIndex = {b:1, c:2}

// Error:
// Type '{ b: number; c: number; d: number; }' is not assignable to type 'FromIndex'.
//  Object literal may only specify known properties, and 'd' does not exist in type 'FromIndex'.
const bad: FromIndex = {b:1, c:2, d:3};
```

이것은 종종 `keyof typeof`와 함께 사용되어 어휘 type을 캡처합니다. 다음페이지에서 설명하겠습니다.

어휘의 명세는 보통 미루어 짐작해 알 수 있습니다:

```ts
type FromSomeIndex<K extends string> = { [key in K]: number }
```

### `string`과 `number`인덱서를 모두 가지고 있는 경우

이 경우는 일반적이지 않은 케이스이나 TypeScript 컴파일러는 이를 지원합니다.

그러나`string`인덱서가 `number` 인덱서보다 엄격하다는 제한이 있습니다. 이부분은 의도된 부분으로 예를들어 아래와 같은 타이핑을 허용하기 위한 것입니다:

```ts
interface ArrStr {
  [key: string]: string | number; // Must accomodate all members

  [index: number]: string; // Can be a subset of string indexer

  // Just an example member
  length: number;
}
```

### 디자인 패턴: 중첩된 인덱스 시그니처

> 인덱스 시그니처 추가시 API 고려 사항

일반적으로 JS 커뮤니티에서 문자열 인덱서를 남용하는 API를 볼 수 있습니다. 예를들면, JS 라이브러리에서 CSS 사이의 공통 패턴으로:

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
이런식으로 문자열 인덱서와 *유효한* 값을 섞지 마십시오. 예를들면, 패딩의 오타가 잡히지 않을 것입니다:

```js
const failsSilently: NestedCSS = {
  colour: 'red', // No error as `colour` is a valid string selector
}
```

대신 중첩된 것을 프로퍼티로 분리하십시오. 예를들면, `nest`(`children` 또는 `subnodes` 등)와 같은 이름으로:

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
