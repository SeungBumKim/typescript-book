## Truthy

JavaScript에는 `truthy`의 개념이 있는데, 예를들어 특정 위치(예, `if`와 `&&` `||` 의 불리언)에서 `true`과 같은 평가를 하는 것입니다. 다음과 같은 내용이 JavaScript의 truthy입니다. 예를들어 `0`이 아닌 다른 숫자값은

```ts
if (123) { // Will be treated like `true`
  console.log('Any number other than 0 is truthy');
}
```

truthy가 아닌경우는 `falsy`로 불립니다.

여기에 참조할만한 간단한 테이블이 있습니다.

| Variable Type   | When it is *falsy*       | When it is *truthy*      |
|-----------------|--------------------------|--------------------------|
| `boolean`       | `false`                  | `true`                   |
| `string`        | `''` (empty string)      | any other string         |
| `number`        | `0`  `NaN`               | any other number         |
| `null`          | always                   | never                    |
| `undefined`     | always                   | never                    |
| Any other Object including empty ones like `{}`,`[]` | never | always |


### 명시적으로 표현

> `!!` 패턴

일반적인 의도는 값을 '불리언'으로 취급하고 그것을 실제로 불리언(`true`|`false` 중 한개로)으로 변환하는 것입니다. 간단하게 `!!`을 앞에 붙이는 것으로 불리언으로 변경할 수 있습니다. 예, `!!foo`. `!`을 *두개*사용합니다. 첫번째 `!`는  변수(여기서는 `foo`)를 부울로 변환하지만 논리를 반전합니다.(*truthy* -`!`> `false`, *falsy* -`!`> `true`). 두번째는 원래 개체의 특성과 일치하도록 다시 토글합니다.(예, *truthy* -`!`> `false` -`!`> `true`).

많은 장소에서 이 패턴을 사용하는 것이 일반적입니다. 예:
```ts
// Direct variables
const hasName = !!name;

// As members of objects
const someObj = {
  hasName: !!name
}

// e.g. ReactJS
{!!someName && <div>{someName}</div>}
```
