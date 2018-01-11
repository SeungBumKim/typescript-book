## 네임스페이스
네임스페이스는 JavaScript에서 사용되는 일반적인 패턴을 중심으로 편리한 구문을 제공합니다:

```ts
(function(something) {

    something.foo = 123;

})(something || (something = {}))
```

기본적으로 `something || (something = {})`은 익명의 함수 `function(something) {}`이 *기존 객체에 내용을 추가*(`something ||`부분)하거나 *새로운 객체를 시작한 다음 그 객체에 내용을 추가*(`|| (something = {})`부분) 할 수 있게합니다. 즉, 두 개의 블록을 일부 실행 경계로 나눌 수 있습니다:

```ts
(function(something) {

    something.foo = 123;

})(something || (something = {}))

console.log(something); // {foo:123}

(function(something) {

    something.bar = 456;

})(something || (something = {}))

console.log(something); // {foo:123, bar:456}

```

이러한 내용은 전역 네임스페이스에 오브젝트가 누출되지 않도록 JavaScript영역에서 일반적으로 사용됩니다. 파일 기반의 모듈에서는 이러한 문제를 걱정할 필요가 없으나 이 패턴은 여러 함수들 사이에서 논리적 그룹화에 여전히 유용합니다. 그래서 TypeScript에서는 이러한 그룹에 `namespace`키워드를 제공합니다. 예를들면:

```ts
namespace Utility {
    export function log(msg) {
        console.log(msg);
    }
    export function error(msg) {
        console.error(msg);
    }
}

// usage
Utility.log('Call me');
Utility.error('maybe!');
```

`namespace` 키워드는 앞에봤던 것과 같은 똑같은 JavaScript코드를 생성합니다:

```ts
(function (Utility) {

// Add stuff to Utility

})(Utility || (Utility = {}));
```

한가지 알아둘 사항을 알려드리자면 네임스페이스는 중첩 될 수 있으므로 `namespace Utility.Messaging`과 같은 작업을 수행하여 `Utility`아래에`Messaging` 네임 스페이스를 포함해서 사용해야 합니다.

대부분의 프로젝트에서는 빠른 구성과 오래된 JavaScript와의 호환을 위해서 외부 모듈과 `namespace`를 사용할 것을 권장합니다. 
