#### IIFE는 무엇인가?
클래스에 대해서 아래와 같이 js로 생성합니다:
```ts
function Point(x, y) {
    this.x = x;
    this.y = y;
}
Point.prototype.add = function (point) {
    return new Point(this.x + point.x, this.y + point.y);
};
```

예와같이 Immediately-Invoked Function Expression (IIFE)로 둘러싸인 이유는

```ts
(function () {

    // BODY

    return Point;
})();
```

상속과 관련이 있습니다. TypeScript가 기본클래스를 변수`_super`로 접근 할 수 있게합니다. 예를들면 아래와 같습니다.

```ts
var Point3D = (function (_super) {
    __extends(Point3D, _super);
    function Point3D(x, y, z) {
        _super.call(this, x, y);
        this.z = z;
    }
    Point3D.prototype.add = function (point) {
        var point2D = _super.prototype.add.call(this, point);
        return new Point3D(point2D.x, point2D.y, this.z + point.z);
    };
    return Point3D;
})(Point);
```

IIFE는 TypeScript가`_super`변수의 `Point`라는 기본 클래스를 쉽게 접근 할 수 있게하고, 해당 내용은 클래스의 본문내용에서 일관되게 사용됩니다.

### `__extends`
클래스를 상속하면 TypeScript는 다음과 같은 함수를 생성합니다:
```ts
var __extends = this.__extends || function (d, b) {
    for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];
    function __() { this.constructor = d; }
    __.prototype = b.prototype;
    d.prototype = new __();
};
```
여기에서 'd'는 파생된 클래스를 의미하고 'b'는 베이스가 되는 클래스를 의미합니다. 이 함수는 두 가지 작업을 수행합니다.
1. 기본 클래스의 정적 멤버를 자식 클래스에 복사합니다. 예를들면, `for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];` 구문입니다.
1. 자식 클래스 함수의 프로토타입에 부모의 `proto`멤버에 접근하게 설정합니다. 예를들면, `d.prototype.__proto__ = b.prototype` 구문입니다.

대부분 첫번째 내용을 이해하는데 어려움이 없으나 두번째 내용은 노력이 필요할 것입니다. 그래서 아래 이어서 설명하겠습니다.

#### `d.prototype.__proto__ = b.prototype`

이것에 대해 많은 사람들을 가르친 후에야 다음과 같은 설명이 가장 단순하다는 것을 알게되었습니다. 먼저`__extends`의 코드가 어떻게 간단한 `d.prototype .__ proto__ = b.prototype`과 같은지 설명 할 것입니다. 그리고 왜 이 구문이 중요한지 설명하겠습니다. 이 모든 것을 이해하기 위해서는 아래와같은 것들을 알아야합니다: 

1. `__proto__`
1. `prototype`
1. 호출 된 함수안의 `this`에 `new`가 미치는 영향
1. `prototype`과 `__proto__`에 `new`가 미치는 영향

모든 JavaScript 오브젝트에는 `__proto__`멤버가 포함되어 있습니다. 이 멤버는 오래된 브라우저에서는 쓰지 못하기도 합니다.(일부 브라우저에서는 `[[prototype]]`로 쓰이며 마법같은 속이라 말하기도 합니다.) 이 멤버는 하나의 오브젝트만 갖고있습니다: 오브젝트에서 프로퍼티를 찾지못하면(예, `obj.property`) `obj.__proto__.property`와 같이 해당 프로퍼티를 찾으려고합니다. 만약 여전히 찾지 못한다면 `obj.__proto__.__proto__.property`와 같이 *`.__proto__`가 null*이 되거나 *찾을때까지* 반복할 것입니다. 이런한 내용은 왜 JavaScript가 자동으로 *프로토타입 상속*을 지원한다고 말하는지 알 수 있습니다. 이러한 내용은 chrome console 또는 Node.js에서 실행가능한 다음예를 통해 알 수 있습니다. 

```ts
var foo = {}

// setup on foo as well as foo.__proto__
foo.bar = 123;
foo.__proto__.bar = 456;

console.log(foo.bar); // 123
delete foo.bar; // remove from object
console.log(foo.bar); // 456
delete foo.__proto__.bar; // remove from foo.__proto__
console.log(foo.bar); // undefined
```

