---
title: "BAEKJOON - Python"
tags : ["baekjoon", "백준"]
---

### 14890. 경사로

```python
def slope(i, c):
    global ans
    cnt = 1
    for j in range(0, N-1):
        d = a[i][j+1]-a[i][j] if c else a[j+1][i]-a[j][i]
        if d == 0:
            cnt += 1
        elif d == 1 and cnt >= L:
            cnt = 1
        elif d == -1 and cnt >= 0:
            cnt = -L+1
        else:
            return
    if cnt >= 0:
        ans += 1

def solve():
    for i in range(N):
        slope(i, 1)
        slope(i, 0)
    print(ans)

N, L = map(int, input().split())
a = [list(map(int, input().split())) for _ in range(N)]
ans = 0
solve()
```

### 14503. 로봇 청소기

```python
dt = [(-1,0),(0,1),(1,0),(0,-1)]   # 북, 동, 남, 서
def leftdir(d):                    # 왼쪽 탐색 index 만들기 위해
    if d == 0:
        return 3
    else:
        return d - 1

n, m = map(int,input().split())
r, c, d = map(int, input().split())
room = [list(map(int, input().split()))for _ in range(n)]
check = 0
x = r
y = c
while True:
    d = d % 4
    if check == 4:                          # 4가지 방향 탐색 후
        bx = x - dt[d][0]                   # 뒤로 한칸 가서
        by = y - dt[d][1]          
        if room[bx][by] == 1:               # 벽이면 break
            break
        else: 
            x = bx
            y = by
            check = 0
    if room[x][y] == 0:                     # 먼저 청소기 놓은곳 2로 변경
        room[x][y] = 2
    nd = leftdir(d)                         # 왼쪽 탐색위한 index변경
    nx = x + dt[nd][0]
    ny = y + dt[nd][1]
    if room[nx][ny] == 0:                   # 왼쪽이 0 이면 다시 청소시작
        x = nx
        y = ny
        d = leftdir(d)
        check = 0
    else:                                  # 왼쪽 청소거나 벽이면 다시 
        d = leftdir(d)
        check += 1
cnt = 0
for i in range(n):
    for j in range(m):
        if room[i][j] == 2:
            cnt += 1
print(cnt)
```

### 12100. 2048(easy)

```python
import copy

def rotation(a):
    for i in range(n):
        for j in range(i):
            a[i][j], a[j][i] = a[j][i], a[i][j]
    return a
def rev(a):
    for i in range(n):
        a[i] = a[i][::-1]
    return a
def left(a):             
    for i in range(n):
        for j in range(a[i].count(0)):
            a[i].append(a[i].pop(a[i].index(0)))
        for j in range(1,n):
            if a[i][j-1] == a[i][j]:
                a[i][j-1] = a[i][j-1]*2
                a[i][j] = 0
        for j in range(a[i].count(0)):
            a[i].append(a[i].pop(a[i].index(0)))
    return a

def right(a):
    rev(a)
    left(a)
    return rev(a)
def up(a):
    rotation(a)
    left(a)
    return rotation(a)
def down(a):
    rotation(a)
    right(a)
    return rotation(a)
def my_max(a):
    max_v = 0
    for i in range(n):
        for j in range(n):
            if a[i][j] > max_v:
                max_v = a[i][j]
    return max_v

def solv(cnt, a):
    global result
    if cnt == 5:
        result = max(result, my_max(a))
    if cnt<5:
        copy_a = copy.deepcopy(a)
        up(copy_a)
        solv(cnt+1, copy_a)
        
        copy_a = copy.deepcopy(a)
        down(copy_a)
        solv(cnt+1, copy_a)

        copy_a = copy.deepcopy(a)
        left(copy_a)
        solv(cnt+1, copy_a)
        
        copy_a = copy.deepcopy(a)
        right(copy_a)
        solv(cnt+1, copy_a)

n = int(input())
a = [list(map(int, input().split()))for _ in range(n)]
result = 0
solv(0,a)
print(result)
```

