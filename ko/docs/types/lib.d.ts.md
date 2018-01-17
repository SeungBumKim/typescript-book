## `lib.d.ts`

특별한 선언 파일`lib.d.ts`는 TypeScript의 모든 설치와 함께 제공됩니다. 이 파일에는 JavaScript 런타임 및 DOM에 있는 다양한 공통 JavaScript 구문에 대한 주변 선언이 포함되어 있습니다.

* 이 파일은 TypeScript 프로젝트의 컴파일 컨텍스트에 자동으로 포함됩니다.
* 이 파일의 목적은 *type이 검증된* JavaScript 코드 작성을 쉽게 시작할 수 있도록 하는 것입니다.

`--noLib`를 컴파일러 명령의 플래그로 지정하여 컴파일 컨텍스트에서이 파일을 제외 할 수 있습니다.(또는 `tsconfig.json`안에 `"noLib" : true`을 추가합니다.)

### 사용 예제

이 파일이 실제로 자주 사용되는 예를 살펴 보겠습니다.

```ts
var foo = 123;
var bar = foo.toString();
```
이 코드 형식은 모든 JavaScript 객체에 대해 `toString` 함수가 `lib.d.ts`에 *정의되어 있기 때문에* 괜찮습니다.

`noLib`옵션이 적용된 상태에서 사용하면, type 체크 에러를 발생할 것입니다:

```ts
var foo = 123;
var bar = foo.toString(); // ERROR: Property 'toString' does not exist on type 'number'.
```
이제`lib.d.ts`의 중요성을 알았으니 내용이 어떻게 생겼는지 아래에서 검토하겠습니다.

### `lib.d.ts` 들여보기

`lib.d.ts`의 내용은 주로 *변수* 선언의 묶음입니다. 예를들면 `window`, `document`, `math`와 *interface*선언과 유사한 `Window` , `Document`, `Math`가 있습니다.

어떠한 type이 코드에 있는지 알아보는 간단한 방법은 예를들어 IDE에서 `Math.floor`와 같이 입력하고 F12(정의로 이동)를 눌러 보는 겁니다.(atom-typescript은 이러한 부분을 잘 지원합니다.)

*변수* 선언의 샘플을 보면 `window`의 정의는 다음과 같습니다:
```ts
declare var window: Window;
```
변수이름(여기서는`window`)과 type 키워드(여기서는`Window` 인터페이스)에 대한 인터페이스가 뒤따르는 단순한 `declare var` 입니다. 이러한 변수는 일반적으로 전역 *인터페이스*를 가르킵니다. 예를들어 여기에 (실제로 꽤 방대한)`Window` 인터페이스의 작은 샘플이 있습니다:

 These variables generally point to some global *interface* e.g. here is a small sample of the (actually quite massive) `Window` interface:

```ts
interface Window extends EventTarget, WindowTimers, WindowSessionStorage, WindowLocalStorage, WindowConsole, GlobalEventHandlers, IDBEnvironment, WindowBase64 {
    animationStartTime: number;
    applicationCache: ApplicationCache;
    clientInformation: Navigator;
    closed: boolean;
    crypto: Crypto;
    // so on and so forth...
}
```
이 인터페이스에는 *다양한* type 정보가 있습니다. TypeScript가 없는 경우 *사용자의 머리 속에* 이 내용을 기억해야합니다. 이제 당신은 `intellisense`와 같은 것들을 사용하여 컴파일러에 대한 지식을 쉽게 접근 할 수 있습니다.

이러한 *인터페이스*를 전역으로 사용해야하는 좋은 이유가 있습니다. `lib.d.ts`를 바꿀 필요없이 전역 변수에 *추가 속성을 추가* 할 수 있습니다. 이 개념은 다음에 이어서 다루겠습니다.

### 네이티브 type의 수정
TypeScript의 `interface`가 열린상태이기 때문에 `lib.d.ts`에 선언된 인터페이스에 멤버를 추가 할 수 있고 이는 TypeScript가 추가로 가져온다는 것을 의미합니다. 이러한 인터페이스가`lib.d.ts`와 관련되게 하려면 다음에서 이러한 변경을 해야합니다.[*global module*](../project/modules.md). 이러한 목적으로 특수한 파일인 다음내용의 파일을 만드는 것을 추천합니다.[`globals.d.ts`](../project/globals.md)

