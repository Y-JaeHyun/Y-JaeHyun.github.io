---
layout:     post
title:      Python Decorator
author:     bcnote3314
subtitle:  	데코레이터
tags: 		Python 
category: Note
---

## Python 데코레이터

C를 주로 쓰기 때문에 전혀 모르는 문법이라 정리한다.

사용자가 구조를 수정하지 않고 기존 객체에 새로운 기능을 추가할수 있도록 도와주는 Python의 디자인 패턴.
정의된 함수 상단에 '@Decorator Name'을 입력하는 형태로 사용한다.

```python
class Test:
	@staticmethod
	def add(a, b):
		print(a + b)
```

데코레이터는 함수를 장식하는 의미를 가진다.
함수의 실행 전과 후에 데코레이터에 정의된 기능을 수행한다.

데코레이터를 정의하는 방법은 함수를 매개변수로 받는 함수를 만드는 것이다.
해당 함수에서는 전달 받은 함수를 호출하며 전처리/후처리를 위한 동작을 정의한다.

```python

#test.py

class DecoratorClass():
    def trace(func):
        def wrapper():
            print(func.__name__, '함수 시작') #전 처리
            func()
            print(func.__name__, '함수 끝') #후 처리
        return wrapper

@DecoratorClass.trace
@DecoratorClass.trace
def hello():
    print('hello')


if __name__ == '__main__':
    hello()
	
```

위 코드와 같은 형태이다.
이떄 @가 여러번 사용될수도 있다.
이때 상단에 있는 데코레이터는 바로 하단의 데코레이터를 감싸는 형태가 된다.

결과적으로 아래와 같은 출력 결과가 나온다.

```
$ python test.py
wrapper 함수 시작
hello 함수 시작
hello
hello 함수 끝
wrapper 함수 끝
```

자주 사용되는 문법으로 보이는데 예외처리나 로깅등의 후처리등 용도로 자주 사용될것 같다.
