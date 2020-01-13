---
title: 파이크러스트 - 코드구조(제어문)
date: 2019-12-12 10:47:50
categories: 
    - Study
    - Python
---

#### 라인유지 `\`

: 라인 끝에 `\`을 입력하면 다음 라인을 여전히 같은 라인으로 인식

~~~python
alphabet = 'abcdefg' + \
'higklmn' + \
'opqrstu' + \
'vwxyz'

print(alphabet) # abcdefghijklmnopqrstuvwxyz
~~~

# 1. 조건문
## if문

: if, else는 조건이 참(True)인지 거짓(False)인지 확인하는 파이썬의 선언문

### 비교 연산자

: `==`, `!=`, `<`, `<=`, `>`, `>=`, `in`(멤버십) (Boolean값을 반환)

### False값

: `null`, `0`, `0.0`, `''`, `[]`, `()`, `{}`, `set()`

<br>

# 2. 반복문

## 2.1 While문

: 루핑 매커니즘 (Looping Mechanism)

~~~python
count = 1

while count <= 5:
  print(count)
  count += 1
~~~

~~~python
while True:
  value = input("Integer, please [q to quit]:")
  if value = 'q':
    break
  number = int(value)
  if number % 2 == 0:
    continue
  print(number, "squared is", number*number)
~~~

## 2.2 for문

: 자료구조를 순회(iterable객체: 리스트, 문자열, 튜플, 딕셔너리, 셋), 데이터 스트림 처리 허용

~~~python
cheeses = []
found_one = False

for cheese in cheeses:
  found_one = True
  print("This shop has some lovely", cheese)
  break
if not found_one:
  print("This is not much of a cheese shop, is it?")
~~~

### for-else문

: for문을 뭔가 찾는 것이고 찾지 못했을 때 else문이 호출된다
(else문 없이 이와 같은 효과를 얻으려면 원하는 값을 찾았는지 여부를 나타내는 변수를 사용)

~~~python
cheeses = []

for cheese in cheeses:
  print("This shop has some lovely", cheese)
  break
else:
  print("This is not much of a cheese shop, is it?")
~~~



#### 중단하기 `break`

: 무한루프 속에 `break`문을 사용해서 빠져나온다.

#### 건너뛰기 `continue`

: 다음 루프로 건너뛰고 싶을 때 `continue`사용

- `zip(순회할 객체1, 순회할 객체2, ...)`: 여러 시퀀스 순회

- `range(start, stop, step)`

  - 자료구조를 생성하여 저장하지 않더라도 특정 범위 내에서 숫자 스트림을 반환
  - 순회 가능한 객체를 반환 → for...in 형태로 값을 순회 가능
  - 객체를 리스트와 같은 시퀀스로 변환 가능

<br>

# 3. 컴프리헨션 (Comprehension)

- (함축)하나이상의 이터레이터로부터 파이썬의 자료구조를 만드는 콤팩트한 방법
- 반복문과 조건 테스트를 결합 가능

#### 종류

1. 리스트 컴프리헨션

   - `[표현식 for 항목 in 순회가능한객체]`
   - `[표현식 for 항목 in 순회가능한객체 if조건'`

2. 딕셔너리 컴프리헨션

   - `{키_표현식 : 값_표현식 for 표현식 in 순회가능한객체}`

     - e.g. `키_표현식 : 순회가능한객체.counts() for 표현식 in set(순회가능한객체)}`: 딕셔너리에 이미 존재하는 항목을 단지 교체만 하기 때문에 set을 사용하면 중복제거와 프로그램이 쓸데없이 돌아가는 것을 방지할 수 있다.

3. 셋 컴프리헨션

   - `{표현식 for 표현식 in 순회가능한객체}`

4. 제너레이터 컴프리헨션: 이터레이터를 생성해주는 함수

   ~~~python
   # 튜플은 컴프리헨션이 없고 이는 제너레이터 컴프리헨션이다
   nubmer_thing = (number for number in range(1, 6))
   
   # 제너레이터 컴프리헨션으로 만들어진 이터레이터 객체를 순회할 수 있다
   for number in number_thing:
     print(number)
   ~~~

   ~~~python
   nubmer_thing = (number for number in range(1, 6))
   
   # list() 호출로 랩핑
   number_list = list(number_thing)
   print(number_list) # [1, 2, 3, 4, 5]
   ~~~

   ~~~python
   # 다시 참조할 수 없다
   try_again = list(number_thing)
   print(try_again) # []
   ~~~

   