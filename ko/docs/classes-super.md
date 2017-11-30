#### `super`

아래 보는 것 같이 자식 클래스에서 `super`를 호출하면 `prototype`로 전환되는 것을 주의하세요.

```ts
class Base {
    log() { console.log('hello world'); }
}

class Child extends Base {
    log() { super.log() };
}
```
생성:

```js
var Base = (function () {
    function Base() {
    }
    Base.prototype.log = function () { console.log('hello world'); };
    return Base;
})();
var Child = (function (_super) {
    __extends(Child, _super);
    function Child() {
        _super.apply(this, arguments);
    }
    Child.prototype.log = function () { _super.prototype.log.call(this); };
    return Child;
})(Base);

```
`_super.prototype.log.call(this)`을 주의 하세요.

멤버 프로퍼티에서 `super`를 사용할 수 없다는 것입니다. 대신 `this`를 사용하면 됩니다.

```ts
class Base {
    log = () => { console.log('hello world'); }
}

class Child extends Base {
    logWorld() { this.log() };
}
```

`Base`와 `Child`클래스 사이에는 `this`만 공유되므로 *다른* 이름을 사용해야합니다.(여기서는 `log`와 `logWorld`)

또한 TypeScript는 잘못사용한 `super`에 대해서는 경고한다는 것에 주의하세요:

```ts
module quz {
    class Base {
        log = () => { console.log('hello world'); }
    }

    class Child extends Base {
        // ERROR : only `public` and `protected` methods of base class are accessible via `super`
        logWorld() { super.log() };
    }
}
```
