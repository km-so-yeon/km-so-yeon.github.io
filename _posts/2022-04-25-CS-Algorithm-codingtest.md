---
title: "[Python] 코딩테스트 유형 정리"
author: "김소연"
date: 2022-04-25 21:00:00 -0500
categories: [Study, Programming]
tags: [Python]
---



## 그리디

현재 상황에서 지금 당장 좋은 것만 고르는 방법

- 기준에 따라 좋은 것을 선택하는 기준을 알려줌 (가장 큰 순서대로, 가장 작은 순서대로 ...)
- 대체로 정렬 알고리즘 사용 시 기준을 만족함
- 문제 풀이를 위한 최소한의 아이디어를 떠올리고 정당한지 검토

 

## 구현

머릿 속에 있는 알고리즘을 소스코드로 바꾸는 과정

- 풀이를 떠올리는 것은 쉽지만 소스코드로 옮기기 어려운 문제 (피지컬이 중요함)
- 완전탐색 : 모든 경우의 수를 주저없이 다 계산하는 해결방법
- 시뮬레이션 : 문제에서 제시한 알고리즘을 한 단계씩 차례대로 직접 수행
- 시간, 메모리 제한 확인
  - 메모리 제한 400MB : 리스트 길이 1000만 이하
  - 시간 제한 1초 : 데이터 개수 100개라면 복잡도 O(NlogN) 이하
- 방향 설정 문제
	
	- 리스트 만들어서 방향 정하기
	
	  ```python
	  step = [(-1, 0), ( ...)]
	  
	  dx = [-1, 0, 1, 0]
	  dy = [0, 1, -1, 0]
	  ```



## DFS/BFS
### 자료구조
#### 스택

- 선입 후출

  ```python
  stack = []
  stack.append(1)
  stack.pop()
  ```

#### 큐
```python
from collections import deque

queue = deque()
queue.append(5)
queue.popleft()

queue.reverse()	// 역순으로 바꾸기
```




#### DFS (Depth First Search, 깊이 우선 탐색)
- 동작과정

1. 탐색 시작 노드를 스택에 삽입하고 방문 처리를 한다.
2. 스택의 최상단 노드에 방문하지 않은 인접 노드가 있으면 그 인접 노드를 스택에 넣고 방문 처리를 한다. 방문하지 않은 노드가 없으면 스택에서 최상단 노드를 꺼낸다.
3. 2번의 과정을 더 이상 수행할 수 없을 때까지 반복한다.

```python
def dfs(graph, v, visited) :
	# 현재 노드를 방문 처리
	visited[v] = True
	print(v, end=' ')
	
	# 현재 노드와 연결된 다른 노드를 재귀적으로 방문
	for i in graph[v] :
		if not visited[i] :
		dfs(graph, i, visitied)

# 각 노드가 연결된 정보를 리스트 자료형으로 표현(2차원 리스트)
graph = [
	[],
	[2, 3, 8],
	[1, 7],
	[1, 4, 5],
	[3, 5],
	[3, 4],
	[7],
	[2, 6, 8],
	[1, 7]
]

# 각 노드가 방문된 정보를 리스트 자료형으로 표현(1차원 리스트)
visited = [False] * 9

# 정의된 DFS 함수 호출
dfs(graph, 1, visited)
```

- 방문 정보(1차원리스트), 노드 정보(2차원리스트) 필요
- 방금 방문한 노드의 리스트를 탐색



