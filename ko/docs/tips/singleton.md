# 싱글턴 패턴

전통적인 싱글턴 패턴은 실제로 모든 코드가 클래스에 있어야 한다는 사실을 극복하는데 사용되는 무언가입니다.

```ts
class Singleton {
    private static instance: Singleton;
    private constructor() {
        // do something construct...
    }
    static getInstance() {
        if (!Singleton.instance) {
            Singleton.instance = new Singleton();
            // ... any one time initialization goes here ...
        }
        return Singleton.instance;
    }
    someMethod() { }
}

let something = new Singleton() // Error: constructor of 'Singleton' is private.

let instance = Singleton.getInstance() // do something with the instance...
```

그러나 늦은 초기화를 원하지 않는다면 대신에 `namespace`를 사용할 수 있습니다:

```ts
namespace Singleton {
    // ... any one time initialization goes here ...
    export function someMethod() { }
}
// Usage
Singleton.someMethod();
```

> 경고: 싱글턴은 전역적으로 사용할 수 있는 이름이됩니다.[global](http://stackoverflow.com/a/142450/390330)

대부분의 프로젝트에서 `namespace`는 *module*로 대체 될 수 있습니다.

```ts
// someFile.ts
// ... any one time initialization goes here ...
export function someMethod() { }

// Usage
import {someMethod} from "./someFile";
```


