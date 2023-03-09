# prefix sum(누적합, 구간합)
 

## 1. 누적합(반복문)
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

## 2. 더 빠르게 누적합 구하기
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

## 3. 부분 구간합 구하기
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

### 추후 작성
- 2차원 배열이 시?
- 누적합을 왜 사용하는가 ?
