---
title: "20210622 DataStructure 06 : Tree (트리), Binary Tree(이진 트리), Binary Search Tree(이중 탐색 트리), python으로 이진 탐색 트리 구현" #제목
category: #카테고리
tag: #태그
  - DataStructure
toc: true #옆에 목차
---

> # DataStructure 06

<br/>

<p align="center">
<img src="../assets/img/dataStructure_logo.png" width="400px" height="400px">
</p>

<br/>
<br/>

> # Tree (트리)

<br/>

- 자료구조이기도 하지만, 알고리즘 같은 로직도 많이 들어가서 알고리즘의 느낌도 있음
- 실제로 구현시 난이도가 높음
- **Tree(트리)** : Node와 Branch를 이용해서, 사이클을 이루지 않도록 구성한(사이클 관계X) 데이터 구조
- 이진 트리 (Binary Tree) 형태의 구조로, 검색 알고리즘 구현을 위해 많이 사용됨

<br/>

## 용어

<br/>

- Node : 트리에서 데이터를 저장하는 기본 요소 (데이터와 다른 연결된 도드에 대한 Branch 정보 포함)
- Root Node : 트리 최상위 노드
- Level : 최상위 노드를 Level 0으로 하였을 때, 하위 Branch로 연결된 해당 노드의 깊이
- Parent Node : 어떤 노드의 상위 레벨을 이루는 노드 (부모 노드)
- Child Node : 상위 레벨에 연결된 노드 (자식 노드)
- Leaf Node (Terminal Node) : Child Node가 하나도 없는 노드(끝나는 노드)
- Sibling (Brother Node) : 동일한 Parent Node를 가진 노드 (같은 레벨이라도, Sibling이 아닐 수 있음)
- Depth: 트리에서 Node가 가질 수 있는 최대 Level

<br/>

## 이진 트리(Binary Tree) vs 이진 탐색 트리(Binary Search Tree)

<br/>

- 이진 트리 : 노드의 최대 Branch가 2인 트리 (child Node가 0, 1, 2)
- **이진 탐색 트리(BST)** : 이진 트리에서 부모 노드 보다 작은 값을 가지는 자식노드는 왼쪽 자식 노드로, 부모 모느 보다 큰 값을 가지는 자식노드는 오른쪽 자식 노드로 배치하는 트리
  - 새로 들어오는 값을 계속 비교해서 배치함

<br/>

## 이진 탐색트리 장단점& 주요용도

<br/>

- 데이터 검색(탐색)에 주로 쓰임
- 장점 : 탐색 속도 개선
- 단점 : 복잡함

<br/>

## 배열 vs 이진 탐색 트리: 탐색 비교

<br/>

- 배열의 경우, 찾을 때 까지 모든 자료를 순회하면서 탐색하게 됨 (최악의 경우 배열의 크기만큼 걸릴 수 있음)
- 이진 탐색 트리의 경우, 해당 자료가 놓인 level 만큼의 횟수로 찾을 수 있음

<br/>
<br/>
<br/>

> # Python 링크드 리스트를 활용한 이진 탐색 트리 구현

<br/>

- 좌 우로 pointer를 가지고 있음

```python
# Node 만들기

class Node:
  def __init__(self, value):
    self.value = value
    self.left = None
    self.right = None
```

```python
# 이진 탐색 트리 구현

class NodeMgmt:
  def __init__(self, head): # root Node 가 Head
    self.head = head

  def insert(self, value):
    self.current_node = self.head
    while True: # 일단 계속 돌게 하기 위해서
      if value < self.current_node.value: # parent Node 보다 넣는 값이 작은 경우
        if self.current_node.left != None: # parent 에서 left에 값이 있는 경우
          self.current_node = self.current_node.left # 현재 노드를 left 노드로 함
        else:
          self.current_node.left = Node(value)
          break
      else: # 값이 같을 경우에도 오른쪽 노드로 배치
        if self.current_node != None:
          self.current_node = self.current_node.right
        else:
          self.current_node.right = Node(value)
          break

  def search(self, value):
    self.current_node = self.head
    while self.current_node:
      if self.current_node.value == value:
        return True
      elif value < self.current_node.value:
        self.current_node = self.current_node.left
      else:
        self.current_node = self.current_node.right
    return False
```

```python
# 이진 탐색 트리 TEST

head = Node(1)
BST = NodeMgmt(head)
BST.insert(2)
BST.insert(3)
BST.insert(0)
BST.insert(4)
BST.insert(8)

BST.search(4) # True
BST.search(-1) # False
```

<br/>
<br/>
<br/>

## 이진 탐색 트리 삭제 구현

<br/>

- 삭제의 경우의 수
  - Leaf Node를 삭제하는 경우
    - 해당 노드 삭제
  - 삭제할 노드의 Child Node가 1개인 경우
    - 해당 자식을 올림
  - **삭제할 노드의 child Node가 2개인 경우**
    - **왼쪽 기준으로 올리는 경우 : left 노드의 후손 중 제일 큰 값인 노드**
    - **오른쪽 기준으로 올리는 경우 : right 노드의 후손 중 제일 작은 값인 노드**
      - **삭제할 노드의 right 노드의 제일 작은 값인 노드(올리는 노드)가 자식을 가지는 경우 해당 자식은 올리는 노드의 부모의 left로 연결해줌**
  - 삭제할 노드가 없는 경우

