---
layout: post
title: "Python Ruby Study _ 다중상속과 Mixin"
date: 2017-02-07
categories: [python, ruby]
---

다중상속은 하나의 클래스가 여러 클래스를 상속받는 것이다. python은 다중상속을 지원하지만
ruby는 다중상속을 지원하지 않는다. 하지만 ruby는 `Mixin`이라는 접근을 통해서 다중상속과
비슷한 효과를 낼 수 있다. 아래 예제를 보자. python에서 다중상속을 하는 방법은 상속받을
클래스를 여러개 쓰면된다. 이때, method `c1_m`이나 `c2_m`처럼 두 부모클래스에게
상속받은 method 이름이 다르면 문제가 없다. 허나 method `m`처럼 이름이 같으면 문제가
생긴다. 다행이도 python에서는 상속받을 클래스를 쓰는곳에 쓰인 순서로 우선순위를 정해줘서
에러가 발생하지 않는다.

**python**

```python
# your code goes here
class C1():
	def c1_m(self):
		print("c1_m")
	def m(self):
		print("C1 m")

class C2():
	def c2_m(self):
		print("c2_m")
	def m(self):
		print("C2 m")

class C3(C1, C2):
	pass


c = C3()
c.c1_m()
c.c2_m()
c.m()
```

**result**

```
c1_m
c2_m
C1 m
```


위 결과를 보면 `c.m()`이 실행되고 `C1_class`의 method `m`이 실행됬다. 그이유는
`class C3(C1, C2):` 에서 `C1_class`가 `C2_class`보다 먼저 쓰여있어서 우선순위 설정이
`C1_class`가 더 높기 때문이다. 참고로 `C3_class`처럼 클래스에 아무것도 없는 경우
`pass` 키워드를 적어서 사용해도 된다고 알려줘야 나중에 사용시 에러가 안난다!

**python**

```python
# your code goes here
class C1():
	def c1_m(self):
		print("c1_m")
	def m(self):
		print("C1 m")

class C2():
	def c2_m(self):
		print("c2_m")
	def m(self):
		print("C2 m")

class C3(C2, C1):
	pass


c = C3()
c.c1_m()
c.c2_m()
c.m()
print(C3.__mro__)
```

**result**

```
c1_m
c2_m
C2 m
(<class '__main__.C3'>, <class '__main__.C2'>, <class '__main__.C1'>, <class 'object'>)
```


위 결과는 `C2_class`를 `C1_class`보다 먼저 적어 `C2_class`의 우선순위가 높다. 그러므로
method `m`이 실행되면 `C2_class`의 method `m`이 실행된다. 그리고 `__mro__` 키워드를
출력하면 이 클래스의 우선순위에 대해 볼 수 있으니 유용히 사용하자! 이제 ruby의 `Mixin`을
통해 다중상속 효과를 내는 방법을 보겠다. `Mixin`은 클래스 안에 모듈의 함수를 포함시키는
방법으로 다중상속 효과를 낸다. 아래 예제처럼 모듈을 만들고 클래스에서 `include`를 하면
포함한 모듈의 method를 사용할 수 있다.

**ruby**

```ruby
# your code goes here

module M1
	def m1_m
		p "m1_m"
	end
end

module M2
	def m2_m
		p "m2_m"
	end
end

class C
	include M1, M2
end

c = C.new()
c.m1_m()
c.m2_m()
```

**result**

```
"m1_m"
"m2_m"
```

$x$에 대한 이차 방정식 $ax^2 + bx + c = 0$의 해는 다음과 같다.
\\[
x = \frac{-b \pm \sqrt{b^2-4ac}}{2a}
\\]
