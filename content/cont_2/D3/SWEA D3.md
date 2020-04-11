---
title: "SWEA - Python - D3"
date: 2020-04-11T14:29:53+09:00
---

### 1240. 단순 2진 암호코드

```python
for t in range(int(input())):
    n = int(input())
    arr = [0]*n
    cnt = 0
    a = list(input().split())
    for i in a:
        flag = 0
        for j in i:
            if not flag and j.isupper():
                flag = 1
            elif j.isdigit() or (flag and j.isupper()):
                flag = 0
                break
        if flag:
            arr[cnt] += 1
        if ('.' in i) or ('!' in i) or ('?' in i):
            cnt += 1
    print(f"#{t+1} {' '.join(map(str, arr))}")
```

### 1289. 원재의 메모리 복구

```python
for test_case in range(int(input())):
    N = list(map(int, ' '.join(input().split())))
    cnt = 0
    a = 0
    if N[0] == 1:
        cnt = 1
    else:
        cnt = 0
    for i in range(1,len(N)):
        if N[i] != N[i-1]:
            cnt += 1
        else:
            cnt += 0
    print(f"#{test_case + 1} {cnt}")
```

### 1491. 원재의 벽 꾸미기

```python
for test_case in range(int(input())):
    N, A, B = map(int, input().split())
    a = []
    for R in range(1, N):
        for C in range(1, N):
            if R * C <= N:
                if A*abs(R-C) + B*(N-R*C) >= 0:
                    a.append(A*abs(R-C) + B*(N-R*C))
    print(f"#{test_case} {min(a)}")
```

### 1493. 수의 새로운 연산

```python
for test_case in range(int(input())):
    p, q = map(int, input().split())
    mapping = [[0 for i in range(300)]for j in range(300)]
    a = 1
    p_x = 0
    p_y = 0
    q_x = 0
    q_y = 0
    for i in range(599):
        for j in range(300):
            if 300 > i - j >= 0:
                mapping[i-j][j] = a
                a += 1
    for i in range(300):
        if p in mapping[i]:
            p_y = mapping[i].index(p)
            p_x = i
    for i in range(300):
        if q in mapping[i]:
            q_y = mapping[i].index(q)
            q_x = i
    
    result = mapping[p_x+q_x+1][p_y+q_y+1]
    print(f"#{test_case + 1} {result}")
    
```

### 1860. 진기의 최고급 붕어빵

```python
for t in range(int(input())):
    N,M,K=map(int,input().split())
    a=list(map(int,input().split()))
    for i in range(len(a)):
        if sorted(a)[i]//M*K<i+1:
            result="Impossible"
            break
        else:
            result="Possible"
    print(f"#{t+1} {result}")
```

### 1873. 상호의 배틀필드

```python
for t in range(int(input())):
    H, W = map(int, input().split())
    move_symbol = ["^", "v", "<", ">"]
    move_command = ["U", "D", "L", "R", "S"]
    move_dir = [[-1, 0], [1, 0], [0, -1], [0, 1]]
    field = []
    startpoint = [0, 0, 0]
    for i in range(H):
        a = list(input())
        field.append(a)
        for j in range(len(a)):
            if a[j] in move_symbol:
                startpoint[0] = i
                startpoint[1] = j
                startpoint[2] = move_symbol.index(a[j])
                field[i][j] = '.'
    N = int(input())
    commandline = list(input())
    for i in commandline:
        move_index = move_command.index(i)
        if move_index < 4:
            i_idx = startpoint[0] + move_dir[move_index][0]
            j_idx = startpoint[1] + move_dir[move_index][1]
            if 0 <= i_idx < H and 0 <= j_idx < W:
                if field[i_idx][j_idx] == '.':
                    startpoint[0] = i_idx
                    startpoint[1] = j_idx
            startpoint[2] = move_index
        else:
            n = startpoint[0]
            m = startpoint[1]
            while True:
                n += move_dir[startpoint[2]][0]
                m += move_dir[startpoint[2]][1]
                if 0 <= n < H and 0 <= m < W:
                    if field[n][m] == "#":
                        break
                    elif field[n][m] == "*":
                        field[n][m] = "."
                        break
                else:
                    break
    field[startpoint[0]][startpoint[1]] = move_symbol[startpoint[2]]
    print(f"#{t+1}",end=' ')
    for i in range(H):
        print(f"{''.join(field[i])}")
```

