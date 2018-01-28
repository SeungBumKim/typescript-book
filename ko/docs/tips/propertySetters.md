## 속성 설정자의 사용 제한

setter/getters에 대한 명시적인 set/get 함수 (예 :`setBar` 및 `getBar` 함수)를 선호합니다.

다음 코드를 보겠습니다:

```ts
foo.bar = {
    a: 123,
    b: 456
};
```

setter/getters가 있는 모습을 보면:

```ts
class Foo {
    a: number;
    b: number;
    set bar(value:{a:number,b:number}) {
        this.a = value.a;
        this.b = value.b;
    }
}
let foo = new Foo();
```

이것은 속성 설정자의 *좋은* 사용하지 않습니다. 첫 번째 코드 샘플을 읽는 사람은 변경될 모든 것에 대한 컨텍스트가 없습니다. 
`foo.setBar (value)`를 호출하는 누군가가 `foo`에서 어떤 것이 바뀔지 모른다는 생각을 가질 수 있습니다.

> 보너스 포인트: 다른 기능를 사용하면 참조 찾기가 더 잘 작동합니다. TypeScript 도구에서는 getter 또는 setter에 대한 참조를 찾으면 *모두*를 얻지만 명시적 함수 호출에서는 관련 함수에 대한 참조만 얻을 수 있습니다.
