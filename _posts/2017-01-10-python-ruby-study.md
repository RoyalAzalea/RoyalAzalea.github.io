---
layout: post
title: "Python Ruby Study _ 변수, 비교와 블리언"
date: 2017-01-10
categories: [python, ruby]
---

python과 ruby의 변수에 대해 보면 일반적인 다른 언어와 동일하다. 다만 세미콜론 ; 을
쓰지 않는다.

**python**

```python
# your code goes here
x = 10
y = 5
print(x+y)

title = "Python & Ruby"
print("study_"+title)
```

**ruby**

{% highlight ruby %}
# your code goes here
x = 10
y = 5
puts(x+y)

title = "Python & Ruby"
puts("study_"+title)
{% endhighlight %}

**result**

```
15
study_Python & Ruby
```


python과 ruby의 비교문법은 다른 언어와 동일하다. 비교문법에 대한 문서를 찾아보면 된다.
python의 경우는 블리안에 True, False가 쓰이고 ruby의 경우는 true, false가 쓰인다.
대소문자를 주의해서 써야함 대충 쓰다간 stderr 볼 수 있음!

**python**

```python
# your code goes here
a = 1
b = 1
print(a==b)
print(1==2)
print(1>2)
print(1<2)
print(True)
print(False)
# print(true) stderr name 'true' is not define
# print(flase) same error
```

**result**

```
True
False
False
True
True
False
```

**ruby**

{% highlight ruby %}
# your code goes here
a=1
b=1
puts(a==b)
puts(1==2)
puts(1>2)
puts(1<2)
puts(true)
puts(false)
# puts(True) stderr uninitialized constant True (NameError)
# puts(False) same error
{% endhighlight %}

**result**

```
true
false
false
true
true
false
```
