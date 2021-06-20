---
title: "20210620 DataStructure 05 : Hash Table(해쉬 테이블), 충돌(Collision), 충돌 해결법(Chaining, Linear Probing), 충돌 개선법, SHA(안전한 해시 알고리즘) " #제목
category: #카테고리
tag: #태그
  - DataStructure
toc: true #옆에 목차
---

> # DataStructure 05

<br/>

<p align="center">
<img src="../assets/img/dataStructure_logo.png" width="400px" height="400px">
</p>

<br/>
<br/>

> # Hash Table (해쉬 테이블)

<br/>

## 해쉬 구조

<br/>

- 키(key)에 데이터(value)를 저장하는 데이터 구조
- **해쉬는 key를 가지고 hash 함수를 계산하면 data가 저장되어야 할 위치(되어 있는 위치)가 나옴**
- 배열은 어떤 데이터를 검색하는 경우 전체 데이터를 순회해야함
- 하지만, hash는 순회할 필요 없이 해당 데이터의 key만 hash 함수로 계산하면 바로 데이터가 저장되어 있는 위치가 나오기 때문에 **성능이 엄청 좋음**
- Python의 딕셔너리(Dictionary) 타입이 해쉬 테이블의 예임 (**key - value 구조**)
  - 그래서, 파이썬에서는 해쉬를 별도 구현할 이유가 없음 - **딕셔너리 타입**을 사용하면 됨
- 보통 배열로 미리 Hash Table 사이즈 만큼 생성 후에 사용(공간과 탐색 시간을 맞바꾸는 기법)
  - 내부적으로 hash table을 구현하는 경우 배열을 많이 사용

<br/>

## 용어

<br/>

- 해쉬(hash): 어떠한 임의 값의 길이(방대한 데이터)라도 이를 고정 길이(256정도)로 변환하는 것 자체를 말함
- 해쉬 테이블 : 키 값의 연산에 의해 직접 접근이 가능한 데이터 구조
  - (해시 주소와 슬롯 정보를 가지고 있는 데이터 구조임)
- 해싱 함수 : key에 대해 산술 연산을 이용해 데이터 위치를 찾을 수 있는 함수
- 해쉬값(해쉬주소) : key를 해싱 함수로 연산해, 해쉬 값(해쉬 주소)을 알아내 해쉬 테이블에서 해당 key에 대한 데이터 위치를 일관성 있게 찾을 수 있음
- 슬롯(Slot): 해쉬테이블 안에서 1개의 데이터를 저장할 수 있는 공간
- 저장할 데이터에 대해 key를 추출할 수 있는 별도 함수도 존재 할 수 있음

<br/>

## 장단점 & 주요용도

<br/>

- 장점
  - 데이터 저장/읽기 **속도가 빠름**(검색 속도가 빠르다)
  - 해쉬는 키에 대한 데이터가 있는지(중복) 확인이 쉬움
- 단점
  - 일반적으로 저장공간이 좀더 많이 필요하다.
    - hash address가 index의 역할을 하다보니, 데이터가 들어 있는 index가 차곡차곡 쌓여 있는 구조가 아니라서 그 중간에 빈 곳도 공간 확보가 필요함
  - **여러 키에 해당하는 주소가 동일할 경우 `충돌`을 해결하기 위한 별도 자료구조가 필요함**
    - key값의 종류가 별로 없는 로직인 경우 중복된 key이 발생할 수 있고, 같은 key값이 사용되어 기존의 값을 덮어씌어 저장하여 이전 값이 날라 갈 수도 있음
    - 그래서, 테이블의 크기를 크게 해놓으면, 충돌 문제를 조금 덜어 줄 수 있음

<br/>

- 주요용도
  - 검색이 많이 필요한 경우
  - **저장, 삭제, 읽기가 빈번한 경우**
  - **캐쉬 구현시 (중복 확인이 쉽기 때문)**
    - 캐시 : 자주 표현되는 데이터의 경우 local에 저장하여 별도의 다운 없이 사용함

<br/>

## 해쉬 예시 : 전화번호 부

<br/>

- python list comprehension : 반복되는 많은 값을 배열로 손쉽게 만드는 방법
  - `[k for i in range(10)]` or `list(k for i in range(10))`
    - -> `[k, k, k, k, k, k, k, k, k, k]`
  - for 앞에 있는 문자나 변수를 for 만큼 반복하여 return

<br/>
<br/>
<br/>

> # 기본 동작 구조 코드

<br/>

