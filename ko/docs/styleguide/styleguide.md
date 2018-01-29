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

* 명시적으로 사용할 수 없는 용도로 사용하지 않아야합니다.

> 이유: 이 값은 일반적으로 값 사이의 일관된 구조를 유지하는 데 사용됩니다. TypeScript에서는 *type*을 사용하여 구조체를 나타냅니다.

**Bad**
```ts
let foo = {x:123,y:undefined};
```
**Good**
```ts
let foo:{x:number,y?:number} = {x:123};
```

* 보통 `undefined`을 사용합니다.(`{valid : boolean, value? : Foo}`와 같은 객체를 반환하는 것을 고려하지 마세요.)

***Bad***
```ts
return null;
```
***Good***
```ts
return undefined;
```

* `null`는 API 또는 기존 API의 일부에 사용합니다.

> 이유: Node.js에서 통상적으로 사용합니다. 예, NodeBack스타일의 콜백은 `error`는 `null`입니다.

**Bad**
```ts
cb(undefined)
```
**Good**
```ts
cb(null)
```

* **오브젝트의**의 *truthy*체크는 `null`또는 `undefined`입니다.

**Bad**
```ts
if (error === null)
```
**Good**
```ts
if (error)
```

* `== undefined` / `!= undefined`사용(`===` / `!==`아닙니다.)은 원시형은 `null` /`undefined` 둘 다 작동하지만 다른 falsy값(`''`,`0`,`false`와 같은)에 작동하지 않는 `null` / `undefined`의 체크를 위한 것 입니다. 예:

**Bad**
```ts
if (error !== null)
```
**Good**
```ts
if (error != undefined)
```

PS: 추가내용은 다음을 보세요.[`null`](../tips/null.md)

## Formatting
TypeScript 컴파일러는 매우 훌륭한 type 지정 언어 서비스와 함께 제공됩니다. 기본적으로 제공되는 출력은 팀의 인지적 과부하를 줄이는데 충분할만큼 모두 괜찮습니다.

`tsfmt`을 사용하면 명령 행에서 코드를 자동으로 형식화합니다.[`tsfmt`](https://github.com/vvakame/typescript-formatter). 또한 IDE (atom/vscode/vs/sublime)에는 이미 포맷 지원 기능이 내장되어 있습니다.

예: 
```ts
// Space before type i.e. foo:<space>string
const foo: string = "hello";
```

## Quotes

* 양식에 벗어나지 않는한 따옴표(`'`)를 사용합니다.

> 이유: 많은 JavaScript팀에서 사용합니다.(예, [airbnb](https://github.com/airbnb/javascript), [standard](https://github.com/feross/standard), [npm](https://github.com/npm/npm), [node](https://github.com/nodejs/node), [google/angular](https://github.com/angular/angular/), [facebook/react](https://github.com/facebook/react)). 입력하기가 더 쉽니다.(키보드의 시프트 키를 안눌러도 됩니다.)[Prettier team recommends single quotes as well](https://github.com/prettier/prettier/issues/1105)

> 쌍따옴표는 다음의 이점이 있습니다: JSON으로 개체 붙여 넣기를 쉽게 복사 할 수 있습니다. 다른 언어를 사용하여 인용 부호를 변경하지 않고도 작업 할 수 있습니다. 어퍼스트로피를 사용할 수 있습니다.(예, `He's not going.`). 그러나 나는 JS 공동체가 공정하게 결정하는 것에서 벗어나지 않을 것입니다.

* 쌍 따옴표를 사용할 수 없는 경우 역 따옴표(\`)를 사용해보십시오.

> 이유: 이들은 일반적으로 복잡한 복잡한 문자열을 나타냅니다.

## 공백

* `2`개 공백을 사용하세요. 탭이 아닙니다. 

> 이유: 많은 JavaScript팀에서 사용합니다.(예. [airbnb](https://github.com/airbnb/javascript), [idiomatic](https://github.com/rwaldron/idiomatic.js), [standard](https://github.com/feross/standard), [npm](https://github.com/npm/npm), [node](https://github.com/nodejs/node), [google/angular](https://github.com/angular/angular/), [facebook/react](https://github.com/facebook/react)). TypeScript/VSCode 팀은 4개의 공백을 사용하지만 예외입니다.

## 세미콜론

* 세미콜론을 사용하세요.

> 이유: 명시적 세미콜론은 언어 서식 도구가 일관된 결과를 제공하는 데 도움이됩니다. 누락된 ASI(자동 세미콜론 삽입)는 예를 들어 새로운 devs를 트립 할 수 있습니다. `foo () \ n (function () {})`은(하나가 아닌) 하나의 문장이 될 것입니다.

## 배열

* 어노테이션 배열은 `foos:Array<Foo>`대신에 `foos:Foo[]`을 사용하세요.

> 이유: 읽기가 쉽습니다. TypeScript팀에서 사용됩니다. `[]`을 알 수 있도록 훈련되어있을 때 무엇이 배열인지 쉽게 알 수 있습니다.

## 파일이름
파일이름은 `camelCase`을 사용합니다. 예 `accordian.tsx`, `myControl.tsx`, `utils.ts`, `map.ts` 등.

> 이유: JS팀에서 많이 사용합니다. 