```python
# Node 만들기

class Node:
  def __init__(self, value):
    self.value = value
    self.left = None
    self.right = None

# 이진 탐색 트리 구현

class NodeMgmt:
  def __init__(self, head): # root Node 가 Head
    self.head = head

  def insert(self, value):
    self.current_node = self.head
    while True:
      if value < self.current_node.value:
        if self.current_node.left != None:
          self.current_node = self.current_node.left
        else:
          self.current_node.left = Node(value)
          break
      else:
        if self.current_node != None:
          self.current_node = self.current_node.right
        else:
          self.current_node.right = Node(value)
          break

  def search(self, value):
    self.current_node = self.head
    while self.current_node:
      if self.current_node.value == value:
        return True
      elif value < self.current_node.value:
        self.current_node = self.current_node.left
      else:
        self.current_node = self.current_node.right
    return False

  def delete(self, value):
    searched = False
    self.current_node = self.head
    self.parent = self.head

    # 해당 Node 찾기 및 parent 기억
    while self.current_node:
      if self.current_node.value == value:
        searched = True
        break
      elif value < self.current_node.value:
        self.parent = self.current_node
        self.current_node = self.current_node.left
      else:
        value > self.current_node.value:
        self.parent = self.current_node
        self.current_node = self.current_node.right

    if searched == False:
      return False

    # Leaf Node
    if self.current_node.left == None and current_node.right == None:
      if value < self.parent.value:
        self.parent.left = None
      else:
        self.parent.right = None

    # 1 child
      # current node에 left에 child가 있는 경우
    elif  self.current_node.left != None and current_node.right == None:
      # current node가 parent의 left인 경우
      if value < self.parent.value:
        self.parent.left = self.current_node.left
      # current node가 parent의 right인 경우
      else:
        self.parent.right = self.current_node.right
    # current node에 right에 child가 있는 경우
    elif self.current_node.left == None and current_node.right != None:
      # current node가 parent의 left인 경우
      if value < self.parent.value:
        self.parent.left = self.current_node.right
      # current node가 parent의 right인 경우
      else:
        self.parent.right = self.current_node.right

    # 2 child
    # (삭제하여, 대체되는 node의 경우 3갈래를 수정해줘야 함)
    elif  self.current_node.left != None and current_node.right != None:
      # current_node : parent의 left에 있는 경우
      if value < self.parent.value:
        self.change = self.current_node.right
        while self.change.left != None:
          self.change_parent = self.change
          self.change = self.change.left
        # change.right 처리
        if self.change.right != None:
          self.change_parent.left = self.change.right
        else:
          self.change_parent.left = None
      # 새로운 current로 오는 change의 3갈래 연결부 연결
        # parent - change
        self.parent.left = self.change
        # change right - 기존 current right
        self.change.right = self.current_node.right
        # change left - 기존 current left
        self.change.left = self.current_node.left
        del self.current_node
      # current_node : parent의 right에 있는 경우 (새로운 parent 연결 차이)
      else:
        self.change = self.current_node.right
        while self.change.left != None:
          self.change_parent = self.change
          self.change = self.change.left
        # change.right 처리
        if self.change.right != None:
          self.change_parent.left = self.change.right
        else:
          self.change_parent.left = None
      # 새로운 current로 오는 change의 3갈래 연결부 연결
        # parent - change
        self.parent.right = self.change
        # change right - 기존 current right
        self.change.right = self.current_node.right
        # change left - 기존 current left
        self.change.left = self.current_node.left

    # 마침내 해당 노드 삭제
    del self.current_node
    return True
```

- 최대의 경우의 수를 고려하고, 각각 나누어 디테일하게 만듦
  - 그림으로 이해
  - 수정될 노드 선정 및 위치 경우의 수
  - 대체될 노드 선정
  - 연결 수정 부분 체크

<br/>
<br/>
<br/>

## python 전체 코드 테스트

<br/>

- random 라이브러리 활용
  - `radom.randint(startNumber, endNumber)` : star ~ end 사이에 있는 숫자를 랜덤하게 return

```python
import random

# 0 ~ 999 중 100개 숫자 랜덤 선택
bst_nums = set() # 중복 제거를 위해 집합을 사용
while len(bst_nums) != 100:
  bst_nums.add(random.randint(0, 999))

# root 노드는 500 (좌우 균형)
head = Node(500)

# 랜덤 100 개의 숫자 BST 에 넣음
binary_tree = NodeMgmt(head)
for num in bst_nums:
  binary_tree.insert(num)

# 100 개 숫자 검색
for num in bst_nums:
  if binary_tree.search(num) == False:
    print('search failed', num)

# 입력한 100개의 숫자 중 10개의 숫자 랜덤 선택
delete_nums = set() # 중복 제거를 위해 set 사용
bst_nums = list(bst_nums)
while len(delete_nums) != 10:
  delete_nums.add(bts_nums[random.randint(0, 99)])

# 랜덤 10개의 숫자 삭제
for del_num in delete_nums:
  if binary_tree.delete(del_num) == False:
    print('delete failed', del_num)
```

<br/>
<br/>
<br/>

> # 이진 탐색 트리의 시간 복잡도와 단점

<br/>

## 시간 복잡도

<br/>

- depth (트리 높이)를 h라고 표기 한다면, `O(h)`
- n개의 노드를 가진 다면, h = log n에 가까우니까 시간복잡도는 `O(log n)`
  - log의 밑은 2 임
  - 한번 찾을 때, 50%의 나머지 경우의 명령을 제거 -> 50%의 실행시간을 단축 시킬수 있음

<br/>

## 단점

<br/>

- 트리가 균형이 잡혀 있는 경우 평균 시간 복잡도는 `O(log n)`이지만,
  - **트리가 균형잡히지 않은 경우 링크드 리스트 등과 동일한 성능인 `O(n)`성능을 보여줌**
  - root 값이 중간 값에서 멀어 질 수록, 들어가는 값이 연속되어 커지는 경우 -> 트리 불균형
  - right로 치우치는 경우 : root(1) - (2) - (3) - (4) - ...
  - left로 치우치는 경우 : root(n) - (n-1) - (n-2) ...