```python
# hash table을 위한 slot 만들기
hash_table = list([i for i in range(10)])
print(hash_table)  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# 해쉬 함수 만들기 (Divison 방법 : 나누기를 통한 나머지 값을 사용하는 기법)
def hash_func(key):
  return key % 5 #  key를 5로 나눈 나머지

# 실제 데이터
data1 = 'Tom'
data2 = 'Jerry'
data3 = 'Snoopy'

# ord() : 해당 문자의 아스키(ASCII) 값을 return 하는 함수

# 일단, 데이터에 따라 필요한 key 생성을 위해서, 데이터의 제일 앞 문자의 아스키 코드 값을 키값으로 활용
print (ord(data1[0]), ord(data2[0]), ord(data3[0])) # 84 74 83
print (ord(data1[0]), hash_func(ord(data1[0]))) # key: 84, Hash Address: 4
# 즉, data :'Tom', key: 84, Hash Address: 4 , hash function : key % 5
```

<br/>

## 함수로 정리하기

<br/>

```python
# slot 만들기
hash_table = list([i for i in range(10)])

# 데이터 저장 함수
def storage_data(data, value):
  key = ord(data[0]) # data로 key 만들기
  hash_address = hash_func(key) # key로 hash address 만들기
  hash_table[hash_address] = value # hash table slot에 해당 hash address에 value 저장하기

# 데이터 저장 하기 (해당 이름 사람의 전화번호)
storage_data('Park', '01012341234')
storage_data('Kim', '01012123434')
storage_data('Lee', '01056567878')

# 데이터 읽기 함수
def get_data(data):
  key = ord(data[0])
  hash_address = hash_func(key)
  return hash_table[hash_address]

# 데이터 읽기
print(get_data('Park')) # 01012123434
print(get_data('Kim')) # 01012123434
print(get_data('Lee')) # 01056567878
```

<br/>
<br/>
<br/>

## hash table 구현 연습

<br/>

- 해쉬 함수 : `key % 8`
- 해쉬키 생성은 python의 내장함수 `hash(data)`로 활용
  - hash 함수의 경우에 계속 동일한 결과를 주진 않고 프로그램을 끄거나, 하면 달라질 수 있음

```python
# slot 만들기 (키의 종류는 8개 뿐이니까 공간 8개)
hash_table = [i for i in range(8)]

def hash_func(key):
    return key % 8

def save_data(data, value):
    hash_address = hash_func(hash(data))
    hash_table[hash_address] = value

def get_data(data):
    hash_address = hash_func(hash(data))
    return hash_table[hash_address]

save_data('Park', '01012341234')
save_data('Kim', '01012123434')
save_data('Go', '01056567878')

print(hash_table)
# ['01056567878', '01012341234', 2, '01012123434', 4, 5, 6, 7]
```

<br/>
<br/>
<br/>

> # 충돌(Collision) 해결 알고리즘 : 좋은 해쉬 함수 사용하기

<br/>

- 충돌현상 : Collisioin, Hash Collision
  - hash address가 중복되어 같은 공간에 저장해야 하는 경우 어떤 것을 저장할지, 아니면 어떻게 둘다 저장 할지 충돌이 생기고, 자의적으로 판단하면 덮어씌어 질수도 있어 전에 있던 데이터가 날라 갈수 있음

<br/>

## Chaining 기법

<br/>

- Open Hashing(개방 해싱) 중 하나로, 해쉬 테이블 저장공간 외의 공간을 활용하는 기법
- **Chaining 기법** : 충돌 발생시, 링크드 리스트 자료구조를 사용해서, **링크드 리스트로 데이터를 추가로 뒤에 연결시켜서 저장하는 기법**

<br/>

- 링크드 리스트 말고, Python의 배열 자료 구조를 활용하여 Chaining 기법 구현하기
  - **기본적으로 , hash address의 중복발생 때문에 발생하는 충돌만 고려하고 key 값은 중복이 없는 data의 고유한 값인 것으로 가정한다.**
  - Chaining 기법의 전체적인 형태는 hash_talbe에서 hash_address 자리의 값이 일반적인 value에서 list 형태로 변하여 안에는 item으로 `[index_key, value]` 형태인 자료구조로 만듦

```js
// Chaining 기법으로 구현하는 hash_table의 자료구조 형태 (list O, linked list X)
// 구현할 형태를 보기에는 js 형식이 더 편한 것 같긴 함
let ChainingHashTable = [
  [['1211241241', 'hash1CollisionData1'], ['32352361214', 'hash1CollisionData2']],
  0,
  [[['6235124346241', 'hash2CollisionData1'], ['723532261214', 'hash2CollisionData2']],
  0,
  0,
  0,
  0,
  0
]
```

