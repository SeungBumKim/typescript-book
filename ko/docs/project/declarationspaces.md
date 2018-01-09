## 선언 공간

TypeScript 두가지 종류의 선언공간이 있습니다: *변수*에대한 선언공간과 *타입*에 대한 선언공간입니다. 이러한 컨셉은 아래에서 살펴보겠습니다.

### 타입 선언 공간
타입 선언공간은 타입의 키워드로 사용할 수 있는 것을 포함합니다. 예를들면, 다음과 같은 타입선언을 들 수 있습니다:

```ts
class Foo {};
interface Bar {};
type Bas = {};
```
위의 의미는 `Foo`, `Bar`, `Bas`를 타입 키워드로 사용할 수 있다는 것입니다. 예를 들면:

```ts
var foo: Foo;
var bar: Bar;
var bas: Bas;
```

주의할 점은 `interface Bar`를 선언할지라도, *변수에는 사용할 수 없다*는 것입니다. *변수 선언 공간*을 만족하지 못하기 때문입니다. 아래 내용을 보시면: 

```ts
interface Bar {};
var bar = Bar; // ERROR: "cannot find name 'Bar'"
```

`cannot find name`라고 나오는 이유는 `Bar`의 이름이 *변수* 선언 공간에 *정의 되지 않았기* 때문입니다. 다음주제로 "변수 선언 공간"에 대해서 보겠습니다.

### 변수 선언 공간
변수 선언 공간은 변수로 사용할 수 있는 것을 포함합니다. `class Foo`는 `Foo`의 *타입* 선언 공간으로 기여하는 것을 보았습니다. 맞춰보시겠나요? 이는 또한 *변수* 선언 공간으로서 *변수* `Foo`에 기여합니다. 아래를 보십시오:

```ts
class Foo {};
var someVar = Foo;
var someOtherVar = 123;
```
때로는 클래스를 변수로 전달하고자 할 때 유용합니다. 기억할 사항은:

* *오직* *타입* 선언 공간으로된 `interface`와 같은 것은 변수로는 사용할 수 없다는 것 입니다. 

유사하게 `var`를 선언하면 *오직* *변수* 선언 공간으로만 사용되고 타입 키워드로는 사용하지 못합니다:

```ts
var foo = 123;
var bar: foo; // ERROR: "cannot find name 'foo'"
```
`cannot find name`로 나오는 이유는 `foo`의 이름이 *type* 선언 공간으로 정의되지 않았기 때문입니다.