### 2805. 농작물 수확하기

```python
for t in range(int(input())):
    N = int(input())
    farm = [list(map(int, input()))for i in range(N)]
    base_sum = sum(farm[N//2])
    for i in range(N//2):
        base_sum += sum(farm[i][N//2-i:N//2+i+1])
    for i in range(N//2+1,N):
        base_sum += sum(farm[i][i-N//2:N-(i-N//2)])
    print(f"#{t+1} {base_sum}")
```

### 2806. N-Queen

```python
def placeQueen(queen_pos):
    cnt = 0
    n = len(queen_pos)
    if n == N:
        return 1
    for i in range(N):
        for j in range(n):
            if i == queen_pos[j]:
                break
            if n - j == i - queen_pos[j]:
                break
            if n - j == queen_pos[j] - i:
                break
        else:
            cnt += placeQueen(queen_pos + [i])
    return cnt
for t in range(int(input())):
    N = int(input())
    print(f"#{t+1} {placeQueen([])}")
```

### 2814. 최장 경로

```python
def dfs(a,b):
    visit[a] = 1
    result.append(b)
    for i in node[a]:
        if visit[i] == 0:
            dfs(i,b+1)
            visit[i] = 0

for t in range(int(input())):
    n, m = map(int, input().split())
    node = dict()
    for i in range(1,n+1):
        node[i] = []
    for i in range(m):
        a, b = map(int, input().split())
        node[a].append(b)
        node[b].append(a)
    result = []
    for i in range(1,n+1):
        visit = [0] * (n+1)
        c = 1
        dfs(i,c)
        result.append(c)
    print(f"#{t+1} {max(result)}")

```

### 2817. 부분 수열의 합

```python
for t in range(int(input())):
    N, K = map(int, input().split())
    a = list(map(int, input().split()))
    n = len(a)
    b = []
    result = 0
    cnt = 0
    for i in range(1 << n):
        for j in range(n+1):
            if i & (1 << j):
                result += a[j]
                if result>K:
                    continue
        if result == K:
            cnt += 1
        result=0

    print(f"#{t+1} {cnt}")
```

### 2948. 문자열 교집합

```python
for t in range(int(input())):
    N, M = map(int,input().split())
    set_1 = set(input().split())
    set_2 = set(input().split())
    result = set_1&set_2
    print(f"#{t+1} {len(result)}")
```

### 3032. 홍준이의 숫자 놀이

```python
def gcd(a, b):
    x,y, u,v = 0,1, 1,0
    while a != 0:
        q, r = b//a, b%a
        m, n = x-u*q, y-v*q
        b,a, x,y, u,v = a,r, u,v, m,n
    return [x, y]

for t in range(int(input())):
    A, B = map(int, input().split())
    print(f"#{t+1} {' '.join(map(str, gcd(A,B)))}")
```

### 3131. 100만 이하의 모든 소수

```python
def prime(n):
    a = [False, False] + [True] * (n - 1) 
    for k in range(2, int(n ** 0.5 + 1.5)):
        if a[k]: 
            a[k*2::k] = [False] * ((n - k) // k) 
    return [x for x in range(n+1) if a[x]]
print(' '.join(map(str, prime(10**6))))
```



### 3142. 영준이와 신비한 뿔의 숲

```python
for t in range(int(input())):
    n, m = map(int, input().split())
    x = n - m
    y = 2*m - n
    print(f"#{t+1} {y} {x}")
```

### 3233. 정삼각현 분할

```python
for t in range(int(input())):
    n, m = map(int, input().split())
    a = n**2//m**2
    print(f"#{t+1} {a}")
```

### 3260. 두 수의 덧셈

```python
for t in range(int(input())):
    N = int(input())
    farm = [list(map(int, input()))for i in range(N)]
    base_sum = sum(farm[N//2])
    for i in range(N//2):
        base_sum += sum(farm[i][N//2-i:N//2+i+1])
    for i in range(N//2+1,N):
        base_sum += sum(farm[i][i-N//2:N-(i-N//2)])
    print(f"#{t+1} {base_sum}")
```

