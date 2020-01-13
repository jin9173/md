---
title: Python 문제풀이 1일차
date: 2020-01-07 11:01:50
categories: 
	- Algorithm
	- Python
---

#### 1. 다음 프로그램은 어떤 값을 출력할까요?

~~~python
print(10 / (10 % 6)) # 2.5
~~~

#### 2. 실행했을 때 콘솔에 `8.0`이 출력되는 프로그램을 모두 고르세요. <span style="color: red; font-size: 19px;">1, 2, 4</span>

1. `print(2 ** 3.0)` # 8.0
2. `print(int("3") + float("5"))` # 8.0
3. `print(str(4.0) * 2)` # 4.04.0
4. `print(float(int(42 / 5)))` # 8.0
5. `print(2 * (3 + 1)) # ` # 8

#### 3. `print`문을 이용하여 다음의 두 문장을 출력하세요.

~~~
'응답하라 1988'은 많은 시청자들에게 사랑을 받은 드라마에요.
데카르트는 "나는 생각한다. 고로 존재한다."라고 말했다.
~~~

~~~python
print("'응답하라 1988'은 많은 시청자들에게 사랑을 받은 드라마에요.")
print('데카르트는 "나는 생각한다. 고로 존재한다."라고 말했다.')
~~~

#### 4. 형변환과 문자열 덧셈 연산을 이용하여 아래의 내용을 출력하세요. 

~~~
오늘은 2016년 12월 25일 일요일입니다.
~~~

~~~python
print('오늘은', str(2016)+'년', str(12)+'월', str(25)+'일', '일요일입니다.')
~~~

#### 5. `wage`는 1시간에 얼마 버는지 나타내는 값이고, `exchange_rate`는 1달러를 한국 돈으로 바꾸면 얼마인지 나타내는 값이다. 이 경우, 1시간동안 번 금액은 `wage * 1`의 결과값인 5달러이고, 이 금액을 한국 돈으로 환전하면 `wage * 1 * exchange_rate`의 결과값인 `5710.8`원이 된다. 문자열 포맷팅의 개념을 이용하여 아래의 문장들을 출력해라.

~~~
1시간에 5달러 벌었습니다.
5시간에 25달러 벌었습니다.
1시간에 5710.8원 벌었습니다.
5시간에 28554.0원 벌었습니다.
~~~

~~~python
wage = 5
exchange_rate = 1142.16

print("%d시간에 %d%s 벌었습니다." % (1, wage * 1, "달러"))
print("%d시간에 %d%s 벌었습니다." % (5, wage * 5, "달러"))
print("%d시간에 %.1f%s 벌었습니다." % (1, exchange_rate * 5, "원"))
print("%d시간에 %.1f%s 벌었습니다." % (5, exchange_rate * 25, "원"))
~~~

#### 6. 실행했을 때, `True`가 나오는 것을 모두 고르세요. <span style="color: red; font-size: 19px;">2, 3, 5</span>

1. `print(int(2.5) + int(3.8) > int(str(1) + str(2)))`

   ~~~
   2 + 3 > 13
   5 > 13
   False
   ~~~

2. `print((12 >= 10 and not 3 > 4) or 3 ** 2 != 9)`

   ~~~
   (True and not False) or False
   (True and True) or False
   True or False
   True
   ~~~

3. `print(True and (True or False))`

   ~~~
   True and True
   True
   ~~~

4. `print(not True or (True and False))`

   ~~~
   False or False
   False
   ~~~

5. `print(False == False)`

   ~~~
   True
   ~~~

#### 7. 다음은 `type`함수에 대한 내용이다. 아래의 보기를 실행했을 때, 콘솔에 출력되는 내용과 잘못 짝지어진 것을 모두 고르세요. <span style="color: red; font-size: 19px;">1, 2, 5</span>

1. `print(type(4 / 2))` - `<class 'int'>`
2. `print(type("True"))` - `<class 'bool'>`
3. `print(type(10 <= 7))` - `<class 'bool'>`
4. `print(type(2.0 ** 3))` - `<class 'float'>`
5. `print(type(2 * 3 == 6))` - `<class 'int'>`

