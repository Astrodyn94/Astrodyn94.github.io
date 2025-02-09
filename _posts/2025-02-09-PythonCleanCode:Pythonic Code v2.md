---
layout: post
title: "Python Clean Code : Pythonic Code (Context Manager)"
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


## 1. 컨텍스트 관리자의 기본 개념

- 컨텍스트 관리자는 리소스를 안정하게 할당하고 해제할 수 있게 도와준다. 이를 통해 예외가 발생하더라도 리소스가 적절히 해제되도록 보장해준다. 컨텍스트 관리자는 __enter__ , __exit__ 두개의 매직 매소드로 구성된다. 
- **`with` 문**: 컨텍스트 관리자를 사용하여 리소스를 관리한다.
- **`__enter__` 메서드**: `with` 문이 시작될 때 호출된다. 리소스를 할당하고 반환한다.
- **`__exit__` 메서드**: `with` 블록이 종료될 때 호출된다. 리소스를 해제한다.

#### 예시: 파일 열기
```python
with open("example.txt", "r") as file:  # 1. 파일을 열고 파일 객체를 `file` 변수에 할당
    content = file.read()  # 2. 파일 내용을 읽어 `content` 변수에 저장
    print(content)  # 3. 파일 내용을 출력
# 파일은 자동으로 닫힘
```

- `open()` 함수는 컨텍스트 관리자 프로토콜을 구현한다.
- `with` 블록이 끝나면 파일이 자동으로 닫히게된다.

---

## 2. 컨텍스트 관리자 직접 구현하기

`__enter__`와 `__exit__` 메서드를 구현하여 사용자 정의 컨텍스트 관리자를 구현할 수도 있다. 

#### 예시 1: 사용자 정의 컨텍스트 관리자
```python
class MyContextManager:
    def __enter__(self):
        print("리소스 할당")
        return self  # as 뒤에 반환될 객체

    def __exit__(self, exc_type, exc_value, traceback):
        print("리소스 해제")
        if exc_type:  # 예외가 발생한 경우
            print(f"예외 발생: {exc_value}")
        return True  # 예외를 무시하고 계속 실행

# 사용 예시
with MyContextManager() as manager:
    print("작업 중")
    # raise ValueError("테스트 예외")  # 예외 발생 시에도 리소스 해제
```

- **`__enter__`**: `with` 문이 시작될 때 호출된다. 리소스를 할당하고 반환한다.
- **`__exit__`**: `with` 블록이 종료될 때 호출된다. 리소스를 해제하고, 예외가 발생한 경우 처리한다.

---

#### 예시 2. 사용자 정의 컨텍스트 관리자: 작업 시간 측정

#### 코드 설명
- **`__enter__`**: `with` 블록이 시작될 때 현재 시간을 기록한다.
- **`__exit__`**: `with` 블록이 끝날 때 현재 시간을 다시 기록하고, 실행 시간을 계산하여 출력한다.
- **활용 사례**: 특정 작업(예: 함수 실행, 데이터 처리 등)의 실행 시간을 측정

#### 예시 코드

```python
import time

class TimerContextManager:
    def __enter__(self):
        self.start_time = time.time()  # 1. `with` 블록이 시작될 때 현재 시간을 기록
        print("작업 시작 시간 기록")
        return self  # 2. `as` 뒤에 반환될 객체 (여기서는 자기 자신)

    def __exit__(self, exc_type, exc_value, traceback):
        self.end_time = time.time()  # 3. `with` 블록이 끝날 때 현재 시간을 기록
        elapsed_time = self.end_time - self.start_time  # 4. 실행 시간 계산
        print(f"작업 완료! 소요 시간: {elapsed_time:.2f}초")  # 5. 실행 시간 출력
        if exc_type:  # 6. 예외가 발생한 경우
            print(f"예외 발생: {exc_value}")  # 7. 예외 정보 출력
        return True  # 8. 예외를 무시하고 계속 실행

# 사용 예시
with TimerContextManager() as timer:  # 9. `with` 문 진입, `__enter__` 호출
    print("작업 중...")  # 10. `with` 블록 내부 작업 실행
    time.sleep(2)  # 11. 2초간 대기 (작업 시뮬레이션)
    # raise ValueError("테스트 예외")  # 12. 예외 발생 시에도 실행 시간 측정
# 13. `with` 블록이 끝나면 `__exit__` 호출
```

---

### 실행 결과

```plaintext
작업 시작 시간 기록
작업 중...
작업 완료! 소요 시간: 2.00초
```

- **예외 발생 시**:
  ```plaintext
  작업 시작 시간 기록
  작업 중...
  예외 발생: 테스트 예외
  작업 완료! 소요 시간: 0.00초
  ```

