---
title: "SWEA - Python - D4"
---

### 1210. Ladder1

```python
for _ in range(10):
    T = input()
    ladder = [list(map(int, input().split()))for _ in range(100)]
    j = 0
    for x in range(100):
        if ladder[99][x] == 2:
            j = x
    start_p = 0
    i = 99
    
    while i >= 0:
        if j - 1 >= 0 and ladder[i][j-1] == 1:
            while 0 <= j - 1 < 100 and ladder[i][j-1] == 1:
                j -= 1
        elif j + 1 < 100 and ladder[i][j+1] == 1:
            while 0 <= j + 1 < 100 and ladder[i][j+1] == 1:
                j += 1
        i -= 1
    print(f"#{T} {j}")
```

### 1211. Ladder2

```python
for _ in range(10):
    t = int(input())
    ladder = [list(map(int, input().split()))for _ in range(100)]
    start_idx = []
    min_cnt = 10000
    min_idx = 0
    for i in range(100):
        if ladder[0][i] == 1:
            start_idx.append(i)
    for i in range(100):
        c = i
        r = 0
        count = 0
        if ladder[r][c] == 1:
            r += 1
            while r < 100:
                if c-1 >= 0 and ladder[r][c-1] == 1:
                    while c-1 >= 0 and ladder[r][c-1] == 1:
                        c -= 1
                        count += 1
                    r += 1
                    count += 1
                elif c+1 <= len(ladder[r])-1 and ladder[r][c+1] == 1:
                    while c+1 <= len(ladder[r])-1 and ladder[r][c+1] == 1:
                        c += 1
                        count += 1
                    r += 1
                    count += 1
                else:
                    r += 1
                    count += 1
            if min_cnt > count:
                min_cnt = count
                min_idx = i
    print(f"#{t} {min_idx}")
                        
```

### 1224. 계산기3

```python
def cal(a):
    stack = []
    for i in a:
        try:
            if i == '+':
                num1 = stack.pop()
                num2 = stack.pop()
                stack.append(num1+num2)
            elif i == '-':
                num1 = stack.pop()
                num2 = stack.pop()
                stack.append(num2-num1)
            elif i == '*':
                num1 = stack.pop()
                num2 = stack.pop()
                stack.append(num1*num2)
            elif i == '/':
                num1 = stack.pop()
                num2 = stack.pop()
                stack.append(num2//num1)
            elif i.isdigit() == True:
                stack.append(int(i))
            elif i == '.':
                return stack
        except:
            return 'error'

# for t in range(int(input())):
#     a = list(input().split())
#     b = cal(a)
#     if len(b) != 1:
#         print(f"#{t+1} error")
#     else:
#         print(f"#{t+1} {''.join(map(str, b))}")
def cal_1(a):
    stack = []
    result = []
    isp = {'(':0,'+':1, '-':1, '*':2, '/':2, ')':3}
    for i in a:
        if i == '(':
            stack.append(i)
        elif i == ')':
            while stack[-1] != '(':
                b = stack.pop()
                result.append(b)
            stack.pop()
        elif i in isp:
            while len(stack) != 0 and isp[stack[-1]] >= isp[i]:
                result.append(stack.pop())
            stack.append(i)
        elif i.isdigit() == True:
            result.append(i)
    while len(stack) != 0 and stack[-1] != '(':
        c = stack.pop()
        result.append(c)
    return result

for t in range(10):
    n = input()
    a = list(input())
    b = cal_1(a)
    b.append('.')
    c = cal(b)
    print(f"#{t+1} {''.join(map(str, c))}")
```

### 1226. 미로1

```python
for _ in range(10):
    T = input()
    maze = [list(map(int, input()))for _ in range(16)]   
    flag = 0
    visit = [[1,1]]
    dx = [-1, 0, 1, 0]
    dy = [0, 1, 0, -1]
    while visit != []:
        i, j = visit.pop()
        for idx in range(4):
            c_i = i + dx[idx]
            c_j = j + dy[idx]
            if maze[c_i][c_j] == 0:
                visit.append([c_i,c_j])
                maze[c_i][c_j] = 1
            if maze[c_i][c_j] == 3:
                flag = 1
                break
        if flag:
            break
    print(f"#{T} {flag}")
```

### 1258. 행렬찾기

```python
for t in range(int(input())):
    n = int(input())
    a = [list(map(int, input().split()))for _ in range(n)]
    result = []
    for i in range(n):
        for j in range(n):
            if a[i][j] == 0:
                continue
            c = i
            while c < n and a[c][j]:
                r = j
                while r < n and a[c][r]:
                    a[c][r] = 0
                    r += 1
                c += 1
            result.append([c-i, r-j])
    result.sort(key=lambda x : (x[0]*x[1], x[0]))
    print(f"#{t+1} {len(result)}", end=' ')
    for i,j in result:
        print(i,j,end=' ')
    print()    
```

### 1486. 장훈이의 높은 선반

