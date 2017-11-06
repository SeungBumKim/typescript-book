## 참조
## References

문자형태 이외에 JavaScript의 오브젝트(함수, 배열, 정규식 등)는 참조형이다. 이 말의 의미는 다음과 같다.

### 변형된 값은 모두 참조형이다.

```js
var foo = {};
var bar = foo; // bar is a reference to the same object

foo.baz = 123;
console.log(bar.baz); // 123
```

### 동등연산자는 참조에 대한 처리이다.

```js
var foo = {};
var bar = foo; // bar is a reference
var baz = {}; // baz is a *new object* distinct from `foo`

console.log(foo === bar); // true
console.log(foo === baz); // false
```
