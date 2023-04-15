# prefix sum(누적합, 구간합)
 

## 1차원 배열
### 1. 누적합(반복문)
- 누적합은 0번째 인덱스부터 N번째까지 탐색하면서 구하는 누적합을 말한다.
```python
arr = [1, 8, 7, 4, 3, 5, 6]
n = len(arr)
psum = [0] * n

for i in range(n):
    for j in range(i+1):
        psum[i] += arr[j]
```
- `O(n^2)` 의 시간복잡도를 가지며 n이 클 수록 기하급수적으로 시간이 늘어나게 된다.

<br />

### 2. 더 빠르게 누적합 구하기
```python
def prefix_sum(arr):
    n = len(arr)
    psum = [0] * n
    psum[0] = arr[0]
    
    for i in range(1, n):
        psum[i] = psum[i-1] + arr[i]
        
    return psum
    
arr = [1, 2, 3, 4, 5]
psum = prefix_sum(arr)
print(psum)  # output: [1, 3, 6, 10, 15]
```
- 누적합이 저장된 이전 값인`psum[i-1]`과 현재 순회 중인 `arr[i]`값을 더함으로써
- 이전 코드보다 더 짧은 `O(n)`의 시간복잡도를 가진다.

<br />

### 3. 부분 구간합 구하기
- 부분 구간합은 누적합 구간 사이의 차이로 구할 수 있다. 
```python
arr = [1, 8, 7, 4, 3, 5, 6]
n = len(arr)

psum = [0] * n
start, end = 1, 5

psum[0] = arr[0]
for i in range(1, n):
    psum[i] = psum[i - 1] + arr[i]

# 누적합 구간 사이의 차이로 부분 구간합 구할 수 있다.
_sum = psum[end] - psum[start - 1]
print(_sum)
```
- 구하려는 `끝` -  `(처음-1)` 이용하여 구할 수 있다. 
- 누적합의 첫 번째 인덱스는 `arr`의 맨 처음 인덱스이다.

<br />
<br />

## 2차원 배열
- 2차원 배열 누적합/구간합은 블로그 그림 설명과 함께 보는 것이 더 좋다고 판단되어 링크 첨부합니다.
    - [2차원배열 누적합/구간합 2](https://jih3508.tistory.com/50)
### 1. 2차원 배열 누적합/구간합 (Python)
    
#### 1) 1차원 구간합
![image](https://user-images.githubusercontent.com/70880695/225345930-c7d5b4dc-87a8-47ee-8ac5-5b39cbcf24f0.png)
> 출처 : https://jih3508.tistory.com/50

<br /> 

#### 2) 2차원 구간합
![image](https://user-images.githubusercontent.com/70880695/225345636-987b6c1f-0b72-4c5c-bd15-10f0b203c420.png)
> 출처 : https://jih3508.tistory.com/50

<br />

```python
x = 4
y = 4
arr = [[1, 2, 3, 4],
       [5, 6, 7, 8],
       [9, 10, 11, 12],
       [13, 14, 15, 16]]

psum = [[0 for _ in range(y + 1)] for _ in range(x + 1)]


def 누적합():
    # 누적합은 1부터 시작
    for i in range(1, x + 1):
        for j in range(1, y + 1):
            '''
                누적합
                1) (i-1, j) 까지의 누적합 + (i, j-1) 까지의 누적합을 더함
                2) (i-1, j-1)까지의 누적합을 빼 줌 즉, (1)에서 누적합을 두 번 더하면 겹치는 부분이 있으니 빼줌
                3) (i, j)까지의 누적합을 구하고 싶으니, (i, j) 위치에 있는 배열 값을 더해줌
            '''
            psum[i][j] = arr[i - 1][j - 1] + psum[i - 1][j] + psum[i][j - 1] - psum[i - 1][j - 1]

    for i in psum:
        print(*i)
    print()


def 구간합():
    x1, y1, x2, y2 = 2, 1, 3, 4
    '''
        구간합
        1) 누적합[(x좌표최대, y좌표최대)] - 누적합[(x좌표최대, y좌표최소-1)] - 누적합[(x좌표최소-1, y좌표최대)] + 누적합[(x좌표최소-1, y좌표최소-1)]
         - 2번째와 3번째에서 빼면서 공통되는 부분을 2번 빼기 때문에 마지막은 다시 채워줌
    '''
    prefix_sum = psum[x2][y2] + psum[x1 - 1][y1 - 1] - psum[x2][y1 - 1] - psum[x1 - 1][y2]
    print(f'{psum[x2][y2]} + {psum[x1 - 1][y1 - 1]} - {psum[x2][y1 - 1]} - {psum[x1 - 1][y2]} = {prefix_sum}')


누적합()
# 누적합을 먼저 구한 후 구간합을 구한다.
구간합()

```

<br />
<br />

## 관련 추천문제
### 1. 1차원 누적합/구간합
1. [백준] [2559 수열](https://www.acmicpc.net/problem/2559)
2. [백준] [11659 구간 합 구하기 4](https://www.acmicpc.net/problem/11659)
3. [백준] [corporate-flight-bookings](https://leetcode.com/problems/corporate-flight-bookings/)


### 2. 2차원 누적합/구간합
1. [백준] [11660 구간 합 구하기 5](https://www.acmicpc.net/problem/11660) 
2. [프로그래머스] [파괴되지 않은 건물](https://programmers.co.kr/learn/courses/30/lessons/92344)

