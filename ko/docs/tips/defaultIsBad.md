## `export default`는 문제를 발생 시킬 수 있습니다. 

예를들어 보겠습니다. 아래와 같은 컨텐츠를 갖고있는 `foo.ts`파일이 있다고 해보겠습니다: 

```ts
class Foo {
}
export default Foo;
```

다음과 같이 ES6문법을 사용해서 임포트(`bar.ts`에) 하겠습니다: 

```ts
import Foo from "./foo";
```

여기에 몇 가지 유지관리 문제가 있습니다:
* `foo.ts`의 `Foo`를 리팩터링하면 `bar.ts`에서 이름을 변경하지 않습니다.
* `foo.ts` (많은 파일들이 가지고있을 것입니다)에서 더 많은 내용을 내보낼 필요가 생기면 임포트 구문을 섞여야합니다.

그러한 이유로 간단하게 exports + 비구조화된 임포트를 사용하는것을 추천합니다. 예를들면 `foo.ts`에:

```ts
export class Foo {
}
```
그리고:

```ts
import {Foo} from "./foo";
```

> **보너스 포인트**: `import {/*here*/} from "./foo";`의 커서 위치에서 자동 완성됩니다. 개발자에게 약간 도움이 될 것입니다.

> **보너스 포인트**: 더 나은 commonJs의 경험을 됩니다. `default`를 사용하면 `const {Foo} = require('module/foo')`대신 `const {default} = require('module/foo');`를 사용해야하는 commonjs 사용자에게 끔찍한 경험이 될 수 있습니다.

> **보너스 포인트**: 개발과정에서 `import Foo from "./foo";`와 `import foo from "./foo";` 같은 오타를 가져 오지 않는다. 

> **보너스 포인트**: 자동 임포트 quickfix가 더 잘 작동합니다. 당신은 `Foo`를 사용하고 자동 임포트는 `import { Foo } from "./foo";`를 작성해서 잘 정의된 이름을 모듈에서 내보냅니다.

> **보너스 포인트**: 재발행은 불필요하게 어렵습니다. 재발행은 npm 패키지의 `index` 파일 루트로 하는 것이 일반적입니다. 예를들면, `import Foo from "./foo"; export { Foo }`(기본) 대 `export * from "./foo"`(명명된 export).
