### 클래스
JavaScript에서 클래스를 가장 중요하게 여기는 이유는:
1. [class는 구조적 추상화를 제공합니다.](./tips/classesAreUseful.md)
1. 개발자가 각 버전별로 있는 프레임워크(emberjs, reactjs등)를 직접 사용하는 대신 클래스로 사용할 수 있는 일관된 방법을 제공합니다.
1. 객체 지향 개발자는 이미 클래스를 이해하고 있습니다.

마지막으로 JavaScript 개발자는 *`클래스`를 사용* 할 수 있습니다. 여기에 Point라는 기본 클래스가 있습니다:
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

### 상속
아래에 표시된 것과 같이 TypeScript의 클래스는 (다른 언어들처럼) `extends` 키워드를 사용하여 *단일*상속을 지원합니다:
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
클래스에 생성자가 있으면 생성자에서 *반드시 부모 생성자를 호출*해야합니다.(TypeScript는 이 내용을 알려줄 것입니다.) 이렇게 해야 'this'에 설정해야 할 내용이 설정됩니다. 생성자에서 `super`를 호출 다음에는 원하는 모든 것을 추가 할 수 있습니다.(여기서는 멤버 `z`를 추가했습니다.)

부모 멤버함수를 쉽게 오버라이드할 수 있고(여기에서는`add`를 오버라이드한다.) 멤버에서 부모클래스의 기능을 사용(super 구문을 사용)할 수 있다는 것을 알고 계셔야합니다.

### Statics
TypeScript 클래스는 클래스의 모든 인스턴스가 공유하는 `static` 속성을 지원합니다. 입력(또는 접근)하는 방식은 클래스 내부에 있고, 그것들은 TypeScript에서 수행합니다:

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

static 멤버뿐 아니라 static 함수도 사용할 수 있습니다.

### 접근 제한자
TypeScript는 아래와같이 `class`의 멤버에대해 접근 가능성을 결정하는 접근제한자 `public`, `private`, `protected`을 제공합니다:

| accessible on   | `public` | `protected` | `private` |
|-----------------|----------|-------------|-----------|
| class           | yes      | yes         | yes       |
| class children  | yes      | yes         | no        |
| class instances | yes      | no          | no        |


접근 제한자가 지정되지 않은 경우 암시적으로`public`입니다. 이러한 부분은 JavaScript의 *편리성*에 부합합니다🌹.

런타임에(생성된 JS에서) 위 내용은 의미가 없지만 잘못 사용하면 컴파일 타임에 오류가 발생한다는 것을 인지해야합니다. 각각의 예가 아래 나와있습니다:

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

이러한 접근 제한자는 멤버속성과 멤버함수에 모두 적용합니다.

### 추상성
`추상성`은 접근제한자처럼 생각할 수 있습니다. 앞에서 다룬 접근제한자와는 다르게 클래스의 모든 멤버뿐만 아니라 클래스에서도 있을 수 있기 때문에 별도로 다루어 집니다. `abstract` 한정자를 갖는 것은 기능적으로 *직접 호출 할 수 없으며* 하위 클래스가 기능을 제공해야 함을 의미합니다.

* `abstract` **classes**는 직접 인스턴트화 할 수 없습니다. 대신 사용자는 `abstract class`를 상속받은 `class`를 생성해야합니다.
* `abstract` **members**는 직접 접근할 수 없고, 하위클래스에서 반드시 제공을 해주어야합니다.

### 생성자는 선택사항입니다.

클래스는 생성자가 필요하지 않습니다. 예를들어, 다음과 같이 사용가능합니다.

```ts
class Foo {}
var foo = new Foo();
```

### 생성자 정의

클래스내에 멤버를 갖을 수 있고, 아래와 같이 초기화 할 수 있습니다:

```ts
class Foo {
    x: number;
    constructor(x:number) {
        this.x = x;
    }
}
```
TypeScript가 *접근제한자*를 접두어로 붙일 수있는 일반적인 패턴이며 클래스에 자동으로 선언되고 생성자에서 복사됩니다. 그래서 위의 예는 아래와 같이 적을 수 있습니다.(`public x:number`를 참고하세요.)

```ts
class Foo {
    constructor(public x:number) {
    }
}
```

### Property initializer
이 기능은 TypeScript에서 지원되는 멋진 기능입니다(실제로 ES7에서 제공됨). 클래스 생성자 밖에서 클래스의 멤버를 초기화 할 수 있으며, 기본형을 제공하는 데 유용합니다.(`members = []`를 참고하세요.)

```ts
class Foo {
    members = [];  // Initialize directly
    add(x) {
        this.members.push(x);
    }
}
```
