---
title: "20210623 DataStructure 07 : Heap(힙), 힙 vs 이진 탐색 트리, 힙 동작 구조, python으로 힙 구현하기(배열활용), 힙의 시간복잡도" #제목
category: #카테고리
tag: #태그
  - DataStructure
toc: true #옆에 목차
---

> # DataStructure 07

<br/>

<p align="center">
<img src="../assets/img/dataStructure_logo.png" width="400px" height="400px">
</p>

<br/>
<br/>

> # Heap (힙)

<br/>

- 힙: 데이터에서 최대값과 최소값을 빠르게 찾기 위해 고안된 완전 이진 트리(Complete Binary Tree)
  - 완전 이진 트리: 노드를 삽입할 때 최하단 왼쪽 노드부터 차례대로 삽입하는 트리

<br/>

- 힙을 사용하는 이유
  - 배열에 데이터를 넣고, 최대값과 최소값을 찾으려면 `O(n)`이 걸림
  - 힙에 데이터를 넣고, 최대값, 최소값을 찾으면 `O(log n)`이 걸림
  - 우선순위 큐와 같이 최대값, 최소값을 빠르게 찾아야 하는 자료구조 및 알고리즘 구현 등에 활용됨

<br/>

## 힙(Heap) 구조

<br/>

- 힙의 구조는 2 가지로, **최대 값을 구하기 위한 구조(Max Heap)** 와 **최소값을 구하기 위한 구조(Min Heap)** 중에서 1개를 가지게 된다.
- Max Heap : 최대 값이 root Node를 이룸, **각 부모노드는 자식 노드 보다 크거나 같은 값을 가짐**
- Min Heap : 최소 값이 root Node를 이룸, **각 부모노드는 자식 노드 보다 작거나 같은 값을 가짐**
- 공통점 : 좌측 부터 Node를 한 level 씩 채움

<br/>
<br/>
<br/>

> # 힙 VS 이진 탐색 트리

<br/>

## 공통점

- 둘 다 이진 트리임

<br/>

## 차이점

- 힙 : 각 노드의 값이 자식 노드보다 크거나 같음(작거나 같음)
  - `부모 노드 >(or <) 좌 자식, 우 자식` (자식의 좌우는 크기 상관 없이 배치됨)
  - **최대 값 또는 최소 값은 최상단 root Node**
  - **최대, 최소값을 빠르게 검색하기 위한 구조**

<br/>

- 이진 탐색 트리 : 부모노드를 중심으로 부모 노드보다 작으면 좌 자식 노드, 크면 우 자식 노드로 배치
  - `좌 자식노드 < 부모 노드 < 우 지식노드` 의 크기 차이를 가지고 배치 되어 있음
  - **최대값은 우 최하단, 최소값은 좌 최하단**
  - **특정 트리를 빠르게 탐색하기 위한 구조**

<br/>
<br/>

> # 힙(Heap) 동작

<br/>

## 데이터를 힙 구조에 삽입

<br/>

- 좌측 하단 부터 차례대로, 각 레벨을 채우고 다음 레벨로 이동하여 채우는 식의 동작
  - **단, 값을 비교해서 구조는 유지하되, 내부적으로 큰값이 위로 가게 배치를 수정 해줘야 함**
- 데이터 추가시 단계별 접근
  - 1단계 : 해당 순서로 값 크기 상관 없이 넣은 값을 배치
  - 2단계 : 배치된 자리에서 부모 노드와 비교 하는 식으로 해서, 배치 수정

<br/>

## 데이터를 힙 구조에서 삭제

<br/>

- 힙에서 데이터를 삭제한다는 것은, 매번 최대 or 최소값을 꺼내 삭제 함 (특정 값을 삭제 X)
- 데이터 삭제시 단계별 접근
  - 1단계 : 최대 or 최소 값 삭제
  - 2단계 : 제일 최근에 넣은 값을 위로 올려 빈 부분을 채움
  - 3단계 : 올린 값의 노드 위치에서, 자식 노드 두개를 비교해 둘중 큰값과 올린값을 비교하여 자리 교체
  - 4단계 : 최초에 올린값과 교체된 위치에서의 자식 노드중 큰값과 비교하여 자리 교체
  - ...
  - n 단계 : 부모 노드 보다, 최초에 올린 값이 작을 경우 교체를 멈추거나, 최초에 올린 값이 자식이 없을 경우 종료

<br/>
<br/>
<br/>

> # Python으로 힙 구현하기

<br/>

- 배열로 표현하기
- 힙의 경우 쌓이는 순서가 존재하기 때문에, 배열로 표현하고 해당 index를 가지고 어떤 노드가 부모인지 자식인지를 구분할 수 있다.
- 힙 구현의 편의를 위해서, 배열의 0번 index를 사용하지 않고, 1번 부터 사용하여 root 노드를 index 1번부터 사용함
  - `부모 노드 인덱스 번호 = 자식 노드 인덱스 번호 // 2 -> (몫)`
  - `왼쪽 자식 노드 인덱스 번호 = 부모 노드 인덱스 번호 * 2`
  - `오른쪽 자식 노드 인덱스 번호 = 부모 노드 인덱스 번호 * 2 + 1`

