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

**result**

```
10
```


객체의 인스턴스에 직접 접근하는 것은 민감하다. 프로그램이 커졌을때 외부에서 인스턴스 변수에
직접 접근을 하다가 실수했다고 생각해보자. 작은 실수로 생각할 수 있겠지만 큰 문제를 초래할수
있다. 그러니 인스턴스 변수에 접근하는 것은 인스턴스 method를 통해 접근하는것이 안전하다.
아래 예제는 `set/get`method를 통해 인스턴스 변수에 접근하는 예제이다.

**python**

```python
# your code goes here
class C(object):
    def __init__(self, v):
        self.value = v
    def show(self):
        print(self.value)
    def getValue(self):
        return self.value
    def setValue(self, v):
        self.value = v

c1 = C(10)
print(c1.getValue())
c1.setValue(20)
print(c1.getValue())
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
  def getValue()
  	return @value
  end
  def setValue(v)
  	@value = v
  end
end  

c1 = C.new(10)
p c1.getValue()
c1.setValue(20)
p c1.getValue()
```

**result**

```
10
20
```


`set`method를 사용할 때 인스턴스 변수에 입력받은 값을 저장하기 전에 안전성을 확보해야
한다. 생성자의 경우도 이와 같이 안전성을 확보해야 한다. 아래 예제를 보자. python의 경우
`isinstance`를 통해 입력받은 값의 인스턴스를 검사할 수 있다. 첫번째 인자가 두번째 인자의
인스턴스이면 `True`를 아니면 `False`를 `return`한다. ruby의 경우 `.is_a?`를 통해 검사를
할 수 있다.

**python**

```python
# your code goes here
class Cal(object):
	def __init__(self,v1,v2):
		if isinstance(v1, int):
			self.v1 = v1
		if isinstance(v2, int):
			self.v2 = v2
	def add(self):
		return self.v1+self.v2
	def subtract(self):
		return self.v1-self.v2
	def setV1(self,v):
		if isinstance(v, int):
			self.v1 = v
	def getV1(self):
		return self.v1

c1 = Cal(10,10)
print(c1.add())
print(c1.subtract())

# wrong insert
c1.setV1('devil')
c1.v2 = 30
print(c1.add())
print(c1.subtract())
```

**result**

```
20
0
40
-20
```

**ruby**

```ruby
# your code goes here
class Cal
	def initialize(v1, v2)
		if v1.is_a?(Integer)
			@v1 = v1
		end
		if v2.is_a?(Integer)
			@v2 = v2
		end
	end
	def add()
		return @v1 + @v2
	end
	def subtract()
		return @v1 - @v2
	end
	def setV1(v)
		if v.is_a?(Integer)
			@v1 = v
		end
	end
	def getV1()
		return @v1
	end
end

c1 = Cal.new(10,10)
p c1.add()
p c1.subtract()

# wrong insert
c1.setV1('devil')
p c1.add()
p c1.subtract()
```

**result**

```
20
0
20
0
```


이제 인스턴스 변수의 속성에 대해 보겠다. python은 인스턴스 변수를 외부에서 접근할 수
있으니 접근을 제한하는 법을 보겠고 ruby는 인스턴스 변수를 외부에서 접근할 수 없으니
접근을 허용하는 법을 보겠다. python은 인스턴스 변수 앞에 `__`를 붙이면 외부에서 접근이
제한된다. ruby는 여러 속성이 있다. `attr_reader`는 외부에서 읽기허용, `attr_writer`는
외부에서 쓰기허용, `attr_accessor`는 외부에서 읽기/쓰기 모두 허용된다.

**python**

```python
# your code goes here
class C(object):
	def __init__(self, v):
		self.__value = v
	def show(self):
		print(self.__value)

c1 = C(10)
# print(c1.__value) expect error! AttributeError: 'C' object has no attribute '__value'
c1.show()
```

**result**

```
10
```

**ruby**

```ruby
# your code goes here
class C
  attr_reader :value
  attr_writer :value
  attr_accessor :value2
  def initialize(v1, v2)
    @value = v1
    @value2 = v2
  end
end

c1 = C.new(10,30)

# change v1
p c1.value
c1.value = 20
p c1.value

# change v2
p c1.value2
c1.value2 = 40
p c1.value2
```

**result**

```
10
20
30
40
```
