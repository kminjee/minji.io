---
layout: post
title: (React Query) useInfiniteQuery
categories: Frontend
published: true
---

<br>

```typescript

const MyComponent = () => {
  const { data, hasNextPage, isFetchingNextPage, fetchNextPage } = useInifiniteQuery({
    initialPageParam: 1,
    getNextPageParam: (lastPage, allPages) => { 
      return allPages.length < 4 && allPages.length + 1;
    }
  });

  return (
    <View>
      {data?.pages.map((group, idx) => ({
        <View>{/* ... */}</View>
      }))}
    </View>
  )
}

```

### useInfiniteQuery 반환
데이터를 추가적으로 받아오는 기능을 구현할 때 사용 (무한 스크롤 등)
- `data.page` 모든 페이지 데이터를 포함하는 배열
- `data.pageParams` 모든 페이지 매개변수를 포함하는 배열
- `fetchPreviousPage` 이전 페이지 데이터 패칭
- `isFetchingPreviousPage` fetchPreviousPage 메서드가 패칭되는 동안 true, 패칭 이후 false
- `hasPreviousPage` 이전 페이지가 있으면 true, 없으면 false
- `fetchNextPage` 다음 페이지 데이터 패칭
- `isFetchingNextPage` fetchNextPage 메서드가 패칭되는 동안 true, 패칭 이후 false
- `hasNextPage` 다음 페이지가 있으면 true, 없으면 false

<br>

### useInfiniteQuery 옵션
- `initialPageParam` 첫 페이지를 가져올 때 사용할 페이지 매개변수 (필수값)
- `getNextPageParam: (lastPage, allPages)` 페이지를 증가 (필수값)
  - `lastPage` 마지막에 가져온 페이지 목록
  - `allPages` 현재까지 가져온 모든 페이지 데이터 