```python
hash_table = list([0 for i in range(8)]) # 처음 default 값은 0으로 되어 있음

def get_key(data):
  return hash(data)

def hash_function(key):
  return key % 8

def save_data(data, value):
  index_key = get_key(data) # hash 중복을 key로 구분 하기에 받아옴
  hash_address = hash_function(index_key(data))
  if hash_table[hash_address] != 0: # 0이 아닌 경우 이미 그자리에 데이터가 들어 있다는 것
    for index in range(len(hash_table[hash_address])):
     # 기존의 value 자리를 list로 만들어서 [index_key, value] 형태들로 채움
      if hash_table[hash_address][index][0] == index_key:
        # 자료를 돌며, 같은 index_key는 덮어 씌워 update 시킴
        hash_table[hash_address][index][1] = value
        return
    # index_key가 같다고 판단되는게 없으면 -> 추가 함
    hash_table[hash_address].append([index_key, value])
  else: # 데이터가 한번도 저장 되지 않은 경우 (초기값 0 인경우)
    hash_table[hash_address] = [[index_key, value]]
    # 2중 배열 형태로 변경 시켜 해당 구조로 자료를 넣음

def get_data(data):
  index_key = get_key(data)
  hash_address = hash_function(index_key(data))

  if hash_table[hash_address] != 0: # 자료가 있는 경우
    for index in range(len(hash_table[hash_address])):
      if hash_table[hash_address][index][0] == index_key:
        return hash_table[hash_address][index][1]
    # 자료를 돌아서 해당 index_key에 해당하는 값을 return
    return None # 맞는게 없으면, None
  else: # 저장된 데이터가 없는 경우
    return None

# hash address충돌 (key 충돌X)
print (hash('Dd') % 8) # 2
print (hash('Data') % 8) # 2

save_data('Dd', '1201023010')
save_data('Data', '3301023010')
get_data('Dd') # '1201023010'
```

<br/>
<br/>
<br/>

## Linear Probing 기법

<br/>

- Close Hasing (폐쇄 해싱) 중 하나로, 해쉬 테이블 저장공간 안에서 충돌 문제를 해결하는 기법
- 충돌시, 중복이 발생 중 나중에 들어온 hash address 부터 맨처음 나오는 hash table의 빈공간에 저장하는 기법 -> **저장 공간 활용도를 높이기 위한 기법**

```js
// hash_talbe =
let LinearProbingHashTable = [
  0,
  ["1211241241", "hash1CollisionData1"],
  ["32352361214", "hash1CollisionData2"],
  (0)[("6235124346241", "hashOrdinaryData")],
  0,
  0,
  0,
];
```

```python
hash_table = list([0 for i in range(8)])

def get_key(data):
  return hash(data)

def hash_function(key):
  return key % 8

def save_data(data, value):
  index_key = get_key(data) # 마찬가지로 key를 같이 가짐으로써 구분하기 위함
  hash_address = hash_function(index_key)
  if hash_table[hash_address] != 0:
    # 찾은 해당 hash_address slot 부터 그 위로 찾음 (그 전에는 안찾음)
    for index in range(hash_address, len(hash_table)):
      if hash_table[index] == 0: # 그 다음으로 돌면서 값이 없는 index를 찾아 해당 값을 넣음
        hash_table[index] = [index_key, value]
        return
      elif hash_table[index][0] == index_key: # 원래 동일한 부분의 Update의도
        hash_table[index][1] == value
        return
  else:
    hash_table[hash_address] = [index_key, value] # 한번도 들어간 적이 없는 경우

def get_data(data):
  index_key = get_key(data)
  hash_address = hash_function(index_key)

  if hash_table[hash_address] != 0:
    for index in range(hash_address, len(hash_table[hash_address])):  # 빈 slot 까지 순회
      if hash_table[index][0] == 0:
        return None
      elif hash_table[index][0] == index_key:
        return hash_table[index][1]
  else:
    return None

# hash address충돌 (key 충돌X)
print (hash('Dd') % 8) # 2
print (hash('Data') % 8) # 2

save_data('Dd', '1201023010')
save_data('Data', '3301023010')
get_data('Dd') # '1201023010'
```

<br/>
<br/>
<br/>

> # 빈번한 충돌을 개선하는 기법

<br/>

- hash가 좋은 자료구조지만, 빈번하게 충돌이 나면 쓸 이유가 없음

<br/>

## 공간(slot)을 늘려서 해결

<br/>

