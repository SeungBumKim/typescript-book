# 당신의 JavaScript는 TypeScript입니다.

*특정 문법*을 *JavaScript*로 변경해주는 컴파일러를 제공하는 많은 경쟁자들이 있었습니다.(지금까지 경쟁하는 곳도 포함해서요..) TypeScript는 다르다는 것을 *당신의 JavaScript는 TypeScript입니다.* 에서 다룰 것 입니다. 아래 도표가 있습니다:

![](https://raw.githubusercontent.com/basarat/typescript-book/master/images/venn.png)

위 도표는 당신이 JavaScript를 배워야한다는 것을 의미합니다. (좋은 소식은 **JavaScript만** *배우면된다는 것입니다.*) ScpeScript는 단지 JavaScript상에서 *우수한 설명서*를 제공하는 모든 방법을 표준화하고 있는 것 입니다.

* 그저 새로운 문법을 제공하는 것은 버그수정에 도움이 되지 않습니다.(CoffeeScript를 보세요.)
* 새로운 언어는 당신을 추상화시켜서 당신의 현재 작업 하는 것들과 커뮤니티에서 멀어지게 합니다.(Dart를 보세요.)

TypeScript는 그저 JavaScript의 문서화된 수준입니다.

## JavaScript를 개선하였습니다.

TypeScript는 JavaScript에서 동작하지 않는 부분은 막으려고 할 것입니다.(그래서 이러한 부분들은 기억할 필요가 없습니다):

```ts
[] + []; // JavaScript ""(의미없는 값)를 부여하지만, TypeScript 에러를 낼 것 입니다.

//
// 아래 다른것들도 JavaScript에서 무의미합니다.
// - 실행 에러는 발생하지 않지만(디버깅을 힘들게 만듭니다.)
// - 그러나 TypeScript에서는 에러를 발생시켜 (디버깅을 할 필요조차 없게 만들어줍니다.)
//
{} + []; // JS : 0, TS Error
[] + {}; // JS : "[object Object]", TS Error
{} + {}; // JS : NaN or [object Object][object Object] depending upon browser, TS Error
"hello" - 1; // JS : NaN, TS Error

function add(a,b) {
  return
    a + b; // JS : undefined, TS Error 'unreachable code detected'
}
```

근본적으로 TypeScript는 JavaScript를 걸러주는 역할을 합니다. *type 정보*가 없는 다른 린터들보다 더 훌륭하게 동작 할 것입니다.

## 여전히 JavaScript를 학습할 필요가 있습니다.

TypeScript가 *JavaScript로 작성할 수 있다는 것*이 매우 실용적이라고 언급했기 때문에, 자바 스크립트에 관해서도 알아야 할 것 들이 있습니다. 다음부터 이에 관해 이야기해 봅시다.

> 노트: TypeScript는 JavaScript를 포함하고 있습니다. 실제로 컴파일러나 IDE에서 사용할 수 있는 형식으로만 작성되어 있으면 됩니다.
