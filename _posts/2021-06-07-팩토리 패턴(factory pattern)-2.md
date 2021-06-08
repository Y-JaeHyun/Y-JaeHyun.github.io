---
layout:     post
title:      팩토리 패턴 - 2(Factory Pattern - 2)
author:     bcnote3314
subtitle:  	디자인 패턴
tags: 		designpattern
category: Pattern
---


## Pizza 클래스에 재료 더하기.. 

(소제목으로 딱히 지을 말이 음슴ㅠ)

![팩토리메소드패턴](http://drive.google.com/uc?export=view&id=1ABWBpNnATv9yBjCP88QQORgB1-3j6sMN)

위와같이 소개했던 피자 클래스에서 각 업체별로 다른 원재료를 사용한다는 형태의 예제로 이 패턴을 소개한다.  

우선 원재료를 생산할 팩토리의 인터페이스를 정의한뒤에 지역별 팩토리를 상속받아 만든다. 
팩토리를 만들고나면 기존에 PizzaStore에 적용하는것으로 모든 것을 하나로 묶는다.

```python
class PizzaIngredientFactory():
	@abstractmethod
	def createDough(self):
		pass
		
	@abstractmethod
	def createSauce(self):
		pass
	
	@abstractmethod
	def createVeggies(self):
		pass
		
class PizzaHutIngredientFactory(PizzaIngredientFactory):
	def createDough(self):
		return ThinCrustDough() #이친구들도 물론 각각 구현되어있어야하지만... 생략!
		
	def createSauce(self):
		return MarinaraSauce()
		
	def createVeggies:
		veggies = [Garlic(), Onion(), MushRoom()]
```

Factory에서 생산된 재료들은 Pizza 클래스에 반영되어야한다.  
또한 각 피자들은 재료들을 생산받을 팩토리를 알고 있어야 한다.  

```python
class Pizza():
	def __init__(self):
		self.name = ""
		self.dough = None
		self.sauce = None
		self.veggies = []
		
	#이 추상메소드에서 각 피자마다의 재료들이 정해지게 되며 이는 팩토리에 의해 결정이 될것이다.
	@abstractmethod
	def prepare(self): 
		pass
	
	
	def bake(self):
		~~~
	
	def cut(self):
		~~~
	
	def box(self):
		~~~
	
	# 생략...
	
class CheesePizza(Pizza):
	def __init__(self, ingredientFactory):
		self.ingredientFactory = ingredientFactory
	
	def prepare(self):
		#팩토리 동작
		self.dough = self.ingredientFactory.createDough()
		self.sauce = self.ingredientFactory.createSauce()
		self.veggies = self.ingredientFactory.createVeggies()
```

마지막으로 각 업체별 Store는 아래와 형태로 구현된다.  

```python
class PizzaHutStore(PizzaStore):
	def createPizza(self, item):
		pizza = None
		ingredientFactory = PizzaHutIngredientFactory()
		
		if item == "cheese":
			pizza = CheesePizza(ingredientFactory)
			pizza.setName("PizzaHut Style Cheese Pizza")
		elif item == "veggie":
			pizza = VeggiePizza(ingredientFactory)
			pizza.setName("PizzaHut Stype Veggie Pizza")
		
		...
		
		return pizza
```

## 추상 팩토리 패턴

> 추상 팩토리 패턴 - 추상 팩토리 패턴에서는 인터페이스를 이용하여 서로 연관된, 또는 의존하는 객체를 구상클래스를 지정하지 않고도 생성할수 있다.

위에서 한 작업들을 정리해보면 제품군을 위한 인터페이스인 PizzaIngredientFactory를 정의했다.  
정의된 PizzaIngredientFactory를 이용해서 각 Pizza 업체에서 본인들의 공장에서 재료를 전달받을수 수정했다.  
제품을 생산하는 팩토리와 구성요소를 분리하는 것으로 상황에 따른 제품 생산이 가능하게 되었다.  

![추상팩토리](http://drive.google.com/uc?export=view&id=1F38rIfofoEWMBo0dWxTvptM3LRlpgJ0d)

잘보면 추상 팩토리 패턴에 보면 결국 팩토리 메소드 패턴이 잠재적으로 존재한다.  
결국 인터페이스에 메소드들은 모두 추상 메소드이며 서브클래스에서 객체로 만들기 떄문이다.


## 팩토리 메서드 패턴 / 추상 팩토리 패턴

팩토리 패턴에서 중요한점은 특정 구현 부분을 분리시킨다는 것이다.  
두가지 패턴 모두 구현부를 분리하기 위한 방법이다.
  
다만 팩토리 메서드 패턴의 경우 상속을 통해 인스턴스를 만들게 된다.  
createPizza 함수를 구현한 서브 클래스를 통해서 실제 Product가 전달되는 형식이다.  

그에 반해 추상 메소드는 구성을 이용하였다.  
자체 변수에 팩토리 객체를 전달받아 사용하는 형식이다.

추상 팩토리 패턴의 경우 제품군에 내용이 추가되면 인터페이스의 형태가 변경되게 되며 모든 서브클래스에 영향을 주는 경우도 존재한다.  
하지만 팩토리 메서드 패턴의 경우 서브 클래스에서 정의된 내용만 바뀌면된다.

다만 팩토리 메소드 패턴의 경우 한가지의 인스턴스를 생산한다. 

## 느낀점

객체지향을 깊게 공부해본적이 없는탓인지.. 헷갈리기 시작했다!  
마지막에 추상팩토리 패턴은 스트라티지 패턴이랑 다를게 뭘까?

스트라티지 패턴이 행동의 변화를 위하여 '구성'이라는 방법을 이용했었다.  
추상 팩토리 패턴도 결국 생성하고자하는 인스턴스의 종류의 변화를 위한 방법으로 '구성'을 이용한 스트라티지 패턴이 아닌가?  

두개의 구분이 단순히 어떤 행동 / 인스턴스 생성이라는 동작과 결과물의 차이인 것인가?
그것도 아니면 '제품군'이라는 부분을 처리하기 위한 팩토리 내에서 사용하는 방법론?  

이해가 잘안간다 ㅠ

