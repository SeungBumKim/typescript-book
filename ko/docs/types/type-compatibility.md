## Type 호환성

Type 호환성(여기서 다룰)은 어떤 값이 다른 곳에 할당 가능한지 결정하는 것입니다. 예를들면, `string`과 `number`는 호환되지 않습니다:

```ts
let str: string = "Hello";
let num: number = 123;

str = num; // ERROR: `number` is not assignable to `string`
num = str; // ERROR: `string` is not assignable to `number`
```

## 견실성

TypeScript의 type 시스템은 편리하고 *오류가능성 있는* 동작을 허용하도록 설계되었습니다. 예를들면 무엇이든`any`에 할당 할 수 있습니다. 근본적으로 컴파일러에게 여러분이 원하는 것을 무엇이든 하도록 허용한다는 것을 의미합니다:

```ts
let foo: any = 123;
foo = "Hello";

// Later
foo.toPrecision(3); // Allowed as you typed it as `any`
```

## 구조적

TypeScript의 오브젝트는 구조적으로 구성 가능합니다. 즉, 구조가 일치하는 한 *이름*은 중요하지 않습니다.

```ts
interface Point {
    x: number,
    y: number
}

class Point2D {
    constructor(public x:number, public y:number){}
}

let p: Point;
// OK, because of structural typing
p = new Point2D(1,2);
```

이를 통해 추측할 수 있을 때마다 안전성을 유지하면서 즉시(vanilla JS같이) 오브젝트를 생성 할 수 있습니다.

또한 *추가적인* 데이터도 잘 처리됩니다:

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

## 변환성

변환성 type 호환성 분석을 위해 이해하기 쉽고 중요한 개념입니다.

간단한 type `Base`와 `Child`가 있고, `Child`가 `Base`의 자식이면, `Child`의 인스턴스는 `Base` type의 변수에 할당 가능합니다.

> 이 내용은 다형성 101입니다.

유사한 시나리오에서 `Base`와`Child`가 어디에 위치하는지에 따라 'Base'와 'Child'로 구성된 복합 type들의 type 호환성은 *변환성*에 의해 좌우됩니다.

* 같이 변화 : (함께) *같은 방향*으로만
* 반대로 변화: (반대쪽) *반대 방향*으로만
* 둘다 변화: (둘) both co and contra.(이해가 안가서요..)
* 불변 : type이 정확하지 않으면 호환되지 않습니다.
 
> 노트: JavaScript와 같이 변경 가능한 데이터가 있는 시스템의 경우 '불변'만 유효한 옵션입니다. 그러나 언급한 바와 같이 *편의성*은 우리로 하여금 오류가능성있는 선택을하도록 강요합니다.

## 함수

두개의 함수를을 비교할 때 고려해야 할 몇 가지 미묘한 사항이 있습니다.

### 반환 Type
`같변화`: 반환 type은 최소한 충분한 데이터를 포함해야합니다.

```ts
/** Type Heirarchy */
interface Point2D { x: number; y: number; }
interface Point3D { x: number; y: number; z: number; }

/** Two sample functions */
let iMakePoint2D = (): Point2D => ({ x: 0, y: 0 });
let iMakePoint3D = (): Point3D => ({ x: 0, y: 0, z: 0 });

/** Assignment */
iMakePoint2D = iMakePoint3D; // Okay
iMakePoint3D = iMakePoint2D; // ERROR: Point2D is not assignable to Point3D
```

### 인자의 갯수
적은 인자의 갯수는 괜찮습니다.(예를들면, 함수는 추가되는 인자를 무시하도록 할 수 있습니다.). 결국 적어도 충분한 인자와 함께 호출을 보장 받을 수 있습니다.

```ts
let iTakeSomethingAndPassItAnErr
    = (x: (err: Error, data: any) => void) => { /* do something */ };

iTakeSomethingAndPassItAnErr(() => null) // Okay
iTakeSomethingAndPassItAnErr((err) => null) // Okay
iTakeSomethingAndPassItAnErr((err, data) => null) // Okay

// ERROR: function may be called with `more` not being passed in
iTakeSomethingAndPassItAnErr((err, data, more) => null); // ERROR
```

### 선택적이고 남는 매개변수

선택적(미리 결정되는 갯수)이고 남는 매개변수(인자의 수)는 편의성을 위해서 호환가능합니다.

```ts
let foo = (x:number, y: number) => { /* do something */ }
let bar = (x?:number, y?: number) => { /* do something */ }
let bas = (...args: number[]) => { /* do something */ }

foo = bar = bas;
bas = bar = foo;
```

> 노트: 선택적이고(예제에서 `bar`) 선택적이지 않은(예제에서 `foo`)는 strictNullChecks가 false인 경우에만 호환됩니다. 

### 인자의 Types
`둘다변화`: 이것은 일반적인 이벤트 처리 시나리오를 지원하도록 설계되었습니다.

```ts
/** Event Hierarchy */
interface Event { timestamp: number; }
interface MouseEvent extends Event { x: number; y: number }
interface KeyEvent extends Event { keyCode: number }

/** Sample event listener */
enum EventType { Mouse, Keyboard }
function addEventListener(eventType: EventType, handler: (n: Event) => void) {
    /* ... */
}

// Unsound, but useful and common. Works as function argument comparison is bivariant
addEventListener(EventType.Mouse, (e: MouseEvent) => console.log(e.x + "," + e.y));

// Undesirable alternatives in presence of soundness
addEventListener(EventType.Mouse, (e: Event) => console.log((<MouseEvent>e).x + "," + (<MouseEvent>e).y));
addEventListener(EventType.Mouse, <(e: Event) => void>((e: MouseEvent) => console.log(e.x + "," + e.y)));

// Still disallowed (clear error). Type safety enforced for wholly incompatible types
addEventListener(EventType.Mouse, (e: number) => console.log(e));
```

