# 왜 TypeScript 인가?
TypeScript에는 두 가지 주요 목적이 있습니다:
* JavaScript를 위한 *선택적 type system*을 제공합니다.
* 향후 자바 스크립트 에디션에서 현재 자바 스크립트 엔진에 이르기까지 관련 기능 제공이 계획되어 있습니다.

이러한 목표에 대한 염원은 다음과 같습니다.
The desire for these goals is motivated below.

## TypeScript의 type system

"**왜 JavaScript에 type을 추가했지?**" 라고 궁금해 하실수도 있습니다..

Type은 코드 품질 및 이해 가능성을 향상시킨다는 입증 되었습니다. 대규모 팀(google, microsoft, facebook)들이 이런 결론에 계속해서 도달했습니다. 특히:

* Type은 리팩토링 속도를 높여줍니다. *컴파일러가 오류를 잡는 것이 런타임에 오류가 발생하는 것보다 더 좋습니다.*
* Type은 최상의 문서 형식 중 하나입니다. *함수 시그니처는 정리(theorem)이고 함수 본문은 증명(proof)입니다.*

그러나 type은 불필요하게 의식적으로 사용해야 합니다. TypeScript는 이러한 장벽을 가능한 한 낮게 유지하는 것을 중요하게 여깁니다. 방법은 다음과 같습니다:

### 당신의 JavaScript는 TypeScript입니다.
TypeScript는 JavaScript 코드에 대해 컴파일 타임 type 안전성을 제공합니다. 이름을 주어지 것은 놀랍지도 않습니다. 가장 좋은 점은 type은 완전히 선택 사항이라는 것입니다. JavaScript 코드 `.js` 파일은 `.ts` 파일로 이름을 바꿀 수 있고, TypeScript는 원래 JavaScript 파일과 동일하게 유효한 `.js`를 제공합니다. TypeScript는 *의도적이고* 엄격하게 선택적 type 검사가 적용된 JavaScript 슈퍼셋입니다.

### Types은 암시적일 수 있습니다.
TypeScript는 코드 개발 중 최소의 생산성 비용으로 type 안전성을 제공하기 위해 최대한 많은 type 정보를 유추하려고 합니다. 예를들면, 다음 예제에서 TypeScript는 foo가 'number'유형임을 알 것이며 아래 보여지는 것 같이 두 번째 줄에 오류가 발생 할 것 입니다:

```ts
var foo = 123;
foo = '456'; // Error: cannot assign `string` to `number`

// Is foo a number or a string?
```
이러한 type의 추론은 관계가 명확히 잘 되어 있습니다. 이 예제에서 보여준 것과 같이 한다면, 나머지 코드에서는`foo`가 `number` 인지 `string` 인지 확신 할 수 없습니다. 이러한 문제는 대규모 다중 파일 코드 기반에서 자주 발생합니다. 나중에 type 유추 규칙에 대해 자세히 살펴볼 것입니다.

### Types은 명시적일 수 있습니다.
이전에 언급했듯이 TypeScript는 최대한 안전하게 유추 할 수 있지만, type을 지정 할 수 있습니다:
1. 컴파일러를 도와주며, 코드를 읽어야하는 다음 개발자를 위한 중요한 문서가 될 것입니다.(미래의 개발자 본인을 위한 것 일 수도 있습니다!)
1. 컴파일러가 바라보는 것을 강요하는 것이 당신이 생각해야 할 것 입니다. 그것은 코드에 대한 당신의 이해를 (컴파일러에 의해 수행되는) 알고리즘의 분석과 일치시키는 것 입니다.

