---
title: "20210616 DataStructure 01 : 자료구조, 알고리즘, Jupyter notebook, Array(1차, 2차 배열), 문제" #제목
category: #카테고리
tag: #태그
  - DataStructure
toc: true #옆에 목차
---

> # DataStructure 01

<br/>

<p align="center">
<img src="../assets/img/dataStructure_logo.png" width="400px" height="400px">
</p>

<br/>
<br/>

> # DataStructure & Algorithm (자료구조와 알고리즘)

<br/>

## 자료구조

<br/>

- 용어 : 자료구조, 데이터 구조, data structure
- **대량의 데이터를 효율적으로 관리할 수 있는 데이터의 구조를 의미**
- 코드상에서 효율적으로 데이터를 처리하기 위해, 데이터 특성에 따라, 체계적으로 데이터를 구조화 해야함
  - 어떤 데이터 구조를 사용하느냐에 따라, 코드 효율이 달라짐

<br/>

- 효율적 데이터를 관리하는 예
  - 우편번호, 학생 관리(학년, 반, 번호) -> 특정 데이터를 효율적으로 찾을 수 있음

<br/>

- 대표적인 자료구조
  - **배열, 스택, 큐, 링크드 리스트, 해쉬 테이블, 힙 등...**

<br/>
<br/>

## 알고리즘

<br/>

- 용어: 알고리즘, algorithm
- **어떤 문제를 풀기 위한 절차/ 방법**
- 어떤 문제에 대해, 특정한 입력을 넣으면, 원하는 출력을 얻을 수 있도록 만드는 프로그래밍
  - **자료를 가공하는 레시피**

<br/>

- 주요요소 : 시간이 얼마나 걸리느냐, 얼마의 저장공간을 사용하는 가 -> 계산 가능해야 함

<br/>

- **자료구조와 알고리즘을 어떻게 쓰느냐에 따라서 성능이 천지 차이라서 프로그래밍을 잘 할 수 있는 기술과 역량을 익히고, 검증할 수 있는 요소로서 작용 함**

<br/>
<br/>
<br/>

> # 강의 환경 설정

<br/>

- 파이썬, 주피터 노트북 설치
- 방법01 : anaconda 설치
  - anaconda : 파이썬 기본 컴파일러, 파이썬 주요 라이브러리, jupyter notebook 등 유용 툴이 포함된 패키지
  - Python 3.7 버전
- 방법02 : 파이썬 컴파일러 설치 후 jupyter notebook 설치
  - `pip install --upgrade pip`
  - `pip install jupyter`
  - jupyter notebook
    - 한줄 한줄 코드 실행 결과 확인이 쉽다
    - 문서와 코드를 함께 작성/저장할 수 있음
    - 문서까지 기록이 가능함

<br/>

> # Jupyter noteBook

<br/>

- 사용법은 간단하게, CMD의 원하는 위치에서 `jupyter notebook` 명령 치면 해당 dir를 root로 해서 브라우저에 열림
- Jupyter noteBook 자체가 브라우저로 동작하는 환경임
- 자체적으로 로컬 서버를 열어서 동작하는 거 같음, 그러므로 해당 cmd의 경우 계속 켜져있어야 함

<br/>

- 파일 생성은 new 탭에 python3를 눌러서 생성함
- 생성하면, 해당 파일의 확장자는 `ipynb`임
- 코드를 작성하는 부분을 하나의 셀이라고 이야기 함
- `셀 active + Run button`, `셀 active + shift + enter` :
  - 해당 셀의 코드 실행 (셀을 클릭하여 파란색으로 active 상태로 만듦)
- 코드가 실행이 되는 중이면 `In [*]` 를 표시하고, 실행이 끝나면 `In [숫자]`를 나타냄
  - 한셀의 코드의 실행이 완료되어어야 다음 셀을 실행시키는데, 무한 loop에 빠져서 코드 완료가 안되는 경우를 주의하자
  - 이경우 `■` 버튼을 눌러서 해당 셀의 코드 실행을 중지 시킬 수 있음
