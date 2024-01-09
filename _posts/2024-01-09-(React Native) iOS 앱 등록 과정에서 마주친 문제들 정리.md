---
layout: post
title: (React Native) iOS 앱 등록 과정에서 마주친 문제들 정리
categories: Frontend
published: true
---

<br>

### 1. 애플 앱 생성 후 팀원과 공유
애플 개발자 멤버십 승인을 받고 App Store Connect에서 앱을 생성했다. 사용자 및 액세스 단계에서 팀원 계정을 등록하는 데 까지는 문제가 없다. <br>
처음에 사용자 액세스에 팀원을 추가하는 걸로 끝나는 줄 알았는데 팀원이 Xcode에서 앱을 빌드하려고 할 때 문제가 발생했다. <br>

![스크린샷 2024-01-09 오후 9 07 13](https://github.com/mj-automne/mj-automne.github.io/assets/86812090/d646a9f4-f1bc-46c8-990f-adb29c1ce26d)

App Store Connect에서 생성한 앱은 고유 번들 아이디가 존재한다. 앱 빌드를 위해 해당 번들 아이디로 접근하려면 `Provisioning Profile`과 `Certificate`가 필요했다. <br>
이 프로비저닝 프로필과 인증서를 공유하기 위해 서치를 했는데, 처음에는 앱을 생성한 계정이 조직 멤버십이 아닌 개인 멤버십이기 때문에 어떻게 공유할 수 있는지 의문이었다. <br>
다행히 아래 블로그에서 친절하게 설명이 되어있어서 인증서를 공유하는것 까지는 잘 되었다. <br>

[협업 시 팀원과 Provisioning Profile 및 Certificate 공유하기](https://youngkdevlog.tistory.com/46#%ED%--%--%EB%A-%-C%EB%B-%--%EC%A-%--%EB%-B%-D%--%ED%--%--%EB%A-%-C%ED%-C%-C%EC%-D%BC-Provisioning%--Profile-%--%EC%-D%B-%EB%-E%--%-F)

<br>

### 2. 프로비저닝 프로필 다운로드 후 Xcode에 등록
프로비저닝 프로필 다운로드를 받고 Xcode에 import를 했는데 프로비저닝 프로필에 인증서가 포함되지 않았다는 메시지가 떴다. <br>

![스크린샷 2024-01-09 오후 9 20 10](https://github.com/mj-automne/mj-automne.github.io/assets/86812090/3790586c-4ae6-4869-9c9f-f48b83608af3)

분명 프로필을 생성할 때 certificates에 체크를 했는데 포함되지 않았다고 해서 프로필을 다시 들어가보니 무슨 차이인지는 모르겠지만 Development 하나가 체크가 안 되어 있었다. <br>
사실 문제가 발생했을 때는 똑같은 이름의 인증서가 여러개가 있었는데 그 중에 하나만 체크해서 하다보니 애플에서 원하는 인증서가 아니였던 모양이다. <br>
너무 헷갈려서 기존에 만들었던 걸 다 지우고 필요한 인증서만 딱 만들고 다시 1번 과정을 거쳐서 해결하니 잘 되었다. <br>

![스크린샷 2024-01-09 오후 9 23 51](https://github.com/mj-automne/mj-automne.github.io/assets/86812090/1f517e6f-a066-4895-8f78-9c2f79d11bd9)

<br>

### 3. 앱 번들 아이디 변경
애플에 앱을 등록하면서 번들 아이디를 새로 생성했는데 프로젝트에도 변경된 번들 아이디로 변경하고 앱을 빌드했는데 Bundle ID가 잘못됐다는 얼럿이 뜨면서 화면을 넘어갈 수가 없었다. <br>
`info.plist`에 env variable이 안 먹히는건가? 라고 추측도 해보고 애플 쪽의 문제일 것이라고 생각했지만....<br>
서치해보니 해당 에러는 Naver Map에서 발생했을 때 나타나는 에러였고 Naver Map에 등록한 앱 정보를 보니 이전 apple 번들 아이디로 되어있었다. <br>
새로 생성한 번들 아이디로 고치니 무슨 일이 있었냐는 듯...바로 뷰가 로드됐다..<br>

