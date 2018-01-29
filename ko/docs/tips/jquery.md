## JQuery 팁

노트: 이 팁을 위해서는 `jquery.d.ts` 파일을 설치해야 합니다.

### 빠르게 새 플로그인 정의하기

`jquery-foo.d.ts`를 아래와 같이 생성하면 됩니다:

```ts
interface JQuery {
  foo: any;
}
```

그리고 `$('something').foo({whateverYouWant:'hello jquery plugin'})`와 같이 사용하면 됩니다.
