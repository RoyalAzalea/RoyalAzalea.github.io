---
layout: post
title: "Python Ruby Study _ 상속"
date: 2017-02-06
categories: [python, ruby]
---

python과 ruby의 상속(`inheritance`)에 대해 보겠다. 상속을 사용하면 여러 장점이 있다.
기존에 있던 객체의 기능을 그대로 가지면서 새로운 기능을 추가할 수 있고, 코드의 중복을
제거할 수 있고, 상속시 부모클래스의 기능이 바뀌면 하위클래스의 기능도 바뀌므로 유지보수가
쉽다. 그리고 상속을 통해 연관된 객체들을 조직화하여 관리할 수 있다. 아래는 상속의 기본
예제이다. python의 경우 기존 `object`자리에 상속받을 클래스 명을 쓴다. ruby의 경우 `<`
를 이용하여 어떤 클래스를 상속받는지 쓴다. 참고로 `c2.method1`이 실행되면 우선 `class2`
에서 `method1`을 찾는다. `method1`이 있으면 바로 실행을 시키지만 없는 경우 부모인 `class1`
에서 `method1`을 찾고 실행을 시켜준다.

**python**

```python
# your code goes here
class Class1(object):
	def method1(self):
		return 'm1'

c1 = Class1()
print(c1, c1.method1())

class Class2(Class1):
	def method2(self):
		return 'm2'

c2 = Class2()
print(c2, c2.method1())
print(c2, c2.method2())
```

**result**

```
<__main__.Class1 object at 0x55ae8d6c> m1
<__main__.Class2 object at 0x55ae8dac> m1
<__main__.Class2 object at 0x55ae8dac> m2
```

**ruby**

```ruby
# your code goes here
class Class1
	def method1()
		return 'm1'
	end
end

c1 = Class1.new()
p c1, c1.method1

class Class2 < Class1
	def method2()
		return 'm2'
	end
end

c2 = Class2.new()
p c2, c2.method1
p c2, c2.method2
```

**result**

```
#<Class1:0x93bd2ac>
"m1"
#<Class2:0x93bd194>
"m1"
#<Class2:0x93bd194>
"m2"
```


이전에 만든 `Cal_Class`를 상속받아 곱하기와 나누기 기능을 추가한 `CalPlus_Class`를
만드는 예제이다. 참고로 `CalPlus_Class`에는 생성자가 없어서 인스턴스 생성시 부모인
`Cal_Class`의 생성자를 사용함!!

**python**

```python
# your code goes here
class Cal(object):
    def __init__(self, v1, v2):
        if isinstance(v1, int):
            self.v1 = v1
        if isinstance(v2, int):
            self.v2 = v2
    def add(self):
        return self.v1+self.v2
    def subtract(self):
        return self.v1-self.v2
    def setV1(self, v):
        if isinstance(v, int):
            self.v1 = v
    def getV1(self):
        return self.v1

class CalPlus(Cal):
	def multiply(self):
		return self.v1 * self.v2
	def divide(self):
		return self.v1 / self.v2

calculator = CalPlus(10,10)
print(calculator.add())
print(calculator.multiply())
print(calculator.divide())
```

**result**

```
20
100
1.0
```

**ruby**

```ruby
# your code goes here
class Cal
  attr_reader :v1, :v2
  attr_writer :v1
  def initialize(v1,v2)
    @v1 = v1
    @v2 = v2
  end
  def add()
    return @v1+@v2
  end
  def subtract()
    return @v1-@v2
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

class CalPlus < Cal
	def multiply()
		return @v1 * @v2
	end
	def divide()
		return @v1 / @v2
	end
end

calculator = CalPlus.new(10,10)
p calculator.add
p calculator.multiply
p calculator.divide
```

**result**

```
20
100
1
```
