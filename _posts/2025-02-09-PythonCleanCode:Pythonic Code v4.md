---
layout: post
title: "Python Clean Code : Pythonic Code (Container object , Dynamic Attributes , Callable object)"
date: 2024-02-09 14:00:00 +0900
categories: Programming Python
tags:
    - Python
    - Pythonic code 
    - Clean Code
description: "pythonic code"
toc: true
toc_sticky: true
---

## 1. **컨테이너 객체 (Container Object)**

컨테이너 객체는 `__contains__` 메서드를 구현한 객체로, `in` 키워드를 사용하여 특정 요소가 컨테이너에 포함되어 있는지 확인할 수 있다.

### 예시 코드

```python
class MyContainer:
    def __init__(self, elements):
        self.elements = elements

    def __contains__(self, element):
        return element in self.elements  # 요소가 컨테이너에 있는지 확인

# 사용 예시
container = MyContainer([1, 2, 3, 4, 5])
print(3 in container)  # 출력: True (container.__contains__(3) 호출)
print(6 in container)  # 출력: False
```

---

## 2. **동적 속성 (Dynamic Attributes)**

`__getattr__` 매직 메서드를 사용하면 객체에서 존재하지 않는 속성을 동적으로 처리할 수 있습니다. 이 메서드는 속성이 발견되지 않을 때 호출된다.

### 예시 코드

```python
class DynamicAttributes:
    def __init__(self):
        self.data = {}

    def __getattr__(self, name):
        if name in self.data:
            return self.data[name]  # 존재하는 속성 반환
        else:
            raise AttributeError(f"'{self.__class__.__name__}' object has no attribute '{name}'")

    def __setattr__(self, name, value):
        if name == "data":
            super().__setattr__(name, value)  # data 속성은 일반적으로 설정 #  기본 저장소로 사용할 data 딕셔너리를 안전하게 초기화하고 접근하기 위한 필수적인 장치
        else:
            self.data[name] = value  # 동적 속성으로 저장

# 사용 예시
obj = DynamicAttributes()
obj.name = "Alice"  # 이 코드가 내부적으로 obj.__setattr__("name", "Alice")를 호출
print(obj.name)  # 출력: Alice (obj.__getattr__("name") 호출)
# print(obj.age)  # ❌ AttributeError 발생
```

---

## 3. **호출 가능한 객체 (Callable Object)**

`__call__` 매직 메서드를 구현하면 객체를 함수처럼 호출할 수 있다. 이 메서드는 객체에 상태를 저장하면서도 함수처럼 동작할 수 있게 해준다.

### 예시 코드

```python
class CallableObject:
    def __init__(self):
        self.counter = 0  # 상태 저장

    def __call__(self, *args, **kwargs):
        self.counter += 1  # 호출될 때마다 카운터 증가
        print(f"Called {self.counter} times with args: {args}, kwargs: {kwargs}")

# 사용 예시
callable_obj = CallableObject()
callable_obj(1, 2, 3)  # 출력: Called 1 times with args: (1, 2, 3), kwargs: {}
callable_obj(a=4, b=5)  # 출력: Called 2 times with args: (), kwargs: {'a': 4, 'b': 5}
```

---

## 4. **내장 타입 확장 (Extending Built-in Types)**

리스트, 문자열, 사전과 같은 내장 타입을 확장하는 올바른 방법은 `collections` 모듈을 사용하는 것이다. `collections.UserList`, `collections.UserDict`, `collections.UserString`을 사용하면 내장 타입을 쉽게 확장할 수 있다.

### 예시 코드

```python
from collections import UserList

class MyList(UserList):
    def __init__(self, initial_data=None):
        super().__init__(initial_data if initial_data else [])

    def append_if_not_exists(self, item):
        if item not in self:
            self.append(item)  # 중복되지 않은 경우에만 추가

# 사용 예시
my_list = MyList([1, 2, 3])
my_list.append_if_not_exists(3)  # 이미 존재하므로 추가되지 않음
my_list.append_if_not_exists(4)  # 추가됨
print(my_list)  # 출력: [1, 2, 3, 4]
```

---

## 5. **요약**

1. **컨테이너 객체**: `__contains__` 메서드를 구현하여 `in` 키워드를 사용할 수 있다.
2. **동적 속성**: `__getattr__` 메서드를 사용하여 존재하지 않는 속성을 동적으로 처리할 수 있다.
3. **호출 가능한 객체**: `__call__` 메서드를 구현하여 객체를 함수처럼 호출할 수 있다.
4. **내장 타입 확장**: `collections` 모듈을 사용하여 내장 타입을 확장할 수 있다.
