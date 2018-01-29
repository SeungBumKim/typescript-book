# TypeScript 스타일 가이드 및 코딩 컨벤션

> 비공식 TypeScript 스타일 가이드

사람들은 내 의견을 물었습니다. 개인적으로 나는 팀과 프로젝트에 많은 것을 시행하지 않지만 강한 일관성을 가질 필요가 있을 때 타이 브레이커로 언급하는 것은 도움이 될 것 입니다. 내가 더 강하게 느끼는 다른 것들이 있으며 그것들은 팁 채터에 작성했습니다. [tips](../tips/main.md)(예, type 주장은 좋지 않으며, 프로퍼티 세터도 좋지 않습니다.)🌹.

## 변수와 함수
* 변수와 함수 이름에 `camelCase`를 사용합니다.

> 이유: 기존의 JavaScript 때문에

**Bad**
```ts
var FooVar;
function BarFunc() { }
```
**Good**
```ts
var fooVar;
function barFunc() { }
```

## 클래스
* 클래스이름을 위해서는 `PascalCase`를 사용합니다.

> 이유: 실제로 이것은 표준 JavaScript에서 상당히 일반적입니다.

**Bad**
```ts
class foo { }
```
**Good**
```ts
class Foo { }
```
클래스 멤버와 메소드에는 `camelCase`을 사용합니다.

> 이유: 자연스럽게 변수와 함수 네이밍을 컨벤션을 따라왔습니다.

**Bad**
```ts
class Foo {
    Bar: number;
    Baz() { }
}
```
**Good**
```ts
class Foo {
    bar: number;
    baz() { }
}
```
## 인터페이스

* 이름을 위해서 `PascalCase`을 사용합니다.

> 이유: 클래스와 유사합니다.

* 멤버를 위해서는 `camelCase`를 사용합니다.

> 이유: 클래스와 유사합니다.

* 앞에 `I`를 **붙이지 않습니다.**

> 이유: 통상적이지 않습니다. `lib.d.ts`의 중요한 인터페이스 정의는 `I`가 없습니다.(예, Window, Document등).

**Bad**
```ts
interface IFoo {
}
```
**Good**
```ts
interface Foo {
}
```

## Type

* 이름을 위해서 `PascalCase`을 사용합니다.

> 이유: 클래스와 유사합니다.

* 멤버에는 `camelCase`을 사용합니다.

> 이유: 클래스와 유사합니다.

## 네임스페이스

* 이름에는 `PascalCase`을 사용합니다.

> 이유: TypeScript팀을 따라서 적용합니다. 네임스페이스는 실제로 정적멤버가 있는 클래스입니다. 클래스 이름은 `PascalCase` 이고 => 그래서 네임스페이스 이름은 `PascalCase`입니다. 

**Bad**
```ts
namespace foo {
}
```
**Good**
```ts
namespace Foo {
}
```

## Enum

* 열거형의 이름은 `PascalCase`을 사용합니다.

> 이유: 클래스와 유사합니다. Type입니다.

**Bad**
```ts
enum color {
}
```
**Good**
```ts
enum Color {
}
```

* 열거형 멤버에는 `PascalCase`을 사용합니다.

> 이유: TypeScript을 따라갑니다. 예를들어, 언어 생성자 `SyntaxKind.StringLiteral`와 같은경우. 또한 TypeScript에서 다른 언어의 번역(코드 생성)에  도움이 됩니다.

**Bad**
```ts
enum Color {
    red
}
```
**Good**
```ts
enum Color {
    Red
}
```

## Null 대 Undefined

* Prefer not to use either for explicit unavailability

> Reason: these values are commonly used to keep a consistent structure between values. In TypeScript you use *types* to denote the structure

**Bad**
```ts
let foo = {x:123,y:undefined};
```
**Good**
```ts
let foo:{x:number,y?:number} = {x:123};
```

* Use `undefined` in general (do consider returning an object like `{valid:boolean,value?:Foo}` instead)

***Bad***
```ts
return null;
```
***Good***
```ts
return undefined;
```

* Use `null` where its a part of the API or conventional

> Reason: It is conventional in Node.js e.g. `error` is `null` for NodeBack style callbacks.

**Bad**
```ts
cb(undefined)
```
**Good**
```ts
cb(null)
```

* Use *truthy* check for **objects** being `null` or `undefined`

**Bad**
```ts
if (error === null)
```
**Good**
```ts
if (error)
```

* Use `== undefined` / `!= undefined` (not `===` / `!==`) to check for `null` / `undefined` on primitives as it works for both `null`/`undefined` but not other falsy values (like `''`,`0`,`false`) e.g.

**Bad**
```ts
if (error !== null)
```
**Good**
```ts
if (error != undefined)
```

PS: [More about `null`](../tips/null.md)

## Formatting
The TypeScript compiler ships with a very nice formatting language service. Whatever output it gives by default is good enough to reduce the cognitive overload on the team.

Use [`tsfmt`](https://github.com/vvakame/typescript-formatter) to automatically format your code on the command line. Also your IDE (atom/vscode/vs/sublime) already has formatting support built-in.

Examples: 
```ts
// Space before type i.e. foo:<space>string
const foo: string = "hello";
```

## Quotes

* Prefer single quotes (`'`) unless escaping.

> Reason: More JavaScript teams do this (e.g. [airbnb](https://github.com/airbnb/javascript), [standard](https://github.com/feross/standard), [npm](https://github.com/npm/npm), [node](https://github.com/nodejs/node), [google/angular](https://github.com/angular/angular/), [facebook/react](https://github.com/facebook/react)). Its easier to type (no shift needed on most keyboards). [Prettier team recommends single quotes as well](https://github.com/prettier/prettier/issues/1105)

> Double quotes are not without merit: Allows easier copy paste of objects into JSON. Allows people to use other languages to work without changing their quote character. Allows you to use apostrophes e.g. `He's not going.`. But I'd rather not deviate from where the JS Community is fairly decided.

* When you can't use double quotes, try using back ticks (\`).

> Reason: These generally represent the intent of complex enough strings.

## Spaces

* Use `2` spaces. Not tabs.

> Reason: More JavaScript teams do this (e.g. [airbnb](https://github.com/airbnb/javascript), [idiomatic](https://github.com/rwaldron/idiomatic.js), [standard](https://github.com/feross/standard), [npm](https://github.com/npm/npm), [node](https://github.com/nodejs/node), [google/angular](https://github.com/angular/angular/), [facebook/react](https://github.com/facebook/react)). The TypeScript/VSCode teams use 4 spaces but are definitely the exception in the ecosystem.

## Semicolons

* Use semicolons.

> Reasons: Explicit semicolons helps language formatting tools give consistent results. Missing ASI (automatic semicolon insertion) can trip new devs e.g. `foo() \n (function(){})` will be a single statement (not two).

## Array

* Annotate arrays as `foos:Foo[]` instead of `foos:Array<Foo>`.

> Reasons: Its easier to read. Its used by the TypeScript team. Makes easier to know something is an array as the mind is trained to detect `[]`.

## Filename
Name files with `camelCase`. E.g. `accordian.tsx`, `myControl.tsx`, `utils.ts`, `map.ts` etc.

> Reason: Conventional across many JS teams.