#### BFS(Breadth First Search, 너비 우선 탐색)
- 동작과정
1. 탐색 시작 노드를 큐에 삽입하고 방문 처리를 한다.
2. 큐에서 노드를 꺼내 해당 노드의 인접 노드 중에서 방문하지 않은 노드를 모두 큐에 삽입하고 방문 처리를 한다.
3. 2번의 과정을 더 이상 수행할 수 없을 때까지 반복한다.

 ```python
from collections import deque

#BFS 메서드 정의
def bfs(graph, start visited) :
	# 큐(queue) 구현을 위해 deque 라이브러리 사용
	queue = deque([start])

	# 현재 노드를 방문 처리
	visited[start] = True
	
	# 큐가 빌 때까지 반복
	while queue :
		# 큐에서 하나의 원소를 뽑아 출력
		v = queue.popleft()
		print(v, end=' ')
	
		# 해당 원소와 연결된, 마치 방문하지 않은 원소들을 큐에 삽입
		for i in graph[i] :
			queue.append(i)
			visited[i] = True

# 각 노드가 연결된 정보를 리스트 자료형으로 표현(2차원 리스트)
graph = [
	[],
	[2, 3, 8],
	[1, 7],
	[1, 4, 5],
	[3, 5],
	[3, 4],
	[7],
	[2, 6, 8],
	[1, 7]
]

# 각 노드가 방문한 정보를 리스트 자료형으로 표현(1차원 리스트)
visited = [False] * 9

# 정의된 BFS 함수 호출
bfs(graph, 1, visited)
 ```

- 방문 정보(1차원리스트), 노드 정보(2차원리스트), 큐 필요
- 방금 방문한 노드의 리스트를 큐에 삽입

 

|           | DFS            | BFS              |
| --------- | -------------- | ---------------- |
| 동작 원리 | 스택           | 큐               |
| 구현 방법 | 재귀 함수 이용 | 큐 자료구조 이용 |

- 2차원 배열에서의 탐색 문제 > 그래프 형태로 바꿔서 표현



## 정렬

데이터를 특정한 기준에 따라서 순서대로 나열하는 것



#### 선택 정렬

1. 가장 작은 데이터를 '선택'해서 맨 앞에 있는 데이터와 바꾼다.
2. 정렬된 맨 앞 데이터를 제외하고 가장 작은 데이터를 선택해 앞에서 두번째 데이터와 바꾼다.
3. 2를 반복한다.

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

for i in range(len(array)) :
	min_index = i	# 가장 작은 원소의 인덱스
	for j in range(i + 1, len(array)) :
		if array[min_index] > array[j] :
			min_index = j
	array[i], array[min_index] = array[min_index], array[i]	# 스와프
	
print(array)
```

- 시간복잡도 : $$O(N^2)$$



#### 삽입 정렬

1. 두 번째 데이터가 어떤 위치로 들어갈지 판단한다. (첫 번째 데이터는 그 자체로 정렬되어 있다고 판단)
2. 그 다음 데이터가 정렬된 데이터 중 어떤 위치로 들어갈지 판단한다. (왼쪽으로 한 칸씩 이동하면서 자신 보다 작은 데이터를 만났을 때 그 위치에 삽입)
3. 2를 반복한다.

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

for i in range(1, len(array)) :
	for j in range(i, 0, -1) : 			# 인덱스 i부터 1까지 감소하며 반복하는 문법
		if array[i] < array[j - 1] :	# 한 칸씩 왼쪽으로 이동
			array[j], array[j - 1] = array[j - 1], array[j]
		else :							# 자기보다 작은 데이터를 만나면 그 위치에서 멈춤
			break
		
print(array)
```

- 시간복잡도 : $$O(N^2)$$
  - 거의 정렬되어 있는 상태라면 $$O(N)$$



#### 퀵 정렬

1. 첫 번째 데이터를 피벗(Pivot)으로 정한다.
2. 왼쪽에서부터 피벗보다 큰 데이터를 찾고, 오른쪽에서부터 피벗보다 작은 데이터를 찾는다. 
3. 큰 데이터와 작은 데이터의 위치를 서로 교환해준다.
4. 왼쪽에서 찾는 값과 오른쪽에서부터 찾는 값의 위치가 서로 엇갈린 경우 '작은 데이터'와 '피벗'의 위치를 서로 변경하여 분할을 수행한다.
5. 분할이 완료되면 왼쪽 리스트와 오른쪽 리스트 개별적으로 정렬을 반복한다.

