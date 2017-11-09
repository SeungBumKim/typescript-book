### Classes
JavaScript에서 class를 가장 중요하게 여기는 이유는:
1. [class는 구조적 추상화를 제공합니다.](./tips/classesAreUseful.md)
1. 개발자가 각 버전별로 있는 프레임워크(emberjs, reactjs등) 직접 사용하는 대신 클래스로 사용할 수 있는 일관된 방법을 제공합니다.
1. 객체 지향 개발자는 이미 클래스를 이해하고 있습니다.

마지막으로 JavaScript 개발자는 *`class`를 사용* 할 수 있습니다. 여기에 Point라는 기본 클래스가 있습니다:
```ts
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
    add(point: Point) {
        return new Point(this.x + point.x, this.y + point.y);
    }
}

var p1 = new Point(0, 10);
var p2 = new Point(10, 20);
var p3 = p1.add(p2); // {x:10,y:30}
```
이 class는 ES5에서 다음 JavaScript를 생성합니다:
```ts
var Point = (function () {
    function Point(x, y) {
        this.x = x;
        this.y = y;
    }
    Point.prototype.add = function (point) {
        return new Point(this.x + point.x, this.y + point.y);
    };
    return Point;
})();
```
이것은 고급 언어 구조로 현재 관용적으로 사용하는 JavaScript class의 전통적인 패턴입니다.

### Inheritance
Classes in TypeScript (like other languages) support *single* inheritance using the `extends` keyword as shown below:

```ts
class Point3D extends Point {
    z: number;
    constructor(x: number, y: number, z: number) {
        super(x, y);
        this.z = z;
    }
    add(point: Point3D) {
        var point2D = super.add(point);
        return new Point3D(point2D.x, point2D.y, this.z + point.z);
    }
}
```
If you have a constructor in your class then you *must* call the parent constructor from your constructor (TypeScript will point this out to you). This ensures that the stuff that it needs to set on `this` gets set. Followed by the call to `super` you can add any additional stuff you want to do in your constructor (here we add another member `z`).

Note that you override parent member functions easily (here we override `add`) and still use the functionality of the super class in your members (using `super.` syntax).

### Statics
TypeScript classes support `static` properties that are shared by all instances of the class. A natural place to put (and access) them is on the class itself and that is what TypeScript does:

```ts
class Something {
    static instances = 0;
    constructor() {
        Something.instances++;
    }
}

var s1 = new Something();
var s2 = new Something();
console.log(Something.instances); // 2
```

You can have static members as well as static functions.

### Access Modifiers
TypeScript supports access modifiers `public`,`private` and `protected` which determine the accessibility of a `class` member as shown below:

| accessible on   | `public` | `protected` | `private` |
|-----------------|----------|-------------|-----------|
| class           | yes      | yes         | yes       |
| class children  | yes      | yes         | no        |
| class instances | yes      | no          | no        |


If an access modifier is not specified it is implicitly `public` as that matches the *convenient* nature of JavaScript 🌹.

Note that at runtime (in the generated JS) these have no significance but will give you compile time errors if you use them incorrectly. An example of each is shown below:

```ts
class FooBase {
    public x: number;
    private y: number;
    protected z: number;
}

// EFFECT ON INSTANCES
var foo = new FooBase();
foo.x; // okay
foo.y; // ERROR : private
foo.z; // ERROR : protected

// EFFECT ON CHILD CLASSES
class FooChild extends FooBase {
    constructor() {
      super();
        this.x; // okay
        this.y; // ERROR: private
        this.z; // okay
    }
}
```

As always these modifiers work for both member properties and member functions.

### Abstract
`abstract` can be thought of as an access modifier. We present it separately because opposed to the previously mentioned modifiers it can be on a `class` as well as any member of the class. Having an `abstract` modifier primarily means that such functionality *cannot be directly invoked* and a child class must provide the functionality.

* `abstract` **classes** cannot be directly instantiated. Instead the user must create some `class` that inherits from the `abstract class`.
* `abstract` **members** cannot be directly accessed and a child class must provide the functionality.

### Constructor is optional

The class does not need to have a constructor. e.g. the following is perfectly fine. 

```ts
class Foo {}
var foo = new Foo();
```

### Define using constructor

Having a member in a class and initializing it like below:

```ts
class Foo {
    x: number;
    constructor(x:number) {
        this.x = x;
    }
}
```
is such a common pattern that TypeScript provides a shorthand where you can prefix the member with an *access modifier* and it is automatically declared on the class and copied from the constructor. So the previous example can be re-written as (notice `public x:number`):

```ts
class Foo {
    constructor(public x:number) {
    }
}
```

### Property initializer
This is a nifty feature supported by TypeScript (from ES7 actually). You can initialize any member of the class outside the class constructor, useful to provide default (notice `members = []`)

```ts
class Foo {
    members = [];  // Initialize directly
    add(x) {
        this.members.push(x);
    }
}
```
