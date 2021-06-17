---
title: "20210617 DataStructure 03 : 연결 리스트(Linked List), 이중 연결 리스트(Double Linked List) " #제목
category: #카테고리
tag: #태그
  - DataStructure
toc: true #옆에 목차
---

> # DataStructure 03

<br/>

<p align="center">
<img src="../assets/img/dataStructure_logo.png" width="400px" height="400px">
</p>

<br/>
<br/>

> # Linked List (연결 리스트)

<br/>

> # Linked List 구조

<br/>

- 연결 리스트라고도 함
- 배열은 순차적으로 연속된 공간에 공간 개수를 예약을 해서 데이터를 나열하는 데이터 구조
  - 이러한 배열의 단점을 보완한 데이터 구조가 연결 리스트 임
- **연결 리스트는 떨어진 곳에 존재하는 데이터를 화살표로 연결해서 관리하는 데이터 구조**
  - **연결 리스트의 경우 공간이 연속되지 않고, 공간을 미리 예약을 하지 않고 필요할때 마다 공간을 추가 할수 있음(무한정 데이터를 저장 할 수 있음)**
  - C언어에서는 주요한 데이터 구조인데, 파이썬은 list type이 연결 리스트 기능을 모두 지원함

<br/>

- 배열은 1개의 공간에 1개의 데이터를 관리하지만, 연결리스트의 경우 배열과 달리 특정한 데이터를 저장하는 경우, 해당 데이터 공간 + 뒤에 나올 데이터의 공간을 가르키는 주소값을 가지는 공간(`포인터, Pointer`)을 세트로 하여 1개의 데이터(`노드(Node)`)로 관리함
- 용어
  - `포인터(Pointer)` : 각 노드 안에서 이전 또는 이후 노드의 연결 정보(위치한 곳)인 주소값을 가지는 공간
  - `노드(Node)` : 데이터값 + 포인터 를 1 세트로 하는 데이터 저장 단위

<br/>

- **맨 앞에 있는 노드의 Pointer(주소)만 알면, 연결 리스트의 모든 데이터에 접근할 수 있음**
- 마지막에 노드에 pointer가 존재하지 않으면, 그 노드가 끝임

<br/>

## 배열 vs 연결 리스트

|       항목       |     배열      |          연결 리스트          |
| :--------------: | :-----------: | :---------------------------: |
|    공간 연속     |       O       |               X               |
|    공간 예약     |       O       |               X               |
|    공간 추가     |       X       |               O               |
|       단위       | 1공간 1데이터 | 2공간 1노드 (데이터 + 포인터) |
|     공간효율     |     좋음      |           좋지 않음           |
|     접근속도     |     빠름      |             느림              |
| 중간 데이터 수정 |  부가작업 X   |        연결 작업 필요         |

<br/>

