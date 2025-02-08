---
layout: post
title: "PythonCleanCode: Magic method, Encapsulation , Inheritance"
date: 2024-02-08 14:00:00 +0900
categories: Programming Python
tags:
   - Python
   - Magic method, Encapsulation , Inheritance
toc: true
toc_sticky: true
---

# 파이썬의 매직 메서드, 캡슐화, 상속 이해하기

파이썬은 객체 지향 프로그래밍(OOP)을 지원하는 언어로, **매직 메서드**, **캡슐화**, **상속**과 같은 개념을 활용하여 코드를 더 효율적이고 유연하게 작성할 수 있다. 

---

## 1. 매직 메서드 (Magic Method)

매직 메서드는 파이썬에서 특별한 목적을 위해 미리 정의된 메서드입니다. 메서드 이름 앞뒤에 **더블 언더스코어(`__`)**가 붙어 있으며, 특정 상황에서 자동으로 호출될 수 있다. 매직 메서드를 사용하면 객체의 행동을 정의하거나, 연산자 오버로딩, 객체의 생성 및 소멸 등을 제어할 수 있다.

### 주요 매직 메서드 예시

#### `__init__`
객체가 생성될 때 초기화를 담당할 수 있다.
```python
class MyClass:
    def __init__(self, value):
        self.value = value

obj = MyClass(10)  # __init__ 메서드가 호출됨
print(obj.value)  # 출력: 10
```

#### `__str__`
객체를 문자열로 표현할 때 사용할 수 있다. `print()` 함수나 `str()` 함수에서 호출된다.
```python
class MyClass:
    def __str__(self):
        return "MyClass instance"

obj = MyClass()
print(obj)  # 출력: MyClass instance
```

#### `__add__`
`+` 연산자를 오버로딩하여 사용자 정의 클래스에서도 덧셈 연산을 지원할 수 있습니다.
```python
class MyClass:
    def __init__(self, value):
        self.value = value
    def __add__(self, other):
        return self.value + other.value

obj1 = MyClass(10)
obj2 = MyClass(20)
print(obj1 + obj2)  # 출력: 30
```

#### `__len__`
`len()` 함수를 사용할 때 호출될 수 있다.
```python
class MyClass:
    def __len__(self):
        return 10

obj = MyClass()
print(len(obj))  # 출력: 10
```

---

## 2. 캡슐화 (Encapsulation)

캡슐화는 객체의 내부 구현을 숨기고, 외부에서 필요한 인터페이스만 제공하는 개념이다. 이를 통해 객체의 안정성을 높이고, 코드의 유지보수성을 향상시킬 수 있다.

### 캡슐화 예시

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # private 변수 (name mangling)
                                    #파이썬이 __balance를 _BankAccount__balance로 자동 변환
                                    #클래스 외부에서 직접 접근을 어렵게 만듦
                                    

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount

    def withdraw(self, amount):
        if 0 < amount <= self.__balance:
            self.__balance -= amount

    def get_balance(self):
        return self.__balance

account = BankAccount(1000)
account.deposit(500)
account.withdraw(200)
print(account.get_balance())  # 출력: 1300
```

- `__balance`는 private 변수로, 외부에서 직접 접근할 수 없다.
- `deposit`, `withdraw`, `get_balance` 메서드를 통해 안전하게 데이터를 조작할 수 있다.

---

## 3. 상속 (Inheritance)

상속은 기존 클래스의 속성과 메서드를 새로운 클래스에서 재사용하는 방법이다. 상속을 통해 코드 중복을 줄이고, 기능을 확장할 수 있다.

### 상속 예시

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "소리 내는 중..."

class Dog(Animal):  # Animal 클래스 상속
    def speak(self):
        return f"{self.name}가 멍멍 짖습니다!"

class Cat(Animal):  # Animal 클래스 상속
    def speak(self):
        return f"{self.name}가 야옹 웁니다!"

dog = Dog("Buddy")
cat = Cat("Kitty")
print(dog.speak())  # 출력: Buddy가 멍멍 짖습니다!
print(cat.speak())  # 출력: Kitty가 야옹 웁니다!
```

- `Dog`와 `Cat` 클래스는 `Animal` 클래스의 `name` 속성과 `speak` 메서드를 상속받는다.
- 각 클래스에서 `speak` 메서드를 오버라이드하여 다른 동작을 정의할 수 있다.

---

## 4. 캡슐화와 상속을 활용한 래퍼 클래스

래퍼 클래스는 기존 객체를 감싸는 클래스로, 기존 객체의 기능을 그대로 사용하면서도 추가적인 기능을 제공할 수 있다. 이를 통해 호환성을 유지하면서도 기능을 확장할 수 있다.

### 래퍼 클래스 예시

```python
class ListWrapper:
    def __init__(self, initial_list=None):
        if initial_list is None:
            self._list = []  # 내부 리스트 초기화
        else:
            self._list = list(initial_list)  # 기존 리스트를 복사하여 저장

    # 리스트의 동일한 메서드를 호출하여 위임
    def append(self, item):
        self._list.append(item)

    def __getitem__(self, index):
        return self._list[index]

    def __len__(self):
        return len(self._list)

    def __str__(self):
        return str(self._list)

    # 추가 기능
    def clear(self):
        self._list.clear()

# 사용 예시
wrapped_list = ListWrapper([1, 2, 3])
wrapped_list.append(4)
print(wrapped_list)  # 출력: [1, 2, 3, 4]
print(len(wrapped_list))  # 출력: 4
wrapped_list.clear()
print(wrapped_list)  # 출력: []
```

- `ListWrapper` 클래스는 내부적으로 리스트(`self._list`)를 사용한다.
- 리스트의 메서드(예: `append`, `__getitem__`, `__len__`)를 직접 호출하여 기본 객체의 동작을 위임할 수 있다.
- 추가적인 기능(예: `clear` 메서드)을 제공할 수 있다.

---

## 결론

- **매직 메서드**: 특별한 목적을 위해 파이썬에서 미리 정의된 메서드로, 객체의 행동을 제어하거나 연산자를 오버로딩할 수 있다.
- **캡슐화**: 객체의 내부 구현을 숨기고, 외부에서 필요한 인터페이스만 제공하여 코드의 안정성을 높일 수 있다.
- **상속**: 기존 클래스의 속성과 메서드를 재사용하여 코드 중복을 줄이고, 기능을 확장할 수 있다.
- **래퍼 클래스**: 기존 객체를 감싸고, 그 객체의 기능을 위임하여 호환성을 유지하면서도 추가 기능을 제공할 수 있다.