```python
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

def quick_sort(array, start, end) : 
	if start >= end : # 원소가 1개인 경우 종료
		return
	pivot = start	# 피벗은 첫 번째 원소
	left = start + 1
	right = end
	while left <= right :
		# 피벗보다 큰 데이터를 찾을 때까지 반복
		while left <= end and array[left] <= array[pivot] :
			left += 1
		# 피벗보다 작은 데이터를 찾을 때까지 반복
		while right > start and array[right] >= array[pivot] :
			right -= 1
		if left > right : 	# 엇갈렸다면 작은 데이터와 피벗을 교체
			array[right], array[pivot] = array[pivot], array[right]
		else : 	# 엇갈리지 않았다면 작은 데이터와 큰 데이터를 교체
			array[left], array[right] = array[right], array[left]
	# 분할 이후 왼쪽 부분과 오른쪽 부분에서 각각 정렬 수행
	quick_sort(array, start, right - 1)
	quick_sort(array, right + 1, end)
	
quick_sort(array, 0, len(array) - 1)
print(array)
```



- 파이썬의 장점을 살린 퀵 정렬 소스코드

  - 전통 퀵 정렬 방식에 비해서 피벗과 데이터를 비교하는 비교 연산 횟수가 증가하므로 시간 면에서는 조금 비효율적이다.
  - 직관적이고 기억하기 쉽다.

  ```python
  array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]
  
  def quick_sort(array) :
  	# 리스트가 하나 이하의 원소만을 담고 있다면 종료
  	if len(array) <= 1 :
  		return array
  	
  	pivot = array[0]	# 피벗은 첫 번째 원소
  	tail = array[1:]	# 피벗을 제외한 리스트
  	
  	left_side = [x for x in tail if x <= pivot]	# 분할된 왼쪽 부분
  	right_side = [x for x in tail if x > pivot]	# 분할된 오른쪽 부분
  	
  	# 분할 이후 왼쪽 부분과 오른쪽 부분에서 각각 정렬을 수행하고, 전체 리스트를 반환
  	return quick_sort(left_side) + [pivot] + quick_sort(right_side)
  	
  print(quick_sort(array))
  ```

- 시간복잡도(평균) : $$O(NlogN)$$

  - 이미 데이터가 정렬되어 있는 경우(최악) : $$O(N^2)$$



#### 계수 정렬

- 특정한 조건이 부합할 때만 사용할 수 있지만 매우 빠른 정렬 알고리즘

1. 가장 큰 데이터와 가장 작은 데이터의 범위가 모두 담길 수 있도록 하나의 리스트를 생성한다.
2. 데이터를 하나씩 확인하며 데이터의 값과 동일한 인덱스의 데이터를 1씩 증가시킨다.

```python
# 모든 원소의 값이 0보다 크거나 같다고 가정
array = [7, 5, 9, 0, 3, 1, 6, 2, 9, 1, 4, 8, 9, 5, 2]
# 모든 범위를 포함하는 리스트 선언(모든 값은 0으로 초기화)
count = [0] + (max(array) + 1)

for i in range(len(array)) :
	count[array[i]] += 1	# 각 데이터에 해당하는 인덱스의 값 증가
	
for i in range(len(count)) : # 리스트에 기록된 정렬 정보 확인
	for j in range(count[i]) :
		print(i, end=' ')	# 띄어쓰기를 구분으로 등장한 횟수만큼 인덱스 출력
```

- 시간복잡도 : $$O(N + K)$$
  - K : 데이터 중 최대값의 크기
- 데이터의 크기가 한정되어있고, 많이 중복되어 있을 수록 유리



#### 파이썬의 정렬 라이브러리

```
array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]

# sorted()
result = sorted(array)

# sort()
array.sort()

# key 활용
def setting(data) :
	return data[1]

result = sorted(array, key=setting)
```

- sorted()
  - 리스트, 딕셔너리 등을 입력받아 정렬된 결과를 반환한다.
  - 집합 자료형이나 딕셔너리 자료형을 입력받아도 반환되는 결과는 리스트 자료형이다.
  - 병합 정렬을 기반으로 만들어졌다.
  - 시간복잡도 : $$O(NlogN)$$
- sort()
  - 별도의 정렬된 리스트가 반환되지 않고 내부 원소가 바로 정렬된다.
- key 활용
  - key 값으로 정렬 기준이 되는 함수가 들어가야 한다.
  - 람다 함수 사용 가능



#### 정렬 정리

