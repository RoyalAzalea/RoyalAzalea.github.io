---
layout: post
title: "Python Ruby Study _ 객체와 변수"
date: 2017-01-31
categories: [python, ruby]
---

객체는 프로그램을 만드는데 있어서 부품처럼 생각될 수 있다. 부품에 케이스를 씌운다고
생각해보자. 부품의 내부는 외부로부터 안전해진다. 이처럼 객체에도 케이스를 씌울 수 있는데
이걸 캡슐화(`Encapsulation`)라고 한다. 캡슐화를 통해 객체 내부의 Data가 외부로부터
영향을 받지 않는다. 아래 예제들은 캡슐화가 되지 않은 예제들이다. python의 경우 인스턴스
외부에서 인스턴스 변수에 대해 접근하는 것을 허용한다. 이와달리 ruby의 경우 인스턴스
외부에서 인스턴스 변수에 대해 접근하는 것을 허용하지 않는다. 참고로 인스턴스 내부 method에서 인스턴스 변수는 둘 다 허용됨!

**python**

```python
# your code goes here
class C(object):
	def __init__(self, v):
		self.value = v
	def show(self):
		print(self.value)

c1 = C(10)
print(c1.value)
c1.value = 20
print(c1.value)
c1.show()
```

**result**

```
10
20
20
```

**ruby**

```ruby
# your code goes here
class C
	def initialize(v)
		@value = v
	end
	def show()
		p @value
	end
end

c1 = C.new(10)
# p c1.value
# c1.value = 20
c1.show()
```

**ruby**

{% highlight ruby %}
# your code goes here
class C
	def initialize(v)
		@value = v
	end
	def show()
		p @value
	end
end

c1 = C.new(10)
# p c1.value
# c1.value = 20
c1.show()
{% endhighlight %}

**result**

```
10
```


객체의 인스턴스에 직접 접근하는 것은 민감하다. 프로그램이 커졌을때 외부에서 인스턴스 변수에
직접 접근을 하다가 실수했다고 생각해보자. 작은 실수로 생각할 수 있겠지만 큰 문제를 초래할수
있다. 그러니 인스턴스 변수에 접근하는 것은 인스턴스 method를 통해 접근하는것이 안전하다.
아래 예제는 `set/get`method를 통해 인스턴스 변수에 접근하는 예제이다.
