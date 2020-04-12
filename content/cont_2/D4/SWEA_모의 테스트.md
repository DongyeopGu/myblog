---
title: "SWEA - Python - 모의 테스트"
tag : ["SWEA", "모의SW 테스트"]
---

### 1949. 등산로 조성

```python
dt = [(1,0), (0,1), (-1,0), (0,-1)]
def sol(x,y,b,cnt=1):
    global max_r
    if max_r < cnt:
        max_r = cnt
    visit[x][y] = 1
    for d in dt:
        dx, dy = x + d[0], y + d[1]
        if not (0 <= dx < n and 0 <= dy < n) or visit[dx][dy] == 1:
            continue
        if Map[x][y] > Map[dx][dy]:
            sol(dx,dy,b,cnt+1)
        elif b > 0 and Map[x][y] > Map[dx][dy] - k:
            temp = Map[dx][dy]
            Map[dx][dy] = Map[x][y] - 1
            sol(dx,dy,0,cnt+1)
            Map[dx][dy] = temp
    visit[x][y] = 0
for t in range(int(input())):
    n, k = map(int, input().split())
    Map = [list(map(int, input().split()))for _ in range(n)]
    visit = [[0 for _ in range(n)]for _ in range(n)]
    top_height = 0
    top_idx = []
    for i in range(n):
        if top_height < max(Map[i]):
            top_height = max(Map[i])
    for i in range(n):
        for j in range(n):
            if Map[i][j] == top_height:
                top_idx.append((i,j))
    max_r = 0
    for top in top_idx:
        sol(top[0],top[1],1)
    print(f"#{t+1} {max_r}")
```

### 1953. 탈주범 검거

```python
dx = [0,0,-1,1]
dy = [-1,1,0,0]
tunnel_type = {
    1:[[1,3,4,5],[1,3,6,7],[1,2,5,6],[1,2,4,7]],
    2:[[],[],[1,2,5,6],[1,2,4,7]],
    3:[[1,3,4,5],[1,3,6,7],[],[]],
    4:[[],[1,3,6,7],[1,2,5,6],[]],
    5:[[],[1,3,6,7],[],[1,2,4,7]],
    6:[[1,3,4,5],[],[],[1,2,4,7]],
    7:[[1,3,4,5],[],[1,2,5,6],[]]
    }

for t in range(int(input())):
    n, m, r, c, l = map(int, input().split())
    tunnel = [list(map(int, input().split()))for _ in range(n)]
    cnt = 1
    start = [(r,c)]
    time = 1
    while True:
        if time == l:
            break
        for i in range(len(start)):
            a = start.pop(0)
            for j in range(4):
                if tunnel_type[tunnel[a[0]][a[1]]][j]:
                    if 0 <= a[0]+dx[j] < n and 0 <= a[1]+dy[j] < m and tunnel[a[0]+dx[j]][a[1]+dy[j]] != 0:
                        if tunnel[a[0]+dx[j]][a[1]+dy[j]] in tunnel_type[tunnel[a[0]][a[1]]][j]:
                            if (a[0]+dx[j],a[1]+dy[j]) not in start:
                                start.append((a[0]+dx[j],a[1]+dy[j]))
                                cnt += 1
            tunnel[a[0]][a[1]] = 0
        time += 1
    print(f"#{t+1} {cnt}")
```

### 4008. 숫자 만들기

```python
def cal(i, numbs):
    global max_value, min_value
    if i == n:
        max_value = max(max_value, numbs)
        min_value = min(min_value, numbs)
    else:
        if operator[0] != 0:
            operator[0] -= 1
            cal(i+1, numbs + numbers[i])
            operator[0] += 1
        if operator[1] != 0:
            operator[1] -= 1
            cal(i+1, numbs - numbers[i])
            operator[1] += 1
        if operator[2] != 0:
            operator[2] -= 1
            cal(i+1, numbs * numbers[i])
            operator[2] += 1
        if operator[3] != 0:
            operator[3] -= 1
            if numbs < 0 and numbs%numbers[i]:
                cal(i+1, numbs//numbers[i]+1)
            else:
                cal(i+1, numbs//numbers[i])
            operator[3] += 1

for t in range(int(input())):
    n = int(input())
    operator = list(map(int, input().split()))
    numbers = list(map(int, input().split()))
    min_value = 100000000
    max_value = -100000000
    cal(1,numbers[0])
    print(f"#{t+1} {max_value - min_value}")
```

### 5650. 핀볼 게임

```python
# 방향 상 좌 하 우
dx = (-1, 0, 1, 0)
dy = (0, -1, 0, 1)
# 1번 블록 : 상 > 하, 좌 > 상, 하 > 우, 우 > 좌
block = ((2, -1, 1, -2), (3, 1, -2, -2), (1, 2, -2, -1), (2, 2, -1, -3), (2, 2, -2, -2))

def sol(x,y,d):
    global result, n
    cnt = 0
    nx = x
    ny = y
    while True:
        nx += dx[d]
        ny += dy[d]
        if not (n > nx >= 0 and n > ny >= 0):       # 벽 만났을 때 튕김(방향은 반대로)
            d = (d + 2) % 4
            cnt += 1
        else:       
            if game_board[nx][ny] == -1 or (nx == x and ny == y):
                result = max(cnt, result)
                return
            elif game_board[nx][ny] == 0:
                continue
            elif 0<game_board[nx][ny] < 6:                # 블록 만났을 때
                d += block[game_board[nx][ny]-1][d]
                cnt += 1
            else:
                for a, b in warmhole[game_board[nx][ny]]:   # 웜홀 만났을때 인덱스 변경
                    if nx != a or ny != b:
                        nx = a
                        ny = b
                        break

for t in range(int(input())):
    n = int(input())
    game_board = [list(map(int, input().split()))for _ in range(n)]
    warmhole = {6:[],7:[],8:[],9:[],10:[]}
    for i in range(n):
        for j in range(n):
            if game_board[i][j] > 5:
                warmhole[game_board[i][j]].append((i,j))
    result = 0
    
    for i in range(n):
        for j in range(n):
            if game_board[i][j] == 0:
                for k in range(4):
                    sol(i,j,k)
    print(f"#{t+1} {result}")
```