- [생활코딩 : linked list](https://opentutorials.org/module/1335/8821)

<br/>

## 연결 리스트의 장단점

- 장점
  - 데이터 공간을 미리 할당하지 않아도 됨
    - 배열은 미리 데이터 공간을 할당 해야 함
- 단점
  - 연결을 위한 별도 데이터 공간이 필요하므로, 저장공간 효율이 높지 않음
  - 연결 정보를 찾는 시간이 필요하므로 접근 속도가 느림
  - 중간 데이터 삭제시, 앞뒤 데이터의 연결을 재구성해야 하는 부가적인 작업필요

<br/>
<br/>
<br/>

> # 간단한 연결 리스트 구현하기

<br/>

- Python으로 연결리스트 구현시, python 클래스를 활용하여 객체지향(OOP) 문법 이해 필요
- 두 개의 데이터(값 + 주소)를 저장해야 하기 때문에 클래스를 사용

<br/>

## 연결 리스트 기본 구현

<br/>

```python
# Node class 정의
# data, next를 인자로 받음 (next 없으면 기본값 None)
class Node:
  def __init__(self, data, next=None):
    self.data = data
    self.next = next

# 데이터 추가
node1 = Node(1) # Node(1, None)
node2 = Node(2) # Node(2, None)

# 포인터 연결
node1.next = node2 # node1 = Node(1, Node(2, None))

# 가장 앞의 노드 주소 명시 (head)
head = node1 # head = node1 = Node(1, Node(2, None))
```

<br/>

## 데이터 추가하면서 포인터 연결하는 함수 만들기

<br/>

- 객체의 property 가 객체를 값으로 하는 컨셉
- head의 기준 값이 있어야 시작 할 수 있고, head의 경우 이미 어떤 값을 넣은 객체이고, 아직 pointer가 없는 상태인 Node(Value, None) 임
- 그리고, 데이터를 추가하는 로직의 경우 **head가 pointer 의 값이 pointer가 없지만 원하는 값을 가진 객체인 `Node(value, None)` 형태를 가지게 해야 한다.**
- 기준 Node를 head로 지정해서 함수에서 head를 계속 변경해서 가져오고 변경해서 가져오는 로직이여야 함

```python
class Node:
  def __init__(self, data, next=None):
    self.data = data
    self.next = next

def add(data):
  # 외부 변수 head값이 add에 의해서 변함
  node = head

  # next가 있으면 next를 가져와서 그 next의 next를 확인해서 next가 없을 때 까지 돌림
  while node.next:
    node = node.next
  # next가 없게 되면, next에 해당 값의 Node 객체를 넣음 (그 객체는 Next는 none 이고)
  node.next = Node(data)

node1 = Node(1) # node1 = Node(1 , None)

head = node1 # head = Node(1, None)
add(2) # -> head = Node(1, Node(2, None))
add(3) # -> head = Node(1, Node(2, Node(3, None)))
add(4) # -> head = Node(1, Node(2, Node(3, Node(4, None))))
add(5) # -> head = Node(1, Node(2, Node(3, Node(4, Node(5, None)))))

# 객체를 타고 값이 연결되는 것!
# 여러 값 추가하기
for i in range(6, 10):
  add(i)
```

<br/>

## 연결 리스트 데이터 출력하기 (검색하기)

```python
# Node 객체 겉에서 부터 안쪽으로 파고듬
node = head
while node.next:
  print(node.data)
  node = node.next
print(node.data) # 마지막 next가 None인 값의 data 출력을 위함
```

<br/>
<br/>
<br/>

> # 연결 리스트의 복잡합 기능 01: 중간에 데이터를 추가하는 경우

- 연결 리스트는 유지 관리에 부가적인 구현이 필요함
  - 새로운 C노드를 A노드(앞)와 B노드(뒤) 사이에 넣어 연결하려고 하는 경우
  - A노드의 주소는 C노드를, C노드의 주소는 B노드를 가르키게 해야함
  - A노드 Pointer = C노드, C노드 Pointer = B노드
  - `Node('A' Node('B' , None))` -> `Node('A' Node('C' Node('B' None)))`

```python
# Node Class 정의
class Node:
  def __init__(self, data, next=None):
    self.data = data
    self.next = next

# Add Funciton 정의
def add(data):
  node = head
  while node.next:
    node = node.next
  node.next = Node(data)

# data 추가
head = Node(1)
for i in range(2, 10):
  add(i)
# 1 2 3 4 5 6 7 8 9

# new Data 정하기
newNode = Node(1.5)

# ---- 데이터 1과 2사이에 1.5데이터를 넣고 싶음 ----

# 현재 상태의 Node 객체 가져오기
node = head

# 수정할 부분 찾기 (data가 1을 가지는 Node 찾기)
search = False
while not search:
  if node.data == 1:
    search = True # node = Node(1, Node(2, ... ))
  else:
    node = node.next

# 수정할 부분의 뒷부분 기억
node_next = node.next # node_next = Node(2, Node(3, ...))

# new Data 연결 작업
node.next = newNode # Node(1, Node(1.5, None))
newNode.next = node_next # Node(1, Node(1.5, Node(2, Node(3 , ...))))

# 값 확인하기
while node.next:
  print(node.data)
  node = node.next
print(node.data)
# 1 1.5 2 3 4 5 6 7 8 9
```

<br/>
<br/>
<br/>

> # Python OOP으로 연결 리스트 구현하기 (시작, 추가, 보기)

```python
# 연결 리스트 구현을 위한 재료인 Node Class
class Node:
  def __init__(self, data, next=None):
    self.data = data
    self.next = next

# Node 관리 Class (시작, 연결&값추가, 보기)
class NodeMgmt:

  def __init__(self, data):
    self.head = Node(data) # 시작시 head를 지정

  def add(self, data):
    if self.head == "": # head가 없으면 head를 만듦 (방어 코드)
      self.head = Node(data)
    else: # head가 있으면 받은 데이터를 head에 계속 연결 시킴
      node = head
      while node.next:
        node = node.next
      node.next = Node(data)

  def desc(self):
    node = self.head # 현재 상태의 head를 가져옴
    while node: # node.next가 None이 되어 node로 할당 될때 까지 해당 node 값을 print
      print(node.data)
      node = node.next

# 연결 리스트 생성
linked_list1 = NodeMgmt(0)

# 연결 리스트 보기
linked_list1.desc() # 0

# 연결 리스트 값 추가
for i in range(1, 10):
  linked_list1.add(i)

# 연결 리스트 보기
linked_list1.desc()
# 0 1 2 3 4 5 6 7 8 9
```

<br/>
<br/>
<br/>

> # 연결 리스트의 복잡합 기능 02: 특정 노드를 삭제하는 경우, 노드 찾기

<br/>

- 노드를 삭제하는 3가지 경우

  - **head의 값을 삭제** -> head를 head 다음 노드로 바꾸고, 기존의 head를 삭제
  - **마지막 노드의 값을 삭제** -> 마지막 노드 삭제, 그전 노드의 주소값 null 변경
  - **중간 노드의 값을 삭제** -> 중간 노드 삭제, 앞 노드의 주소값 과 뒷노드의 값을 연결
    - **공통적으로 마지막, 중간 노드의 값 삭제의 경우 둘다, 삭제하는 노드의 pointer를 앞 노드 pointer에 붙이면 됨**

<br/>

- 값을 기준으로 해당 노드를 찾는 함수 만들기
  - node를 돌면서, 해당 data가 맞으면 해당 node를 return 하면 됨

```python
class Node:
  def __init__(self, data, next=None):
    self.data = data
    self.next = next

class NodeMgmt:
  def __init__(self, data):
    self.head = Node(data)

  def add(self, data):
    if self.head == "":
      self.head = Node(data)
    else:
      node = head
      while node.next:
        node = node.next
      node.next = Node(data)

  def desc(self):
    node = self.head
    while node:
      print(node.data)
      node = node.next

  def delete(self, data):
    if self.head == "": # 방어 코드
      print("생성된 노드가 없습니다.")
      return

    if self.head.data == data: # head 삭제의 경우
      temp = self.head
      self.head = self.head.next
      del temp
    else: # 중간. 마지막 삭제의 경우
      node = self.head # Node(1, Node(2, Node(3, ...)))      # Node(1, Node(2, None))
      while node.next: # Node(2, Node(3, ...))               # Node(2, None)
        if node.next.data == data: # 2 == data (중간노드)     # 2 == data (끝 노드)
          temp = node.next # temp = Node(2, Node(3, ...))    # temp = Node(2, None)
          node.next = node.next.next # Node(1, Node(3, ...)) # Node(1, None)
          del temp # 제거
          return # 나가기
        else:
          node = node.next # 다음 노드 data 확인

    # 특정값의 노드 찾아 가져오기
    def search_node(self, data):
      node = self.head
      while node:
        if node.data == data:
          return node
        else:
          node = node.next

### 공통적으로 중간, 마지막 삭제의 경우 삭제하는 노드의 pointer를 앞 노드의 pointer에 연결하면 됨

linkedList1 = NodeMgmt(0)
linkedList1.head # <__main__.Node at 0x1099fc6a0> 즉, head 노드인 Node(0)을 표시
linkedList1.delete(0) # head 삭제
linkedList1.head # 아무것도 출력 되지 않음 -> 이때를 위해서 head 방어코드 만든 것

for i in range(1, 10): # 데이터 추가
  linkedList1.add(i)
linkedList1.desc() # 0 1 2 3 4 5 6 7 8 9

linkedList1.delete(4) # 중간 노드 삭제
linkedList1.desc() # 0 1 2 3 5 6 7 8 9

linkedList1.delete(9) # 끝 노드 삭제
linkedList1.desc() # 0 1 2 3 5 6 7 8

search_node3 = linkedList1.search_node(3) # 노드 찾기
print(search_node3.data) # 3
```

<br/>
<br/>
<br/>

> # 다양한 연결 리스트 구조 : Doubly linked list(이중 연결 리스트)

<br/>

## 이중 연결 리스트 기본 구조

<br/>

- 일반 연결 리스트의 경우 무수히 많은 데이터 중에서 엄청 뒤에 있는 데이터를 찾고자 한다면, head에서 부터 찾아야 해서 시간이 오래 걸리게 됨
- 그래서 원하는 데이터가 앞에서 가까운지, 뒤에서 가까운지만 대략적으로 안다면 시간을 조금 더 절약 할 수 있음
- 그래서, 이중 연결 리스트를 활용함
- **하나의 노드가 앞 노드, 뒷 노드의 주소 모두를 가지고 있는 형태 -> Pointer가 2개**

```python
class Node:
  def __init__(self, data, prev=None, next=None):
    self.data = data
    self.prev = prev
    self.next = next

class NodeMgmt:
  def __init__(self, data): # head -> Node(1 ,None, None) <- tail
    self.head = Node(data) # head는 다음 노드를 가르키고
    self.tail = self.head # tail은 이전 노드를 가르킴

  def insert(self, data):
    if self.head == None:
      self.head = Node(data)
      self.tail = self.head
    else:
      node = self.head # 해당 노드를 가져와서 기준으로 함
      while node.next: # 해당 노드의 다음 노드가 있으면 다음 노드를 기준으로 함
        node = node.next # 결국엔 node가 tail 전 노드로 마지막 노드를 가르킴
      new = Node(data) # 새로운 노드를 new에 기억함
      node.next = new # 해당 노드의 next에 새로운 노드를 가르키게 함
      new.prev = node # 새로운 노드의 prev의 기준 노드를 가르키게 함
      self.tail = new # tail 새로운 노드를 가르키게 함

  # prev, next는 노드 전체를 가르킨다! 다음 또는 전 노드의 prev, next가 아니라

  def desc(self):
    node = self.head
    while node:
      print(node.data)
      node = node.next

  def search_from_head(self, data):
    if self.head == None: # 방어 코드
      return False

    node = self.head
    while node:
      if node.data == data:
        return node
      else:
        node = node.next
    return False

  def search_from_tail(self, data):
    if self.tail == None: # 방어 코드
      return False

    node = self.tail
    while node:
      if node.data == data:
        return node
      else:
        node = node.prev
    return False

# 첫번째 매개변수가 새로운 Node 만들때 쓰는 값
# 두번째 매개변수가 노드 찾을 때 쓰는 값

  def insert_before(self, data, before_data):
    if self.head == None:
      self.head = Node(data)
      return True
    else:
      node = self.tail
      while node.data != before_data:
        node = node.prev
        if node == None:
          return False
      new = Node(data)
      before_new = node.prev
      if before_new != None: # 제일 첫번째 노드를 기준으로 추가하는 경우
        before_new.next = new
        new.prev = before_new
      else:
        self.head = new
        new.prev = self.head
      new.next = node
      node.prev = new
      return True

  def insert_after(self, data, after_data):
    if self.head == None:
      self.head = Node(data)
      return True
    else:
      node = self.head
      while node.data != after_data:
        node = node.next
        if node == None:
          return False
      new = Node(data)
      after_new = node.next
      new.next = after_new
      new.prev = node
      node.next = new
      if new.next == None: # 마지막 노드를 기준으로 추가하는 경우
        self.tail = new
      return True

# 반복문을 사용할 때는 해당 노드를 찾는 용도로 사용하고, 수정하는 작업은 반복문 나와서 다음에 작업 하는 걸로 해야 변함
```

## Test

```python
double_linked_list = NodeMgmt(0)
for data in range(1, 10):
  double_linked_list.insert(data)

double_linked_list.desc() # 1 2 3 4 5 6 7 8 9

search3_from_head = double_linked_list.search_from_head(3)
print(search3_from_head) # 3

search7_from_tail = double_linked_list.search_from_tail(7)
print(search7_from_tail) # 7

double_linked.insert_before("insertBefore0", 0)
double_linked.desc()
# insertBefore0 0 1 2 3 4 5 6 7 8 9

double_linked.insert_after("insertAfter9", 9)
double_linked.desc()
# insertBefore0 0 1 2 3 4 5 6 7 8 9 insertAfter9
```
