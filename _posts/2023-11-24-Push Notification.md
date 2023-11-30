---
layout: post
title: 푸시 알림 (Push Notification / OneSignal & Firebase)
categories: Programming
published: true
---

## 푸시 알림이란?

모바일 앱에서 폰으로 메시지를 전송하는 기능이다. <br>
앱이 실행되지 않은 상태이거나 휴대폰이 잠겨있더라도 알림을 받을 수 있다. <br>
푸시 알림을 받기 위해서는 사용자가 모바일 앱 내에서 푸시 알림 수신을 허용해야 하는데 <br>
일반적으로 앱 초기 설치에 옵트인 요청을 보내 알림을 허용하거나 거부할 수 있도록 한다. <br>
기본적으로 기기 플랫폼(iOS, Android)에는 자체 OSPNS(운영체제 푸시 알림 서비스)가 있다.
<br>

이런 OSPNS에는 푸시 알림에 텍스트나 이미지, 앱 배지 및 사운드를 포함하도록 허용한다. <br>
OSPNS의 일종인 푸시 알림 서비스에는 대표적으로 FCM(Firebase Coud Mssaging)이 있다.

<br>

## 푸시 알림의 좋은점

- 알림을 통해 앱 리텐션을 높일 수 있다.
- 사용자는 알림 수신을 선택할 수 있으며 유연한 알림 기본 설정으로 알림 받는 방법을 제어할 수 있다.

<br>

## 푸시 알림 서비스 비교 (OneSignal vs Firebase)

### OneSignal

- 가입자 수를 기준으로 가격을 산정 (1만 가입자까지 무료 요금제 제공, API 콜 비용 없음)
- 저장된 사용자 데이터를 불러와 실시간으로 동작하여 모든 사용자가 명단에 등록됨
- 이미지 알림 무료

### Firebase

- 메시지 전송량에 따른 비용 발생 및 사운드, 액션버튼 등 추가 옵션에도 비용 발생
- 앱을 한 번 이상 실행한 유저만 명단에 등록할 수 있음 (따라서 일부 사용자가 제외될 수 있음)
- 이미지 알림 추가 비용 지불

<br>

## React Native에서 OneSignal 적용하는 방법

[Implement push notifications in React Native with OneSignal - LogRocket Blog](https://blog.logrocket.com/implement-push-notifications-react-native-onesignal/)

### 1. 환경 세팅

- Node & Yarn
- Firebase 계정
- OneSignal 계정
- Apple 개발자 계정
- Xcode 12 이상
- iOS 9 이상 기기 (Simulator에서는 푸시 알림을 테스트 할 수 없음)
- Google Play Service 앱이 있는 Android 4 이상 기기 or Emulator

### 2. iOS용 푸시 알림 설정

1. 앱 ID 및 프로비저닝 프로필 만들기
