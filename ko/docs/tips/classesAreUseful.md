## 클래스의 활용

다음과 같은 구조를 가지는 것은 매우 일반적입니다:

```ts
function foo() {
    let someProperty;

    // Some other initialization code

    function someMethod() {
        // Do some stuff with `someProperty`
        // And potentially other things
    }
    // Maybe some other methods

    return {
        someMethod,
        // Maybe some other methods
    };
}
```

이것은 *공개 모듈 패턴*으로 알려져 있으며 JavaScript(JavaScript 클로저 활용)에서 흔히 볼 수 있습니다.


*파일 모듈*[file modules](../project/modules.md)을 (실제로 전역 범위로 사용은 좋지 않습니다.)사용기 위해서는 *동일한 파일*이어야 합니다. 그러나 사람들이 다음과 같은 코드를 작성하는 경우가 너무 많습니다:

```ts
let someProperty;

function foo() {
   // Some initialization code
}
foo(); // some initialization code

someProperty = 123; // some more initialization

// Some utility function not exported

// later
export function someMethod() {

}
```

비록 제가 상속에 대한 열렬한 팬이 아니지만, 사람들이 클래스를 사용하면 코드를 더 잘 구성 할 수 있습니다:

```ts
class Foo {
    public someProperty;

    constructor() {
        // some initialization
    }

    public someMethod() {
        // some code
    }

    private someUtility() {
        // some code
    }
}

export = new Foo();
```

 클래스에 대한 훌륭한 시각화를 제공하는 개발 도구를 만드는 것이 훨씬 더 보편적이고 개발자뿐만 아니라 팀이 이해하고 유지해야하는데 필요한 패턴을 하나 더 줄일 수 있습니다.

> PS: 보일러 플레이트를 *얕은*클래스 계층 구조로 재사용하고 줄이는 것이 중요하다고 생각하는 것에는 아무런 문제가 없습니다.