#### 8. `name`, `nationality`, `phone_number`이라는 변수를 만들고, 변수와 문자열 포맷팅을 이용하여 아래와 같이 출력되게 하세요.

~~~
Hi, my name is Sejin Kim. I'm Korean.
My phone number is 010-1234-1234.
~~~

~~~python
name = "Sejin Kim"
nationality = "Korean"
phone_number = "010-1234-1234"

print("Hi, my name is %s. I'm from %s" % (name, nationality))
print("My phone number is %s" % (phone_number))
~~~

#### 9. 문자열 `first_name`과 문자열 `last_name`을 파라미터로 받는 함수 `print_full_name`을 쓰세요. `print_full_name`은 `first_name`과 `last_name`을 다음과 같은 형태로 합쳐서 출력한다.

~~~
여, 진구
아, 이유
~~~

~~~python
def print_full_name(first_name, last_name):
  print("%s, %s" % (first_name, last_name))

print_full_name("여", "진구")
print_full_name("아", "이유")
~~~

#### 10. 거스름돈을 계산해주는 프로그램을 만드려고 합니다. 예를 들어 `33,000`원짜리 물건을 사기 위해 `100,000`원을 냈다면, `50,000`원 `1`장, `10,000`원 `1`장, `5,000`원 `1`장, `1,000`원 `2`장과 같이 '가장 적은 수'의 지폐를 거슬러 주는 방식이다. `payment`(지불한 금액)와 `cost`(가격)라는 파라미터 두개를 필요로 하는 함수 `calculate_change`를 쓰세요. 이 함수는 거스름돈을 위해 `50,000`원짜리와 `10,000`원짜리, `5,000`원짜리, `1,000`원짜리가 각각 몇 장 필요한지 출력해주는 역할을 한다. 

~~~
50000원 지폐: 1장
10000원 지폐: 1장
5000원 지폐: 1장
1000원 지폐: 2장

50000원 지폐: 2장
10000원 지폐: 2장
5000원 지폐: 0장
1000원 지폐: 2장
~~~

~~~python
def calculate_change(payment, cost):
    five_million = 50000
    one_million = 10000
    five_thousand = 5000
    one_thousand = 1000
    
    change = payment - cost
    
    five_million_count = int(change / five_million)
    change = change % five_million
    
    one_million_count = int(change / one_million)
    change = change % one_million
    
    five_thousand_count = int(change / five_thousand)
    change = change % five_thousand
    
    one_thousand_count = int(change / one_thousand)
    change = change % one_thousand
    
    print("%s원 지폐: %d장" % (five_million, five_million_count))
    print("%s원 지폐: %d장" % (one_million, one_million_count))
    print("%s원 지폐: %d장" % (five_thousand, five_thousand_count))
    print("%s원 지폐: %d장" % (one_thousand, one_thousand_count))

calculate_change(28000, 10000)
~~~

#### 11. `print`문과 `return`문에 대한 설명입니다. 다음 중 틀린 설명을 모두 고르세요.<span style="font-size: 19px; color: red"> 1, 2</span>

~~~python
def calculate_seven():
    return int("5") + int(2.0)
    print("럭키 7을 출력합니다.")

calculate_seven()
~~~

1. `7`과 `"럭키 7을 출력합니다."`가 각각 출력됩니다.
2. `7`만 출력됩니다.
3. `return`문에 도달하면 함수는 중단되므로, `"럭키 7을 출력합니다."`는 **dead code**라고 불립니다.

<span style="font-size: 13px; color: teal">`return`문은 함수의 실행을 중단하기 때문에 어떠한 경우에도 `calculate_seven()`함수의 `print`문에 도달할 수 없다. 따라서 **1**번은 틀린 설명이다. <br> `int("5")`는 정수형 `5`이고 `int(2.0)`은 정수형 `2`이기 때문에 `5 + 2`의 결과값이 `7`이 `calculate_seven()`의 자리에 대체된다. 그러나 대체된 값을 출력하라는 명령이 없기 때문에 아무 값도 출력되지 않는다. 따라서 **2**번 보기는 틀린 설명이다.</span> 