- slot의 개수에 비해 저장한 데이터 개수가 slot 개수의 50 % 이상 인경우 충돌 발생 확률이 높아짐
  - 그래서, 일반적으로 저장할 데이터 개수에 2배로 slot을 늘려 사용함

```python
# 만약 저장할 데이터가 8개 미만인 경우
hash_table = [None for i in range(16)]

def hash_func(key):
  return key % 16
# 저장할 데이터의 2배인 slot 의 개수 16개로 설계
```

<br/>
<br/>
<br/>

> # 유명한 해쉬 함수와 키 생성 함수

<br/>

- SHA(Secure Hash Algorithm, 안전한 해시 알고리즘)
  - 어떤 데이터도 유일한 고정된 크기의 고정값을 return해주므로, 해쉬 함수로 유용하게 활용 가능

<br/>

## SHA-1

<br/>

- hash library의 경우 모두 원본 데이터의 바이트 형태로 데이터를 받음
  - `encode()`를 활용하거나, 문자 앞에 `b`를 사용함 (예.`b'test'`)
- 추출의 경우 `hexdigest()` 활용하여 보통 16진수로 추출함
- **문자열이 같으면 무조건 동일한 해쉬값이 나옴**

```python
import hashlib

data = 'test'.encode()
# 데이터를 바이트 형태로 풀어 표현해서 넣어 주어야 함

hash_object = hashlib.sha1()
# hashlib에서 제공하는 hash 방식을 선택하여 해당 방식의 object를 생성

hash_object.update(data)
# object에 있는 update 함수를 사용하여 해당 데이터를 넣어줌

hex_dig = hash_object.hexdigest()
# 해당 데이터를 가지고 있는 object에서 16진수로 추출(보통 16진수로 추출함)

print(hex_dig)
# a94a8fe5ccb19ba61c4c0873d391e987982fbbd3 (문자열이 같으면 무조건 동일한 해쉬값이 나옴)
```

<br/>

## SHA-256

<br/>

- sha-256의 경우 해시값을 가지고 **원본 데이터를 추론할 수 없기 때문에**, sha-1 보다 더 안전함
- 사용 방식은 SHA-1과 동일하고, Object 만들때 `sha256()`을 사용하면 됨

```python
import hashlib

data = 'test'.encode()
hash_object = hashlib.sha256()
hash_object.update(data)
hex_dig = hash_object.hexdigest()
print (hex_dig)
# 9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08
```

<br/>

## 기존 Chaining 기법 sha256 해쉬 알고리즘 활용하여 수정

<br/>

```python
import hashlib
hash_table = list([0 for i in range(8)])

# 고유값을 hash 화
def get_key(data):
  hash_obj = hashlib.sha256()
  hash_obj.update(data.encode())
  hex_dig = hash_obj.hexdigest()
  return int(hex_dig, 16) # 문자를 나눌 수는 없으므로, 숫자화

def hash_function(key):
  return key % 8

def save_data(data, value):
  index_key = get_key(data)
  hash_address = hash_function(index_key)
  if hash_table[hash_address] != 0:
    for index in range(len(hash_table[hash_address])):
      if hash_table[hash_address][index][0] == index_key:
        hash_table[hash_address][index][1] = value
        return
    hash_table[hash_address].append([index_key, value])
  else:
    hash_table[hash_address] = [[index_key, value]]

def get_data(data):
  index_key = get_key(data)
  hash_address = hash_function(index_key)

  if hash_table[hash_address] != 0:
    for index in range(len(hash_table[hash_address])):
      if hash_table[hash_address][index][0] == index_key:
        return hash_table[hash_address][index][1]
    return None
  else:
    return None

# Test
print (get_key('db') % 8) # 1
print (get_key('da') % 8) # 2
print (get_key('dh') % 8) # 2

save_data('da', '2312352352323')
save_data('dh', '23623624262')
get_data('dh') # 23623624262
```

<br/>
<br/>
<br/>

> # Hash table의 시간 복잡도

<br/>

- 일반적인 경우로 충돌이 없는 경우 : `O(1)`
- 최악의 경우로 모두 충돌이 발생하는 경우 : `O(n)`
  - bigO 표현으로 하면 최악으로 표기 해야 하지만, 해쉬 테이블을 만들 때 충돌을 없애고자 하기 때문에 일반적으로 `O(1)`로 봄

<br/>

## 배열 vs 해쉬 테이블

<br/>

- 16개의 배열에 데이터를 저장하고, 검색하는 경우 : `O(n)`
- 16개의 데이터 저장공간을 가진 해쉬 테이블에 데이터를 저장하고, 검색할때 : `O(1)`