- 문제에서 별도의 요구가 없이 단순 정렬해야하는 상황에서는 기본 정렬 라이브러리 사용
- 데이터의 범위가 한정되어 있으며, 더 빠르게 동작해야하는 경우 계수 정렬 사용

정렬 알고리즘이 사용되는 문제 유형 3가지

1. 정렬 라이브러리로 풀 수 있는 문제 : 기본 정렬 라이브러리 사용
2. 정렬 알고리즘의 원리에 대해서 물어보는 문제 : 선택, 삽입, 퀵 정렬 등
3. 더 빠른 정렬이 필요한 문제 : 계수 정렬 등 이용하거나 기존 알고리즘의 구조적인 개선을 거쳐야 풀 수 있다.



## 이진 탐색

#### 순차 탐색

리스트 안에 있는 특정한 데이터를 찾기 위해 앞에서부터 데이터를 하나씩 차례대로 확인하는 방법

```python
# 순차탐색 소스코드 구현
def sequential_search(n, target, array) :
	# 각 원소를 하나씩 확인
	for i in range(n) :
		# 현재의 원소가 찾고자 하는 원소와 동일한 경우
		if array[i] == target :
			return i + 1	# 현재의 위치 반환(인덱스는 0부터 시작하므로 1 더하기)
        
input_data = input().split()
n = int(input_data[0])		# 원소의 개수
target = input_data[1]		# 찾고자 하는 문자열

array = input().split()

# 순차 탐색 수행 결과 출력
print(sequential_search(n, target, array))
```



#### 이진 탐색

반으로 쪼개면서 탐색하기

- 배열 내부의 데이터가 정렬되어 있어야 사용할 수 있다.
- 위치를 나타내는 변수 3개를 사용한다. (시작점, 끝점, 중간점)
  - 중간점이 실수일 때는 소수점을 버린다.
- 찾으려는 데이터와 중간점 위치에 있는 데이터를 반복적으로 비교해서 원하는 데이터를 찾는 것
  - 찾으려는 데이터가 중간 데이터보다 작을 경우
    - 시작점 그대로
    - 끝점 = 중간점 - 1
  - 찾으려는 데이터가 중간 데이터보다 클 경우
    - 시작점 = 중간점 + 1
    - 끝점 그대로
- 시간복잡도 : $$O(logN)$$
  - 한 번 확인할 때마다 확인하는 원소의 개수가 절반씩 줄어든다.



- 재귀 함수로 구현한 이진 탐색

  ```python
  # 이진 탐색 소스코드 구현 (재귀함수)
  def binary_search(array, target, start, end) :
  	if start > end :
  		return None
  	mid = (start + end) // 2
  	# 찾은 경우 중간점 인덱스 반환
  	if array[mid] == target :
  		return mid
  	# 중간점의 값보다 찾고자 하는 값이 작은 경우 왼쪽 확인
  	elif array[mid] > target :
  		return binary_search(array, target, start, mid - 1)
  	# 중간점의 값보다 찾고자 하는 값이 큰 경우 오른쪽 확인
  	else :
  		return binary_search(array, target, mid + 1, end)
  	
  # 원소의 개수, 찾고자 하는 문자열 입력받기
  n, target = list(map(int, input().split()))
  # 전체 원소 입력받기
  array = list(map(int, input().split()))
  
  # 이진 탐색 수행 결과
  result = binary_search(array, target, 0, n - 1)
  if result == None :
  	print("원소가 존재하지 않습니다.")
  else :
  	print(result + 1)
  ```

- 반복문으로 구현한 이진 탐색

  ```python
  # 이진 탐색 소스코드 구현(반복문)
  def binary_search(array, target, start, end) :
  	while start <= end :
  		mid = (start + end) // 2
  		# 찾은 경우 중간점 인덱스 반환
  		if array[mid] == target :
  			return mid
  		# 중간점의 값보다 찾고자 하는 값이 작은 경우 왼쪽 확인
  		elif array[mid] > target :
  			end = mid - 1
  		# 중간점의 값보다 찾고자 하는 값이 큰 경우 오른쪽 확인
  		else :
  			start = mid + 1
  	return None
  	
  # 위와 동일..
  ```



#### 빠르게 입력받기

