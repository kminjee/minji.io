---
layout: post
title: (React Native) Android Emulator 'Network Reqeust Failed' 에러
categories: Frontend
published: true
---

<br>

### Android Emulator 'Network Reqeust Failed'
iOS simulator에서 API 요청에 문제가 없었는데, Android Emulator에서는 요청 자체가 가지 않는 문제를 확인했다. <br>
서버에서도 요청 받은 로그 자체가 없어서 에뮬레이터의 문제인가 싶어 안드로이드 공식문서를 찾아보니,

에뮬레이터는 자체적으로 격리되어 있기 때문에 외부의 다른 네트워크를 직접 확인할 수가 없다고 한다. <br>
따라서 인터넷에 직접적으로 연결되거나 엑세스하지 않고 가상의 라우터나 방화벽을 통해 연결하는데, <br>
에뮬레이터 인스턴스의 가상 라우터는 10.0.2.xx 형식으로 된 네트워크 주소 공간을 관리한다.

`개발 머신의 주소 127.0.0.1은 에뮬레이터의 루프백 인터페이스에 상응합니다. 개발 머신 루프백 인터페이스에서 실행 중인 서비스에 액세스하려면 특수 주소 10.0.2.2를 대신 사용하세요.`

공식 문서를 보고 주소를 `10.0.2.2`로 고치니 정상적으로 API 호출이 되었다.
