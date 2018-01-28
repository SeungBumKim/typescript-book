# Null 은 좋지 않습니다. 
JavaScript(및 확장 TypeScript)에는 `null` and `undefined` 두가지 type이 있습니다. 그것들은 다른 것을 *의미*합니다:

* 무언가 초기화 되지 않았다:  `undefined`
* 무언가 현재 활용가능하지 않다: `null`

대부분의 다른 언어에서는 한가지만(보통 `null`로 불립니다.) 있습니다. 기본적으로 JavaScript는 초기화되지 않은 변수/매개변수/속성을 `undefined`로 평가할 것이기 때문에 (선택을하지 않아도) *사용할 수 없는* 상태로 사용하고 `null`을 신경 쓸 필요가 없습니다.

## 현재 개발상황에서의 토론
TypeScript팀에서는 `null`을 사용하지 않습니다: [TypeScript coding guidelines](https://github.com/Microsoft/TypeScript/wiki/Coding-guidelines#null-and-undefined) 그리고 이는 어떠한 문제도 야기시키지 않습니다.
Douglas Crockford는 `null`은 좋지 않고 생각합니다. [`null` is a bad idea](https://www.youtube.com/watch?v=PSGEjv3Tqo0&feature=youtu.be&t=9m21s). 그리고 우리도 모두 `undefined`을 사용합니다.

## `null` 스타일 코드베이스 다루기
코드베이스가 `null`을 줄 수 있는 다른 API와 상호 작용할 경우 `== undefined`(`===`대신에)로 검사합니다. 이것을 사용하면 다른 잠재적인 잘못된 값에 대해서도 안전합니다.
```ts
/// Image you are doing `foo == undefined` where foo can be one of:
console.log(undefined == undefined); // true
console.log(null == undefined); // true
console.log(0 == undefined); // false
console.log('' == undefined); // false
console.log(false == undefined); // false
```

## 추가 팁

### `undefined`의 명시적 사용의 제한
또한 TypeScript를 사용하면 구조 대신에 값과 별도로 구조를 문서화 할 수 있는 기회가 주어지기 때문에: 

```ts
function foo(){
  // if Something
  return {a:1,b:2};
  // else
  return {a:1,b:undefined};
}
```
type 어노테이션을 사용해야합니다:
```ts
function foo():{a:number,b?:number}{
  // if Something
  return {a:1,b:2};
  // else
  return {a:1};
}
```

### Node스타일의 콜백
Node스타일의 콜백 함수(예, `(err,somethingElse)=>{ /* something */ }`)는 에러 상황이 아니라면, 보통 호출된 `err`를 `null`로 합니다. 보통 참값인지 확인해 사용합니다:

```ts
fs.readFile('someFile', 'utf8', (err,data) => {
  if (err) {
    // do something
  }
  // no error
});
```
자신의 API를 만들때 일관성을 위해 이 경우에는 `null`을 사용하는 것이 좋습니다. promise에서 자신의 API에 대한 모든 참값을 살펴 봅니다. 그래서 이 경우에는 실제로 오류값을 신경 쓰지 않아도됩니다.(`.then`과 `.catch`로 처리 가능합니다.)

### *유효성*을 나타내는 수단으로 `undefined`를 사용않기

예를들어, 이런 끔찍한 함수가 있습니다: 

```ts
function toInt(str:string) {
  return str ? parseInt(str) : undefined;
}
```
다음과 같이 하면 더 나아질 것입니다:
```ts
function toInt(str: string): { valid: boolean, int?: number } {
  const int = parseInt(str);
  if (isNaN(int)) {
    return { valid: false };
  }
  else {
    return { valid: true, int };
  }
}
```