- 입력 데이터의 개수가 많은 문제에 input() 함수를 사용하면 동작 속도가 느려서 시간 초과로 오답 판정을 받을 수 있다.

- sys 라이브러리의 readline() 함수 이용

  ```python
  import sys
  
  # 하나의 문자열 데이터 입력받기
  input_data = sys.stdin.readline().rstrip()
  ```

  - 한 줄 입력받고 나서 rstrip() 호출 : readline()으로 입력하면 엔터가 줄바꿈 기호로 입력되기 때문



## 다이나믹 프로그래밍

1. 큰 문제를 작은 문제로 나눌 수 있다.
2. 작은 문제에서 구한 정답은 그것을 포함하는 큰 문제에서도 동일하다. 
   (같은 문제라면 한번씩만 풀어서 문제를 효율적으로 해결)

- 메모이제이션

  - 한 번 구한 정보를 리스트에 저장
  - 다이나믹 프로그래밍을 재귀적으로 수행하다가 같은 정보가 필요할 때 이미 구한 답을 그대로 리스트에서 가져온다.

  ```python
  # 한 번 계산된 결과를 메모이제이션 하기 위한 리스트 초기화
  d = [0] * 100
  
  # 피보나치 함수를 재귀함수로 구현(탑다운 다이나믹 프로그래밍)
  def fibo(x) :
      print('f(' + str(x) + ')', end=' ')
      
  	# 종료 조건(1 혹은 2일 때 1을 반환)
  	if x == 1 or x == 2 :
  		return 1
  	# 이미 계산한 적 있는 문제라면 그대로 반환
  	if d[x] != 0 :
  		return d[x]
  	# 아직 계산하지 않은 문제라면 점화식에 따라서 피보나치 결과 반환
  	d[x] = fibo(x - 1) + fibo(x - 2)
  	return d[x]
  
  print(fibo(6))
  
  # 호출되는 함수 f(6) f(5) f(4) f(3) f(2) f(1) f(2) f(3) f(4)
  ```

  ![dp](/assets/img/codingtest-dp.png){:width="80%" height="80%"}



- 탑다운 / 보텀업
  - 탑다운(Top-Down) 방식
    - 큰 문제를 해결하기 위해 작은 문제를 호출한다.
    - 재귀 함수를 이용하여 작성
    - 메모이제이션 : 한 번 계산된 결과를 저장, 때에 따라 다른 자료형 이용(딕셔너리 등)
    - 하향식
  - 보텀업(Botton-Up) 방식
    - 작은 문제부터 차근차근 답을 도출한다.
    - 반복문을 이용하여 작성
    - DP 테이블 : 결과 저장용 리스트
    - 상향식

```python
# 앞서 계산된 결과를 저장하기 위한 DP테이블 초기화
d = [0] * 100

# 첫 번째 피보나치 수와 두 번째 피보나치 수는 1
d[1] = 1
d[2] = 1
n = 99

# 피보나치 함수 반복문으로 구현 (보텀업 다이나믹 프로그래밍)
for i in range(3, n + 1) :
	d[i] = d[i - 1] + d[i - 2]
	
print(d[n])
```



#### 문제를 푸는 방법

1. 주어진 문제가 다이나믹 프로그래밍 유형임을 파악하는 것
   (특정 문제를 완전 탐색 알고리즘으로 접근했을 때 너무 오래 걸리면 중복되는 부분이 있는지 확인)
2. 재귀함수로 작성한 뒤에 메모이제이션 적용할 수 있으면 코드를 개선하기
3. 가능하다면 보텀업 방식으로 구현하는 것을 권장
   (시스템상 재귀함수의 스택 크기가 한정되어 있을 수 있기 때문)
   - 오천 번째 이상의 큰 피보나치 수를 구하면 recursion dept 관련 오류가 발생할 수 있다. -> 이 경우 sys 라이브러리의 setrecursionlimit() 함수를 호출하여 재귀 제한을 완하할 수 있다.



## 최단 경로

가장 짧은 경로를 찾는 알고리즘



### 다익스트라 최단 경로 알고리즘

특정한 노드에서 출발하여 다른 노드로 가는 각각의 최단 경로를 구해주는 알고리즘

