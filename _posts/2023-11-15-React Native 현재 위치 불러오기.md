---
layout: post
title: React Native 현재 위치 불러오기
categories: ReactNative
published: false
---

React Native에서 지도에 현재 내 위치를 띄우는 기능을 구현하였습니다. <br>
react-native-nmap 라이브러리를 활용하여 네이버 맵뷰를 띄웠고 그 위에 사용자의 현재 위치를 마커로 표시했습니다.

<br>

맵의 경우에는 개발중인 앱에서 메인 탭과 지도 탭에 보여집니다. <br>
따라서 재사용을 위해 컴포넌트로 분리하였습니다. <br>
맵 컴포넌트에는 다양한 옵션들이 있습니다. <br>
홈 탭과 지도 탭에 보여지는 맵뷰의 기능이 모두 같지 않기 때문에 여러 옵션들을 만들어두었습니다.

<br>

홈 탭에서는 현재 위치로 이동하는 버튼이 필요하기 때문에 `showMyLocationButton` 옵션을 추가했습니다.

```tsx
<JDMap height={"500px"} showsMyLocationButton scrollGesturesEnabled={false} />
```

<br>

맵 컴포넌트에서는 `showMyLocationButton` 이 활성화 되었을 경우 맵뷰에 버튼이 보여집니다. <br>
버튼을 클릭하면 현재 위치의 좌표값을 받아 상태를 저장합니다. 그리고 상태값이 변함에 따라 맵이 이동됩니다.

```tsx
const handleLocationRequest = () => {
  // ...

  if (!isActivated) {
    getLocation().then((coord) => setLocation(coord as CoordinateType));
  }
};
```

<br>

`getLocation()` 을 호출하면 권한 상태를 확인하고 그에 따라 좌표값을 반환하는 함수를 호출합니다. <br>
만약 거부 상태라면 함수는 즉시 종료되고 얼럿창을 띄웁니다.

```tsx
// 권한을 요청하고 상태를 반환
const requestLocationPermission = async (): Promise<AuthorizationResult> => {
  return await Geolocation.requestAuthorization("always");
};
```

```tsx
// 권한 상태에 따른 처리
const permissionStatus = await requestLocationPermission();

switch (permissionStatus) {
  case "denied":
  case "disabled":
    return presentToast(
      "위치 권한이 거부 또는 비활성 상태입니다. 기기 설정에서 확인해주세요."
    );
  case "granted":
  case "restricted":
    return fetchLocation();
}
```

```tsx
// 위치 조회 후 좌표값을 반환
const position: GeoPosition = await new Promise((resolve, reject) => {
  Geolocation.getCurrentPosition(
    (position) => resolve(position),
    (error) => reject(error)
  );
});

return {
  latitude: Number(JSON.stringify(position.coords.latitude)),
  longitude: Number(JSON.stringify(position.coords.longitude)),
};
```

현재 위치 관련한 비즈니스 로직들은 모두 util로 분리하였는데, <br>
이유는 맵 컴포넌트에서는 위치를 가져오는 로직보다는 뷰를 보여주는 역할에 집중하게 하기 위함입니다.

<br>

또 `getLocation()` 을 호출하는 과정에는 권한 요청과 위치 조회 함수들이 숨겨져 있는데, <br>
비동기 코드를 숨김으로써 컴포넌트에서는 단순히 `getLocation()` 함수만 호출하면 되므로 <br>
컴포넌트의 역할이 명확해지고 위치 관련 라이브러리에 대한 의존성을 줄일 수 있습니다.

<br>

총 세 가지의 함수로 분리가 되었는데 담당하는 역할에 집중할 수 있도록 각 목적에 맞게 따로 분리하였습니다.
