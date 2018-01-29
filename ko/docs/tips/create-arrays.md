## 배열 생성

빈 배열을 생성하는 것은 매우 쉽습니다:

```ts
const foo:string[] = [];
```

만약 어떠한 컨텐츠로 미리 값을 넣어서 생성하고 싶으면 ES6의 `Array.prototype.fill`를 사용하세요:

```ts
const foo:string[] = new Array(3).fill('');
console.log(foo); // ['','',''];
```