- 변수의 스코프는 셀에 국한 되지 않고, 해당 파일 전체임
- `셀 active + a` : 해당 셀 위에 셀을 추가
- `셀 active + b` : 해당 셀 아래에 셀을 추가

<br>

- 셀 선택후 code 메뉴를 Markdown으로 변경하면, 해당 셀은 Markdown 작성이 가능 함

<br/>
<br/>
<br/>

> # Array (배열)

<br/>

- 데이터를 나열하고, 각 데이터를 **Index**에 대응하도록 구성한 데이터 구조
- 파이썬에서는 **리스트 타입**이 배열 기능을 제공함

<br/>

## 배열이 필요한 이유

<br/>

- 하나의 연결된 데이터 공간에 저장할 수 있음
- **같은 종류의 데이터**를 효율적으로 관리
- **같은 종류의 데이터**를 **순차적(index가 있으므로)**으로 저장

<br/>

## 장점

<br/>

- index 번호를 통해 빠르게 데이터에 접근이 가능함

<br/>

## 단점

<br/>

- 이미 배열이 선언될 때 미리 최대 길이를 지정함으로서 추후의 **데이터 추가/삭제가 어려움**
- 데이터 추가의 경우 :
  - 이미 길이가 지정되었는데, 추가를 하면 더 **데이터 공간을 따로 더 마련 해서 다시 배열을 생성해야 함**
- 데이터 삭제의 경우 :
  - 중간에 값이 삭제되어 빈공간이 생기면 **뒤의 값을 모두 앞으로 당겨야 하는 일이 생김**

<br/>

## C 언어와 python Array 비교

- **python에서의 경우 기본적인 배열 보다 추가적인 기능을 가지고 있어서 배열의 단점이 그렇게 와닿지는 않음**

```c++
#include <stdio.h>

int main(int argc, char * argv[])
{
    char country[3] = "US";
    // 배열의 길이를 3으로 미리 지정
    // -> 3자리 이상의 데이터를 넣으면 변수를 재지정해줘야 함
    printf ("%c%c\n", country[0], country[1]);
    printf ("%s\n", country);
    return 0;
}
```

```python
country = 'US'
print(coutry)
# US

country = country + 'A'
print(country)
# USA
```

<br/>
<br/>
<br/>

> # Python과 Array

- 파이썬 리스트 활용

<br/>

## 1차원 배열: 리스트로 구현

<br/>

```python
data_list = [1, 2, 3, 4, 5]
print(data_list)
# [1, 2, 3, 4, 5]
```

<br/>

## 2차원 배열: 리스트로 구현

<br/>

- 배열안에 배열이 들어간 구조

```python
data_list = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
print(data_list)
# [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

## 2차원 배열 접근
print(data_list[0]) # [1, 2, 3]
print (data_list[0][0]) # 1
print (data_list[0][1]) # 2
print (data_list[0][2]) # 3
print (data_list[1][0]) # 4
print (data_list[1][1]) # 5
print (data_list[2][2]) # 9
print (data_list[2][1]) # 8
print (data_list[2][0]) # 7
```

## 문제

- 위의 dataset 리스트에서 전체 이름 안에 M 은 몇 번 나왔는지 빈도수 출력하기
- `range(stop)` : stop 미만의 번호 만큼 index 생성 (기본 0에서 시작)
- `range(start, stop, step)` : start 를 넣는 경우엔 start 에서 시작하여 stop 미만의 번호만큼 index 생성함, step을 넣으면 step 만큼 더해가면서 stop 미만 까지 index를 생성함

```python
# dataset은 string을 가지고 있는 Array임 ()
dataset = ['Msdfs','mdfse','efwegw']
m_count = 0
for data in dataset:
  for index in range(len(data)):
    if data[index] == 'M':
      m_count += 1
print(m_count)
```