### 3304. 최장 공통 부분

```python
def lcs(a,b):
    arr = [[0 for _ in range(len(b)+2)]for _ in range(len(a)+2)]
    for i in range(1,len(a)+1):
        for j in range(1,len(b)+1):
            if a[i-1] == b[j-1]:
                arr[i][j] = arr[i-1][j-1]+1
            else:
                arr[i][j] = max(arr[i-1][j],arr[i][j-1])
    return arr[len(a)][len(b)]

for t in range(int(input())):
    a, b = map(str, input().split())
    print(f"#{t+1} {lcs(a,b)}")
```

### 3307. 최장 증가 부분

```python
def lis(a):
    d = [0] * (N)
    for i in range(1,N):    
        for j in range(i):
            if a[i] > a[j]:
                d[i] = max(d[i],d[j]+1)
    return max(d)

for t in range(int(input())):
    N = int(input())
    a_list = list(map(int, input().split()))
    print(f"#{t+1} {lis(a_list)+1}")
```

### 3282. 0/1 Knapsack

```python
def sol(k,n):
    a = [[0 for _ in range(k+2)]for _ in range(n+2)]
    for i in range(1,n+1):
        for j in range(1,k+1):
            if v_list[i-1] > j:
                a[i][j] = a[i-1][j]
            else:
                a[i][j] = max(c_list[i-1]+a[i-1][j-v_list[i-1]], a[i-1][j])
    return a[n][k]

for t in range(int(input())):
    N, K = map(int, input().split())
    sack = [list(map(int, input().split()))for _ in range(N)]
    v_list = []
    c_list = []
    for i in range(N):
        v_list.append(sack[i][0])
        c_list.append(sack[i][1])
    print(f"#{t+1} {sol(K,N)}")
        
```

### 3314. 보충학습과 평균

```python
for t in range(int(input())):
    score = list(map(int, input().split()))
    for i in range(len(score)):
        if score[i] < 40:
            score[i] = 40
    print(f"#{t+1} {sum(score)//5}")
```

### 3376. 파도반 수열

```python
def wave(n):
    a = [1,1,1]
    b = [1] * n
    if n <=3:
        return 1
    else:
        for i in range(3,n):
            b[i] = b[i-2] + b[i-3]
    return b[n-1]
for t in range(int(input())):
    N = int(input())
    print(f"#{t+1} {wave(N)}")
```

### 3408. 세가지합

```python
for t in range(int(input())):
    N = int(input())
    sum_1 = N*(N+1)//2
    sum_2 = N*(N*2)//2
    sum_3 = N*(N*2+2)//2
    print(f"#{t+1} {sum_1} {sum_2} {sum_3}")
```

### 3431. 준환이의 운동관리

```python
for t in range(int(input())):
    L, U, X = map(int, input().split())
    if U < X:
        print(f"#{t+1} {-1}")
    elif L <= X <= U:
        print(f"#{t+1} {0}")
    else:
        print(f"#{t+1} {L-X}")
```

### 3456. 직사각형 길이 찾기

```python
for t in range(int(input())):
    a = list(map(int, input().split()))
    for i in range(3):
        if a.count(a[i])%2==1:
            print(f"#{t+1} {a[i]}")
            break
```

### 3499. 퍼펙트 셔플

```python
for t in range(int(input())):
    N = int(input())
    card = list(map(str, input().split()))
    print(f"#{t+1}",end=' ')
    if len(card) % 2 == 0:
        for i in range(N//2):
            print(f"{card[i]} {card[N//2+i]}",end=" ")
        print()
    else:
        for i in range(N//2):
            print(f"{card[i]} {card[N//2+i+1]}",end=" ")
        print(f"{card[N//2]}",end=" ")
        print()

```

### 3750. Digit sum

```python
for t in range(int(input())):
    print(f"#{t+1} {(int(input())-1)%9+1}")
```

### 3809. 화섭이의 정수 나열

```python
for t in range(int(input())):
    n = int(input())
    a = ''
    while True:
        a += ''.join(map(str, input().split()))
        if len(a) == n:
            break
    print(a)
    b = 0
    c = 0
    while True:
        if str(b) not in a:
            c = b
            break
        b += 1
    print(f"#{t+1} {c}")
```

### 3975. 승률 비교하기

