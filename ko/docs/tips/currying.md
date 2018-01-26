## Currying

두꺼의 화살표함수의 체인을 사용하시면 됩니다:

```ts
// A curried function
let add = (x: number) => (y: number) => x + y;

// Simple usage
add(123)(456);

// partially applied
let add123 = add(123);

// fully apply the function
add123(456);
```
