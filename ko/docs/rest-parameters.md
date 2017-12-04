### 나머지 매개변수
나머지 매개변수는(마지막 인수에 `...argumentName`와 같이 적는것) 함수에서 다중의 인수를 빠르게 받아들일 수 있게 해주고 배열로 받아서 쓸 수 있습니다. 이는 아래의 예에서 설명됩니다.

```ts
function iTakeItAll(first, second, ...allOthers) {
    console.log(allOthers);
}
iTakeItAll('foo', 'bar'); // []
iTakeItAll('foo', 'bar', 'bas', 'qux'); // ['bas','qux']
```

나머지 매개변수는 어떤 함수에서는 `function`/`()=>`/`class member`와 같이 사용됩니다.
