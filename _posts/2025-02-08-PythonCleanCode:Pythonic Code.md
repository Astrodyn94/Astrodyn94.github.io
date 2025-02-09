---
layout: post
title: "Python Clean Code : Pythonic Code (Sequence & Iterator)"
date: 2024-02-08 14:00:00 +0900
categories: Programming Python
tags:
    - Python
    - Pythonic code 
    - Clean Code
description: "pythonic code"
toc: true
toc_sticky: true
---

## 프로그래밍 언어에서의 Idiom

프로그래밍 이디엄(Idiom)은 특정 언어에서 자주 사용되는 관용적인 코드 패턴을 의미한다. 파이썬에서는 시퀀스 객체와 매직 메서드를 활용한 이디엄이 많이 사용된다.


## 1.  시퀀스 객체란?

시퀀스 객체는 순서가 있는 데이터를 다루는 파이썬의 기본 개념이다. 리스트, 튜플, 문자열과 같이 순서대로 데이터를 저장하고 접근할 수 있는 객체를 의미한다. 시퀀스 객체는 두 가지 핵심 메서드(`__getitem__`과 `__len__`)를 구현하여 인덱싱, 슬라이싱, 반복 등의 기능을 제공한다.

### 1-1 인덱싱과 슬라이싱

####  **인덱싱(Indexing)**
인덱싱은 시퀀스 객체의 특정 위치에 있는 요소에 접근하는 방법이다.

```python
# 리스트 인덱싱 예시
numbers = [1, 2, 3, 4, 5]
print(numbers[0])    # 출력: 1
print(numbers[-1])   # 출력: 5

# 문자열 인덱싱 예시
text = "Python"
print(text[0])      # 출력: P
print(text[-1])     # 출력: n
```

####  **슬라이싱(Slicing)**
슬라이싱은 시퀀스 객체의 일부분을 추출하는 방법이다. `[시작:끝:단계]` 형식을 사용한다.

```python
# 리스트 슬라이싱 예시
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(numbers[2:5])    # 출력: [2, 3, 4]
print(numbers[::2])    # 출력: [0, 2, 4, 6, 8]
print(numbers[::-1])   # 출력: [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

# 문자열 슬라이싱 예시
text = "Hello, Python!"
print(text[0:5])      # 출력: Hello
print(text[7:])       # 출력: Python!
print(text[::-1])     # 출력: !nohtyP ,olleH
```

### 1-2 매직 메서드의 이해

####  **__getitem__ 메서드**
`__getitem__` 메서드는 시퀀스 객체의 인덱싱과 슬라이싱 동작을 정의한다.

```python
class NumberSequence:
    def __init__(self, numbers):
        self.numbers = numbers
        
    def __getitem__(self, key):
        return self.numbers[key]

# 사용 예시
seq = NumberSequence([1, 2, 3, 4, 5])
print(seq[0])       # 출력: 1
print(seq[1:3])     # 출력: [2, 3]
```

####  **__len__ 메서드**
`__len__` 메서드는 시퀀스 객체의 길이를 반환한다.

```python
class NumberSequence:
    def __init__(self, numbers):
        self.numbers = numbers
        
    def __len__(self):
        return len(self.numbers)
    
    def __getitem__(self, key):
        return self.numbers[key]

# 사용 예시
seq = NumberSequence([1, 2, 3, 4, 5])
print(len(seq))     # 출력: 5
```

### 1-3. 커스텀 시퀀스 객체 구현 예시

좀 더 실용적인 예시로, 범위 내의 피보나치 수열을 생성하는 시퀀스 객체를 구현해보자:

```python
class FibonacciSequence:
    def __init__(self, max_value):
        self.max_value = max_value
        self._sequence = self._generate_sequence()
    
    def _generate_sequence(self):
        sequence = [0, 1]
        while sequence[-1] <= self.max_value:
            sequence.append(sequence[-1] + sequence[-2])
        return sequence[:-1]  # 최대값을 초과하는 마지막 항목 제거
    
    def __len__(self):
        return len(self._sequence)
    
    def __getitem__(self, key):
        return self._sequence[key]

# 사용 예시
fib = FibonacciSequence(100)
print(list(fib))     # 출력: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
print(fib[3:7])      # 출력: [2, 3, 5, 8]
print(len(fib))      # 출력: 12
```