```python
for t in range(int(input())):
    n, b = map(int, input().split())
    height = list(map(int, input().split()))
    min_ = 10000*20
    for i in range(1<<n):
        sum_ = 0
        for j in range(n):
            if i &(1<<j):
                sum_ += height[j]
        if sum_ >= b and sum_ < min_:
            min_ = sum_
    print(f"#{t+1} {min_-b}")
```

### 1861. 정사각형 방

```python
dt = [(1,0), (0,1), (-1,0), (0,-1)]
for t in range(int(input())):
    n = int(input())
    room = [list(map(int, input().split()))for _ in range(n)]
    max_value = 0
    result = n ** 2 + 1
    for i in range(n):
        for j in range(n):
            start_num = room[i][j]
            cnt = 1
            while True:
                for delta in dt:
                    dx, dy = i + delta[0], j + delta[1]
                    if 0 <= dx < n and 0 <= dy < n and room[dx][dy] - room[i][j] == 1:
                        cnt += 1
                        i, j = dx, dy
                        break
                else:
                    break
            if cnt > max_value:
                max_value = cnt
                result = start_num
            elif cnt == max_value and start_num < result:
                max_value = cnt
                result = start_num
    print(f"#{t+1} {result} {max_value}")
```

### 1865. 동철이의 일 분배

```python
def solv(per, sum_val, a=0):
    global result
    if sum_val <= result:
        return
    if per == n:
        if sum_val > result:
            result = sum_val
        return
    for i in range(a,n):
        n_list[i],n_list[a] = n_list[a], n_list[i]
        solv(per+1,sum_val*p[a][n_list[a]]/100,a+1)
        n_list[i],n_list[a] = n_list[a], n_list[i]
    
for t in range(int(input())):
    n = int(input())
    n_list = list(range(n))
    p = [list(map(int, input().split()))for _ in range(n)]
    result = 0
    solv(0,1)
    print(f"#{t+1} {100*result:.6f}")
```

### 2819. 격자판의 숫자 이어 붙이기

```python
dt = [(-1,0), (0,1), (1,0), (0,-1)]
def sol(x,y,num,cnt=0):
    global result
    if cnt == 6:
        result.add(num)
        return
    for delta in dt:
        dx, dy = x + delta[0], y + delta[1]
        if not (0 <= dx < 4 and 0 <= dy < 4):
            continue
        sol(dx,dy,num+a[dx][dy], cnt+1)           
        
for t in range(int(input())):
    a = [list(map(str, input().split()))for _ in range(4)]
    result = set()
    for i in range(4):
        for j in range(4):
            sol(i,j,a[i][j])
    print(f"#{t+1} {len(result)}")
   
```

### 3347. 올림픽 종목 투표

```python
for t in range(int(input())):
    n, m = map(int, input().split())
    a = list(map(int,input().split()))
    b = list(map(int,input().split()))
    c = [0] * n
    for i in range(m):
        for j in range(n):
            if a[j]<=b[i]:
                c[j] += 1
                break
    print(f"#{t+1} {c.index(max(c))+1}")
```

### 3752. 가능한 시험 점수

```python
for t in range(int(input())):
    N = int(input())
    a = list(map(int, input().split()))
    sum_score = {0}
    for i in a:
        for j in list(sum_score):
            sum_score.add(j+i)
    print(f"#{t+1} {len(sum_score)}")
```

### 4408. 자기 방으로 돌아가기

```python
for t in range(int(input())):
    room = [0] * 400
    n = int(input())
    a = [list(map(int, input().split()))for _ in range(n)]
    for i in range(n):
        if a[i][0] > a[i][1]:
            a[i][0], a[i][1] = a[i][1], a[i][0]
        if a[i][0] % 2 == 0:
            a[i][0] += 1
        if a[i][1] % 2:
            a[i][1] += 1
        for j in range(a[i][0],a[i][1]):
            room[j] += 1
    print(f"#{t+1} {max(room)}")
```

### 5432. 쇠막대기 자르기

```python
for t in range(int(input())):
    a = input()
    b = a.replace('()','-')
    stack = []
    cnt = 0
    for i in b:
        if i == '(':
            cnt += 1
            stack.append(i)
        elif i == ')':
            stack.pop()
        else:
            cnt += len(stack)
    print(f"#{t+1} {cnt}")

```

### 6109. 추억의 2048게임

```python
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

for t in range(int(input())):
    n, case = map(str, input().split())
    n = int(n)
    a = [list(map(int, input().split()))for _ in range(n)]
    print(f"#{t+1}")
    if case == "left":
        a = left(a)
        for i in range(n):
            print(' '.join(map(str, a[i])))
    if case == "right":
        a = right(a)
        for i in range(n):
            print(' '.join(map(str, a[i])))
    if case == "up":
        a = up(a)
        for i in range(n):
            print(' '.join(map(str, a[i])))
    if case == "down":
        a = down(a)
        for i in range(n):
            print(' '.join(map(str, a[i])))       
```