- ''음의 간선''이 없을 때 정상적으로 동작한다.
- '각 노드에 대한 현재까지의 최단거리 정보'를 항상 1차원 리스트에 저장하여 리스트를 계속 갱신한다. (최단거리 테이블)



#### 원리

1. 출발 노드를 설정한다.
2. 최단 거리 테이블을 초기화한다. (출발 노드는 0, 나머지는 무한)
3. 방문하지 않은 노드에서 최단 거리가 가장 짧은 노드를 선택한다.
4. 해당 노드를 거쳐 다른 노드로 가는 비용을 계산하여 최단 거리 테이블을 갱신한다.
5. 3, 4번을 반복한다.



알고리즘이 진행되면서 한 단계당 하나의 노드에 대한 최단 거리를 확실히 찾는다.

- 방문하지 않은 노드 중 가장 최단거리가 짧은 노드를 선택하는 과정을 반복하는데, 이렇게 선택된 노드는 '최단 거리'가 완전히 선택된 노드이므로, 더 이상 알고리즘을 반복해도 최단 거리가 줄어들지 않는다.



#### 구현 방법

1. 구현하기 쉽지만 느리게 동작하는 코드
2. 구현하기 조금 까다롭지만 빠르게 동작하는 코드



간단한 다익스트라 알고리즘

```python
import sys
input = sys.stdin.readline
INF = int(1e09)	# 무한을 의미하는 값으로 10억을 설정

# 노드의 개수, 간선의 개수를 입력받기
n, m = map(int, input().split())
# 시작 노드 번호 입력받기
start = int(input())
# 각 노드에 연결되어 있는 노드에 대한 정보를 담는 리스트를 만들기
graph = [[] for i in range(n + 1)]
# 방문한 적이 있는지 체크하는 목적의 리스트를 만들기
visited = [False] * (n + 1)
# 최단 거리 테이블을 모드 무한으로 초기화
distance = [INF] * (n + 1)

# 모든 간선 정보를 입력받기
for _ in range(m) :
	a, b, c = map(int, input().split())
	# a번 노드에서 b번 노드로 가는 비용이 c라는 의미
	graph[a].append((b, c))
	
# 방문하지 않은 노드 중에서, 가장 최단 거리가 짧은 노드와 번호를 반환
def get_smallest_node() :
	min_value = INF
	index = 0 # 가장 최단 거리가 짧은 노드 (인덱스)
	for i in range(1, n + 1) :
		if distance[i] < min_value and not visited[i] :
			min_value = distance[i]
			index = i
	return index

def dijkstra(start) :
	# 시작 노드에 대해서 초기화
	distance[start] = 0
	visited[start] = True
	for j in graph[start] :
		distance[j[0]] = j[1]
	# 시작 노드를 제외한 전체 n - 1개의 노드에 대해 반복
	for i range(n - 1) :
		# 현재 최단 거리가 가장 짧은 노드를 꺼내서 방문 처리
		now = get_smallest_node()
		visited[now] = True
		# 현재 노드와 연결된 다른 노드를 확인
		for j in graph[now] :
			cost = distance[now] + j[1]
			# 현재 노드를 거쳐서 다른 노드로 이동하는 거리가 더 짧은 경우
			if cost < distance[j[0]] :
				distance[j[0]] = cost
				
# 다익스트라 알고리즘 수행
dijkstra(start)

# 모든 노드로 가기 위한 최단 거리를 출력
for i in range(1, n + 1) :
	# 도달할 수 없는 경우, 무한(INFINITY)라고 출력
	if distance[i] == INF :
		print("INFINITY")
	# 도달할 수 있는 경우, 거리를 출력
	else :
		print(distance[i])
```

- 최단 거리가 가장 짧은 노드를 찾기 위해 최단 거리 테이블을 앞에서부터 하나씩(선형적으로) 탐색
- input()을 sys.std.readline()으로 치환하여 사용
- 모든 리스트는 (노드의 개수 + 1)의 크기로 할당하여, 노드의 번호를 인덱스로 바로 접근할 수 있도록 한다.
- 시간복잡도 : $$O(V^2)$$
  - V는 노드의 개수
  - 노드의 개수가 10,000개를 넘어가는 경우 문제 해결하기 어려움



