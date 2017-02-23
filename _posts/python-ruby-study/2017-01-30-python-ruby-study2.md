---
layout: post
title: "Python Ruby Study _ 객체 만들기"
date: 2017-01-30
categories: [python, ruby]
---

python과 ruby에서 객체를 직접 만들어 보겠다. python에서는 `class`이름 뒤에 `(object)`를
써주고 `:`를 사용한다. ruby에서는 `class-end`를 사용한다.  

**python**

```python
class Cal(object):
```

**ruby**

{% highlight ruby %}
class Cal
end
{% endhighlight %}


객체는 생성되면 생성자를 자동으로 실행하게 된다. 생성자는 객체가 생성될 때, 필요한 Data
를 매개변수로 받거나 필요한 작업을 실행한다.  

이 생성자를 만들어 보겠다. python에서는 `__init__`을 선언한다.
**이때, 생성자의 매개변수 첫번째 인자는 생성자가 속해있는 인스턴스를 받는다.**
ruby에서는 `initialize`를 선언한다.  

그리고 매개변수로 받아온 값들은 지역적이므로 인스턴스 변수에 저장을 해야지 다른
method에서 사용할 수 있다. 인스턴스 변수를 만드는 법은 python의 경우 아래 `self`처럼
method의 첫번째 인자를 이용하면 된다. ruby의 경우는 `@`를 변수 앞에 붙여서 인스턴스
변수를 이용한다.  

**python**

```python
class Cal(object):
  def __init__(self, v1, v2):
		print("construction", v1, v2)
		self.v1 = v1
		self.v2 = v2
```

**ruby**

{% highlight ruby %}
class Cal
	def initialize(v1,v2)
		p "construction", v1, v2
		@v1 = v1
		@v2 = v2
	end
end
{% endhighlight %}


이제 간단하게 덧셈, 뺄셈을 할 수 있는 객체를 만들어서 사용해 보겠다. **python의 경우
class안에 method들은 method가 속해있는 인스턴스를 첫번째 매개변수로 받는다.**  

참고로 ruby에서 `puts`대신 `p`라는 함수를 사용할 수 있다.
그리고 python에서 `print("construction", v1, v2)`, ruby에서 `p "construction", v1, v2`를
보면 여러가지를 `,`를 통해 동시에 출력할 수 있다.  

**python**

```python
# your code goes here
class Cal(object):
	def __init__(self, v1, v2):
		print("construction", v1, v2)
		self.v1 = v1
		self.v2 = v2

	def add(self):
		print("add")
		return self.v1 + self.v2

	def subtract(s):
		print("sub")
		return s.v1 - s.v2

c1 = Cal(10,10)
print(c1.add())
print(c1.subtract())

c2 = Cal(30,20)
print(c2.add())
print(c2.subtract())
```

**result**

```
construction 10 10
add
20
sub
0
construction 30 20
add
50
sub
10
```

**ruby**

{% highlight ruby %}
# your code goes here
class Cal
	def initialize(v1,v2)
		p "construction", v1, v2
		@v1 = v1
		@v2 = v2
	end
	def add()
		p "add method"
		return @v1 + @v2
	end
	def subtract()
		p "sub method"
		return @v1 - @v2
	end
end

c1 = Cal.new(10,10)
p c1.add()
p c1.subtract()

c2 = Cal.new(30,20)
p c2.add()
p c2.subtract()
{% endhighlight %}

**result**

```
"construction"
10
10
"add method"
20
"sub method"
0
"construction"
30
20
"add method"
50
"sub method"
10
```