```python
T = int(input())
g = []
for t in range(T):
    a,b,c,d = map(int, input().split())
    e = a / b
    f = c / d
    if e < f:
        g.append("BOB")
    elif e > f:
        g.append("ALICE")
    else:
        g.append("DRAW")
    
for t in range(T):
    print(f"#{t+1} {g[t]}")
```

### 4047. 영준이의 카드 카운팅

```python
for t in range(int(input())):
    a=input()
    b=[]
    c=1
    d=[a.count("S"),a.count("D"),a.count("H"),a.count("C")]
    for i in range(0,len(a),3):
        b.append(a[i:i+3])
    if len(set(b))<len(b):
        c=0
        print(f"#{t+1} ERROR")
    else:
        print(f"#{t+1} {13-d[0]} {13-d[1]} {13-d[2]} {13-d[3]}")
```

### 4299. 태혁이의 사랑은 타이밍

```python
for t in range(int(input())):
    d, h, m = map(int, input().split())
    a = [11, 11, 11]
    result = 0
    if d - a[0] < 0:
        result = -1
    elif a[0] == d:
        if h - a[1] < 0:
            result = -1
        else:
            if m - a[2] < 0:
                result = -1
            else:
                result += (d-a[0]) * 60 * 24
                result += (h-a[1]) * 60
                result += (m-a[2])
    else:
        result += (d-a[0]) * 60 * 24
        result += (h-a[1]) * 60
        result += (m-a[2])
    print(f"#{t+1} {result}")
```

### 4371. 항구에 들어오는 배

```python
for t in range(int(input())):
    n = int(input())
    happyday = []
    for _ in range(n):
        happyday.append(int(input())-1)
    del happyday[0]
    for i in happyday:
        happyday=set(happyday)
        happyday -= set(range(i*2,max(happyday)+1, i))
        continue
    print(f"#{t+1} {len(happyday)}")
```

### 4406. 모음이 보이지 않는 사람

```python
for t in range(int(input())):
    a = list(input())
    for i in ['a','e','i','o','u']:
        for j in range(len(a)):
            if i == a[j]:
                a[j]=''
    print(f"#{t+1} {''.join(map(str, a))}")
```

### 4466. 최대 성적표 만들기

```python
for t in range(int(input())):
    n, k = map(int, input().split())
    a = list(map(int, input().split()))
    b = sorted(a,reverse=True)
    result = sum(b[:k])
    print(f"#{t+1} {result}")
```

### 4522. 세상의 모든 팰린드롬

```python

```

### 4579. 세상의 모든 팰린드롬2

```python

```

### 4615. 재미있는 오셀로 게임

```python
def limit(a, b, n):
    if a >= 0 and a < n and b >= 0 and b < n:
        return True
    else:
        return False

for t in range(int(input())):
    N, M = map(int, input().split())
    data = [[0] * N for _ in range(N)]
    data[N // 2 - 1][N // 2] = 1
    data[N // 2][N // 2 - 1] = 1
    data[N // 2 - 1][N // 2 - 1] = 2
    data[N // 2][N // 2] = 2
  
    dx = [-1, 0, 1, 1, 1, 0, -1, -1]
    dy = [-1, -1, -1, 0, 1, 1, 1, 0]
  
    for _ in range(M):
        a, b, stone = map(int, input().split())
        x = a - 1
        y = b - 1
        data[x][y] = stone
        for i in range(8):
            cnt = 0
            next_x = x + dx[i]
            next_y = y + dy[i]
            while limit(next_x, next_y, N) and stone != data[next_x][next_y] and data[next_x][next_y] != 0:
                cnt += 1
                next_x = next_x + dx[i]
                next_y = next_y + dy[i]
  
            if limit(next_x, next_y, N) and data[next_x][next_y] == stone:
                next_x = x + dx[i]
                next_y = y + dy[i]
                for f in range(cnt):
                    data[next_x][next_y] = stone
                    next_x = next_x + dx[i]
                    next_y = next_y + dy[i]
                      
    cnt1 = 0
    cnt2 = 0
    for i in range(N):
        for j in range(N):
            if data[i][j] == 1:
                cnt1 += 1
            elif data[i][j] == 2:
                cnt2 += 1
    print(f"#{t+1} {cnt1} {cnt2}")
```

