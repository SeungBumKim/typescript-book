## 함수에서의 상태
다른 프로그래밍 언어의 공통적인 특징은 `static` 키워드를 사용하여 함수안의 변수의 수명(*범위*가 아님)을 연장시킵니다. 이것을 만족하는`C` 샘플이 있습니다:

```c
void called() {
    static count = 0;
    count++;
    printf("Called : %d", count);
}

int main () {
    called(); // Called : 1
    called(); // Called : 2
    return 0;
}
```

JavaScript(또는 TypeScript)에는 함수의 statics가 없으므로 지역 변수를 감싸는 다양한 추상화를 사용하여 동일한 결과를 얻을 수 있습니다. 예를들면, `class`를 사용할 수 있습니다:

```ts
const {called} = new class {
    count = 0;
    called = () => {
        this.count++;
        console.log(`Called : ${this.count}`);
    }
};

called(); // Called : 1
called(); // Called : 2
```

> C++ 개발자는 또한 `functor`(`()`연산를 오버라이드한 클래스)라고 부르는 패턴을 사용하여 이것을 시도하고 달성합니다.