### 1012. 유기농 배추

```python
def DFS(y,x):
    dy = [0,1,0,-1]
    dx = [1,0,-1,0]
    board[y][x] = 0
    for i in range(4):
        ny = y + dy[i]
        nx = x + dx[i]
        if ny < 0 or ny >= N or nx < 0 or nx >= M:
            continue
        if board[ny][nx]:
            DFS(ny,nx)

for _ in range(int(input())):
    M, N, K = map(int, input().split())
    board = [[0 for _ in range(M)] for _ in range(N)]
    for _ in range(K):
        X, Y = map(int, input().split())
        board[Y][X] = 1
    cnt = 0
    for i in range(N):
        for j in range(M):
            if not board[i][j]:
                continue
            cnt += 1
            DFS(i,j)
    print(cnt)
```

### 1244. 스위치 켜고 끄기

```python
n = int(input())    #스위치개수
switch = list(map(int, input().split()))
m = int(input())    #사람수

for _ in range(m):
    student = list(map(int, input().split()))
    if student[0] == 1:
        k = student[1] - 1
        while k < n:
            switch[k] = 1 - switch[k]
            k += student[1]
    else:
        j = student[1] - 1
        switch[j] = 1 - switch[j]

        for a in range(1,n-1):
            if j - a >= 0 and j + a < n and switch[j - a] == switch[j + a]:
                switch[j - a] = 1 - switch[j - a]
                switch[j + a] = 1 - switch[j + a]
                
            else:
                break
for i, e in enumerate(switch):
    if i and not (i%20):
        print()
    print(e, end=' ')
```

### 2583. 영역구하기

```python
from sys import*
setrecursionlimit(10**6)
dt = ([1, 0], [-1, 0], [0, 1], [0, -1])
def solv(y,x):
    global cnt
    visit[y][x] = 1
    cnt += 1
    for i in range(4):
        nx = x + dt[i][0]
        ny = y + dt[i][1]
        if 0 <= nx < n and 0 <= ny < m:
            if arr[ny][nx] == 0 and visit[ny][nx]==0:
                solv(ny,nx)
            
m, n, k = map(int, input().split())
arr = [[0 for _ in range(n)]for _ in range(m)]
for i in range(k):
    a, b, c, d = map(int, input().split())
    for x in range(a,c):
        for y in range(b,d):
            arr[y][x] = 1
arr = list(reversed(arr))
visit = [[0 for _ in range(n)]for _ in range(m)]
result = []
for i in range(m):
    for j in range(n):
        if arr[i][j] == 0 and visit[i][j] == 0:
            cnt = 0
            solv(i,j)
            result.append(cnt)

print(len(result))
print(' '.join(map(str, sorted(result))))
        

```

### 2589. 보물섬

```python
dt = [(1,0), (-1,0), (0,1), (0,-1)]
def xy(i,j):
    return 0 <= i < row and 0 <= j < col and Map[i][j] == 'L'

def solv(x,y):
    q.append([x,y])
    dist = [[0 for _ in range(col)]for _ in range(row)]
    dist[x][y] = 1
    result = 0
    while q:
        i, j = q.pop(0)
        for idx in dt:
            nx = i + idx[0]
            ny = j + idx[1]
            if xy(nx,ny) and dist[nx][ny] == 0:
                dist[nx][ny] = dist[i][j] + 1
                result = max(result, dist[nx][ny]) 
                q.append([nx,ny])

    return result - 1

row, col = map(int, input().split())
Map = [list(map(str, input()))for _ in range(row)]
visit = [[0 for _ in range(col)]for _ in range(row)]
q = []
cnt = 0
for i in range(row):
    for j in range(col):
        if Map[i][j] == 'L':
            cnt = max(cnt, solv(i,j))
print(cnt)
```

