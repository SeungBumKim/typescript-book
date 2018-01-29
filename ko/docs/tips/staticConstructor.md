# TypeScript에서의 정적 생성자

TypeScript `class`(JavaScript `class`와 같은)는 정적 생성자를 가질 수 없습니다. 그러나 단지 그것을 호출함으로써 동일한 효과를 얻을 수 있습니다:

```ts
class MyClass {
    static initialize() {
        // Initialization
    }
}
MyClass.initialize();
```
