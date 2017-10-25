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

### 당신의 JavaScript는 TypeScript다.
TypeScript는 JavaScript 코드에 대해 컴파일 타임 type 안전성을 제공합니다. 이름을 주어지 것은 놀랍지도 않습니다. 가장 좋은 점은 type은 완전히 선택 사항이라는 것입니다. JavaScript 코드 `.js` 파일은 `.ts` 파일로 이름을 바꿀 수 있고, TypeScript는 원래 JavaScript 파일과 동일하게 유효한 `.js`를 제공합니다. TypeScript는 *의도적이고* 엄격하게 선택적 type 검사가 적용된 JavaScript 슈퍼셋입니다.

### Types은 암시적일 수 있다.
TypeScript는 코드 개발 중 최소의 생산성 비용으로 type 안전성을 제공하기 위해 최대한 많은 type 정보를 유추하려고 합니다. 예를들면, 다음 예제에서 TypeScript는 foo가 'number'유형임을 알 것이며 아래 보여지는 것 같이 두 번째 줄에 오류가 발생 할 것 입니다:

```ts
var foo = 123;
foo = '456'; // Error: cannot assign `string` to `number`

// Is foo a number or a string?
```
이러한 type의 추론은 관계가 명확히 잘 되어 있습니다. 이 예제에서 보여준 것과 같이 한다면, 나머지 코드에서는`foo`가 `number` 인지 `string` 인지 확신 할 수 없습니다. 이러한 문제는 대규모 다중 파일 코드 기반에서 자주 발생합니다. 나중에 type 유추 규칙에 대해 자세히 살펴볼 것입니다.

### Types은 명시적일 수 있다.
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

### Types are structural
In some languages (specifically nominally typed ones) static typing results in unnecessary ceremony because even though *you know* that the code will work fine the language semantics force you to copy stuff around. This is why stuff like [automapper for C#](http://automapper.org/) is *vital* for C#. In TypeScript because we really want it to be easy for JavaScript developers with a minimum cognitive overload, types are *structural*. This means that *duck typing* is a first class language construct. Consider the following example. The function `iTakePoint2D` will accept anything that contains all the things (`x` and `y`) it expects:

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

### Type errors do not prevent JavaScript emit
To make it easy for you to migrate your JavaScript code to TypeScript, even if there are compilation errors, by default TypeScript *will emit valid JavaScript* the best that it can. e.g.

```ts
var foo = 123;
foo = '456'; // Error: cannot assign a `string` to a `number`
```

will emit the following js:

```ts
var foo = 123;
foo = '456';
```

So you can incrementally upgrade your JavaScript code to TypeScript. This is very different from how many other language compilers work and yet another reason to move to TypeScript.

### Types can be ambient
A major design goal of TypeScript was to make it possible for you to safely and easily use existing JavaScript libraries in TypeScript. TypeScript does this by means of *declaration*. TypeScript provides you with a sliding scale of how much or how little effort you want to put in your declarations, the more effort you put the more type safety + code intelligence you get. Note that definitions for most of the popular JavaScript libraries have already been written for you by the [DefinitelyTyped community](https://github.com/borisyankov/DefinitelyTyped) so for most purposes either:

1. The definition file already exists.
1. Or at the very least, you have a vast list of well reviewed TypeScript declaration templates already available

As a quick example of how you would author your own declaration file, consider a trivial example of [jquery](https://jquery.com/). By default (as is to be expected of good JS code) TypeScript expects you to declare (i.e. use `var` somewhere) before you use a variable
```ts
$('.awesome').show(); // Error: cannot find name `$`
```
As a quick fix *you can tell TypeScript* that there is indeed something called `$`:
```ts
declare var $:any;
$('.awesome').show(); // Okay!
```
If you want you can build on this basic definition and provide more information to help protect you from errors:
```ts
declare var $:{
    (selector:string): any;
};
$('.awesome').show(); // Okay!
$(123).show(); // Error: selector needs to be a string
```

We will discuss the details of creating TypeScript definitions for existing JavaScript in detail later once you know more about TypeScript (e.g. stuff like `interface` and the `any`).

## Future JavaScript => Now
TypeScript provides a number of features that are planned in ES6 for current JavaScript engines (that only support ES5 etc). The typescript team is actively adding these features and this list is only going to get bigger over time and we will cover this in its own section. But just as a specimen here is an example of a class:

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

and the lovely fat arrow function:

```ts
var inc = (x)=>x+1;
```

### Summary
In this section we have provided you with the motivation and design goals of TypeScript. With this out of the way we can dig into the nitty gritty details of TypeScript.

[](Interfaces are open ended)
[](Type Inferernce rules)
[](Cover all the annotations)
[](Cover all ambients : also that there are no runtime enforcement)
[](.ts vs. .d.ts)
