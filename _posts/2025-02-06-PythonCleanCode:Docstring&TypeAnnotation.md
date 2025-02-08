---
layout: post
title: "PythonCleanCode: Docstring, Type Annotation"
date: 2024-02-08 00:00:00 +0900
categories: Programming Python
tags:
   - Python
   - Type Hints
   - Code Quality
   - Documentation
description: "Docstring, Type Annotation, Mypy, Pylint"
toc: true
toc_sticky: true
---

# Python 코드 작성의 기본 개념: Docstring, 어노테이션, 타입 힌팅

## Docstring 이해하기

Docstring은 Python 코드의 문서화를 위한 핵심 기능이다. 함수, 클래스, 모듈의 설명을 작성할 때 사용된다.

```python
def calculate_area(width: float, height: float) -> float:
    """직사각형의 넓이를 계산합니다.
    
    Args:
        width (float): 직사각형의 가로 길이
        height (float): 직사각형의 세로 길이
        
    Returns:
        float: 계산된 직사각형의 넓이
    """
    return width * height

# Docstring 조회 방법
print(calculate_area.__doc__)  # help(calculate_area)로도 조회 가능
```

# Python 코드 작성의 기본 개념: 상세 가이드

## 1. Docstring 실행 예시

```python
def calculate_area(width: float, height: float) -> float:
    """직사각형의 넓이를 계산합니다.
    
    Args:
        width (float): 직사각형의 가로 길이
        height (float): 직사각형의 세로 길이
        
    Returns:
        float: 계산된 직사각형의 넓이
    """
    return width * height

# Docstring 조회
print(calculate_area.__doc__)
# 실행 결과:
"""
직사각형의 넓이를 계산합니다.

Args:
    width (float): 직사각형의 가로 길이
    height (float): 직사각형의 세로 길이
    
Returns:
    float: 계산된 직사각형의 넓이
"""

# 함수 실행
result = calculate_area(5.0, 3.0)
print(result)  # 출력: 15.0
```

## 2. 타입 어노테이션(Type Annotation)과 Typing 모듈

### 타입 어노테이션이란?
타입 어노테이션은 Python 3.5부터 도입된 기능으로, 변수나 함수의 매개변수, 반환값의 타입을 명시적으로 선언하는 방법이다.

기본적인 타입 어노테이션 예시:
```python
# 변수에 대한 타입 어노테이션
name: str = "Python"
age: int = 30
height: float = 175.5
is_student: bool = True

# 함수에 대한 타입 어노테이션
def greeting(name: str) -> str:
    return f"Hello, {name}!"

# 실행
result = greeting("Python")
print(result)  # 출력: Hello, Python!
```

### Typing 모듈 상세 활용
Typing 모듈은 복잡한 타입 힌팅을 위한 다양한 타입을 제공한다:

```python
from typing import List, Dict, Tuple, Optional, Union, Any

# 1. List 타입 - 리스트의 요소 타입을 지정
numbers: List[int] = [1, 2, 3, 4, 5]
names: List[str] = ["Alice", "Bob", "Charlie"]

# 2. Dict 타입 - 키와 값의 타입을 지정
user_info: Dict[str, Union[str, int]] = {
    "name": "John",
    "age": 30,
    "city": "Seoul"
}

# 3. Optional 타입 - None이 될 수 있는 타입을 표현
def get_user_name(user_id: int) -> Optional[str]:
    users = {1: "John", 2: "Jane"}
    return users.get(user_id)

# 실행
print(get_user_name(1))  # 출력: John
print(get_user_name(3))  # 출력: None

# 4. Union 타입 - 여러 타입 중 하나를 허용
def process_data(data: Union[str, int, float]) -> str:
    return str(data)

# 실행
print(process_data("hello"))  # 출력: hello
print(process_data(123))      # 출력: 123
print(process_data(3.14))     # 출력: 3.14

# 5. 제네릭 타입 예시
from typing import TypeVar, Generic

T = TypeVar('T')

class Box(Generic[T]):
    def __init__(self, content: T) -> None:
        self.content = content
    
    def get_content(self) -> T:
        return self.content

# 실행
string_box = Box[str]("Hello")
number_box = Box[int](123)

print(string_box.get_content())  # 출력: Hello
print(number_box.get_content())  # 출력: 123
```

### 타입 힌팅의 장점
1. 코드 가독성 향상
   - 다른 개발자가 코드를 이해하기 쉬워짐
   - 함수의 입출력 타입을 명확히 알 수 있음

2. IDE 지원
   - 코드 자동 완성 기능 강화
   - 타입 관련 오류를 미리 발견 가능

3. 디버깅 용이성
   - 타입 관련 버그를 개발 단계에서 발견 가능
   - 런타임 에러 감소

```python
# 실제 개발에서의 활용 예시
from typing import List, Dict, Optional

class User:
    def __init__(self, name: str, age: int) -> None:
        self.name = name
        self.age = age

def find_adult_users(users: List[User]) -> List[Dict[str, Any]]:
    """성인 사용자를 찾아 반환합니다."""
    return [{"name": user.name, "age": user.age} 
            for user in users if user.age >= 18]

# 실행
users = [
    User("Alice", 20),
    User("Bob", 17),
    User("Charlie", 25)
]

adult_users = find_adult_users(users)
print(adult_users)
# 출력: [{'name': 'Alice', 'age': 20}, {'name': 'Charlie', 'age': 25}]
```

이러한 타입 힌팅과 Typing 모듈의 활용은 특히 대규모 프로젝트에서 코드의 안정성과 유지보수성을 크게 향상시킬 수 있다.
## Mypy를 사용한 타입 검사

Mypy는 정적 타입 검사기로, 코드 실행 전에 타입 관련 오류를 찾아낼 수 있다.

```python
# example.py
def greet(name: str) -> str:
    return "Hello, " + name

# 잘못된 타입 사용
result = greet(123)  # Mypy에서 오류 감지
```

터미널에서 다음과 같이 실행하여 타입 검사를 수행할 수 있다:
```bash
mypy example.py
```

## Pylint를 사용한 코드 검사

Pylint는 코드 스타일과 에러를 검사하는 도구이다.

```python
# bad_code.py
class badClass:  # 클래스 이름은 CamelCase로 작성해야 함
    def __init__(self,name):  # 매개변수 뒤에 공백이 필요함
        self.name=name  # 연산자 주위에 공백이 필요함
    
    def print_name(self):
        print (self.name)  # 괄호 앞에 불필요한 공백이 있음
```

터미널에서 다음과 같이 실행하여 코드 검사를 수행할 수 있다:
```bash
pylint bad_code.py
```

이러한 도구들을 활용하면 더 깔끔하고 유지보수하기 쉬운 Python 코드를 작성할 수 있다. 

