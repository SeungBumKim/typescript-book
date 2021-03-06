### 선언 파일
`declare` 키워드를 사용하여 다른 곳(JavaScript/CoffeeScript로 작성/브라우저 또는 Node.js와 같은 런타임 환경)에 있는 코드를 기술하려고 한다는 것을 TypeScript에 알릴 수 있습니다. 간단한 예를 보면:

```ts
foo = 123; // Error: `foo` is not defined
```
vs.
```ts
declare var foo: any;
foo = 123; // allowed
```

이러한 선언을 `.ts` 파일이나`.d.ts`파일에 넣을 수 있습니다. 실제 프로젝트에서는 별도의`.d.ts` 파일을 사용하는 것이 좋습니다.(`globals.d.ts` 또는`vendor.d.ts`와 같은 것으로 시작하십시오.)


파일의 확장자가 `.d.ts`이면, 각 루트 레벨 정의는 `declare`키워드를 앞에 붙여야합니다. 이렇게하면 작성자는 TypeScript가 *내보내는 코드가 아님*을 분명히 알 수 있습니다. 작성자는 선언된 항목이 런타임에 존재하는지 확인해야 합니다.

> * Ambient 선언은 컴파일러를 사용하여 만드는 약속입니다. 런타임에 존재하지 않고 이를 사용하려고 시도하면 경고없이 중단됩니다.
* Ambient 선언은 문서와 비슷합니다. 소스가 변경되면 문서를 업데이트해야 합니다. 따라서 런타임시 작동하는 새로운 동작이 있을 수 있지만 Ambient 선언은 업데이트되지 않으므로 컴파일러 오류가 발생합니다.
