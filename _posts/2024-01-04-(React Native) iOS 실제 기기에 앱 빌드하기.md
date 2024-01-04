---
layout: post
title: (React Native) iOS 실제 기기에 앱 빌드하기
categories: Frontend
published: true
---
 
<br>

## 앱 빌드 단계
### 1. `yarn add ios-deploy` 설치
ios-deploy를 설치하는 이유는 실제 디바이스 기기를 구동하기 위해서 이다.
### 2. 기기 USB 연결
### 3. 프로젝트 실행 및 iOS 실행
첫 실행에는 새 터미널에서 metro가 실행된다.

<br>

## 앱 빌드 중에 발생할 수 있는 에러
### 1. 기기에 앱이 설치되었는데 앱이 열리지 않는 경우
기기 설정 -> 일반 -> VPN 및 기기 관리 -> 개발 앱 신뢰 선택 <br>

<img src="https://github.com/mj-automne/mj-automne.github.io/assets/86812090/5b668f34-bf9f-4b6c-affd-cfc7e1fcbff2" width="300" />
<img src="https://github.com/mj-automne/mj-automne.github.io/assets/86812090/54fea8c5-e2b7-4960-9f40-5ee44bad05d4" width="300" />
<img src="https://github.com/mj-automne/mj-automne.github.io/assets/86812090/742dc7b4-dcb2-489f-80cd-15744b0e99bf" width="300" />

### 2. 앱 실행 시 'No bundle URL present'가 뜨는 경우
[[React-Native Error Log] No bundle URL present 이슈](https://velog.io/@haerim95/React-Native-Error-Log-No-bundle-URL-present-%EC%9D%B4%EC%8A%88) <br>
1. Xcode 실행 -> TARGETS 에서 BuildPhases 탭 클릭
2. Copy Bundle Resources 에서 `main.jsbundle`이 있는지 확인
3. `main.jsbundle`이 존재하지 않으면 프로젝트 루트 경로에서 아래 명령어 입력 <br>
   `yarn react-native bundle --entry-file='index.js' --bundle-output='./ios/main.jsbundle' --dev=false --platform='ios'`
4. 다시 Xcode로 돌아와서 Copy Bundle Resources 아래에 + 클릭 -> Add Other... 클릭
5. ios 폴더 안에 main.jsbundle 파일 추가
6. 추가 후에 다시 프로젝트를 실행해보면 에러가 해결된 것을 확인할 수 있다.