## 시퀀스 객체의 특징

1. **순서 보장**
   - 데이터의 순서가 유지되며 인덱스로 접근 가능

2. **반복 가능**
   - for 루프에서 사용 가능
   - 리스트 컴프리헨션 사용 가능

3. **길이 확인**
   - len() 함수로 크기 확인 가능

4. **멤버십 테스트**
   - in 연산자로 포함 여부 확인 가능

```python
# 시퀀스 객체의 주요 기능 예시
numbers = [1, 2, 3, 4, 5]

# 반복
for num in numbers:
    print(num)

# 멤버십 테스트
print(3 in numbers)      # 출력: True

# 리스트 컴프리헨션
squares = [x**2 for x in numbers]
print(squares)           # 출력: [1, 4, 9, 16, 25]
```


---

## 2. 이터러블 객체의 특징

- **정의** : __iter__ 메서드를 구현한 객체로, 반복 가능한 객체이다.
- **반복 가능**: 이터러블 객체는 `for` 루프에서 사용할 수 있습니다.
- **`__iter__` 메서드**: 이터러블 객체는 `__iter__` 메서드를 구현해야 합니다. 이 메서드는 **이터레이터(Iterator)**를 반환합니다.
- **`__next__` 메서드**: 이터레이터는 `__next__` 메서드를 구현해야 합니다. 이 메서드는 다음 요소를 반환하거나, 더 이상 요소가 없을 경우 `StopIteration` 예외를 발생시킨다.
- **예시** : 리스트, 튜플, 문자열, 딕셔너리, 집합, 파일 객체 등.
- **동작원리** : for 루프는 이터러블 객체의 __iter__ 메서드를 호출하여 이터레이터를 얻는다. 이터레이터는 __next__ 메서드를 반복적으로 호출하여 요소를 하나씩 가져온다.
---

### 2-1.  이터러블 객체의 예시

-파이썬에서 대표적인 이터러블 객체는 다음과 같다:
-for 루프는 이터러블 객체의 __iter__ 메서드를 호출하여 이터레이터를 얻는다.

- **리스트(List)**
  ```python
  my_list = [1, 2, 3]
  for item in my_list: 
      print(item)
  # 출력: 1 2 3
  ```

- **튜플(Tuple)**
  ```python
  my_tuple = (1, 2, 3)
  for item in my_tuple:
      print(item)
  # 출력: 1 2 3
  ```

- **문자열(String)**
  ```python
  my_string = "Hello"
  for char in my_string:
      print(char)
  # 출력: H e l l o
  ```

- **딕셔너리(Dictionary)**
  ```python
  my_dict = {"a": 1, "b": 2, "c": 3}
  for key in my_dict:
      print(key, my_dict[key])
  # 출력: a 1, b 2, c 3
  ```

---

### 2-2. 이터러블 객체의 동작 원리

이터러블 객체는 `for` 루프에서 다음과 같이 동작한다:

1. `for` 루프는 이터러블 객체의 `__iter__` 메서드를 호출하여 **이터레이터(Iterator)**를 얻는다.
2. 이터레이터의 `__next__` 메서드를 반복적으로 호출하여 요소를 하나씩 가져온다.
3. 더 이상 요소가 없을 경우 `StopIteration` 예외가 발생하고, 루프가 종료된다.

#### 예시: `for` 루프의 동작 과정
```python
my_list = [1, 2, 3]
iterator = iter(my_list)  # __iter__ 메서드 호출

print(next(iterator))  # 출력: 1 (__next__ 메서드 호출)
print(next(iterator))  # 출력: 2
print(next(iterator))  # 출력: 3
# print(next(iterator))  # StopIteration 예외 발생
```

---

### 2-3. 사용자 정의 이터러블 객체 만들기

클래스에 `__iter__` 메서드와 `__next__` 메서드를 구현하면 사용자 정의 이터러블 객체를 만들 수 있다.

