---
layout: post
title: "PythonCleanCode: Decorator 패턴의 이해"
date: 2024-02-08 00:00:00 +0900
categories: Programming Python
tags:
    - Python
    - Decorator
    - Clean Code
    - Design Pattern
    - Best Practices
description: "파이썬 데코레이터 패턴의 기본 개념부터 고급 활용까지: functools.wraps와 실전 예제"
toc: true
toc_sticky: true
---

## 데코레이터란?
데코레이터는 함수를 인자로 받아서 새로운 함수를 반환하는 함수로, 주로 로깅, 접근 제어, 성능 측정, 캐싱 등에 사용된다. 기존 함수의 동작을 변경하지 않고 추가 기능을 부여할 수 있다.

## 데코레이터 기본 구조와 동작 원리

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        # 함수 실행 전 추가 작업
        print("함수 실행 전")
        
        # 원래 함수 실행
        result = func(*args, **kwargs)
        
        # 함수 실행 후 추가 작업
        print("함수 실행 후")
        return result
    return wrapper

@my_decorator
def say_hello(name):
    print(f"Hello, {name}!")

# 함수 호출
say_hello("Alice")
```

**실행 결과:**
```
함수 실행 전
Hello, Alice!
함수 실행 후
```

### 동작 원리:
1. `@my_decorator`는 `say_hello` 함수를 `my_decorator` 함수의 인자로 전달
2. `my_decorator`는 `wrapper` 함수를 반환
3. `say_hello` 함수가 호출되면, 실제로는 `wrapper` 함수가 실행
4. `wrapper` 함수는 원래 함수를 호출하고, 추가 작업을 수행

## 데코레이터 활용 예제

### 1. 함수 실행 시간 측정

```python
import time

def timer_decorator(func):
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = func(*args, **kwargs)
        end_time = time.time()
        print(f"{func.__name__} 실행 시간: {end_time - start_time:.4f}초")
        return result
    return wrapper

@timer_decorator
def slow_function():
    time.sleep(2)

slow_function()
```

**실행 결과:**
```
slow_function 실행 시간: 2.0002초
```

### 2. 로깅 데코레이터

```python
def logging_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"{func.__name__} 함수가 호출되었습니다.")
        result = func(*args, **kwargs)
        print(f"{func.__name__} 함수가 종료되었습니다.")
        return result
    return wrapper

@logging_decorator
def add(a, b):
    return a + b

print(add(3, 5))
```

**실행 결과:**
```
add 함수가 호출되었습니다.
add 함수가 종료되었습니다.
8
```

### 3. 접근 제어 데코레이터

```python
def admin_required(func):
    def wrapper(*args, **kwargs):
        user_role = kwargs.get("role", "user")
        if user_role == "admin":
            return func(*args, **kwargs)
        else:
            return "접근 거부: 관리자만 접근할 수 있습니다."
    return wrapper

@admin_required
def delete_user(role):
    return "사용자가 삭제되었습니다."

print(delete_user(role="admin"))  # 허용
print(delete_user(role="user"))   # 거부
```

**실행 결과:**
```
사용자가 삭제되었습니다.
접근 거부: 관리자만 접근할 수 있습니다.
```

### 4. 매개변수가 있는 데코레이터

```python
def repeat(num_times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(num_times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(num_times=3)
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
```

**실행 결과:**
```
Hello, Alice!
Hello, Alice!
Hello, Alice!
```

## functools.wraps의 중요성

데코레이터를 사용할 때는 원래 함수의 메타데이터(이름, docstring 등)가 보존되어야한다. 이를 위해 `functools.wraps`를 사용한다.

### wraps를 사용하지 않은 경우:

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        """이것은 wrapper 함수입니다"""
        print("함수 실행 전")
        result = func(*args, **kwargs)
        print("함수 실행 후")
        return result
    return wrapper

@my_decorator
def greet(name):
    """인사를 하는 함수입니다"""
    return f"안녕하세요, {name}님!"

# 함수 정보 확인
print(greet.__name__)  # 출력: wrapper
print(greet.__doc__)   # 출력: 이것은 wrapper 함수입니다
```

### wraps를 사용한 경우:

```python
from functools import wraps

def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        """이것은 wrapper 함수입니다"""
        print("함수 실행 전")
        result = func(*args, **kwargs)
        print("함수 실행 후")
        return result
    return wrapper

@my_decorator
def greet(name):
    """인사를 하는 함수입니다"""
    return f"안녕하세요, {name}님!"

# 함수 정보 확인
print(greet.__name__)  # 출력: greet
print(greet.__doc__)   # 출력: 인사를 하는 함수입니다
```

### wraps 사용의 주요 이점:
1. 원래 함수의 이름(`__name__`) 유지
2. 원래 함수의 docstring 보존
3. 함수의 모든 메타데이터 보존
4. 디버깅과 문서화 도구 사용 시 정확한 정보 제공

## 클래스 데코레이터

데코레이터는 클래스에도 적용할 수 있다:

```python
def add_method(cls):
    def new_method(self):
        return "새로운 메서드입니다."
    cls.new_method = new_method
    return cls

@add_method
class MyClass:
    pass

obj = MyClass()
print(obj.new_method())
```

**실행 결과:**
```
새로운 메서드입니다.
```

## 데코레이터 사용 시 주의사항

1. **과도한 사용 지양**
   - 너무 많은 데코레이터는 코드 흐름을 복잡하게 만듦
   - 디버깅이 어려워질 수 있음

2. **함수 시그니처 유지**
   - 데코레이터는 원래 함수의 인자와 반환값 구조를 변경하지 않아야 함
   - `functools.wraps` 사용 권장

3. **성능 고려**
   - 데코레이터는 추가적인 함수 호출을 발생시킴
   - 성능이 중요한 경우 영향을 고려해야 함

## 데코레이터의 장점

1. **코드 재사용성**
   - 공통 기능을 여러 함수에 쉽게 적용 가능
   - 중복 코드 감소

2. **관심사의 분리**
   - 핵심 비즈니스 로직과 부가 기능을 분리
   - 코드 가독성과 유지보수성 향상

3. **유연한 기능 확장**
   - 기존 코드 수정 없이 새로운 기능 추가 가능
   - Open-Closed Principle 준수

