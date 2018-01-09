## 모듈

### 전역 모듈

기본적으로 새로운 TypeScript의 파일에 코드를 작성하면 *전역* 네임스페이스로 됩니다. 데모로서 `foo.ts`를 보겠습니다:

```ts
var foo = 123;
```

이제 *새롭게* 같은 프로젝트안에 `bar.ts`파일은 만들면, TypeScript의 타입 시스템은 `foo`를 사용 가능하다면 전역으로 *허용*해줍니다:

```ts
var bar = foo; // allowed
```
말할 필요도없이 전역 네임 스페이스를 갖는 것은 변수명의 충돌로 코드를 열 때 위험합니다. 그래서 다음에 제시하는 파일 모듈을 사용할 것을 추천합니다.

### 파일 모듈
*외부 모듈*이라고도 불립니다. TypeScript file의 시작부분에 `import`나 `export`를 추가하면 파일안의 *지역*범위로 생성됩니다. 그래서 앞의 `foo.ts`를 다음과 같이 수정(`export` 사용에 중의하세요.)한다면: 

```ts
export var foo = 123;
```

`foo`가 전역 네임스페이스에 있을 걱정을 하지 않아도 됩니다. 이러한 내용은 새로운 `bar.ts`를 다음과 같이 생성해서 확인해볼 수 있습니다:

```ts
var bar = foo; // ERROR: "cannot find name 'foo'"
```

만약 `bar.ts`에서 `foo.ts`의 것을 사용하고 싶으면 *명시적으로 추가* 해주어야 합니다. 이 내용은 다음 변경사항이 적용된 `bar.ts`에서 확인 할 수 있습니다:

```ts
import {foo} from "./foo";
var bar = foo; // allowed
```
`bar.ts`에서 `import`의 사용은 다른 파일의 것을 사용할 수 있을 뿐 아니라 `bar.ts`를 *모듈*로서 마크해두고 그래서 `bar.ts`안의 선언들은 전역 네임스페이스에 추가되지 않을 것 입니다.

외부 모듈을 사용하는 TypeScript 파일에서 생성되는 JavaScript는 `module`이라는 컴파일러 플래그에 의해 처리됩니다.