### 4676. 늘어지는 소리 만들기

```python
for t in range(int(input())):
    string = list(input())
    h = int(input())
    idx = list(map(int, input().split()))
    idx_sort =sorted(idx,reverse=True)
    for i in range(len(idx)):
        string.insert(idx_sort[i],'-')
    print(f"#{t+1} {''.join(map(str, string))}")
```

### 4751. 다솔이의 다이아몬드 장식

```python
for t in range(int(input())):
    a = list(input())
    n = (len(a) * 4) + 1
    b = ['.','.','#','.']*(n//4)+['.']
    c = ['.','#']*(n//2)+['.']
    d = ['#','.']*(n//2)+['#']
    print(''.join(b))
    print(''.join(c))
    for i in range(len(a)):
        d[4*i+2]=a[i]
    print(''.join(d))
    print(''.join(c))
    print(''.join(b))
```

### 4789. 성공적인 공연 기획

```python
for t in range(int(input())):
    a = list(map(int, input()))
    cnt = 0
    result = 0
    for i in range(len(a)):
        while True:
            if cnt < i:
                cnt += 1
                result += 1
            else:
                cnt += a[i]
                break
    print(f"#{t+1} {result}")
```

### 5162. 두가지 빵의 딜레마

```python
for t in range(int(input())):
    a, b, c = map(int, input().split())
    result = 0
    if a < b:
        result = c // a
    else:
        result = c // b
    print(f"#{t+1} {result}")
```

### 5215. 햄버거 다이어트

```python
for t in range(int(input())):
    n, l = map(int, input().split())
    hamb = [list(map(int, input().split()))for _ in range(n)]
    cal = []
    score = []
    for i in range(n):
        score.append(hamb[i][0])
        cal.append(hamb[i][1])
    board = [[0 for _ in range(l + 1)] for _ in range(n + 1)]
    for i in range(1, n + 1):
        for j in range(1, l + 1):
            if cal[i - 1] > j:
                board[i][j] = board[i - 1][j]
            else:
                board[i][j] = max(score[i - 1] + board[i - 1][j - cal[i - 1]], board[i - 1][j])
    print(f"#{t+1} {board[n][l]}")
```

### 5356. 의석이의 세로로 말해요

```python
T=int(input())
for tc in range(1, T+1):
    arr = [input() for _ in range(5)]
    l = [len(i) for i in arr]  # 각 문자열의 길이
    ml = max(l) # 문자열 중 최대길이
 
    temp = ""
    for i in range(ml):
        for j in range(5):
            if l[j] > i:  # 자신의 문자열의 길이보다 열번호가 작으면 문자 추가하지 않음
                temp += arr[j][i]
    print("#%d %s"%(tc, temp))  
```

### 5431. 민석이의 과제 체크하기

```python
for t in range(int(input())):
    n, k = map(int, input().split())
    a = list(map(int, input().split()))
    n_li = list(range(1,n+1))
    for i in a:
        if i in n_li:
            n_li.remove(i)
    print(f"#{t+1} {' '.join(map(str, n_li))}")
```

### 5515. 2016년 요일 맞추기

```python
for t in range(int(input())):
    m, d = map(int, input().split())
    day = [0,31,29,31,30,31,30,31,31,30,31,30,31]
    days = 0
    for i in range(m):
        days += day[i]
    print(f"#{t+1} {(days+d+3)%7}")

```

### 5549. 홀수일까 짝수일까

```python
for t in range(int(input())):
    a = input()
    if int(a[-1])%2 == 0:
        print(f"{t+1} Even")
    else:
        print(f"{t+1} Odd")
```

### 5601. 쥬스 나누기

```python
for t in range(int(input())):
    n = int(input())
    print(f"#{t+1}",end=' ')
    for i in range(n):
        print(f"1/{n}", end=' ')
    print()
```

### 5603. 건초더미

```python
for t in range(int(input())):
    n = int(input())
    a = []
    for i in range(n):
        a.append(int(input()))
    avg = sum(a)//n
    result = 0
    for i in range(n):
        result += abs(a[i] - avg)
    print(f"#{t+1} {result//2}")
```

### 5642. 합

