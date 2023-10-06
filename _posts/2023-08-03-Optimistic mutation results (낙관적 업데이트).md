---
layout: post
title: Optimistic mutation results (낙관적 업데이트)
categories: Frontend
published: true
---

<br/>

### Optimistic mutation results (낙관적 업데이트)

서버로부터 응답받기 전 성공했다는 가정 하에 클라이언트에서 먼저 데이터를 변경하고 UI 업데이트 해준다. <br/>
낙관적 업데이트를 활성화하기 위해 mutation 실행 시 mutate 함수에 optimisticResponse 옵션을 제공한다. <br/>
`optimisticResponse` 값은 서버에서 응답하는 형태와 일치하다.

### **Optimistic mutation lifecycle**

1.  - apollo client 캐시는 `id`, `__typename` 필드 값을 사용해 고유 캐시 식별자를 생성한다.
    - mutate를 실행하면 지정한 필드값을 가진 개체를 캐시에 저장한다.
    - 동일한 식별자는 기존에 캐싱된 데이터를 덮어쓰지 않고 별도로 optimistic 버전을 저장한다.
      `optimisticResponse` 가 잘못된 경우에 기존에 캐싱된 데이터를 유지할 수 있기 때문이다.

1.  - apollo client는 모든 활성화된 쿼리를 알려주고 자동으로 업데이트 되며 관련 구성 요소가 다시 렌더링되어 낙관적 데이터를 반영한다.
    - 따라서 네트워크 요청이 필요하지 않기 때문에 사용자에게 즉각적으로 화면을 업데이트하여 보여줄 수 있다.
    - apollo client 캐시는 데이터의 optimistic 버전을 제거한다. 또한 서버에서 반환된 값으로 정식 캐시 버전을 덮어쓴다.
    - 연결된 컴포넌트는 다시 렌더링되지만 서버의 응답이 우리의 optimisticResponse 와 일치하면 사용자에게 표시되지 않는다.

### 새로운 데이터를 추가하는 mutation의 경우?

- 아직 클라이언트가 새 데이터의 id값을 가지고 있지 않기 때문에 optimistic 결과를 저장할 수 있도록 임시로 id값을 제공해야한다.
- 서버가 새 데이터의 실제 id값을 응답하면 optimistic 개체는 제거되고 응답받은 개체로 캐싱된다.
