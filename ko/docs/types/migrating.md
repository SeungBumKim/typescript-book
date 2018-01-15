## JavaScript으로부터 마이그레이션

일반적으로 다음과 같은 단계의 프로세스로 구성됩니다:

* `tsconfig.json` 추가합니다.
* 소스파일의 확장자를 `.js`에서 `.ts`로 변경합니다. `any`를 사용하여 오류를 *회피*하십시오.
* TypeScript에 새로운 코드를 작성하고 가능한 한`any`를 거의 사용하지 마십시오.
* 이전 코드로 돌아가서 type 키워드를 추가하고 확인된 버그를 수정하십시오.
* 타사 JavaScript 코드에 대한 정의를 사용하십시오.

이 점에 대해 몇 가지 더 논의 해 보겠습니다.

모든 JavaScript는 *유효한* TypeScript입니다. 이말은 어떤 JavaScript를 TypeScript compiler에서 동작시킨다면 원래의 JavaScript와 동일하게 동작해야할 것입니다. 즉, 확장자를 `.js`에서 `.ts`로 변경해도 코드에는 영향을 미치지 않습니다.

### 에러 처리
TypeScript는 즉시 코드의 TypeChecking을 시작하며 원래의 JavaScript코드는 *생각했던대로 잘 돌아 가지 않으므로* 진단 오류가 발생할 수 있습니다. 이러한 부분의 많은 에러는 `any`를 사용해서 피할 수 있습니다. 예를들면: 

```ts
var foo = 123;
var bar = 'hey';

bar = foo; // ERROR: cannot assign a number to a string
```

**에러가 유효함**(대부분의 추측된 정보는 코드의 다른 부분의 원저자가 상상한 것보다 낫나을 것입니다.)에도 불구하고, 이전 코드베이스를 점진적으로 업데이트하여 TypeScript의 새로운 코드를 작성하게 되는 것에 초점을 두어야합니다. 아래와 같이 type 경고에 대한 에러를 회피할 수 있습니다:

```ts
var foo = 123;
var bar = 'hey';

bar = foo as any; // Okay!
```


다른 방식으로는 `any`로 키워드를 넣을 수도 있습니다:

```ts
function foo() {
    return 1;
}
var bar = 'hey';
bar = foo(); // ERROR: cannot assign a number to a string
```

회피:

```ts
function foo(): any { // Added `any`
    return 1;
}
var bar = 'hey';
bar = foo(); // Okay!
```

> 노트: 에러회피는 위험합니다. 그러나 *새로운* TypeScript 코드에서 오류를 발견 할 수 있게 해줍니다. 만약 그냥 넘어가고 싶다면 `// TODO:` 주석을 넣어주세요.

### Third Party JavaScript
작성한 JavaScript는 TypeScript로 변경할 수 있으나 TypeScript를 사용하도록 모든것을 변경할 수는 없습니다. TypeScript의 주변 정의 지원이 들어오게 된 것입니다. 처음에는 `vendor.d.ts`(`.d.ts` 확장은  *선언 파일*이라고 지정합니다) 파일을 만들고 필요한 것을 추가하는 방식을 추천합니다. 또는 라이브러리와 관련된 파일을 만듭니다(예: jquery를 위한`jquery.d.ts`).

> 노트: 거의 상위 90%의 JavaScript 라이브러리에 대해 잘 관리되고 강력하게 정의된 정의는 다음 OSS 저장소에 있습니다.[DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped). 여기에 제시된대로 자신만의 정의를 만들기 전에 거기를 살펴 보는 것이 좋습니다. 이러한 빠른 방법은 TypeScript로 초기 마찰을 줄이기 위한 중요한 지식입니다.

`jquery`의 경우를 생각해보면, *별쓸모없는* 정의를 쉽게 정의할 수 있습니다: 
```ts
declare var $: any;
```

때로는 뭔가 (예:`JQuery`)에 명시적 키워드을 추가하고 *유형 선언 공간*에서 사용할 무엇인가가 필요할 수도 있습니다. `type` 키워드를 사용하면 쉽게 사용할 수 있습니다: 
```ts
declare type JQuery = any;
declare var $: JQuery;
```

이것은 더 쉽게 업데이트 방식을 제공할 것입니다.

다시 한번 말하지만 꽤 좋은 `jquery.d.ts`가 다음에 있습니다.[DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped). 그러나 이제부터 타사 JavaScript를 사용할 때 JavaScript -> TypeScript 마찰을 *신속*하게 극복하는 방법또한 배우겠습니다. 다음에는 주변 선언을 자세히 살펴 보겠습니다.

# 타사 NPM 모듈들

전역 변수 선언과 유사하게 전역 모듈 또한 쉽게 선언할 수 있습니다. 예를들면, (https://www.npmjs.com/package/jquery)의 `jquery`모듈을 사용한다면 다음과 같이 작성할 수 있습니다: 

```ts
declare module "jquery";
```

그런 다음 필요에 따라 파일로 가져올 수 있습니다:

```ts
import * as $ from "jquery";
```

> 다시 한번 말하지만 꽤 좋은 `jquery.d.ts`가 다음에 있습니다.[DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped). 그러나 당신의 라이브러리에 있는것은 아닐 것이며, 이제는 마이그레이션을 신속하게 진행할 수 있습니다.🌹


# 외부의 js가 아닌 리소스

모든 파일 가져오기를 허용 할 수도 있습니다. 예를들면, `.css` 파일(단순한 `*`스타일 선언과 함께 webpack과 같은 것을 사용하는 경우):

```ts
declare module "*.css";
```

`import * as foo from "./some/file.css";` 이렇게 사용하면 됩니다.