개선된 다익스트라 알고리즘

```python
import heapq
import sys
input = sys.stdin.readline
INF = int(1e09)	# 무한을 의미하는 값으로 10억을 설정

# 노드의 개수, 간선의 개수를 입력받기
n, m = map(int, input().split())
# 시작 노드 번호 입력받기
start = int(input())
# 각 노드에 연결되어 있는 노드에 대한 정보를 담는 리스트를 만들기
graph = [[] for i in range(n + 1)]
# 방문한 적이 있는지 체크하는 목적의 리스트를 만들기
visited = [False] * (n + 1)
# 최단 거리 테이블을 모드 무한으로 초기화
distance = [INF] * (n + 1)

# 모든 간선 정보를 입력받기
for _ in range(m) :
	a, b, c = map(int, input().split())
	# a번 노드에서 b번 노드로 가는 비용이 c라는 의미
	graph[a].append((b, c))

def dijkstra(start) :
    q = []
	# 시작 노드로 가기 위한 최단 경로는 0으로 설정하여, 큐에 삽입
    heapq.heappush(q, (0, start))
    distance[start] = 0
    while q : # 큐가 비어있지 않다면
        # 가장 최단 거리가 짧은 노드에 대한 정보 꺼내기
        dist, now = heapq.heappop(q)
        # 현재 노드가 이미 처리된 적이 있는 노드라면 무시
        if distance[now] < dist :
            continue
        # 현재 노드와 연결된 다른 인접한 노드들을 확인
        for i in graph[now] :
            cost = dist + i[1]
            # 현재 노드를 거쳐서, 다른 노드로 이동하는 거리가 더 짧은 경우
            if cost < distance[i[0]] :
                distance[i[0]] = cost
                heapq.heappush(q, (cost, i[0]))
				
# 다익스트라 알고리즘 수행
dijkstra(start)

# 모든 노드로 가기 위한 최단 거리를 출력
for i in range(1, n + 1) :
	# 도달할 수 없는 경우, 무한(INFINITY)라고 출력
	if distance[i] == INF :
		print("INFINITY")
	# 도달할 수 있는 경우, 거리를 출력
	else :
		print(distance[i])
```

- 힙 자료구조 사용
  - 우선순위 큐 라이브러리 사용 (heapq)
    - 우선순위 기준을 정해서, 기준값이 가장 크거나, 가장 작은 데이터가 먼저 나온다. (각각 최대 힙, 최소 힙)
      `(거리, 노드) -> '노드' 값이 우선순위 값`
    - 파이썬 라이브러리에서 기본 적으로 최소 힙 구조를 이용한다.
      - 다익스트라 최단 경로 알고리즘에서는 비용이 적은 노드를 우선하여 방문하므로 최소 힙 구조를 사용하면 적합하다.
  - 최단 거리가 가장 짧은 노드를 선택하는 과정을 다익스트라 최단 경로 함수 안에서 우선순위 큐를 이용하는 방식으로 대체한다. (get_smallest_node() 함수 필요 없음)
- 시간복잡도 : $$O(ElogV)$$
  - V는 노드의 개수, E는 최대 간선의 개수
  - 최대 E개의 간선 데이터를 힙에 넣었다가 다시 빼는 것이므로 $$O(ElogE)$$
    중복 간선을 포함하지 않는 경우 E는 항상 $$V^2$$보다 작다. (모든 노드끼리 연결되어 있을 경우 간선의 개수는 약 $$V^2$$이고, E는 항상 $$V^2$$이기 때문이다.) -> $$logE$$는 $$logV^2$$보다 작다. -> $$logV^2$$ == $$2logV$$ == $$logV$$
    따라서 전체 시간 복잡도는 $$O(ElogV)$$



### 플로이드 워셜 알고리즘

모든 지점에서 다른 모든 지점까지의 최단 경로를 모두 구하는 경우 사용할 수 있는 알고리즘

