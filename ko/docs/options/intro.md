# 편의성 대 제약성

TypeScript가 배타적으로 할 수 없는 몇가지 사항이 있습니다. 예를들어 *선언되지 않은 변수를 사용하는 경우*입니다.(물론 외부시스템에 *선언파일* 을 사용할 수 있습니다.)

즉, 전통적으로 프로그래밍 언어는 type 시스템에 허용되는 것과 허용되지 않는 것 사이의 경계가 있습니다. TypeScript는 그 슬라이더를 놓는 위치를 제어 할 수 있다는 점이 다릅니다. 이렇게하면 실제로 알고있는 JavaScript를 **당신이 원하는** 안전성만큼 사용할 수 있습니다. 이 슬라이더를 정확하게 제어 할 수 있는 많은 컴파일러 옵션이 있으므로 살펴 보겠습니다.

### Boolean Options

`boolean`인 `compilerOptions`는 `tsconfig.json`에서 `compilerOptions`로 지정할 수 있습니다:

```json
{
    "compilerOptions": {
        "someBooleanOption": true
    }
}
```

또는 커맨드라인에서는:

```sh
tsc --someBooleanOption
```

> 위와같은 모든경우는 `false`가 기본값입니다.

다음을 보시면 컴파일러 옵션이 있습니다. [here](https://www.typescriptlang.org/docs/handbook/compiler-options.html)