#### 예시: 범위를 반복하는 이터러블 객체
```python
class MyRange:
    def __init__(self, start, end):
        self.start = start
        self.end = end

    def __iter__(self):
        return self  # 이터레이터로 자기 자신을 반환

    def __next__(self):
        if self.start >= self.end:
            raise StopIteration
        current = self.start
        self.start += 1
        return current

# 사용 예시
my_range = MyRange(1, 4)
for num in my_range:
    print(num)
# 출력: 1 2 3
```

- `__iter__` 메서드는 이터레이터를 반환합니다. 여기서는 자기 자신(`self`)을 반환한다.
- `__next__` 메서드는 다음 값을 반환하거나, `StopIteration` 예외를 발생시킨다.

---

### 2-4. 이터러블 vs 이터레이터

- **이터러블(Iterable)**: `__iter__` 메서드를 구현한 객체. 반복 가능한 객체이다.
- **이터레이터(Iterator)**: `__iter__` 메서드와 `__next__` 메서드를 구현한 객체. 실제로 반복을 수행하는 객체이다.

모든 이터레이터는 이터러블이지만, 모든 이터러블이 이터레이터인 것은 아니다. 예를 들어, 리스트는 이터러블이지만 이터레이터는 아니다. 리스트에서 `iter()` 함수를 호출하면 이터레이터가 반환된다.

#### 예시: 리스트의 이터레이터
```python
my_list = [1, 2, 3]
iterator = iter(my_list)  # 이터레이터 생성

print(next(iterator))  # 출력: 1
print(next(iterator))  # 출력: 2
print(next(iterator))  # 출력: 3
# print(next(iterator))  # StopIteration 예외 발생
```

---

### 2-5. 이터러블 객체의 장점

- **메모리 효율성**: 이터러블 객체는 모든 요소를 한 번에 메모리에 로드하지 않고, 필요할 때마다 하나씩 생성할 수 있다. 이는 큰 데이터를 처리할 때 유용하다.
- **유연성**: 사용자 정의 이터러블 객체를 만들어 복잡한 반복 로직을 구현할 수 있다다.
- **호환성**: `for` 루프, `while` 루프, 리스트 컴프리헨션 등 다양한 파이썬 기능과 호환된다.

---

### 요약

- **이터러블 객체**: 반복 가능한 객체로, `for` 루프에서 사용할 수 있다.
- **이터레이터**: 실제로 반복을 수행하는 객체로, `__next__` 메서드를 통해 요소를 하나씩 반환한다.
- **사용자 정의 이터러블**: `__iter__`와 `__next__` 메서드를 구현하여 만들 수 있다.
- **장점**: 메모리 효율성, 유연성, 호환성 등이 있다.

---
물론이죠! 아래는 **이터러블(Iterable)**과 **시퀀스(Sequence)**의 차이를 정리한 표를 마크다운 형식으로 작성한 것입니다.

---

### 이터러블(Iterable) vs 시퀀스(Sequence)

| 특징                | 이터러블(Iterable)                     | 시퀀스(Sequence)                     |
|---------------------|----------------------------------------|--------------------------------------|
| **정의**            | `__iter__` 메서드를 구현한 객체         | `__getitem__`와 `__len__` 메서드를 구현한 객체 |
| **인덱싱 지원**      | ❌ (일반적으로 지원하지 않음)           | ⭕ (인덱싱 `[]` 지원)                 |
| **슬라이싱 지원**    | ❌ (일반적으로 지원하지 않음)           | ⭕ (슬라이싱 `[:]` 지원)              |
| **길이 확인**        | ❌ (일반적으로 지원하지 않음)           | ⭕ (`len()` 함수 지원)                |
| **반복 가능**        | ⭕ (`for` 루프에서 사용 가능)           | ⭕ (`for` 루프에서 사용 가능)         |
| **메모리 효율성**    | ⭕ (필요할 때마다 요소 생성 가능)       | ❌ (모든 요소를 메모리에 저장)        |
| **예시**            | 리스트, 튜플, 문자열, 딕셔너리, 파일 객체 | 리스트, 튜플, 문자열, 바이트 배열     |

---


