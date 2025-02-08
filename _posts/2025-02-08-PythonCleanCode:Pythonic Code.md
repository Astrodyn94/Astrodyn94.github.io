---
layout: post
title: "Python Clean Code : Pythonic Code"
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


## 시퀀스 객체란?

시퀀스 객체는 순서가 있는 데이터를 다루는 파이썬의 기본 개념이다. 리스트, 튜플, 문자열과 같이 순서대로 데이터를 저장하고 접근할 수 있는 객체를 의미한다. 시퀀스 객체는 두 가지 핵심 메서드(`__getitem__`과 `__len__`)를 구현하여 인덱싱, 슬라이싱, 반복 등의 기능을 제공한다.

## 인덱싱과 슬라이싱

### 1. 인덱싱(Indexing)
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

### 2. 슬라이싱(Slicing)
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

## 매직 메서드의 이해

### 1. __getitem__ 메서드
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

### 2. __len__ 메서드
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

## 커스텀 시퀀스 객체 구현 예시

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

이러한 매직 메서드와 시퀀스 객체의 이해는 파이썬의 데이터 구조를 효과적으로 다루는 데 필수적이다. 특히 커스텀 데이터 구조를 구현할 때 이러한 개념들을 활용하면 파이썬의 기본 기능들과 자연스럽게 통합되는 객체를 만들 수 있다.