<br/>

> ## 힙에 데이터 삽입 구현(Max heap)

<br/>

- 값을 넣는 단계 (원래 들어가는 위치에)
- 값을 조건에 맞는 위치로 수정하는 단계
  - 비교하는 단계
  - 값을 바꾸는 단계
  - 올리는 단계

```python
class Heap:
  def __init__(self, data):
    self.heap_array = list()
    self.heap_array.append(None)
    self.heap_array.append(data)

  def move_up(self, inserted_idx):
    if inserted_idx <= 1: # 올라간 위치가 root 노드인 경우 멈춤
      return False
    # 부모 노드와 새로 추가한 노드 비교
    parent_idx = inserted_idx // 2
    if self.heap_array[inserted_idx] > self.heap_array[parent_idx]:
      return True
    else:
      return False


  def insert(self, data):
    if len(self.heap_array) == 0: # 방어코드
      self.heap_array.append(None)
      self.heap_array.append(data)
      return True
    # 값 추가 (크기 고려 X)
    self.heap_array.append(data)
    # 값 배치 수정 (크기 고려 O)
    insert_idx = len(self.heap_array) - 1 # 추가한 값의 index 가져오기
    # 반복 비교를 통해서 조건에 맞는 위치로 올리기
    while self.move_up(inserted_index): # 비교해서 올려야 하면 True
      parent_idx = inserted_index // 2
      self.heap_array[inserted_index], self.heap_array[parent_idx] = self.heap_array[parent_idx], self.heap_array[inserted_index] # 둘이 바꿔 주기 위함 (다중 할당)
      inserted_index = parent_idx # inserted_index를 부모 노드 index로 올림
    return True

# insert Test
heap = Heap(15)
heap.insert(10)
heap.insert(8)
heap.insert(5)
heap.insert(4)
heap.insert(20)
print(heap.heap_array) # [None, 20, 10, 15, 5, 4, 8] -> 20 (10, 15) - 10(5, 4) - 15(8)
```

<br/>
<br/>
<br/>

> ## 힙에 데이터 삭제 구현(Max heap)

<br/>

- 값을 빼오는 단계
- 제일 최근의 값을 root로 올리는 단계
- 올라온 값을 올바른 위치로 수정할지 결정할 단계 - True, False를 해줘야 각 구간마다 끊을 수 있음
  - 수정하면서, 결정하려면 pop에 따로 변수 만들어서 계속 검토할 부분에서는 True로 유지하고, 끝나는 구조에 False로 바꾸어 while을 끊어야 함
- root로 올라온 값을 올바른 위치로 수정하는 단계
  - 비교할 자식이 없는 경우 -> 비교 멈춤
  - 비교할 자식이 1개(left)인 경우 -> 해당 자식과 비교
  - 비교할 자식이 2개(left, right)인 경우 -> 둘 중 큰것을 선택해서 비교 -> left가 큰경우, right가 큰경우