TypeScript는 다른 *선택적* type주석을 사용하는 언어(예, ActionScript and F#)에서 널리 사용되는 접미사 유형의 type주석를 사용합니다.

```ts
var foo: number = 123;
```
그래서 뭔가 잘못하면 컴파일러에서 오류가 발생합니다. 예를들면:

```ts
var foo: number = '123'; // Error: cannot assign a `string` to a `number`
```

TypeScript에서 지원되는 모든 type주석 구문에 대한 자세한 내용은 이후 장에서 다룰 것입니다.

### Types는 구조적입니다.
몇몇 언어(특히 일반적으로 type을 지정하는 언어)는, 정적 입력 결과를 의식할 필요가 없을 것 입니다. 왜냐하면 언어 의미론적으로 당신이 무언가를 복사(동일한 형식)된 것을 사용하도록 강요되기에 당신은 그 코드를 잘 동작 할 것을 알고 있기 때문입니다. 이러한 이유로 [automapper for C#](http://automapper.org/)같은 것은 C#에서는 필수적입니다. TypeScript에서는 JavaScript 개발자가 최소한으로 부하없이 쉽게 사용할 수 있기를 원하기 때문에, type은 *구조적*으로 하였습니다. 즉, *duck typing*으로 일류 언어 구조입니다. 다음 예제를 보시면, `iTakePoint2D` 함수는 기대되는 (`x`와`y`)를 포함하는 것은 어떤 것이든 받아 들일 것입니다:

```ts
interface Point2D {
    x: number;
    y: number;
}
interface Point3D {
    x: number;
    y: number;
    z: number;
}
var point2D: Point2D = { x: 0, y: 10 }
var point3D: Point3D = { x: 0, y: 10, z: 20 }
function iTakePoint2D(point: Point2D) { /* do something */ }

iTakePoint2D(point2D); // exact match okay
iTakePoint2D(point3D); // extra information okay
iTakePoint2D({ x: 0 }); // Error: missing information `y`
```

### Type 오류로 인해 JavaScript가 생성을 막지 않습니다.
JavaScript 코드를 TypeScript로 쉽게 이전 할 수 있도록, 컴파일 오류가 있어도 기본적으로 TypeScript는 *가능한 최선의 JavaScript*를 생성 할 것 입니다. 예를들면,

```ts
var foo = 123;
foo = '456'; // Error: cannot assign a `string` to a `number`
```

아래와 같이 js을 만들어 낼 것 입니다:

```ts
var foo = 123;
foo = '456';
```

따라서 JavaScript 코드를 TypeScript 코드로 점진적으로 개선 해야합니다. 이것은 많은 다른 언어 컴파일러에서 동작하는 지와 TypeScript로 이동해야 하는 또 다른 이유와 매우 다른 일입니다.

### Types 주변요소가 되어야합니다.(뭐라 적어야 할지... ㅠ 원문: Types can be ambient)
TypeScript의 주요 디자인 목표는 TypeScript에서 기존 JavaScript 라이브러리를 안전하고 쉽게 사용할 수 있게 하는 것 입니다. TypeScript는 *선언*을 통해 이를 수행합니다. TypeScript는 선언에 들일 수 있는 노력을 단계별로 제공하는데, 많은 노력을 할수록 더 많은 type 안전성 + 코드 인텔리전스를 얻을 수 있습니다. 대부분의 유명한 JavaScript 라이브러리에 대한 정의는 [DefinitelyTyped community](https://github.com/borisyankov/DefinitelyTyped)에 이미 작성되어 있으므로 대부분의 경우:

1. 정의파일은 이미 있습니다.
1. 또는 최소한 이미 잘 검토 된 TypeScript 선언 템플릿 목록이 이미 있습니다.

자신의 선언 파일을 작성하는 방법에 대한 간단한 예로서 [jquery](https://jquery.com/)의 예제를 고려해보십시오. 기본적으로 (좋은 JS코드가 되기를 바라는 것처럼) TypeScript는 변수를 사용하기 전에 선언(예 :`var`를 사용) 될 것을 기대합니다
```ts
$('.awesome').show(); // Error: cannot find name `$`
```
간단한 수정으로 TypeScript에게 실제로 `$`라는 것이 있음을 알릴 수 있습니다:
```ts
declare var $:any;
$('.awesome').show(); // Okay!
```
원한다면 기본 정의를 기반으로 작성하고 오류로부터 사용자를 보호하는 데 도움이되는 추가 정보를 제공 할 수 있습니다.
```ts
declare var $:{
    (selector:string): any;
};
$('.awesome').show(); // Okay!
$(123).show(); // Error: selector needs to be a string
```

TypeScript에 대한 자세한 내용(예를들면, `interface`나 `any` 같은 것들)을 알고 나면 나중에 기존 JavaScript에 대한 TypeScript 정의를 만드는 방법에 대해 자세히 설명 할 것 입니다.

## 미래의 JavaScript => 지금(의 Typescript)
TypeScript는 JavaScript 엔진(현재는 ES5만 지원합니다, 과거 이야기 같네요..)을 위해 ES6에서 계획된 많은 기능을 제공합니다. TypeScript팀은 이러한 기능을 적극적으로 추가하고 있으며이 목록은 시간이 지남에 따라 커질 것입니다. 우리는 자체적인 섹션에서 이러한 내용을 다룰 것입니다. 아래있는 내용이 다룰 내용의 예입니다.

```ts
class Point {
    constructor(public x: number, public y: number) {
    }
    add(point: Point) {
        return new Point(this.x + point.x, this.y + point.y);
    }
}

var p1 = new Point(0, 10);
var p2 = new Point(10, 20);
var p3 = p1.add(p2); // {x:10,y:30}
```

그리고 매우 사랑스러운 화살표 함수:
and the lovely fat arrow function:

```ts
var inc = (x)=>x+1;
```

### 정리
이 섹션에서는 TypeScript의 동기와 디자인 목표에 대해 적었습니다. 이제부터는 TypeScript의 자세한 내용을 살펴볼 수 있습니다.

[](Interfaces are open ended)
[](Type Inferernce rules)
[](Cover all the annotations)
[](Cover all ambients : also that there are no runtime enforcement)
[](.ts vs. .d.ts)
