---
layout:     post
title:      팩토리 패턴 - 1(Factory Pattern - 1)
author:     bcnote3314
subtitle:  	디자인 패턴
tags: 		designpattern
category: Pattern
---

## 구상 객체

Java에는 "new"라는 예약어가 존재한다.  
new는 클래스의 인스턴스를 만들기 위한 예약어이다.  
Java 외의 객체지향 언어에서는 new를 사용하지 않는 경우도 있지만 어찌되었든 클래스를 인스턴스로 만들기 위한 동작이 존재한다.

이 책에서는 지금까지 구현이 아닌 인터페이스를 기준으로 프로그래밍을 하라고 강조했다.  
하지만 객체를 인스턴스화 하기 위해서는 결국 언젠가는 인터페이스가 아닌 구현된 구상 클래스를 사용할수 밖에 없다.

예를들어 타입에 따라 조금씩 다르게 구현된 클래스의 인스턴스를 만들기 위한 코드로 아래와 같이 작성되는 경우가 많다.

```python

if type == cheese:
	pizza = CheesePizza();
elif type == greek:
	pizza = GreekPizza();
elif type == pepperoni:
	pizza = PepperoniPizzaa();
	
```

이러한 코드들은 타입 종류가 늘어나거나 변경됨에 따라 항상 같이 수정되어야 한다.  
하지만 그렇다고 인스턴스를 안만들수는 없는 노릇이다.  

이러한 각 인스턴스 생성을 분리하고 관리하기 위하여 만들어진 패턴이 팩토리 패턴이다.


## 심플 팩토리 (Simple Factory)

패턴이라고 하기는 모호한 프로그래밍을 하다보면 자연스럽게 자주 사용되는 형태의 모델(?)이다.  

항상 변경되는 부분을 따로 캡슐화 하는 것을 생각한다면 위에서 이야기한 변경되는 부분인 객체 생성 부분을 캡슐화 하는 방법이다.  

```python 

def orderPizza(type):
	pizza = None
	
	if type == cheese:
		pizza = CheesePizza();
	elif type == greek:
		pizza = GreekPizza();
	elif type == pepperoni:
		pizza = PepperoniPizzaa();
		
	pizza.prepare()
	pizza.bake()
	pizza.cout()
	pizza.box()
	
	return pizza
```

위와 같은 코드가 있다고 가정하고 각 pizza 생성 방식은 항상 동일하게 변경되지 않는다고 가정하면 변경될 가능성이 있는 부분은 메뉴의 종류가 추가되는 것이다.

해당 부분을 분리하여 객체 생성을 다른 클래스에서 하도록 맡긴다.

전체적인 클래스 다이어 그램은 아래와 같은 형태가 된다.

![심플팩토리](http://drive.google.com/uc?export=view&id=1qnAr4zxz-DUI50ByEZnsBzyBMq_rkK5h)

여기서 SimpleFactory는 또 다시 구성을 활용하여 동적으로 변경할수 있다.  
예를들어 각 브랜드별로 팩토리에서 전달해주는 피자의 내부 값이 다를 수 있다.  

1장의 스트래티지 패턴을 활용하여 PizzaStore 객체 생성시 생성자에 전달해주는 Factory에 어떤 팩토리를 넣어주느냐에 따라 동적인 변화가 가능하다.  
물론 각 브랜드별로 Factory를 상속받은 구상 클래스가 구현되어야한다.

![심플팩토리2](http://drive.google.com/uc?export=view&id=1fPKi7Jpgfzy3clqHmMas6Vils5Uvqu4Q)


## 심플 팩토리 모델 고도화?

현재 Store에 Factory를 구성요소로 활용하여 연결되어 있긴 하지만 피자 생성의 주체는 SimpleFactory 클래스이다.  
즉 피자를 생성하고 배달하는 일련의 과정이 두개의 클래스에에서 처리된다.  

이런 과정을 하나의 클래스에 국한시키면서도 각 브랜드별로 고유한 Pizza를 생성하는 방식도 가능하다.

![팩토리메소드](http://drive.google.com/uc?export=view&id=1RqkEXRPi1Bm3VGT7jDWBkaFc4m-JxzYu)

SimpleFactory에 존재하는 CreatePizza 함수를 추상메소드의 형태로 PizzaStore 클래스에 선언하는 것이다.  
각 브랜드별 Store 클래스에서 PizzaStore를 상속받아 createPizza 함수를 구현하는 형태이다.  

이때 만든 추상메소드를 팩토리 메소드라고 부른다. (서브 클래스를 통해 인스턴스를 결정하는 메소드)

이렇게되면 orderPizza 함수 입장에서는 어떤 Pizza인지 모르는 상태이며 서브클래스에 의하여 결정이 된다.

# 팩토리 메소드 패턴

팩토리 패턴은 여러가지 방식이 존재한다.  
다만 모든 팩토리 패턴은 객체생성을 캡슐화 하는 형태이다.  

먼저 알아본 팩토리 메소드 패턴에서는 서브클래스에서 어떤 클래스를 만들지 결정하게 하는 방식으로 객체 생성을 캡슐화 했다.  

팩토리 메소드 클래스는 크게 생산자 클래스와 제품 클래스로 구분된다.  

![팩토리메소드패턴](http://drive.google.com/uc?export=view&id=1ABWBpNnATv9yBjCP88QQORgB1-3j6sMN)

Pizza 클래스와 PizzaStore 클래스는 각각 추상 클래스를 상속받아 확장/구체적인 구현을 담당하는 구상클래스들을 가지는 동일한 형태의 병렬적인 구조를 가지고 있다.  
이때 생산자 클래스에는 팩토리 메소드를 포함하고 있으며 팩토리 메소드는 제품을 만드는 것에 대한 정보들이 캡슐화 되어 있는 것이다.  