```python
for t in range(int(input())):
    n = int(input())
    a = list(map(int, input().split()))
    max_ = -10000
    sum_ = 0
    for i in range(n):
        sum_ += a[i]
        if sum_ > max_:
            max_ = sum_
        if sum_ < 0:
            sum_ = 0
    print(f"#{t+1} {max_}")
```



### 5789. 현주의 상자 바꾸기

```python
for t in range(int(input())):
    n, q = map(int, input().split())
    box = [0 for _ in range(n)]
    for j in range(1,q+1):
        l, r = map(int, input().split())
        for i in range(l-1,r):
            box[i] = j
    print(f"#{t+1} {' '.join(map(str, box))}")
```

### 5948. 새샘이의 7-3-5 게임

```python
for t in range(int(input())):
    a = list(map(int, input().split()))
    b = sorted(a,reverse=True)
    cnt = 0
    result = []
    for i in range(7):
        for j in range(i+1,7):
            for k in range(j+1,7):
                result.append(b[i]+b[j]+b[k])
    c = sorted(result,reverse=True)
    v = 0
    for i in range(len(c)-1):
        if c[i] != c[i+1]:
            cnt += 1
        if cnt == 5:
            v = c[i]
            break
    print(f"#{t+1} {v}")
```

### 5986. 새샘이와 세 소수

```python
def prime(number):
    a = [False, False] + [True] * (number-1)
    for (i, e) in enumerate(a):
        if e:
            k = i * 2
            while k <= n:
                a[k] =False
                k += i
    return [x for (x, y) in enumerate(a) if y]

for t in range(int(input())):
    n = int(input())
    a = prime(n)
    cnt = 0
    for i in range(len(a)):
        for j in range(i,len(a)):
            for k in range(j,len(a)):
                if a[i] + a[j] + a[k] == n:
                    cnt+=1
                else:
                    continue
           
    print(f"#{t+1} {cnt}")
```

### 6019. 기차 사이의 파리

```python
for t in range(int(input())):
    d,a,b,f = map(int, input().split())
    result = (d / (a+b))*f
    print(f"#{t+1}", "%.10f" %result)
```

### 6190. 정곤이의 단조 증가하는 수

```python
for t in range(int(input())):
    n=int(input())
    a=list(map(int,input().split()))
    f=-1
    for i in range(n-1):
        for j in range(i+1,n):
            m=a[i]*a[j]
            if f>m:
                continue
            c=m
            b=m%10
            c=c//10
            while c:
                if c%10>b:
                    break
                b=c%10
                c=c//10
            if not c:
                f=m
    print(f"#{t+1} {f}")
```

### 6485. 삼성시의 버스 노선

```python
for t in range(int(input())):
    arr = [0]*5001
    for i in range(int(input())):
        a , b = map(int, input().split())
        for j in range(a,b+1):
            arr[j] += 1
    p = int(input())
    arr_1 = [0]*p
    for i in range(p):
        c = int(input())
        arr_1[i] = arr[c]
    print(f"#{t+1} {' '.join(map(str, arr_1))}")
```

### 6485.

```python
for t in range(int(input())):
    arr = [0]*5001
    for i in range(int(input())):
        a , b = map(int, input().split())
        for j in range(a,b+1):
            arr[j] += 1
    p = int(input())
    arr_1 = [0]*p
    for i in range(p):
        c = int(input())
        arr_1[i] = arr[c]
    print(f"#{t+1} {' '.join(map(str, arr_1))}")
```

### 6692. 다솔이의 월급 상자

```python
for t in range(int(input())):
    N = int(input())
    result = 0
    for i in range(N):
        a, b = map(float, input().split())
        result += a * b
    print(f"{t+1}", "%.6f" % result)
```

### 7675. 통역사 성경이

```python
for t in range(int(input())):
    n = int(input())
    arr = [0]*n
    cnt = 0
    a = list(input().split())
    for i in a:
        flag = 0
        for j in i:
            if not flag and j.isupper():
                flag = 1
            elif j.isdigit() or (flag and j.isupper()):
                flag = 0
                break
        if flag:
            arr[cnt] += 1
        if ('.' in i) or ('!' in i) or ('?' in i):
            cnt += 1
    print(f"#{t+1} {' '.join(map(str, arr))}")
```