아래에 `window`, `Math`, `Date`의 예제 케이스가 있습니다:

#### 예제 `window`

`Window`인터페스를 추가하면 됩니다:

```ts
interface Window {
    helloWorld(): void;
}
```

이렇게하면 *type 안정적*으로 사용할 수 있습니다:

```ts
// Add it at runtime
window.helloWorld = () => console.log('hello world');
// Call it
window.helloWorld();
// Misuse it and you get an error:
window.helloWorld('gracius'); // Error: Supplied parameters do not match the signature of the call target
```

#### 예 `Math`
전역변수 `Math`는 `lib.d.ts`에 정의되어 있습니다.(개발툴을 이용해서 정의로 이동하십시오.):

```ts
/** An intrinsic object that provides basic mathematics functionality and constants. */
declare var Math: Math;
```

변수 `Math`는 `Math`인터페이스의 인스턴스 입니다. `Math`의 정의는 다음과 같습니다:

```ts
interface Math {
    E: number;
    LN10: number;
    // others ...
}
```

즉, `Math`를 전역변수에 추가하고 싶다면 `Math`전역 인터페이스에 추가 하면됩니다. 예를들어, [`seedrandom` project](https://www.npmjs.com/package/seedrandom)를 보면 `seedrandom` 함수를 전역 `Math` 오브젝트에 추가했습니다. 이것은 아주 쉽게 선언 될 수 있습니다:
```ts
interface Math {
    seedrandom(seed?: string);
}
```

그리고나서 다음과 같이 사용하면 됩니다:

```ts
Math.seedrandom();
// or
Math.seedrandom("Any string you want!");
```

#### 예 `Date`

`lib.d.ts`안의 `Date` *변수* 정의를 살펴보면 다음과 같습니다:

```ts
declare var Date: DateConstructor;
```
`DateConstructor` 인터페이스는 `Date` 전역 변수(예를들면, `Date.now()`)에서 사용할 수 있는 멤버가 포함되어 있다는 점에서 이전에 `Math`와 `Window`에서 보았던 것과 유사합니다. 이 멤버들 외에도 `Date` 인스턴스를 생성(예를들면, `new Date()`) 할 수 있는 *생성자* 시그니처가 있습니다. `DateConstructor`의 일부내용을 보면 아래와 같습니다:

```ts
interface DateConstructor {
    new (): Date;
    // ... other construct signatures

    now(): number;
    // ... other member functions
}
```

[`datejs`](https://github.com/abritinthebay/datejs)프로젝트를 참고하세요. 
Consider the project [`datejs`](https://github.com/abritinthebay/datejs). DateJS adds members to both the `Date` global variable and `Date` instances. Therefore a TypeScript definition for this library would look like ([BTW the community has already written this for you in this case](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/datejs/index.d.ts)):

```ts
/** DateJS Public Static Methods */
interface DateConstructor {
    /** Gets a date that is set to the current date. The time is set to the start of the day (00:00 or 12:00 AM) */
    today(): Date;
    // ... so on and so forth
}

/** DateJS Public Instance Methods */
interface Date {
    /** Adds the specified number of milliseconds to this instance. */
    addMilliseconds(milliseconds: number): Date;
    // ... so on and so forth
}
```
This allows you to do stuff like the following in a TypeSafe manner:

```ts
var today = Date.today();
var todayAfter1second = today.addMilliseconds(1000);
```

#### Example `string`

If you look inside `lib.d.ts` for string you will find stuff similar to what we saw for `Date` (`String` global variable, `StringConstructor` interface, `String` interface). One thing of note though is that the `String` interface impacts string *literals* as well as demonstrated in the below code sample:

```ts

interface String {
    endsWith(suffix: string): boolean;
}

String.prototype.endsWith = function(suffix: string): boolean {
    var str: string = this;
    return str && str.indexOf(suffix, str.length - suffix.length) !== -1;
}

console.log('foo bar'.endsWith('bas')); // false
console.log('foo bas'.endsWith('bas')); // true
```

Similar variable / interfaces exist for other things that have both static and instance member like `Number`, `Boolean`, `RegExp`, etc. and these interfaces affect literal instances of these types as well.

### Example `string` redux

We recommended creating a `global.d.ts` for maintainability reasons. However you can break into the *global namespace* from within *a file module* if you desire so. This is done using `declare global { /*global namespace here*/ }`. E.g. the previous example can also be done as:

```ts
// Ensure this is treated as a module.
export {};

declare global {
    interface String {
        endsWith(suffix: string): boolean;
    }
}

String.prototype.endsWith = function(suffix: string): boolean {
    var str: string = this;
    return str && str.indexOf(suffix, str.length - suffix.length) !== -1;
}

console.log('foo bar'.endsWith('bas')); // false
console.log('foo bas'.endsWith('bas')); // true
```

### Using your own custom lib.d.ts
As we mentioned earlier using the `noLib` boolean compiler flag causes TypeScript to exclude the automatic inclusion of `lib.d.ts`. There are various reasons why this is a useful feature. Here are a few of the common ones:

* You are running in a custom JavaScript environment that differs *significantly* from the standard browser based runtime environment.
* You like to have *strict* control over the *globals* available in your code. E.g. lib.d.ts defines `item` as a global variable and you don't want this to leak into your code.

Once you have excluded the default `lib.d.ts` you can include a similarly named file into your compilation context and TypeScript will pick it up for type checking.

> Note: be careful with `--noLib`. Once you are in noLib land, if you chose to share your project with others, they will be *forced* into noLib land (or rather *your lib* land). Even worse, if you bring *their* code into your project you might need to port it to *your lib* based code.

### Compiler target effect on `lib.d.ts`

Setting the compiler target to be `es6` causes the `lib.d.ts` to include *additional* ambient declarations for more modern (es6) stuff like `Promise`. This magical effect of the compiler target changing the *ambience* of the code is desirable for some people and for others it's problematic as it conflates *code generation* with *code ambience*.

However if you want finer grained control of your environment you should use the `--lib` option which we discuss next.

### lib option

Sometimes (many times) you want to decouple the relationship between the compile target (the generated JavaScript version) and the ambient library support. A common example is `Promise`, e.g. today (in June 2016) you most likely want to `--target es5` but still use latest stuff like `Promise`. To support this you can take explicit control of `lib` using the `lib` compiler option.

> Note: using `--lib` decouples any lib magic from `--target` giving you better control.

You can provide this option on the command line or in `tsconfig.json` (recommended):

**Command line**:
```
tsc --target es5 --lib dom,es6
```
**tsconfig.json**:
```json
"compilerOptions": {
    "lib": ["dom", "es6"]
}
```

The libs can be categorized into categories:

* JavaScript Bulk Feature:
    * es5
    * es6
    * es2015
    * es7
    * es2016
    * es2017
    * esnext
* Runtime Environment
    * dom
    * dom.iterable
    * webworker
    * scripthost
* ESNext By-feature options (even smaller than bulk feature)
    * es2015.core
    * es2015.collection
    * es2015.generator
    * es2015.iterable
    * es2015.promise
    * es2015.proxy
    * es2015.reflect
    * es2015.symbol
    * es2015.symbol.wellknown
    * es2016.array.include
    * es2017.object
    * es2017.sharedmemory
    * esnext.asynciterable


> NOTE: the `--lib` option provides extremely fine tuned control. So you most likey want to pick an item from the bulk + enviroment categories.
> If --lib is not specified a default library is injected:
  - For --target es5 => es5, dom, scripthost
  - For --target es6 => es6, dom, dom.iterable, scripthost

My Personal Recommentation:

```json
"compilerOptions": {
    "target": "es5",
    "lib": ["es6", "dom"]
}
```

Example Including Symbol with ES5
Symbol API is not included when target is es5. In fact, we receive an error like: [ts] Cannot find name 'Symbol'.
We can use "target": "es5" in combination with "lib" to provide Symbol API in TypeScript:
```json
"compilerOptions": {
    "target": "es5",
    "lib": ["es5", "dom", "scripthost", "es2015.symbol"]
}
```
