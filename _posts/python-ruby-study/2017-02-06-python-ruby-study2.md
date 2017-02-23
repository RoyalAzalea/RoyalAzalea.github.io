---
layout: post
title: "Python Ruby Study _ 클래스 멤버"
date: 2017-02-06
categories: [python, ruby]
---

클래스 멤버는 인스턴스에 소속되지 않고 클래스에 소속되는 멤버이다.  

아래 ruby예제를 보며 설명을 하겠다.
이 예제는 ruby에서 제공하는 `date`라는 클래스를 가져와서 사용한다. `Date`클래스를
이용하여 `yyyy-mm-dd`를 담을 수 있다. 이렇게 각각의 `yyyy-mm-dd`이 담긴 `d1`과 `d2`의
`year` method를 사용하면 각각의 년도가 출력된다. `year` method가 하는 일은 같지만
각각의 인스턴스의 Data에 따라 다른 값을 출력한다. 따라서 `year`는 인스턴스에 소속된거로
볼 수 있다.  

`today` method는 현재의 `yyyy-mm-dd`를 출력한다. `year` method와 달리 `today`
method는 인스턴스에 소속되기 애매하다. 이런 경우 클래스에 소속시킨다.  

**정리해보면 인스턴스의 내부적인 data와 연관성을 가지고 동작하는 경우는 인스턴스에
소속시키면 되고, 인스턴스에 소속되기 애매한 경우 클래스 멤버로 만들어 클래스에 소속시키면
된다.**  

**ruby**

```ruby
# your code goes here
require 'date'

d1 = Date.new(2000,1,1)
d2 = Date.new(2010,1,1)

p d1.year
p d2.year
p Date.today()
```

**result**

```
2000
2010
#<Date: 2017-02-06 ((2457791j,0s,0n),+0s,2299161j)>
```


클래스 멤버에는 클래스 method와 클래스 변수가 있다.  

아래는 클래스 method의 기본적인 사용 예제이다. ruby의 경우 클래스 method를 정의할 때
이름 앞에 `class_name.`을 붙여준다.
python의 경우 클래스 method 정의 위에 `@classmethod`를 적고 인자로 보통 `cls`를
적는다. **클래스 method의 첫번 째 인자는 이 method가 속하는 클래스를 가리키는 값을 받는다.**  

참고로 python의 경우 method 정의 위에 `@staticmethod`를 적으면 static method가 됨!!
그리고 클래스 method를 사용할 때는 `class_name.classmethod_name` 이렇게 호출해야 된다.

**python**

```python
# your code goes here
class Cs:
	@staticmethod
	def static_method():
		print("Static method")
	@classmethod
	def class_method(cls):
		print("Class method")
	def instance_method(self):
		print("Instance method")

i = Cs()
Cs.static_method()
Cs.class_method()
i.instance_method()
```

**result**

```
Static method
Class method
Instance method
```

**ruby**

```ruby
# your code goes here
class Cs
	def Cs.class_method()
		p "Class method"
	end
	def instance_method()
		p "instance method"
	end
end

i = Cs.new()
Cs.class_method()
i.instance_method()

# Cs.instance_method() expect error!
# i.class_method() expect error!
```

**result**

```
"Class method"
"instance method"
```


아래는 클래스 변수의 기본 사용 예제이다. ruby의 경우 변수 앞에 `@@`를 쓰면 클래스 변수가
된다. python의 경우 클래스 안쪽 그리고 method들의 바깥쪽에 변수를 선언하면 클래스 변수가
된다.  

참고로 클래스 변수의 경우 인스턴스 변수와 다르게 모든 인스턴스가 클래스 변수를 공유함!!!

**python**

```python
# your code goes here
class Cs:
	count = 0
	def __init__(self):
		Cs.count = Cs.count + 1
	@classmethod
	def getCount1(cls):
		print(cls)
		return Cs.count
	@classmethod
	def getCount2(cls):
		print(cls)
		return cls.count #same Cs.count because 'cls' receive 'Cs'
	def getC(self):
		return Cs.count

i1 = Cs()
i2 = Cs()
i3 = Cs()
i4 = Cs()

print(Cs.getCount1())
print(Cs.getCount2())
print(i2.getC())
print(i3.getC())
```

**result**

```
<class '__main__.Cs'>
4
<class '__main__.Cs'>
4
4
4
```

**ruby**

```ruby
# your code goes here
class Cs
	@@count = 0
	def initialize()		
		@@cc = 0
		@@count = @@count + 1
	end
	def Cs.getCount()
		@@cc = 1
		return @@count
	end
	def getC()
		p "#{@@count}, #{@@cc}"
	end
end

i1 = Cs.new()
i2 = Cs.new()
i3 = Cs.new()
i4 = Cs.new()

p Cs.getCount()
i2.getC
i3.getC
```

