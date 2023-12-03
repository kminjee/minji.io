---
layout: post
title: (React Native) EXC_BAD_ACCESS 에러
categories: Frontend
published: true
---

iOS 시뮬레이터에서 앱 실행 초기에 발생했던 에러로 특정 컴포넌트에서 클릭 이벤트가 발생하면 앱이 종료되는 현상이였다. <br>
특정 컴포넌트에서만 발생해서 코드를 뜯어봤는데 로그도 안 찍혀서 더 의문이었다. <br>
Xcode로 실행하면 혹시나 에러 시점을 알려줄까해서 빌드해봤는데 VSCode나 Flipper에서 발견되지 않은 에러가 났다.

<br>

정확한 원인은 모르겠지만 react-native-reanimated 라이브러리가 영향이 있는 것으로 보인다. <br>
`App.tsx`에 `import 'react-native-reanimated'`를 넣어주니 바로 해결되었다. <br>

참고 링크 https://github.com/software-mansion/react-native-reanimated/issues/4836
