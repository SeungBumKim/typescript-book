## Barrel

배럴은 여러 모듈에서 하나의 편리한 모듈로 익스포트를 묶는 방법입니다. 배럴 자체는 다른 모듈의 선택된 익스포트를 다시 익스포트하는 모듈 파일입니다.

다음과 같이 라이브러리안의 클래스구조를 생각해보겠습니다: 

```ts
// demo/foo.ts
export class Foo {}

// demo/bar.ts
export class Bar {}

// demo/baz.ts
export class Baz {}
```

배럴이 없다면, 3개의 임포트문의 작성이 필요합니다:

```ts
import { Foo } from '../demo/foo';
import { Bar } from '../demo/bar';
import { Baz } from '../demo/baz';
```

대신 배럴 `demo/index.ts`에 다음과 같이 추가하면:

```ts
// demo/index.ts
export * from './foo'; // re-export all of its exports
export * from './bar'; // re-export all of its exports
export * from './baz'; // re-export all of its exports
```

배럴에서 필요한 것만 임포트 할 수 있습니다:

```ts
import { Foo, Bar, Baz } from '../demo'; // demo/index.ts is implied
```

### 명명된 익스포트
익스포트 대신에 이름을 붙여 익스포트 할 수 있도록 선택할 수 있습니다. 예를들면 `baz.ts` 다음과 같이 함수가 있다고 하면:

```ts
// demo/foo.ts
export class Foo {}

// demo/bar.ts
export class Bar {}

// demo/baz.ts
export function getBaz() {}
export function setBaz() {}
```

데모에서 `getBaz` / `setBaz`을 익스포트 하는 대신에 대신 변수를 이름으로 가져와서 아래에 표시된대로 그 이름을 내보내 변수에 넣을 수 있습니다:

```ts
// demo/index.ts
export * from './foo'; // re-export all of its exports
export * from './bar'; // re-export all of its exports

import * as baz from './baz'; // import as a name
export { baz }; // export the name
```

사용할 때는 아래와 같이 하면됩니다: 

```ts
import { Foo, Bar, baz } from '../demo'; // demo/index.ts is implied

// usage
baz.getBaz();
baz.setBaz();
// etc. ...
```