**result**

```
4
"4, 1"
"4, 1"
```


아래는 상속과 클래스 멤버를 활용한 예제이다. 이전에 만든 `Cal_Class`를 활용해
`CalMultiply_Class`와 `CalDivide_Class`를 만든다. 그리고 method들의 실행결과를 간략히
기록한다.  

참고로 클래스 인스턴스 뿐아니라 그 클래스를 상속받은 인스턴스들도 같이 클래스
멤버를 공유한다.  

그리고 문자열 사이에 변수의 값을 넣어서 출력하는 방법으로 python은
숫자의 경우 문자열 안에 `%d`를 사용하여 `"%d" % (variable_name) ` 이런 구조를
사용한다. ruby는 `#{variable_name}` 이런 구조를 사용한다.

**python**

```python
# your code goes here
class Cal(object):
    _history = []

    def __init__(self, v1, v2):
        if isinstance(v1, int):
            self.v1 = v1
        if isinstance(v2, int):
            self.v2 = v2
    def add(self):
        result = self.v1+self.v2
        Cal._history.append("add : %d+%d=%d" % (self.v1, self.v2, result))
        return result
    def subtract(self):
        result = self.v1-self.v2
        Cal._history.append("subtract : %d-%d=%d" % (self.v1, self.v2, result))
        return result
    def setV1(self, v):
        if isinstance(v, int):
            self.v1 = v
    def getV1(self):
        return self.v1
    @classmethod
    def history(cls):
        for item in Cal._history:
            print(item)
class CalMultiply(Cal):
    def multiply(self):
        result = self.v1*self.v2
        Cal._history.append("multiply : %d*%d=%d" % (self.v1, self.v2, result))
        return result
class CalDivide(CalMultiply):
    def divide(self):
        result = self.v1/self.v2
        Cal._history.append("divide : %d/%d=%d" % (self.v1, self.v2, result))
        return result

c1 = CalMultiply(10, 10)
print(c1, c1.add())
print(c1, c1.multiply())

c2 = CalDivide(20, 10)
print(c2, c2.add())
print(c2, c2.subtract())
print(c2, c2.multiply())
print(c2, c2.divide())

Cal.history()
```

**result**

```
<__main__.CalMultiply object at 0x55a84e6c> 20
<__main__.CalMultiply object at 0x55a84e6c> 100
<__main__.CalDivide object at 0x55a84ecc> 30
<__main__.CalDivide object at 0x55a84ecc> 10
<__main__.CalDivide object at 0x55a84ecc> 200
<__main__.CalDivide object at 0x55a84ecc> 2.0
add : 10+10=20
multiply : 10*10=100
add : 20+10=30
subtract : 20-10=10
multiply : 20*10=200
divide : 20/10=2
```

**ruby**

```ruby
# your code goes here
class Cal
	attr_reader :v1, :v2
	attr_writer :v1
	@@_history = []

	def initialize(v1, v2)
		@v1 = v1
		@v2 = v2
	end
	def add()
		result = @v1+@v2
		@@_history.push("add : #{@v1}+#{@v2}=#{result}")
		return result
	end
	def subtract()
		result = @v1-@v2
		@@_histroy.push("subtract: #{@v1}-#{@v2}=#{result}")
		return result
	end
	def setV1(v)
	    if v.is_a?(Integer)
	      @v1 = v
	    end
    end
	def getV1()
	  return @v1
	end
	def Cal.history()
		for item in @@_history
			p item
		end
	end
end

class CalMultiply < Cal
	def multiply()
		result = @v1*@v2
		@@_history.push("multiply : #{@v1}*#{@v2}=#{result}")
		return result
	end
end

class CalDivide < CalMultiply
	def divide()
		result = @v1/@v2
		@@_history.push("divide : #{@v1}/#{@v2}=#{result}")
		return result
	end
end

c1 = CalMultiply.new(10,10)
p c1, c1.add
p c1, c1.multiply

c2 = CalDivide.new(20,10)
p c2, c2.add
p c2, c2.divide
p c2, c2.multiply

Cal.history()
```

**result**

```
#<CalMultiply:0x9e09b48 @v1=10, @v2=10>
20
#<CalMultiply:0x9e09b48 @v1=10, @v2=10>
100
#<CalDivide:0x9e098f0 @v1=20, @v2=10>
30
#<CalDivide:0x9e098f0 @v1=20, @v2=10>
2
#<CalDivide:0x9e098f0 @v1=20, @v2=10>
200
"add : 10+10=20"
"multiply : 10*10=100"
"add : 20+10=30"
"divide : 20/10=2"
"multiply : 20*10=200"
```