```python
class Heap:
  def __init__(self, data):
    self.heap_array = list()
    self.heap_array.append(None)
    self.heap_array.append(data)

  def move_up(self, inserted_idx):
    if inserted_idx <= 1:
      return False
    parent_idx = inserted_idx // 2
    if self.heap_array[inserted_idx] > self.heap_array[parent_idx]:
      return True
    else:
      return False


  def insert(self, data):
    if len(self.heap_array) == 0:
      self.heap_array.append(None)
      self.heap_array.append(data)
      return True
    self.heap_array.append(data)
    insert_idx = len(self.heap_array) - 1
    while self.move_up(inserted_index):
      parent_idx = inserted_index // 2
      self.heap_array[inserted_index], self.heap_array[parent_idx] = self.heap_array[parent_idx], self.heap_array[inserted_index]
      inserted_index = parent_idx
    return True

  def move_down(self, poped_idx):
    left_child_popped_idx = popped_idx * 2
    right_child_popped_idx = popped_idx * 2 + 1
    # 비교할 자식이 없는 경우
    if left_child_popped_idx >= len(self.heap_array):
      return False
    # 비교할 자식이 1개
    elif right_child_popped_idx >= len(self.heap_array):
      if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
        return True
      else:
        return False
    # 비교할 자식이 2개
    else:
      # left가 더 큰 경우
      if self.heap_array[left_child_popped_idx] > self.heap_array[right_child_popped_idx]:
        if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
          return True
        else:
          return False
      # right가 더 큰 경우
      else:
        if self.heap_array[popped_idx] < self.heap_array[right_child_popped_idx]:
          return True
        else:
          return False


  def pop(self):
    if len(self.heap_array) <= 1: # 방어 코드
      return None

    # 값 빼오기 (어차피 덮어 씌워지기 때문에 삭제 작업은 필요 없음)
    return_data = self.heap_array[1]
    return returned_data

    # 제일 최근의 값을 root로 올리는 단계
    self.heap_array[1] = self.heap_array[-1]
    del self.heap_array[-1]
    popped_idx = 1

    # 올바른 위치로 수정하는 단계
    while self.move_down(popped_idx):
      left_child_popped_idx = popped_idx * 2
      right_child_popped_idx = popped_idx * 2 + 1

      # 비교할 자식이 1개
      elif right_child_popped_idx >= len(self.heap_array):
        if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
          self.heap_array[popped_idx], self.heap_array[left_child_popped_idx] = self.heap_array[left_child_popped_idx], self.heap_array[popped_idx]
          popped_idx = left_child_popped_idx  # index 수정

      # 비교할 자식이 2개
      else:
        # left가 더 큰 경우
        if self.heap_array[left_child_popped_idx] > self.heap_array[right_child_popped_idx]:
          if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
            self.heap_array[popped_idx], self.heap_array[left_child_popped_idx] = self.heap_array[left_child_popped_idx], self.heap_array[popped_idx]
            popped_idx = left_child_popped_idx # index 수정

        # right가 더 큰 경우
        else:
          if self.heap_array[popped_idx] < self.heap_array[right_child_popped_idx]:
            self.heap_array[popped_idx], self.heap_array[right_child_popped_idx] = self.heap_array[right_child_popped_idx], self.heap_array[popped_idx]
            popped_idx = right_child_popped_idx # index 수정

    return returned_data

# pop TEST

heap = Heap(15)
heap.insert(10)
heap.insert(8)
heap.insert(5)
heap.insert(4)
heap.insert(20)
print(heap.heap_array) # [None, 20, 10, 15, 5, 4, 8]

print(heap.pop()) # 20
print(heap.heap_array) # [None, 15, 10, 8, 5, 4]
```

<br/>
<br/>
<br/>

## move_down을 없애고 pop에 합친 버전

<br/>

```python
class Heap:
  def __init__(self, data):
    self.heap_array = list()
    self.heap_array.append(None)
    self.heap_array.append(data)

  def pop(self):
    if len(self.heap_array) <= 1:
      return None

    returned_data = self.heap_array[1]
    self.heap_array[1] = self.heap_array[-1]
    del self.heap_array[-1]
    popped_idx = 1

    check = True
    while check:
      left_child_popped_idx = popped_idx * 2
      right_child_popped_idx = popped_idx * 2 + 1
    # 비교할 자식이 없는 경우
      if left_child_popped_idx >= len(self.heap_array):
        check = False
    # 비교할 자식이 1개
      elif right_child_popped_idx >= len(self.heap_array):
        if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
          self.heap_array[popped_idx], self.heap_array[left_child_popped_idx] = self.heap_array[left_child_popped_idx], self.heap_array[popped_idx]
          popped_idx = left_child_popped_idx
        else:
          check = False
    # 비교할 자식이 2개
        else:
        # left가 더 큰 경우
          if self.heap_array[left_child_popped_idx] > self.heap_array[right_child_popped_idx]:
            if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
              self.heap_array[popped_idx], self.heap_array[left_child_popped_idx] = self.heap_array[left_child_popped_idx], self.heap_array[popped_idx]
              popped_idx = left_child_popped_idx
            else:
              check = False
        # right가 더 큰 경우
          else:
            if self.heap_array[popped_idx] < self.heap_array[right_child_popped_idx]:
              self.heap_array[popped_idx], self.heap_array[right_child_popped_idx] = self.heap_array[right_child_popped_idx], self.heap_array[popped_idx]
              popped_idx = right_child_popped_idx
            else:
              check = False

    return returned_data

  def move_up(self, inserted_idx):
      if inserted_idx <= 1:
          return False
      parent_idx = inserted_idx // 2
      if self.heap_array[inserted_idx] > self.heap_array[parent_idx]:
          return True
      else:
          return False

  def insert(self, data):
      if len(self.heap_array) == 1:
          self.heap_array.append(data)
          return True

      self.heap_array.append(data)
      inserted_idx = len(self.heap_array) - 1

      while self.move_up(inserted_idx):
          parent_idx = inserted_idx // 2
          self.heap_array[inserted_idx], self.heap_array[parent_idx] = self.heap_array[parent_idx], self.heap_array[inserted_idx]
          inserted_idx = parent_idx
      return True
```

<br/>
<br/>
<br/>

> # 힙 시간 복잡도

<br/>

- depth (트리 높이)를 h로 표기시
- n개의 노드를 가지는 heap에 데이터삽입, 삭제시 최악의 경우 root 노드에서 leaf 노드까지 깊숙히 비교해야하므로 h = log n
- `O(log n)`
- 계속 한번에 50%씩 노드를 처리함
