---
layout: post
title: (React Native) FlareLane 연동하면서 겪은 에러
categories: Frontend
published: true
---
 
<br>

## 1. NotificationService.swift 파일에 코드 수정할 때
Extention 파일을 생성할 때 내 프로젝트의 이름을 붙여서 만드는데, <br>
가이드 문서에는 'FlareLaneNotificationServiceExtension' 으로 예시를 보여줬다. <br>
그래서 나는 내 프로젝트에 맞게 'ProjectNameNotificationServiceExtension' 이라고 해줬는데 <br>
생성한 NotificationService.swift 파일의 코드를 모두 지우고 아래의 코드로 수정하라고 되어있다.

```swift
import FlareLane

class NotificationService: FlareLaneNotificationServiceExtension {
}
```

나는 여기서 착각을 하고 FlareLane이 제공하는 클래스를 상속해야 하는데 <br> 
가이드 예시의 파일 이름과 똑같아서 내가 만든 파일명으로 적용해야 되는 줄 알고 넣었다가 에러가 나서 헤맸었다.

## 2. Podfile 최상단에 use_frameworks! 추가할 때
use_frameworks! 를 추가해서 Dynamic Framework를 활성화 하라고 알려주고 있다. <br>
문서대로 똑같이 추가해주고 `pod install`을 하는데 마지막에 에러가 나면서 install에 실패했다.

```
target has transitive dependencies that include statically linked binaries: (Flipper-Boost-iOSX)
```

이유는 못 찾았지만, https://github.com/facebook/flipper/issues/3938 이 이슈에서 `use_modular_headers!` 로 바꿔주라는 댓글이 있어서 시도해보니 해결됐다.

## 3. iOS Development Target 버전이 안 맞는다고 할 때
```
The iOS Simulator deployment target 'IPHONEOS_DEPLOYMENT_TARGET' is set to 9.0, but the range of supported deployment target versions is 11.0 to 16.2.99. (in target 'DKPhotoGallery' from project 'Pods')
```
![스크린샷 2024-01-13 오후 10 30 26](https://github.com/mj-automne/mj-automne.github.io/assets/86812090/495280ee-2b96-49e9-b7c9-a201e18d2b96)

이 에러도 의문이긴 한데, 캡쳐에서 보이듯이 Xcode에는 버전이 범위 내로 정상적으로 표시되어 있었다. <br>
어쨌거나 해결 방법은 `Podfile` 에 코드를 추가해주면 되는데 나는 이 코드를 추가하는데도 에러가 나서 시간이 걸렸다. <br>

```swift
post_install do |installer|
 installer.pods_project.targets.each do |target|
  target.build_configurations.each do |config|
   config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '11.0'
  end
 end
end
```

<br>

내 원래 코드는 이런식으로 되어있었는데, 이 안에 수정 코드를 추가하려다 보니까 첫번째 `end`가 `react_native_post_install(` 코드의 끝부분이겠거니 생각하고 그 아래에 추가했다가 에러가 났다. 
또 서치하고 찾아보다가 누군가 코드에 end가 모두 아래에 있는걸 보고 아차 싶어서 고치니 바로 해결됐다.

**잘못된 예**
```swift
post_install do |installer|
    # https://github.com/facebook/react-native/blob/main/packages/react-native/scripts/react_native_pods.rb#L197-L202
    react_native_post_install( <--
      installer,
      config[:reactNativePath],
      :mac_catalyst_enabled => false
    )
    __apply_Xcode_12_5_M1_post_install_workaround(installer)
  end

  # 첫번째 end 바로 아래에 여기다 추가를 했다가 에러가 남.
   installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
     config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '11.0'
    end
  end 
end
```

**올바른 예**
```swift
post_install do |installer|
    # https://github.com/facebook/react-native/blob/main/packages/react-native/scripts/react_native_pods.rb#L197-L202
    react_native_post_install( <--
      installer,
      config[:reactNativePath],
      :mac_catalyst_enabled => false
    )
    __apply_Xcode_12_5_M1_post_install_workaround(installer)

   installer.pods_project.targets.each do |target|
      target.build_configurations.each do |config|
        config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '11.0'
      end
    end
  end
end
```

## 4. Could not build module 'Darwin' 에러
![스크린샷 2024-01-13 오후 9 56 20](https://github.com/mj-automne/mj-automne.github.io/assets/86812090/42b5faf7-0dd9-4c1d-b204-61b16e92aec5)

이 에러도 처음 보는데, flareLane을 설치하면서 처음 난거라 falreLane하고 flipper가 안 맞는거라도 있는건지....<br>

```swift
# flipper_config = ENV['NO_FLIPPER'] == "1" ? FlipperConfiguration.disabled : FlipperConfiguration.enabled
```

꽤 많은 사람들이 해결한 방법은 `Podfile` 에서 해당 코드를 주석하는건데 나는 주석을 하면 다른 에러가 발생했다. <br>
그래서 주석 대신 FlipperConfiguration 가 활성화되지 않게 disabled만 남겨뒀다. <br>

```swift
flipper_config = FlipperConfiguration.disabled
```
