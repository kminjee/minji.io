---
layout: post
title: 푸시 알림 (Push Notification)
categories: Programming
published: true
---

### 푸시 알림이란?

모바일 앱에서 폰으로 메시지를 전송하는 기능이다. <br>
앱이 실행되지 않은 상태이거나 휴대폰이 잠겨있더라도 알림을 받을 수 있다. <br>
푸시 알림을 받기 위해서는 사용자가 모바일 앱 내에서 푸시 알림 수신을 허용해야 하는데 <br>
일반적으로 앱 초기 설치에 옵트인 요청을 보내 알림을 허용하거나 거부할 수 있도록 한다. <br>
기본적으로 기기 플랫폼(iOS, Android)에는 자체 OSPNS(운영체제 푸시 알림 서비스)가 있다. <br>
이런 OSPNS에는 푸시 알림에 텍스트나 이미지, 앱 배지 및 사운드를 포함하도록 허용한다. <br>
OSPNS의 일종인 푸시 알림 서비스에는 대표적으로 FCM(Firebase Coud Mssaging)이 있다.

<br>

### 푸시 알림의 좋은점

- 알림을 통해 앱 리텐션을 높일 수 있다.
- 사용자는 알림 수신을 선택할 수 있으며 유연한 알림 기본 설정으로 알림 받는 방법을 제어할 수 있다.

<br>

### 푸시 알림 앱 서비스에 등록하는 방법

포스트 참고 | [ReactNative, 푸시 알림 완벽하게 구현하기](https://honeystorage.tistory.com/306)
