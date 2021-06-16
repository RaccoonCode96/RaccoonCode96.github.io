---
title: "20210616 DataStructure 02 : Queue(큐), Stack(스택), Python&JavaScript" #제목
category: #카테고리
tag: #태그
  - DataStructure
toc: true #옆에 목차
---

> # DataStructure 02

<br/>

<p align="center">
<img src="../assets/img/dataStructure_logo.png" width="400px" height="400px">
</p>

<br/>
<br/>

> # Queue (큐)

<br/>

- 배열과 함께 가장 쉬운 자료구조 중 하나
- 운영체제, 네트워크 기능에서 많이 쓰임
  - **멀티 태스킹을 위한 프로세스 스케쥴링 방식 구현에 많이 사용됨 (운영체제)**
- 특별히 장단점은 없음

<br/>

## 큐의 구조

<br/>

- 줄을 서는 행위와 유사함
- 가장 먼저 넣은 데이터를 가장 먼저 꺼낼 수 있는 구조 (**선입 선출**)
  - **FIFO**(First-In, First-Out 첫번째로 들어간게 첫번째로 나옴) 또는 **LILO**(Last-In, Last-Out 마지막으로 들어간게 마지막으로 나옴) 방식으로 **스택과 꺼내는 순서가 반대임**
- 단순히 어떤것을 꺼내라는 명령이 없고, 꺼내라 라는 명령만 있음(자동으로 당겨지니까)
- 기능 2가지 : 데이터를 넣는것(Enqueue), 데이터를 꺼내는 것(Dequeue)
- [Data Structure 시각화 사이트](https://visualgo.net/en/list)

<br/>

> ## Python Queue 라이브러리

<br/>

## FIFO Queue (가장 일반적인 큐)

<br/>

```python
import queue
# make queue
data_queue = queue.Queue()

# put
data_queue.put("Enqueue")
data_queue.put(1)

# qsize
data_queue.qsize() # 2

# get
data_queue.get() # "Enqueue"
data_queue.qsize() # 1
data_queue.get() # 1
data_queue.qsize() # 0
```

<br/>

## LIFO Queue (변형된 큐)

<br/>

```python
import queue
# make LifoQueue
data_queue = queue.LifoQueue()

# put
data_queue.put("Enqueue")
data_queue.put(25)

# qsize
data_queue.qsize() # 2

# get
data_queue.get() # 25
data_queue.qsize() # 1
data_queue.get() # "Enqueue"
data_queue.qsize() # 0
```

<br/>

## Priority Queue (우선순위 큐)

<br/>

- 자료구조, 알고리즘에서 많이 사용됨
- 시간의 순서로 데이터를 꺼내오는 것이 아니고, **정해진 우선순위에 따라서 데이터를 꺼내옴**
- 데이터를 넣을 때 우선순위도 넣어야 해서 `(우선순위, 값)` 의 **튜플 구조**로 넣게 됨
- **우선순위의 값이 작을 수록 우선순위가 높음 (등수 제)**
- **넣었을 때 튜플로 넣었으므로 꺼내올 때도 튜플로 꺼내짐**

```python
import queue
# make Priority Queue
data_queue = queue.PriorityQueue()

# put
data_queue.put((10, "Enqueue"))
data_queue.put((5, 1))
data_queue.put((17, True))

# qsize
data_queue.qsize() # 3

# get
data_queue.get() # (5, 1)
data_queue.get() # (10, "Enqueue")
data_queue.get() # (17, True)
```

<br/>

## python의 list 변수로 Queue 구현하기

<br/>

- list를 사용하고, enqueue, dequeue의 기능이 있어야 함
- append의 경우 뒤에 값을 추가
- list의 indexing을 0으로 하여 앞에서 값을 꺼내옴

<br/>

```python
queue_list = list()
def enqueue(data):
    queue_list.append(data)
def dequeue():
    data = queue_list[0]
    del queue_list[0]
    return data
# dequeue는 원본에서 제거하고, 꺼내 보여주어야 함

for index in range(10):
    enqueue(index)

print(queue_list) # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
len(queue_list) # 10

print(dequeue()) # 0
print(dequeue()) # 1
print(dequeue()) # 2
print(dequeue()) # 3
print(len(queue_list)) # 6
```

<br/>

## JavaScript 에서 Queue

<br/>

- 일반적인 큐인 FIFO 방식이라면, 추가와 제거가 앞, 뒤로 구분 되어 있다.
- Array API 중에서 이미 구현된 함수가 있다.
  - `shift()` : 앞 제거 , `push()` : 뒤 추가
  - `unshift()` : 앞 추가, `pop()` : 뒤 제거
- 위와 같이 짝으로 사용시 FIFO Queue 방식이 됨

<br/>
<br/>
<br/>

> # Stack

<br/>

- 데이터를 제한적으로 접근할 수 있는 구조
  - **한쪽 끝에서만 자료를 넣거나 뺄 수 있는 구조**
- 가장 나중에 쌓은 데이터를 가장 먼저 빼낼 수 있는 데이터 구조
  - Queue : FIFO 정책 (선입 선출)
  - Stack : LIFO 정책 (후입 선출)

<br/>

## 스택 구조

<br/>

- 스택은 LIFO(마지막에 넣은 것을 첫번째로 추출) 또는 FILO(첫번째로 넣은 것을 마지막으로 추출) 데이터 관리 방식을 따름
- **컴퓨터 내부의 프로세스 구조의 함수 동작 방식**에서 대표적으로 스택이 활용됨
- 주요 기능 : `push()` (데이터를 스택에 넣기), `pop()` (데이터를 스택에서 꺼내기)
- [Data Structure 시각화 사이트](https://visualgo.net/en/list)

<br/>

## 스택 구조와 프로세스 스택

- 함수 호출시 프로세스 실행 구조를 스택과 비교해서 이해
- 아래 재귀 함수의 경우, 함수에서 자기함수를 호출함으로 써 어떻게 명령을 처리하는지 볼수 있음
  - 해당 함수로 들어오는 인자가 음수가 되면 "end"를 표시함
  - 0을 포함한 양수인 경우에는 해당 인자를 표시하고 자신을 불러 인자 값을 1씩 감소시켜 넣음 그리고 자기 함수를 처리하면, "returned"와 데이터를 표시함
  - 결국, 함수 처리가 완료가 되어야 다음 코드를 진행을 함
  - 1개의 Stack에 1개의 함수 처리가 들어가 data가 저장되고, 함수처리가 완료되면 data가 삭제됨

```python
# 재귀 함수 : 함수에서 자기 함수를 호출하는 함수
def recursive(data):
  if data < 0:
    print("end")
  else:
    print(data)
    recursive(data - 1)
    print("returned", data)

recursive(4)

# --- Process Stack ---
# |   recursive(-1)  |
# |   recursive(0)   |
# |   recursive(1)   |
# |   recursive(2)   |
# |   recursive(3)   |
# |   recursive(4)   |

# 4 <- recursive(4)
# 3 <- recursive(3)
# 2 <- recursive(2)
# 1 <- recursive(1)
# 0 <- recursive(0)
# "ended" <- recursive(-1)
# "returned" 0 <- recursive(0), del
# "returned" 1 <- recursive(1), del
# "returned" 2 <- recursive(2), del
# "returned" 3 <- recursive(3), del
# "returned" 4 <- recursive(4), del
```

<br/>

## 스택의 장단점

<br/>

- 장점
  - 구조가 단순해서, 구현이 쉽다
  - 데이터 저장/읽기 속도가 빠르다

<br/>

- 단점 (일반적인 스택 구현시)
  - 데이터 최대 갯수를 미리 정해야 함 (stack 공간을 미리 확보 해야함)
    - Python의 경우 재귀함수는 1000번 까지 호출이 가능함
  - 저장 공간의 낭비가 발생 할 수 있음 (저장 공간을 확보 해야 함으로)

<br/>
<br/>
<br/>

> ## Python list 기능에서 제공하는 메서드로 스택 사용하기

<br/>

- python의 경우 `append()`(push), `pop()` method 제공

```python
data_stack = list()

data_stack.append(1)
data_stack.append(2)

print(data_stack) # [1, 2]

data_stack.pop() # 2
```

<br/>

## 문제

- 리스트 변수로, pop, push 함수 구현해 보기

```python
stack_list = list()

def push(data):
  stack_list.append(data)

def pop():
  data = stack_list[-1]
  del stack_list[-1]
  return data

for index in range(10):
  push(index)

print(stack_list) # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(pop()) # 9
print(pop()) # 8
print(pop()) # 7
print(pop()) # 6
print(pop()) # 5
```

<br/>
<br/>
<br/>

> ## JavaScript Array를 이용하여 메서드로 스택 사용하기

<br/>

- 사실, JS에서는 이미 해당 함수를 가지고 있다.
- 아래의 javascript에서 제공하는 함수의 짝을 사용하면 Stack을 구현 할 수 있다.
  - `shift()` : 앞 제거 , `unshift()` : 앞 추가
  - `pop()` : 뒤 제거, `push()` : 뒤 추가

<br/>

- JS에서 제공하는 함수 말고, 스택 메서드 만들어 보기
  - Array에 추가하는 것은 push, unshift()를 사용할 수 밖에 없음 (python에서의 append 역할)
  - javascript의 경우 **음수 인덱싱은 지원하지 않고**, 배열에서 `splice(startIndex, delCount, insertItem)`로 어떤 값을 **삭제, 빼오기, 대체, 수정**이 가능하다.
  - range 함수가 따로 없어서 for문을 이용해서 연속된 숫자를 이용해야 한다.

```js
const stack_list = [];

function pop1() {
  const data = stack_list.splice(-1, 1);
  return data[0];
}

function push1(data) {
  stack_list.push(data);
}

for (let i = 0; i < 10; i += 1) {
  push1(i);
}

console.log(stack_list); // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
console.log(pop1()); // 9
console.log(pop1()); // 8
console.log(pop1()); // 7
console.log(pop1()); // 6
console.log(stack_list); // [0, 1, 2, 3, 4, 5]
```