---

## 2. **`contextlib`를 사용한 시간 측정 컨텍스트 관리자**

`contextlib` 모듈은 컨텍스트 관리자를 더 간단하게 구현할 수 있도록 도와준다. 특히 `contextmanager` 데코레이터를 사용하면 제너레이터 함수로 컨텍스트 관리자를 만들 수 있다.

### 예시 코드

```python
from contextlib import contextmanager
import time

@contextmanager
def timer_context_manager():
    start_time = time.time()  # 1. `with` 블록이 시작될 때 현재 시간을 기록
    print("작업 시작 시간 기록")
    try:
        yield  # 2. `with` 블록 내부 코드 실행
    except Exception as e:  # 3. 예외가 발생한 경우
        print(f"예외 발생: {e}")  # 4. 예외 정보 출력
    finally:
        end_time = time.time()  # 5. `with` 블록이 끝날 때 현재 시간을 기록
        elapsed_time = end_time - start_time  # 6. 실행 시간 계산
        print(f"작업 완료! 소요 시간: {elapsed_time:.2f}초")  # 7. 실행 시간 출력

# 사용 예시
with timer_context_manager():  # 8. `with` 문 진입, `yield`까지 실행
    print("작업 중...")  # 9. `with` 블록 내부 작업 실행
    time.sleep(2)  # 10. 2초간 대기 (작업 시뮬레이션)
    # raise ValueError("테스트 예외")  # 11. 예외 발생 시에도 실행 시간 측정
# 12. `with` 블록이 끝나면 `finally` 블록 실행
```

---

### 실행 결과

```plaintext
작업 시작 시간 기록
작업 중...
작업 완료! 소요 시간: 2.00초
```

- **예외 발생 시**:
  ```plaintext
  작업 시작 시간 기록
  작업 중...
  예외 발생: 테스트 예외
  작업 완료! 소요 시간: 0.00초
  ```


### **활용 사례**

이 컨텍스트 관리자는 다음과 같은 작업에 활용할 수 있습니다:
1. **함수 실행 시간 측정**:
   ```python
   def heavy_computation():
       time.sleep(3)  # 무거운 계산 작업 시뮬레이션

   with TimerContextManager():
       heavy_computation()
   ```

2. **데이터 처리 시간 측정**:
   ```python
   def process_data(data):
       time.sleep(1)  # 데이터 처리 작업 시뮬레이션

   data = [1, 2, 3, 4, 5]
   with TimerContextManager():
       process_data(data)
   ```

3. **네트워크 요청 시간 측정**:
   ```python
   import requests

   with TimerContextManager():
       response = requests.get("https://www.example.com")
       print(response.status_code)
   ```



### 예시코드 2 -  파일 관리
```python
@contextmanager
def open_file(file, mode):
    f = open(file, mode)
    try:
        yield f
    finally:
        f.close()

with open_file("example.txt", "r") as file:
    content = file.read()
    print(content)
```

### 예시코드 3 -  데이터베이스 연결 관리
```python
@contextmanager
def db_connection(db_url):
    connection = connect(db_url)  # 1. 데이터베이스에 연결
    try:
        yield connection  # 2. 연결 객체를 `as` 뒤에 반환
    finally:
        connection.close()  # 4. `with` 블록이 끝나면 연결을 닫음

with db_connection("my_db_url") as conn:  # 3. `with` 문 진입, `yield`까지 실행
    cursor = conn.cursor()  # 5. 커서 객체 생성
    cursor.execute("SELECT * FROM my_table")  # 6. SQL 쿼리 실행
# 7. `with` 블록이 끝나면 `finally` 블록 실행 (연결 닫기)
```

### 예시코드 4 - 임시 파일 관리
```python
import tempfile
import os

@contextmanager
def temp_file():
    temp = tempfile.NamedTemporaryFile(delete=False)
    try:
        yield temp
    finally:
        temp.close()
        os.unlink(temp.name)

with temp_file() as temp:
    temp.write(b"Hello, World!")
    temp.seek(0)
    print(temp.read())
```

---

## 요약

- **컨텍스트 관리자**: 리소스를 안전하게 관리하기 위한 도구로, `with` 문과 함께 사용된다.
- **직접 구현**: `__enter__`와 `__exit__` 메서드를 구현하여 사용자 정의 컨텍스트 관리자를 만들 수 있다.
- **`contextlib` 모듈**: `contextmanager` 데코레이터를 사용하면 제너레이터 함수로 간단히 컨텍스트 관리자를 구현할 수 있다.


