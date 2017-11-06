## Null과 Undefined

둘 다 다루어야할 내용입니다. 아래 `==`동작을 확인해 보십시오.

```ts
/// Imagine you are doing `foo.bar == undefined` where bar can be one of:
console.log(undefined == undefined); // true
console.log(null == undefined); // true
console.log(0 == undefined); // false
console.log('' == undefined); // false
console.log(false == undefined); // false
```
보통은 둘 사이를 구분해서 하기를 원하지 않을 것입니다. `undefined`과 `null` 중에서 사용을 한다면 `== null`을 추천합니다. 

## undefined

`== null`을 사용해한다고 적은 것을 기억하실 겁니다. 물론 (방금전에 적었기 때문에) 기억 할 것입니다. 이 방식을 루트레벨(전역/최상위 오브젝트?)에서는 사용하지 마십시오. strict모드 상태에서 `foo` 변수를 사용하는데 `foo` 변수가 정의되어있지 않으면 `ReferenceError` **예외**가 발생할 것이고, 모든 스택은 해제될 것입니다.

> strict모드를 사용해야 합니다.. 그리고 사실 모듈들을 사용한다면 TS 컴파일러는 strict모드를 추가할 것입니다... 관련 내용은 이책의 뒷부분에 언급 할 것이기에 지금 자세히 언급하지는 않겠습니다. :)

따라서 변수가 *전역* 수준에서 정의되었는지 여부를 확인하려면 일반적으로 `typeof`를 사용합니다. :

```ts
if (typeof someglobal !== 'undefined') {
  // someglobal is now safe to use
  console.log(someglobal);
}
```
