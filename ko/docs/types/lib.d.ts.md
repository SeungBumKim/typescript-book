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
변수이름(여기서는`window`)과 type 어노테이션(여기서는`Window` 인터페이스)에 대한 인터페이스가 뒤따르는 단순한 `declare var` 입니다. 이러한 변수는 일반적으로 전역 *인터페이스*를 가르킵니다. 예를들어 여기에 (실제로 꽤 방대한)`Window` 인터페이스의 작은 샘플이 있습니다:

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

[`datejs`](https://github.com/abritinthebay/datejs)프로젝트를 참고하세요. DateJS는 `Date` 글로벌 변수와 `Date` 인스턴스 모두에 멤버를 추가합니다. 따라서이 라이브러리의 TypeScript 정의는 다음과 같습니다. ([BTW the community has already written this for you in this case](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/datejs/index.d.ts)):

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
이렇게하면 Type 안정적인 방식으로 다음과 같은 작업을 수행 할 수 있습니다:

```ts
var today = Date.today();
var todayAfter1second = today.addMilliseconds(1000);
```

#### 예 `string`

문자열에 `lib.d.ts`를 들여다보면 `Date`에 대해 본 것과 비슷한 것들을 발견 할 수 있습니다.(`String` 전역변수, `StringConstructor` 인터페이스, `String` 인터페이스). 
한 가지 주목할 점은 `String` 인터페이스가 문자열 *리터럴*에 영향을 주며 아래의 코드 샘플에도 설명되어 있습니다:

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

비슷한 변수/인터페이스는 `Number`,`Boolean`,`RegExp` 등과 같이 정적 멤버와 인스턴스 멤버 모두를 가진 것들을 위해 존재합니다. 이러한 인터페이스는 이러한 유형의 문자 그대로의 인스턴스에도 영향을 줍니다.

### 예 `string` 리덕스 

`global.d.ts`를 만드는 것이 유지보수 차원에서 추천드립니다. 그러나 원하는 경우 *파일 모듈*에서 *전역 네임 스페이스*로 접근 할 수 있습니다. 이것은 `declare global {/ * global namespace here * /}`를 사용하여 수행됩니다. 예제: 이전 예도 다음과 같이 수행 할 수 있습니다:

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

### 사용자 정의 lib.d.ts
앞에서 언급했듯이 `noLib`의 컴파일 플래그는 TypeScript에서 자동으로 `lib.d.ts`을 포함하는 것을 하지 않게 합니다. 해당 내용이 유용한 이유는 다양합니다. 여기에 대표적인 것들을 소개하겠습니다: 

* 표준 브라우저 기반 런타임 환경과 *크게* 다른 사용자 정의 JavaScript 환경에서 실행하려고 할 떄 입니다.
* 코드에서 사용할 수 있는 전역 변수를 *엄격하게* 제어하려고합니다. 예를 들면, lib.d.ts에 전역변수로 `item`을 정의하고 이러한 내용이 코드로 보여지기를 원할 떄 사용할 수 있습니다. 

기본 `lib.d.ts`을 포함하지 않으면 비슷하게 명명된 파일을 컴파일 컨텍스트에 포함시킬 수 있으며 TypeScript는 type 검사를 위해 이를 가져옵니다.

> 노트: `--noLib` 사용에 주의하세요. noLib을 사용하고 다른 프로젝트와 공유하면 *강제로* noLib가 적용됩니다.(또는 *당신의 라이브러리*가 포함됩니다.). 더 나쁜 것은, *이러한* 코드를 프로젝트에 가져오면 *당신의 라이브러리* 코드로 이식해야 할 수도 있습니다.

### `lib.d.ts`에 대한 컴파일러 타겟 효과

컴파일러 타겟을 `es6`으로 설정하면 `lib.d.ts`는 `Promise`와 같은보다 현대적인 (es6) 것들을 위한 *추가​​* 주변 선언을 포함하게 됩니다. 컴파일러 타겟이 코드의 *주변선언*를 변경하는 이 마법같은 효과는 일부 사람들에게는 바람직하지만 다른 사람들에게는 *코드 생성*을 *코드 주변선언*과 융합시키는 것이 문제가 됩니다.

그러나 환경을 세밀하게 제어하려면 다음에 설명할 `--lib` 옵션을 사용해야합니다.

### lib 옵션

때로(혹은 많이) 컴파일대상(생성된 JavaScript 버전)과 주변 라이브러리 지원 사이의 관계를 분리하려고 합니다. 일반적인 예는 `Promise`입니다. 오늘(2016년6월) 당신은 `--target es5`를 원하지만 `Promise`와 같은 최신 내용을 사용하고 싶을 것입니다. 이를 지원하기 위해 `lib` 컴파일러 옵션을 사용하여 `lib`를 명시적으로 제어 할 수 있습니다.

> 노트: `--lib`를 사용하면 `--target`의 모든 lib를 분리하여 더 잘 제어 할 수 있습니다.

이 옵션은 명령행이나 `tsconfig.json`(권장)을 통해서 적용 할 수 있습니다:

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

libs는 범주로 분류 될 수 있습니다:

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


> 노트: `--lib` 옵션은 매우 미세한 제어를 제공합니다. 따라서 가장 큰 환경 카테고리에서 항목을 선택할 수 있습니다.
> --lib가 지정되지 않으면 기본 라이브러리가 주입됩니다.
  - --target es5 => es5, dom, scripthost
  - --target es6 => es6, dom, dom.iterable, scripthost

개인적으로 추천하자면:

```json
"compilerOptions": {
    "target": "es5",
    "lib": ["es6", "dom"]
}
```

ES5에 심볼 포함 예제
심볼 API는 es5에는 포함되어있지 않습니다. 실제로, 다음과 같은 오류가 발생합니다: [ts] Cannot find name 'Symbol'.
"target": "es5"를 "lib"와 함께 사용하여 TypeScript에서 Symbol API를 제공 할 수 있습니다.
```json
"compilerOptions": {
    "target": "es5",
    "lib": ["es5", "dom", "scripthost", "es2015.symbol"]
}
```