#### 12. `print`문과 `return`문에 대한 설명입니다. 다음 중 틀린 설명을 모두 고르세요. <span style="font-size: 19px; color: red">1, 4</span> 

~~~python
def sum(a, b):
    return a + b

def print_sum(a, b):
    print(a + b)

# 첫번째 명령
sum(2, 4)

# 두번째 명령
print(sum(2, 4))

# 세번째 명령
print_sum(2, 4)

# 네번째 명령
print(print_sum(2, 4))
~~~

1. 첫 번째 명령의 리턴 값은 `6`이므로, `6`이 출력됩니다.
2. 두 번째 명령은 `sum(2, 4)`에 의해 리턴된 `6`의 값을 출력하라는 명령이므로, `6`이 출력됩니다.
3. 세 번째 명령은 `print_sum` 함수를 호출하는 명령이므로, `2`와 `4`를 더한 `6`이 출력됩니다.
4. 네 번째 명령에서 `print_sum(2, 4)`의 리턴 값은 `6`이므로, `6`이 출력됩니다.
5. 네 번째 명령은 리턴 값이 지정되어 있지 않기 때문에, `None`이 리턴됩니다.
6. 네 번째 명령에 의해, `6`과 `None`이 출력됩니다.

<span style="font-size: 13px; color: teal">첫번째 명령의 리턴값은 `6`이지만 이를 출력하라는 명령이 없기 때문에 아무것도 출력되지 않는다. 따라서 **1**번은 틀린 설명이다.<br> 두번째 명령은 `return`문에 의해 대체된 `6`의 값을 출력하라는 명령이므로 `6`이 출력된다. 따라서 **2**번은 옳은 설명이다. </br> 세번째 명령은 `print_sum()`이라는 함수를 호출하는 명령이고 함수의 `print`문에 의해 파라미터의 값을 더한 `6`이 출력된다. 따라서 **3**번은 옳은 설명이다. <br> 네번째 명령에서 `print_sum(2, 4)`는 `2 + 4`의 결과 값인 `6`을 출력한다. 하지만 `print_sum()`함수는 리턴값이 지정되어 있지 않아 `None`을 출력한다. 즉, 이는 `6`을 출력하고 `None`을 출력한다. 따라서 **4**번은 틀린 설명이고, **5**번과 **6**번은 옳은 설명이다. </span>

#### 13. `global`변수에 대한 설명 중 옳은 설명을 모두 고르세요. <span style="font-size: 19px; color: red">3, 5</span>

~~~python
def multiply_by_two():
    global x
    x = x * 2

def multiply_by_three():
    y = 2
    y = y * 3

x = 2
multiply_by_two()
print(x)

y = 2
multiply_by_three()
print(y)
~~~

1. `multiply_by_two` 함수 내부에 있는 `x`와, 함수 바깥에 정의된 `x`는 서로 다른 변수입니다.
2. 파이썬을 실행하면, `4`와 `6`이 출력됩니다.
3. 파이썬을 실행하면, `4`와 `2`가 출력됩니다.
4. 파이썬을 실행하면, `4`와 오류 메시지가 출력됩니다.
5. `multiply_by_three` 함수 내부에 있는 `y`와, 함수 바깥에 정의된 `y`는 서로 다른 변수입니다.

<span style="font-size: 13px; color: teal">`multiply_by_two()`함수의 첫번째 줄 `global x`는 함수 내부의 `x`와 함수 바깥의 `x`가 같은 변수라고 표시하는 명령이다. 따라서 **1**번은 틀린 설명이다. <br> 처음에 `x`는 `2`의 값을 갖고, `multiply_by_three()`함수가 호출된다. 하지만 `multiply_by_three()`함수 안에 있는 `y`는 `local`변수이기 때문에 함수 내부의 `y`는 `6`의 값을 갖게 되지만 함수 바깥의 `y`는 여전히 `2`의 값을 갖고 있다. `print(y)`는 함수 바깥에 있으므로 `2`가 출력된다. 즉, **3**번과 **5**번이 옳은 설명이다.</span>