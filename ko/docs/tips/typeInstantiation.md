## 제네릭을 위한 Type 인스턴스화

제네릭 파라미터가 있는 무언가가 있다고 가정해보겠습니다. 예를들어 `Foo`클래스: 

```ts
class Foo<T>{
	foo: T;
}
```

특정 type에 대해 특화된 버전을 만들고 싶을 경우, 방식은 항목을 새로운 변수에 복사하고 제네릭을 구체적인 type으로 대체한 type 어노테이션을 제공하는 것입니다. 예를들어, `Foo<number>`를 그렇게하고 싶다면:

```ts
class Foo<T>{
	foo: T;
}
let FooNumber = Foo as { new ():Foo<number> }; // ref 1
```
`ref 1`에서 `FooNumber`는 `Foo`와 같다고 하지만 `new` 연산자로 호출했을 때 `Foo <Number>`의 인스턴스를 주는 것으로 처리됩니다.

### 상속
타입 어설션 패턴은 당신이 옳은 일을 하고 있다고 믿기 때문에 안전하지 못합니다. *클래스에 대한* 다른 언어의 일반적인 패턴은 상속을 사용하는 것입니다:

```ts
class FooNumber extends Foo<number>{}
```

여기 한마디 주의를 주자면: 기본 클래스에 데코레이터를 사용하면 상속된 클래스는 기본 클래스와 동일한 동작을 하지 않을 수 있습니다.(더 이상 데코레이터로 감싸지지 않습니다.).

물론 클래스를 특화시키지 않으면 여전히 작업에 관한 강제적이고/지정한 패턴을 만들어야하므로 일반적인 지정 패턴을 먼저 보여 주어야합니다. 예를들면:

```ts
function id<T>(x: T) { return x; }
const idNum = id as {(x:number):number};
```

> 다음 stackoverflow 질문에서 참고하였습니다. [stackoverflow question](http://stackoverflow.com/a/34864705/390330)
