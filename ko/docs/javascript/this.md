## this

함수내의 `this` 키워드에 대한 모든 액세스는 실제로 어떤 함수에서 호출하느냐에 따라 결정됩니다. 이런 상황을 일반적으로 `calling context`라고 합니다.

아래 예제가 있습니다:

```ts
function foo() {
  console.log(this);
}

foo(); // logs out the global e.g. `window` in browsers
let bar = {
  foo
}
bar.foo(); // Logs out `bar` as `foo` was called on `bar`
```

그러므로 `this` 사용에 대해서 유의해야합니다. 호출 컨텍스트에서 `this`를 연결 해제하려면 화살표 함수를 사용하십시오. [자세한 내용은 뒷부분에][화살표 함수].

[화살표 함수]:../arrow-functions.md
