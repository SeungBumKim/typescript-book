## 컴파일 컨텍스트
컴파일 컨텍스트는 기본적으로 TypeScript가 파싱하고 분석하여 무엇이 유효한지 아닌지를 결정하는 파일을 그룹화하는 용어입니다. 파일에 대한 정보와 함께 컴파일 컨텍스트에는 *컴파일러 옵션*에 대한 정보가 들어 있습니다. 이 논리적 그룹(우리는 *project*이라는 용어를 사용하는 것을 좋아합니다)을 정의하는 좋은 방법은`tsconfig.json` 파일을 사용하는 것입니다.
