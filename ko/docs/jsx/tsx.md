# JSX 지원
TypeScript는 JSX 추출 및 코드 분석을 지원합니다. JSX에 익숙하지 않은 분은 JSX에서 발췌 한 내용이 있습니다.[JSX](https://facebook.github.io/jsx/):

> JSX는 고정된 의미 체계없이 ECMAScript에 대한 XML과 유사한 확장된 구문입니다. 엔진이나 브라우저에의해 구현되는 것은 아닙니다. JSX를 ECMAScript 사양 자체에 통합하는 것은 아닙니다. 다양한 사전처리기(transpilers)가 이 토큰을 표준 ECMAScript로 변환하기 위해 사용하기 위한 것입니다.

JSX의 동기는 사용자가 *JavaScript*에서로 HTML과 같은 뷰로 작성할 수 있도록 허용하는 것입니다:
* JavaScript를 검사할 동일한 코드로 뷰 type 검사를 받습니다.
* 뷰가 다음에서 작동할 상황을 인식하도록하십시오.(예를들면, 전통적인 MVC에서 *컨트롤러 - 뷰* 연결을 강화)

이렇게하면 오류 가능성이 줄어들고 사용자 인터페이스의 유지 관리가 향상됩니다. 이 시점에서 JSX의 주요 소비자는 페이스북의 ReactJS입니다([ReactJS](http://facebook.github.io/react/)). JSX의 사용법은 여기에서 설명 할 것입니다.

## Setup

* 파일 확장자는 (`.ts` 대신) `.tsx`을 사용하십시오.
* `tsconfig.json`의 `compilerOptions`에 `"jsx" : "react"`을 작성해주세요.
* 프로젝트에 JSX 및 React에 대한 정의를 설치하십시오. : (`npm i -D @types/react @types/react-dom`)
* `.tsx`파일에 react를 import해주세요. (`import * as React from "react"`).

## HTML Tags 대 컴포넌트
React는 HTML 태그(문자열) 또는 React 컴포넌트(클래스)를 렌더링 할 수 있습니다. 이 요소들에 대한 JavaScript 표현(`React.createElement('div')` 대 `React.createElement(MyComponent)`)은 다릅니다. 이것이 결정되는 방식은 *첫 번째* 문자의 *상황*에 의한 것입니다. `foo`는 HTML 태그로 취급되고`Foo`는 컴포넌트로 취급됩니다.

## Type 체크

### HTML 태그
HTML 태그 `foo`는 `JSX.IntrinsicElements.foo` type입니다. 이 type은 이미 설정의 일부로 설치 한`react-jsx.d.ts` 파일의 모든 주요 태그에 대해 이미 정의되어 있습니다. 다음은 파일 내용의 샘플입니다:


```ts
declare module JSX {
    interface IntrinsicElements {
        a: React.HTMLAttributes;
        abbr: React.HTMLAttributes;
        div: React.HTMLAttributes;
        span: React.HTMLAttributes;

        /// so on ...
    }
}
```

### 컴포넌트
컴포넌트는 컴포넌트의 `props` 속성에 따라 type을 검사합니다. 이것은 JSX가 어떻게 변형되었는지 모델링 한 것입니다. 예를들어 속성은 컴포넌트의 `props`가됩니다.

React 컴포넌트를 만들기 위해서 ES6클래스를 사용할 것을 추천합니다. `react.d.ts` 파일은 `Props`와 `State` 인터페이스를 제공하는 클래스에서 확장해야만하는 `React.Component <Props, State>`클래스를 정의합니다. 자세한 내용은 아래있습니다:

```ts
interface Props {
  foo: string;
}
class MyComponent extends React.Component<Props, {}> {
    render() {
        return <span>{this.props.foo}</span>
    }
}

<MyComponent foo="bar" />
```

### React JSX 팁: 렌더링 가능한 인터페이스

React는`JSX` 나 `string`과 같은것을 렌더링 할 수 있습니다. 모든 type이 `React.ReactNode` type으로 통합되었으므로 렌더러를 받아 들일 때 사용하십시오. 예를들어:

```ts
interface Props {
  header: React.ReactNode;
  body: React.ReactNode;
}
class MyComponent extends React.Component<Props, {}> {
    render() {
        return <div>
            {header}
            {body}
        </div>;
    }
}

<MyComponent foo="bar" />
```

### React JSX 팁: 컴포넌트의 인스턴스 수용.
react type의 정의는 `React.ReactElement <T>`를 제공하여 `<T />` 컴포넌트 인스턴스화의 결과에 어노테이션을 달 수 있습니다. 예를들어:

```js
const foo: React.ReactElement<MyAwesomeComponent> = <MyAwesomeComponent/>; // Okay
const bar: React.ReactElement<MyAwesomeComponent> = <NotMyAwesomeComponent/>; // Error!
```

> 물론 이것을 함수 인자 어노테이션과 React 컴포넌트 prop 멤버로 사용할 수 있습니다.

### React JSX 팁: 제네릭 컴포넌트
JSX에는 제네릭 매개변수를 제네릭 컴포넌트에 적용하는 구문이 없습니다. 먼저 구체적인 type의 제네릭 매개변수를 제거하는 변수에 제네릭 클래스를 저장해야 합니다. 예를들어 `T`를 구체적인 `string` type으로 대체합니다:

```ts
/** A generic component */
type SelectProps<T> = { items: T[] }
class Select<T> extends React.Component<SelectProps<T>, any> { }

/** Specialize Select to use with strings */
const StringSelect = Select as { new (): Select<string> };

/** Usage */
const Form = () => <StringSelect items={['a','b']} />;
```
당신의 생성자가 props을 가지고 있다면 그것도 수용 할 수 있습니다:

```ts
/** Generic component */
interface SelectProps<T> { items: T[] }
class Select<T> extends Component<SelectProps<T>, any> {
    constructor(props: SelectProps<T>) { super(props) }
}
/** Specialization */
const StringSelect = Select as { new (props: SelectProps<string>): GenericList<string> };
```

## React JSX 이외
TypeScript는 type 안전 방식으로 React with JSX 이외의 것을 사용할 수 있는 기능을 제공합니다. 다음에 사용자 지정 가능한 지점을 나열하나 이것은 고급 UI 프레임워크 작성자를 위한 것임에 유의하십시오:

* `"jsx" : "preserve"`옵션을 사용하여 `react`스타일 emit을 비활성화 할 수 있습니다. 이것은 JSX가 *그대로 있는* 상태로 emit된다는 것을 의미합니다. 그런다음 사용자 정의 변환기를 사용하여 JSX 부분을 가로 챌 수 있습니다.
* `JSX` 글로벌 모듈 사용하기:
    * `JSX.IntrinsicElements` 인터페이스 멤버를 커스터마이징하면 사용할 수 있는 HTML 태그와 그 type의 체크 방법을 제어 할 수 있습니다.
    * 컴포넌트를 사용할 때는:
        * 기본 `interface ElementClass extends React.Component <any, any> {}`선언을 커스터마이즈함으로써 어떤 클래스가 컴포넌트에 상속되어야 하는지를 제어 할 수 있습니다.
        * `declare module JSX { interface ElementAttributesProperty { props: {}; } }`를 커스터마이징함으로써 어느 속성이 속성 검사를 위해 사용되는지(기본값은 `props`) 제어 할 수 있습니다.

## `reactNamespace`

`--reactNamespace <JSX factory name>`과`--jsx react`를 함께 사용하면 기본값인 `React`에서 다른 JSX팩토리를 사용할 수 있습니다.

새팩토리 이름은`createElement` 함수를 호출하는 데 사용됩니다.

##### Example

```ts
import {jsxFactory} from "jsxFactory";

var div = <div>Hello JSX!</div>
```

컴파일은:

```shell
tsc --jsx react --reactNamespace jsxFactory --m commonJS
```

:

```js
"use strict";
var jsxFactory_1 = require("jsxFactory");
var div = jsxFactory_1.jsxFactory.createElement("div", null, "Hello JSX!");
```