`__proto__`을 명확하게 이해할 수 있습니다. 또 다른 유용한 정보는 JavaScript의 모든 `함수`는 `prototype`이라는 속성을 가지고 있고, `prototype`은 함수자체를 가리키는 '생성자'멤버가 있음을 알 수 있습니다. 이러한 내용은 아래 확인할 수 있습니다:

```ts
function Foo() { }
console.log(Foo.prototype); // {} i.e. it exists and is not undefined
console.log(Foo.prototype.constructor === Foo); // Has a member called `constructor` pointing back to the function
```

이제 *호출 된 함수안의 `this`에 `new`가 미치는 영향*를 살펴보겠습니다. 기본적으로 호출된 함수 내부의 `this`는 함수에서 반환될 새로 생성된 객체를 가리킬 것입니다. 함수안의 `this`의 프로퍼티를 변경시키는 것으로 간단한게 이해할 수 있습니다:

```ts
function Foo() {
    this.bar = 123;
}

// call with the new operator
var newFoo = new Foo();
console.log(newFoo.bar); // 123
```

이제 알아야 할 유일한 다른 것은 함수에서 `new`를 호출하면 함수 호출에서 반환된 새로 생성된 객체의 `__proto__`에 함수의 `prototype`이 할당된다는 것입니다. 아래에 완전히 이해하기 위한 실행할 수 있는 코드가 있습니다:

```ts
function Foo() { }

var foo = new Foo();

console.log(foo.__proto__ === Foo.prototype); // True!
```

이게 전부입니다. 이제`__extends` 다음 내용을 보셔도 됩니다. 아래 줄에 번호를 메겨놓았습니다:

```ts
1  function __() { this.constructor = d; }
2   __.prototype = b.prototype;
3   d.prototype = new __();
```

이 함수를 거꾸로 보면 라인3의 `d.prototype = new __()`는 `d.prototype = {__proto__ : __.prototype}`의 의미(`prototype` 와 `__proto__`에서 `new`를 하였기 때문에)를 갖고, 앞의 라인(라인2의 `__.prototype = b.prototype;`)과 합쳐져서 `d.prototype = {__proto__ : b.prototype}`와 같이 얻게됩니다.

하지만 우리가 예를들어 `d.prototype.__proto__`와 같은 원한다면 그저 proto만 변경하고 `d.prototype.constructor`은 유지해도됩니다. 이러한 내용은 첫 번째 라인의 의미(예를들면 `function __() { this.constructor = d; }`)에 들어있습니다. 여기에는 `d.prototype = {__proto__ : __.prototype, d.constructor = d}`한 의미(호출된 함수의 `this`에서 `new`를 해서)가 있습니다. 그래서`d.prototype.constructor`를 복원한 후에는 변겨되는 것은 `__proto__`뿐이므로 `d.prototype .__ proto__ = b.prototype`와 같이 볼 수 있습니다.

#### `d.prototype.__proto__ = b.prototype` significance

중요한 점은 하위 클래스에 멤버 함수를 추가하고 기본 클래스에서 다른 멤버를 상속 할 수 있다는 것입니다. 이것은 다음의 간단한 예제에 의해 증명됩니다:

```ts
function Animal() { }
Animal.prototype.walk = function () { console.log('walk') };

function Bird() { }
Bird.prototype.__proto__ = Animal.prototype;
Bird.prototype.fly = function () { console.log('fly') };

var bird = new Bird();
bird.walk();
bird.fly();
```
기본적으로 `bird.fly`는 `bird.__proto__.fly`에서부터(`new`는 `Bird.prototype`를 가르키는 `bird.__proto__`을 만든다는 것을 기억해봅시다.) 찾고, `bird.walk`(상속된 멤버)는 `bird.__proto__.__proto__.walk`에서부터 찾을 것입니다. (`bird.__proto__ == Bird.prototype` 이고 `bird.__proto__.__proto__` == `Animal.prototype`이기 때문에)
