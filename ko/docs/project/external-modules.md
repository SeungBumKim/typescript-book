## 외부 모듈
TypeScript 외부 모듈 패턴에는 많은 기능과 유용성이 있습니다. 여기서는 실제로 사용에 반영할 필요한 기능과 패턴에 대해서 설명하겠습니다.

### 파일 찾기
다음 내용을 보시면:

```ts
import foo = require('foo');
```

TypeScript 컴파일러에서는 다음과 같은 양식으로 외부 모듈 선언을 찾도록 지시합니다:

```ts
declare module "foo" {
    // Some variable declarations

    export var bar: number; /*sample*/
}
```
중요한 것은 경로와 관련된 것 입니다. 예를들면:

```ts
import foo = require('./foo');
```
TypeScript 컴파일러에서는 `./foo.ts`또는 `./foo.d.ts`와 같은 TypeScript 파일을 현재 파일의 경로를 기준으로 찾습니다.

이방식이 완전하지는 않지만 가지고 있고 사용하기에 알맞은 모델입니다. 나중에 자세한 내용을 다룰 것입니다.

## 컴파일러 모듈 옵션
다음 내용을 보시면:

```ts
import foo = require('foo');
```

컴파일러 *모듈*옵션(`--module commonjs` 또는 `--module amd` 또는 `--module umd` 또는 `--module system`)에 따라 *다른* JavaScript를 생성합니다.

개인적으로 추천하자면: `--module commonjs`을 사용하면 Node.js에서도 동작하고 `webpack`같은 프론트엔드에서도 사용할 수 있습니다.

### 타입만 가져오기
다음 내용을 보시면:

```ts
import foo = require('foo');
```

실제로는 *두가지* 역할을 합니다:

* foo 모듈의 타입 정보를 가져옵니다.
* foo 모듈에 대한 런타임 종속성을 지정합니다.

타입 정보만 로드되고 런타임 종속성이 갖지 않도록 선택할 수 있습니다. 계속하기전에 필요하시면 이 책의 [*declaration spaces*](../project/declarationspaces.md)섹션을 다시 보시기를 바랍니다.

변수 선언공간에 이름을 추가하지 않으면 추가된 내용은 JavaScript를 생성할 때 삭제될 것입니다. 이러한 내용은 아래 예로 잘 설명됩니다. 일단 이해가 되면 아래 사용 예를 제시하겠습니다.

#### Example 1
```ts
import foo = require('foo');
```
JavaScript를 생성하면:

```js

```
맞습니다. foo에 관해서 *비어있고* 사용하지 못할 것입니다.

#### Example 2
```ts
import foo = require('foo');
var bar: foo;
```
JavaScript를 생성하면:
```js
var bar;
```
`foo`(또는 `foo`의 프로퍼티들 예를들면, `foo.bas`)는 변수로서 사용하지 못할 것입니다.

#### Example 3
```ts
import foo = require('foo');
var bar = foo;
```
JavaScript를 생성하면(commonjs로 했다고 가정하면):
```js
var foo = require('foo');
var bar = foo;
```
`foo`는 변수로 사용할 수 있습니다.


### 사용 예: 늦은 로딩
타입 유추는 *선행*되어야 합니다. 이 말의 의미는 `foo`파일의 어떤 타입을 `bar` 파일에서 사용하려면 다음과 같이해야 한다는 것입니다:
```ts
import foo = require('foo');
var bar: foo.SomeType;
```
그러나 확실한 상황에서는 `foo`파일에 동작할 때만 로딩하기를 원할 수도 있습니다. 이런 상황에서는 이름을 `import`하기보다는 *타입 키워드*로 사용하고 *변수*로는 **사용하지 않습니다**. 이렇게하면 TypeScript에 의해 삽입된 모든 *선행* 런타임 종속성 코드가 제거됩니다.그런 다음 모듈 로더와 관련된 코드를 사용하여 실제 *모듈을 수동*으로 가져옵니다.

예를 들면, 특정 함수 호출에서 `'foo'`모듈만 로드하는 다음 `commonjs`기반의 코드를 고려하십시오:

```ts
import foo = require('foo');

export function loadFoo() {
    // This is lazy loading `foo` and using the original module *only* as a type annotation
    var _foo: typeof foo = require('foo');
    // Now use `_foo` as a variable instead of `foo`.
}
```

`amd`(requirejs을 사용해서)의 유사한 샘플로는 다음과 같습니다:
```ts
import foo = require('foo');

export function loadFoo() {
    // This is lazy loading `foo` and using the original module *only* as a type annotation
    require(['foo'], (_foo: typeof foo) => {
        // Now use `_foo` as a variable instead of `foo`.
    });
}
```

이러한 패턴은 보통 다음과 같이 사용됩니다:
* 특정 경로에서 특정 JavaScript를 로드하는 웹 앱.
* 어플리케이션의 시작을 빠르게하기 위해서 특정 모듈만 로드하는 node 어플리케이션.

### 사용 예: 순환 종속성 끊기

늦은 로딩과 유사한 사용으로 특정 모듈 로더(commonjs/node와 amd/requirejs)로는 순환 종속성을 잘 동작시키지 못합니다. 이러한 경우에는 한방향은 *늦은 로딩* 코드를 적용하고 다른쪽 방향으로는 모듈을 선행으로 로드하는 것이 유용합니다.

### 사용 예: 가져오기(Import) 보장

때로는 사이드 이펙트 처리를 위해서 파일을 로드하려 할 겁니다.(예를들면, 모듈은 다음과 같은 라이브러리와 함께 자체 등록 할 수 있습니다. [CodeMirror addons](https://codemirror.net/doc/manual.html#addons) 등) 그러나 `import/require`만 하면 변환된 JavaScript에는 모듈에 대한 의존성이 없어지며 모듈 로더(예:webpack)가 가져오기를 하면 완전히 무시될 것입니다. 이러한 경우에 `ensureImport`변수를 이용하면 컴파일된 JavaScript가 모듈의 종속성을 갖는 것을 보장해줍니다. 예를들면:

```ts
import foo = require('./foo');
import bar = require('./bar');
import bas = require('./bas');
const ensureImport: any =
    foo
    || bar
    || bas;
```

`var/require`대신 `import/require`을 사용하는 주요 이점은 파일패스를 goto definition navigation등을 통해서 완벽하게 얻을 수 있다는 것입니다.

[](// TODO: es6 modules)
