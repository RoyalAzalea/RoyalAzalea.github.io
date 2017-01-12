---
layout: post
title: "Python Ruby Study _ 논리 연산"
date: 2017-01-12
categories: [python, ruby]
---

python과 ruby의 논리 연산은 거의 비슷하나 조금 다르다. 우선 둘다 OR연산시 or을 AND연산시
and를 그리고 Not연산시 not을 사용한다. python의 기본 예제를 보겠다.

**python**

```python
# your code goes here
print(True or True)
print(True or False)
print(False or True)
print(False or False)

print(True and True)
print(True and False)
print(False and True)
print(False and False)

print(not True)
print(not False)
```

**result**

```
True
True
True
False
True
False
False
False
False
True
```


ruby의 기본예제를 보기 전 ruby문법에서 몇 가지 알아둘게 있다. 우선 puts안에 바로
true or true 를 사용하면 syntax error가 발생한다. 반드시 괄호를 사용하여 논리 연산을
먼저 처리해 줘야한다. 그리고 추가적으로 변수에 논리 연산의 결과를 대입할 때도 반드시
괄호를 사용해야한다. 아래 예제를 보자.

**ruby**

{% highlight ruby %}
# your code goes here
var = true and false
puts(var)

var = (true and false)
puts(var)

if true and false
	puts("true!")
else
	puts("false!")
end
{% endhighlight %}

**result**

```
true
false
false!
```


결과를 보면 true와 false의 and연산을 하면 false가 나와야하는데 첫 줄에 true가 나왔다.
논리 연산의 결과를 변수에 저장할 때 괄호를 사용하지 않으면 ruby에서는 변수 var에
논리 연산의 결과가 아닌 true를 저장한다. 그 후 변수 var와 and연산을 진행하게되
그 줄에서는 false값을 유지한다. 허나 puts로 var를 출력해보면 앞서 저장된 true가
출력된다. if문 처럼 한줄에 true and false를 쓰면 그 줄에서는 false가 유지되서 정상적인
동작을 하지만 변수에 저장할 땐 괄호를 사용하여 주의해야한다!


**ruby**

{% highlight ruby %}
# your code goes here
# puts(true or true) stderr syntax error, unexpected keyword_or, expecting ')'!
puts((true or true))
puts((true or false))
puts((false or true))
puts((false or false))

var = (true and true)
puts(var)
var = (true and false)
puts(var)
var = (false and true)
puts(var)
var = (false and false)
puts(var)

puts((not true))
puts((not false))
{% endhighlight %}

**result**

```
true
true
true
false
true
false
false
false
false
true
```


python에는 or, and, not으로 논리 연산을 모두 처리한다. 하지만 ruby의 경우에는 다른 언어들의 논리 연산자인 &&, ||, !를 추가적으로 사용한다. 그리고 &&, ||, !의 연산순위가 or, and,
not보다 높다. 앞에서 ruby의 경우 논리 연산의 결과를 변수에 저장할 때는 괄호를 사용해야
된다고 했다. &&, ||, !의 경우는 괄호를 사용하지 않아도 된다. 하지만 코드의 가독성 및
통일을 위해 괄호를 사용하는 것이 좋다. 아래는 연산순위가 어떻게 영향을 주는지 보여준다.

**ruby**

{% highlight ruby %}
# your code goes here
var = true && false
puts(var)

# !true가 쓰인 후 false가 된 후 &&연산을 진행함
var = (!true && false)
puts(var)

# &&가 연산순위가 더 높아 true && false가 진행된 후 not연산이 진행됨
var = (not true && false)
puts(var)
{% endhighlight %}

**result**

```
false
false
true
```


이제는 논리연산을 if문과 함께 사용하는 예제를 보겠다. 논리연산을 사용하면 if를 중첩
해서 사용하지 않아도 되서 코드가 깔끔해진다!

**python**

```python
# your code goes here
in_str = input("plz write your name : ")
print("\n")
passenger1 = "RoyalAzalea"
passenger2 = "Mike"
if in_str == passenger1 or in_str == passenger2:
	print("Welcome!")
else:
	print("Sorry, we can't find your reservation")
```

**ruby**

{% highlight ruby %}
# your code goes here
puts("plz write your name : ")
in_str = gets.chomp()
passenger1 = "RoyalAzalea"
passenger2 = "Mike"
if in_str == passenger1 or in_str == passenger2
	puts("Welcome!")
else
	puts("Sorry, we can't find your reservation")
end
{% endhighlight %}

**input**

```
RoyalAzalea
```

**result**

```
plz write your name :

Welcome!
```

**input**

```
John
```

**result**

```
plz write your name :
Sorry, we can't find your reservation
```

**python**

```python
# your code goes here
in_str = input("plz write your name : ")
print("\n")
in_age = input("plz write your age : ")
print("\n")
passenger1 = "RoyalAzalea"
passenger2 = "Mike"
age_passenger1 = "25"
age_passenger2 = "29"
if in_str == passenger1 and in_age == age_passenger1:
	print("Welcome!")
elif in_str == passenger2 and in_age == age_passenger2:
	print("Welcom!")
else:
	print("Sorry, we can't find your reservation")
```

**ruby**

{% highlight ruby %}
# your code goes here
puts("plz write your name : ")
in_str = gets.chomp()
puts("plz write your age : ")
in_age = gets.chomp()
passenger1 = "RoyalAzalea"
passenger2 = "Mike"
age_passenger1 = "25"
age_passenger2 = "29"
if in_str == passenger1 and in_age == age_passenger1
	puts("Welcome!")
elsif in_str == passenger2 and in_age == age_passenger2
	puts("Welcome!")
else
	puts("Sorry, we can't find your reservation")
end
{% endhighlight %}

**input**

```
RoyalAzalea
25
```

**result**

```
plz write your name :

plz write your age :

Welcome!
```

**input**

```
Mike
27
```

**result**

```
plz write your name :
plz write your age :
Sorry, we can't find your reservation
```
