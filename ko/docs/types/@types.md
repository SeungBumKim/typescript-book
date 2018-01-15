# `@types`

[Definitely Typed](https://github.com/DefinitelyTyped/DefinitelyTyped)은 TypeScript의 가장 큰 강점 중 하나입니다. 커뮤니티는 사실상 앞서 가고 있으며, 상위 JavaScript 프로젝트의 거의 90%가 위에 **문서화**되어 있습니다.

즉, 프로젝트를 대화식으로 탐색 할 수 있다는 것입니다. 별도의 창에서 문서를 열고 오타를 만들지 않아도 됩니다.

## `@types` 사용

설치는`npm`의 위에서 작동하기 때문에 상당히 간단합니다. 예를 들어`jquery`에 대한 타입 정의를 다음과 같이 간단히 설치할 수 있습니다:

```
npm install @types/jquery --save-dev
```

`@types` *전역* 및 *모듈* type 정의를 모두 지원합니다.


### 전역 `@types`

기본적으로 전역 사용를 지원하는 정의가 자동으로 포함됩니다. 예를들어 `jquery`의 경우 시작할 때 `$`를 붙이는 것으로 *전역적으로* 프로젝트에서 사용할 수 있습니다.

그러나 *라이브러리*(`jquery`와 같은)의 경우 일반적으로 *모듈* 사용을 권장합니다:

### 모듈 `@types`

설치 후 특별하게 필요한 설정은 없습니다. 예제와같이 모듈을 사용하듯이 하시면됩니다:

```ts
import * as $ from "jquery";

// Use $ at will in this module :)
```

## 전역 제어

자동으로 전역사용을 지원하는 정의가 있는 것은 일부 팀에게는 문제가 될 수 있으므로 `tsconfig.json`의 `compilerOptions.types`를 사용하여 *필요한* 유형만 가져올 수 있습니다:

```json
{
    "compilerOptions": {
        "types" : [
            "jquery"
        ]
    }
}
```

위의 예제는`jquery`만 사용할 수있는 샘플을 보여줍니다. `npm install @types/node`와 같은 또 다른 정의를 설치하더라도 전역 변수(예: [`process`](https://nodejs.org/api/process.html))는 추가 할 때까지 코드에 노출되지 않습니다 필요한것은 `tsconfig.json` type 옵션에 추가하십시오.
