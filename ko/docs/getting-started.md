# TypeScript 시작하기

TypeScript는 JavaScript로 컴파일됩니다. JavaScript는 실제로 브라우저나 서버에서 실행될 것 입니다. 그래서 다음 내용을 필요로 합니다 :

* TypeScript 컴파일러 (활용 가능한 OSS  [in source](https://github.com/Microsoft/TypeScript/)와 [NPM](https://www.npmjs.com/package/typescript))

* TypeScript 에디터 (원하시면 노트패드를 사용하셔도 됩니다. 저자는 [alm 🌹](http://alm.tools)을 사용합니다. 이 외에 [지원하는 다양한 IDE들이 있습니다]( https://github.com/Microsoft/TypeScript/wiki/TypeScript-Editor-Support))


![](https://raw.githubusercontent.com/alm-tools/alm-tools.github.io/master/screens/main.png)


## TypeScript 버전

*안정적인* TypeScript 컴파일러를 사용 않고, 아직 버전에 연결이 안 된 많은 새로운 내용들이 책에 제시 될 것입니다. **시간이 지남에 따라 컴파일러 테스트 셋에서 더 많은 버그를 잡을 수 있기 때문에** 야간 빌드 버전 사용을 추천합니다.
다음과 같은 명령어로 설치 할 수 있습니다.

```
npm install -g typescript@next
```

이제 명령어 'tsc'는 최신이고, 가장 훌륭할 것 입니다. 다양한 IDE에서 이와같은 기능을 지원합니다. 예를들면,

* `alm`은 항상 최신 TypeScript와 함께 제공됩니다.
* vscode에 다음과 같은 내용으로 `.vscode / settings.json`을 생성하여 지정한 버전을 사용하도록 요청할 수 있습니다.

```json
{
  "typescript.tsdk": "./node_modules/typescript/lib"
}
```

## 소스코드 가져오기

이 책의 소스는 github 저장소인 https://github.com/SeungBumKim/typescript-book/tree/master/ko/code 에서 볼 수 있습니다. 대부분의 샘플 코드는 alm으로 복사하여 그대로 사용할 수 있습니다. 추가 설정이 필요한 샘플 코드(예: npm 모듈)의 경우, 코드가 시작되기 전에 해당 링크를 제공할 것 입니다. 예를들면 아래와 같습니다.

`this/will/be/the/link/to/the/code.ts`
```ts
// This will be the code under discussion
```


개발환경 설치가 끝났다면, TypeScript 구문을 보시면됩니다.