함수가 호환되기 때문에 `Array <Base>`(같이 변화)에 `Array <Child>`를 할당 할 수 있게한다. 배열이 같이 변화하는 것은 모든 `Array<Child>`은 `Array<Base>`로 할당 가능하다는 것입니다. 예를들면, 함수 인자가 둘다변화가 가능 해졌기때문에 `push(t:Child)`는 `push(t:Base)`에 할당 가능합니다. 

**이러한 내용은 다른 언어에서 온 사람들에게 혼란 스러울 수 있습니다.** 다음과 같은 오류를 기대하지만 TypeScrip에서는 그렇지 않습니다:

```ts
/** Type Heirarchy */
interface Point2D { x: number; y: number; }
interface Point3D { x: number; y: number; z: number; }

/** Two sample functions */
let iTakePoint2D = (point: Point2D) => { /* do something */ }
let iTakePoint3D = (point: Point3D) => { /* do something */ }

iTakePoint3D = iTakePoint2D; // Okay : Reasonable
iTakePoint2D = iTakePoint3D; // Okay : WHAT
```

## 열거형

* 열거형은 numbers로 호환가능하고, numbers은 열거형으로 호환가능합니다.

```ts
enum Status { Ready, Waiting };

let status = Status.Ready;
let num = 0;

status = num; // OKAY
num = status; // OKAY
```

* 다른 열거형 type에서 온 열거형 값은 호환되지 않습니다. 이것은 열거형을 쓸모없게 만들었습니다.(구조형과는 반대로)

```ts
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };

let status = Status.Ready;
let color = Color.Red;

status = color; // ERROR
```

## 클래스

* 오직 인스턴스 멤버와 함수만 비교됩니다.*생성자*와 *statics*는 아무 것도하지 않습니다.

```ts
class Animal {
    feet: number;
    constructor(name: string, numFeet: number) { /** do something */ }
}

class Size {
    feet: number;
    constructor(meters: number) { /** do something */ }
}

let a: Animal;
let s: Size;

a = s;  // OK
s = a;  // OK
```

* `private`와 `protected` 멤버는 *반드시 같은 클래스로부터 나오는 것이어야합니다*. 그러한 멤버는 본질적으로 클래스를 *유명무실하게* 만듭니다.

```ts
/** A class hierarchy */
class Animal { protected feet: number; }
class Cat extends Animal { }

let animal: Animal;
let cat: Cat;

animal = cat; // OKAY
cat = animal; // OKAY

/** Looks just like Animal */
class Size { protected feet: number; }

let size: Size;

animal = size; // ERROR
size = animal; // ERROR
```

## Generics

TypeScript는 구조적인 type 시스템이므로 type 매개 변수는 멤버가 사용할 때만 호환성에 영향을 줍니다. 예를들면, 다음의 `T`는 호환성에 영향을 주지 않습니다:

```ts
interface Empty<T> {
}
let x: Empty<number>;
let y: Empty<string>;

x = y;  // okay, y matches structure of x
```

그러나 `T`가 사용된다면, 아래 그림과 같이 *인스턴스화*를 기반으로한 호환성에서 역할을 수행합니다:

```ts
interface NotEmpty<T> {
    data: T;
}
let x: NotEmpty<number>;
let y: NotEmpty<string>;

x = y;  // error, x and y are not compatible
```

generic 인자가 인스턴스화되지 않은 경우 호환성을 검사하기 전에`any`로 대체됩니다:

```ts
let identity = function<T>(x: T): T {
    // ...
}

let reverse = function<U>(y: U): U {
    // ...
}

identity = reverse;  // Okay because (x: any)=>any matches (y: any)=>any
```

## 각주: 불변성

불변성이 유일한 정상적인 옵션이라고 말했다. 다음은 반대와 함께하는 변화성이 모두 배열에 안전하지 않은 것으로 보이는 예제입니다.

```ts
/** Heirarchy */
class Animal { constructor(public name: string){} }
class Cat extends Animal { meow() { } }

/** An item of each */
var animal = new Animal("animal");
var cat = new Cat("cat");

/**
 * Demo : polymorphism 101
 * Animal <= Cat
 */
animal = cat; // Okay
cat = animal; // ERROR: cat extends animal

/** Array of each to demonstrate variance */
let animalArr: Animal[] = [animal];
let catArr: Cat[] = [cat];

/**
 * Obviously Bad : Contravariance
 * Animal <= Cat
 * Animal[] >= Cat[]
 */
catArr = animalArr; // Okay if contravariant
catArr[0].meow(); // Allowed but BANG 🔫 at runtime


/**
 * Also Bad : covariance
 * Animal <= Cat
 * Animal[] <= Cat[]
 */
animalArr = catArr; // Okay if covariant
animalArr.push(new Animal('another animal')); // Just pushed an animal into catArr!
catArr.forEach(c => c.meow()); // Allowed but BANG 🔫 at runtime
```
