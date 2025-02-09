---
layout: post
title: "Python Clean Code : Pythonic Code (Property , Attribute, Object methods)"
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


---

## 1. **속성(Attribute)**, **프로퍼티(Property)**, **객체 메서드(Object Methods)**

### 1) **속성(Attribute)**
- **정의**: 객체의 상태를 저장하는 변수이다.
- **특징**: 객체의 데이터를 직접 저장하거나 참조한다.
- **예시**:
  ```python
  class Person:
      def __init__(self, name):
          self.name = name  # name은 속성(attribute)

  person = Person("Alice")
  print(person.name)  # 출력: Alice
  ```

### 2) **프로퍼티(Property)**
- **정의**: 속성에 대한 접근을 제어하는 방법이다. 속성을 읽거나 쓸 때 추가적인 로직을 실행할 수 있다.
- **특징**:
  - `@property` 데코레이터를 사용하여 정의한다.
  - 속성처럼 접근할 수 있지만, 실제로는 메서드이다.
- **예시**:
  ```python
  class Circle:
      def __init__(self, radius):
          self.radius = radius

      @property
      def area(self):
          return 3.14 * self.radius ** 2  # 계산된 값을 반환

  circle = Circle(5)
  print(circle.area)  # 출력: 78.5 (속성처럼 접근)
  ```

### 3) **객체 메서드(Object Methods)**
- **정의**: 객체의 행동을 정의하는 함수이다.
- **특징**: 객체의 상태를 변경하거나, 특정 작업을 수행한다.
- **예시**:
  ```python
  class Person:
      def __init__(self, name):
          self.name = name

      def greet(self):  # 객체 메서드
          return f"Hello, {self.name}!"

  person = Person("Bob")
  print(person.greet())  # 출력: Hello, Bob!
  ```

---

## 2. **파이썬에서의 접근 제어**

파이썬은 모든 속성과 메서드가 기본적으로 **public**이다. 즉, 호출자가 객체의 속성이나 메서드에 접근할 수 있다. 그러나 몇 가지 규칙을 통해 **private** 또는 **protected**처럼 동작하도록 할 수 있다.

### 1) **밑줄(`_`)로 시작하는 속성**
- **의미**: 해당 속성은 private으로 간주되며, 외부에서 접근하지 않기를 기대한다.
- **예시**:
  ```python
  class Person:
      def __init__(self, name):
          self._name = name  # protected 속성 (관례적으로 private처럼 사용)

  person = Person("Alice")
  print(person._name)  # 출력: Alice (접근 가능하지만, 권장하지 않음)
  ```

### 2) **이중 밑줄(`__`)로 시작하는 속성 (Name Mangling)**
- **의미**: 해당 속성은 클래스 외부에서 직접 접근하기 어렵도록 이름이 변경된다. (Name Mangling)
- **목적**: 서브클래스에서 속성 이름 충돌을 방지한다.
- **예시**:
  ```python
  class Person:
      def __init__(self, name):
          self.__name = name  # private 속성 (Name Mangling 적용)

  person = Person("Alice")
  # print(person.__name)  # ❌ 접근 불가 (AttributeError)
  print(person._Person__name)  # 출력: Alice (Name Mangling으로 접근)
  ```

---

## 3. **프로퍼티(Property)를 사용하는 이유**

객체의 속성에 접근할 때 추가적인 로직(예: 유효성 검사, 계산 등)이 필요한 경우, 프로퍼티를 사용하면 코드를 더 깔끔하고 안전하게 작성할 수 있다.

### 프로퍼티 사용 예시

```python
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius  # 내부적으로는 _celsius로 저장

    @property
    def celsius(self):
        return self._celsius  # celsius 속성 값을 반환

    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Temperature cannot be below -273.15°C")  # 유효성 검사
        self._celsius = value  # 유효한 값만 저장

    @property
    def fahrenheit(self):
        return (self._celsius * 9/5) + 32  # 계산된 값을 반환

# 사용 예시
temp = Temperature(25)
print(temp.celsius)  # 출력: 25 (속성처럼 접근)
print(temp.fahrenheit)  # 출력: 77.0 (계산된 값)

temp.celsius = 30  # setter 호출
print(temp.celsius)  # 출력: 30
# temp.celsius = -300  # ❌ ValueError 발생
```

---

## 4. **프로퍼티를 사용하는 이유와 장점**

1. **캡슐화**: 속성에 접근할 때 추가적인 로직을 실행할 수 있다.
2. **유효성 검사**: 속성에 잘못된 값이 할당되는 것을 방지할 수 있다.
3. **계산된 값**: 속성의 값을 동적으로 계산하여 반환할 수 있다.
4. **코드 가독성**: 속성처럼 접근하지만, 내부적으로는 메서드로 동작한다.

---

## 5. **예시 코드**

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance  # protected 속성

    @property
    def balance(self):
        return self._balance  # balance 속성 값을 반환

    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError("Balance cannot be negative")  # 유효성 검사
        self._balance = value  # 유효한 값만 저장

    def deposit(self, amount):
        self.balance += amount  # setter 호출

    def withdraw(self, amount):
        if amount > self.balance:
            raise ValueError("Insufficient balance")  # 유효성 검사
        self.balance -= amount  # setter 호출

# 사용 예시
account = BankAccount(1000)
print(account.balance)  # 출력: 1000

account.deposit(500)
print(account.balance)  # 출력: 1500

account.withdraw(200)
print(account.balance)  # 출력: 1300

# account.balance = -100  # ❌ ValueError 발생
# account.withdraw(2000)  # ❌ ValueError 발생
```

---

## 6. **요약**

- **속성(Attribute)**: 객체의 상태를 저장하는 변수.
- **프로퍼티(Property)**: 속성에 대한 접근을 제어하는 방법.
- **객체 메서드(Object Methods)**: 객체의 행동을 정의하는 함수.
- **접근 제어**: 밑줄(`_`)을 사용하여 private/protected 속성을 표시할 수 있다.
- **프로퍼티 사용**: 속성 접근 시 추가적인 로직이 필요할 때 유용

