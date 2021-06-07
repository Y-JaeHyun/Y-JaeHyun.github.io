---
layout:     post
title:      팩토리 패턴 - 2(Factory Pattern - 2)
author:     bcnote3314
subtitle:  	디자인 패턴
tags: 		designpattern
category: Pattern
---

## 객체의 의존성


팩토리의 개념이 없는 상태에서 PizzaStore를 만들었을때 아래의 코드와 같았다.

```python 

class PizzaStore():
	def orderPizza(type):
		pizza = None
	
		if type == cheese:
			pizza = CheesePizza();
		elif type == greek:
			pizza = GreekPizza();
		elif type == pepperoni:
			pizza = PepperoniPizza();
		
		pizza.prepare()
		pizza.bake()
		pizza.cout()
		pizza.box()

		return pizza
```

이 클래스는 총 3개의 구상 클래스에 의존하게 된다. (CheesePizza, GreekPizza, PepperoniPizza)  
피자의 종류가 늘어나면 더 많은 클래스에 의존하게 되며 구상클래스의 변경사항에 따라 PizzaStore의 변경도 필요할 수 있다.  

의존성 뒤집기 원칙(Dependency Inversion Principle)
> 추상화된 것에 의존하도록 만드어라. 구상 클래스에 의존하도록 만들지 않도록 한다.

이 원칙에는 고수준 구성 요소가 저수준 구성요소에 의존하면 안된다는 것이 내포되어 있다.  
고수준 구성요소란 다른 저수준 구성 요소에 의해 정의되는 행동이 들어있는 구성요소를 말한다.  
예제에서는 PizzaStore의 경우 pizza.prepare() 와 같은 Pizza 객체들에 의하여 행동이 정의 되기 떄문에 고수준 구성요소라 할수 있다.  

하지만 고수준 구성요소인 PizzaStore가 저수준 구성요소인 Pizza에 의존하고 있다.  

![원칙미적용](http://drive.google.com/uc?export=view&id=109uxP-YZqyrahHqQ66rpJHsE6073UUye)

원칙을 적용하기 위해 팩토리 메소드 패턴을 사용하면 아래와 같은 형태로 변하게 되며 고수준 구성요소와 저수준 구성요소 모두 추상클래스에 의존하게 된다.

![원칙적용](http://drive.google.com/uc?export=view&id=11QJ6jV7LhHbc3dupQWKvx8jKdMzHpsqw)


## 원칙을 지키기 위한 가이드라인

* 어떤 변수에도 구상 클래스에 대한 레퍼런스를 저장하지 말자.
  * 직접 인스턴스를 new하는 형태가 아니라 팩토리를 통하여 전달 받는 형태를 사용한다.
* 구상 클래스에서 유도된 클래스를 만들지 말자.
  * 구상 클래스에서 유도된 클래스는 이미 구상 클래스에 의존하는 것이다. 추상 클래스나 인터페이스같이 추상화된 것으로 부터 시작되어야한다.
* 베이스 클래스에 이미 구현된 메소드를 오버라이드 하지 말자.

물론 항상 지킬수는 없고 지향하는 방향이다.  


## 추상 팩토리 패턴

TODO...


