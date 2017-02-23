---
layout: post
title: "Python Ruby Study _ 재정의"
date: 2017-02-07
categories: [python, ruby]
---

재정의(`Override`)는 상속시 자식클래스에서 부모클래스로부터 상속받은 기능이 수정이
필요할때 사용한다. **재정의를 하는 방법은 부모클래스의 수정할 method 이름을 그대로
사용하여 자식클래스에서 method를 정의하면 된다. 재정의를 하면 부모클래스의 method가
자식클래스의 method에 의해 가려지게 된다.**  
아래 기본 예제를 보자. `o.m()`이 실행되면 `C1_class`의 method인 `m`이 아닌 `C2_class`의
method인 `m`이 실행된다. 만약 부모클래스의 method를 사용하고 싶으면 `super` 키워드를
사용하면된다.  

참고로 python과 ruby에서 `super` 키워드의 의미가 다르니 조심할 것!!! python에서 `super`는 부모클래스를 의미한다. 그러므로 `super().m()` 이런식으로 부모클래스의 method를 직접 호출 해야한다. 하지만 ruby에서 `super`는 `super`가 쓰인 현재 method와 동일한 이름을 가진 부모클래스의
method를 의미한다. 그러므로 `super()` 이런식으로만 써도 부모클래스의 method `m`이 실행된다.

**python**

```python
# your code goes here

class C1:
	def m(self):
		return 'parent'

class C2(C1):
	def m(self):
		return super().m() + ' child'
	pass

o = C2()
print(o.m())
```

**result**

```
parent child
```

**ruby**

```ruby
# your code goes here
class C1
	def m()
		return 'parent'
	end
end

class C2 < C1
	def m()
		return super()+' child'
	end
end

o = C2.new()
p o.m()
```

**result**

```
"parent child"
```

아래 예제는 이전에 만든 `Cal_class`를 이용해 재정의를 활용한 예제이다. `CalMultiply_Class`는 `Cal_class`의 `info` method를 재정의하고, `CalDivide_Class`는
`CalMultiply_Class`의 `info` method를 재정의한다.  

참고로 ruby에서 `#{super()}`를 사용하여 부모클래스의 `info` method의 `return` 값을
받아 바로 문자열안에 넣을 수 있다!

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
    def info(self):
    	return "Cal => v1 : %d, v2 : %d" % (self.v1, self.v2)

class CalMultiply(Cal):
    def multiply(self):
        result = self.v1*self.v2
        Cal._history.append("multiply : %d*%d=%d" % (self.v1, self.v2, result))
        return result
    def info(self):
        return "CalMultiply => %s" % super().info()
class CalDivide(CalMultiply):
    def divide(self):
        result = self.v1/self.v2
        Cal._history.append("divide : %d/%d=%d" % (self.v1, self.v2, result))
        return result
    def info(self):
        return "CalDivide => %s" % super().info()  	

c0 = Cal(30, 60)
print(c0.info())
c1 = CalMultiply(10, 10)
print(c1.info())
c2 = CalDivide(20, 10)
print(c2.info())
```

**result**

```
Cal => v1 : 30, v2 : 60
CalMultiply => Cal => v1 : 10, v2 : 10
CalDivide => CalMultiply => Cal => v1 : 20, v2 : 10
```

**ruby**

```ruby
# your code goes here
class Cal
  #attr_reader :v1, :v2
  #attr_writer :v1
  @@_history = []
  def initialize(v1,v2)
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
    @@_history.push("subtract : #{@v1}-#{@v2}=#{result}")
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
  def info()
    return "Cal => v1 : #{@v1}, v2 : #{@v2}"
  end
end
class CalMultiply < Cal
  def multiply()
    result = @v1*@v2
    @@_history.push("multipy : #{@v1}*#{@v2}=#{result}")
    return result
  end
  def info()
    return "CalMultiply => #{super()}"
  end
end
class CalDivide < CalMultiply
  def divide()
    result = @v1/@v2
    @@_history.push("divide : #{@v1}/#{@v2}=#{result}")
    return result
  end
  def info()
    return "CalDivide => #{super()}"
  end
end

c0 = Cal.new(30, 60)
p c0.info
c1 = CalMultiply.new(10, 10)
p c1.info
c2 = CalDivide.new(20, 10)
p c2.info
```

**result**

```
"Cal => v1 : 30, v2 : 60"
"CalMultiply => Cal => v1 : 10, v2 : 10"
"CalDivide => CalMultiply => Cal => v1 : 20, v2 : 10"
```
