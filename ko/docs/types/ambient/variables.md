### 변수

예를 들어 TypeScript에 [`process` variable](https://nodejs.org/api/process.html)를 알려 주려면 다음과 같이 하시면 됩니다:

```ts
declare var process: any;
```

> 이미 다음으로 유지되고 있으므로 `process`를 위해 이런것을 수행 할 필요는 없습니다[community maintained `node.d.ts`](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/node/index.d.ts).

이렇게하면 TypeScript에서 문제없이 `process` 변수를 사용할 수 있습니다:

```ts
process.exit();
```

가능하면 인터페이스를 사용하는 것이 좋습니다. 예를들면:

```ts
interface Process {
    exit(code?: number): void;
}
declare var process: Process;
```

이렇게하면 다른 사람들이 이러한 전역 변수의 본질을 확장하면서도 그러한 수정에 대해 TypeScript에 알릴 수 있습니다. 예를들어, exitWithLogging 함수를 추가 한 다음 사례를 고려해보십시오:

```ts
interface Process {
    exitWithLogging(code?: number): void;
}
process.exitWithLogging = function() {
    console.log("exiting");
    process.exit.apply(process, arguments);
};
```

인터페이스에 대해 좀 더 자세히 살펴 보겠습니다.