- 각 단계에서 해당 노드를 거쳐가는 모든 경우를 고려한다.
  - 현재 확인하고 있는 노드를 제외하고 N-1 개의 노드 중에서 서로 다른 노드 (A, B) 쌍을 선택하여 자기 자신을 거쳐가는 거리를 계산하여 최단 거리를 갱신한다. 
  - 점화식 : `A -> B` 최소 비용과 `A -> 현재노드 -> B` 중에 더 작은 값으로 갱신한다.
  - $${}_N-1P_2$$ 개의 쌍을 단계마다 반복해서 확인 = $$O(N^2)$$ 과 같다고 볼 수 있음
  - 각 단계를 N번 반복하므로 `시간복잡도 $$O(N^3)$$`
- '2차원 리스트'에 최단 거리 정보를 저장한다.
  - 모든 노드에 대하여 다른 모든 노드로 가는 최단 거리 정보를 담아야 하기 때문이다.



#### 원리

1. 초기 테이블을 설정한다.
   연결된 간선은 값을 채워넣고, 연결되지 않은 간선은 '무한' 값을 넣는다.
   자기 자신에서 자기 자신으로 가는 비용은 0으로 초기화한다.
2. 모든 노드에 대하여 각 노드를 거쳐가는 경우를 갱신한다.



```python
INF = int(1e9)	# 무한을 의미하는 값으로 10억을 설정

# 노드의 개수 및 간선의 개수를 입력받기
n = int(input())
m = int(input())
# 2차원 리스트(그래프 표현)를 만들고, 모든 값을 무한으로 초기화
graph = [[INF] * (n + 1) for _ in range(n + 1)]

# 자기 자신에서 자기 자신으로 가는 비용은 0으로 초기화
for a in range(1, n + 1) :
	for b in range(1, n + 1) :
		if a == b :
			graph[a][b] == 0

# 각 간선에 대한 정보를 입력받아, 그 값으로 초기화
for _ in range(m) :
	# A에서 B로 가는 비용은 C라고 설정
	a, b, c = map(int, input().split())
	graph[a][b] = c
	
# 점화식에 따라 플로이드 워셜 알고리즘을 수행
for k in range(1, n + 1) :
	for a in range(1, n + 1) :
		for b in range(1, n + 1) :
			graph[a][b] = min(graph[a][b], graph[a][k] + graph[k][b])
			
# 수행된 결과를 출력
for a in range(1, n + 1) :
	for b in range(1, n + 1) :
		# 도달할 수 없는 경우, 무한(INFINITY)이라고 출력
		if graph[a][b] == INF :
			print("INFINITY", end=' ')
		# 도달할 수 있는 경우, 거리를 출력
		else :
			print(graph[a][b], end=' ')
	print()
```



## 그래프 이론

### 서로소 집합

공통 원소가 없는 두 집합

- 서로소 집합 자료구조 : 서로소 부분 집합들로 나누어진 원소들의 데이터를 처리하기 위한 자료구조
- union, find 연산으로 조작할 수 있다.
  - union : 2개의 원소가 포함된 집합을 하나로 합치는 연산
  - find : 특정한 원소가 속한 집합이 어떤 집합인지 알려주는 연산

- 서로소 집합을 활용한 사이클 판별
  1. 각 간선을 확인하며 두 노드의 루트 노드를 확인한다.
     1. 루트 노드가 서로 다르다면 두 노드에 대하여 union 연산을 수행한다.
     2. 루트 노드가 서로 같다면 사이클이 발생한 것이다.
  2. 그래프에 포함되어 있는 모든 간선에 대하여 1번 과정을 반복한다.



### 신장 트리

하나의 그래프가 있을 때 모든 노드를 포함하면서 사이클이 존재하지 않는 부분 그래프

#### 크루스칼 알고리즘

최소 신장 트리 알고리즘 (최소 비용으로 만들 수 잇는 신장 트리를 찾는 알고리즘)

- 시간복잡도 : $$O(ElogE)$$



### 위상 정렬

방향 그래프의 모든 노드를 '방향성에 거스르지 않도록 순서대로 나열하는 것'

- 시간복잡도 : $$O(V + E)$$



### 출처📎

이것이 취업을 위한 코딩 테스트다 with 파이썬