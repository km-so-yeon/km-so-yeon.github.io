---
title: "[Algorithm] 코딩테스트 간단 정리"
author: "김소연"
date: 2022-10-12 21:00:00 -0500
categories: [Computer Science, Algorithm]
tags: [Algorithm]
---



코딩테스트를 앞두고 아주 간단하게 정리하기 위해 작성한 글입니다.



## 코딩테스트 풀이 순서

1. 문제에서 구하는 것을 순서대로 해보기

- 모든 것을 다해봐야 함. n이 작거나 오래걸리지 않음 → 완전탐색
- 앞에 것에서 영향을 받음 → DP(순서대로 구하기, 배열로 저장, 반복문 사용/재귀)
- 모든 것을 다하기에는 너무 많이 해야 함 → 이분탐색(찾아야하는 값을 범위로 두기 left right, 범위를 줄이는 기준 정하기, 반복문 사용)

2. 가장 좋은 기준이 있는 경우 → 그리디(기준으로 정렬)
3. 경로찾기, 백트래킹 → DFS(재귀함수)
4. 최단거리 → BFS(deque 사용)
5. 연속된 리스트 찾기 → 투포인터(start end, for문, while문 사용)



#### 이 외

- 해시맵
- Trie 알고리즘
- 유니온 파인드
- 크루스칼 알고리즘
- 트리의 지름 구하기



## 코딩테스트 문제집

- https://github.com/encrypted-def/basic-algo-lecture/blob/master/workbook.md
- https://github.com/tony9402/baekjoon




### 참고📎

- https://myeongmy.tistory.com/55