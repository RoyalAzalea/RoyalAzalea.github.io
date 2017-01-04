---
layout: post
title: "Python Ruby Study _ 문자열"
date: 2017-01-04
categories: [python, ruby]
---

python과 ruby에서 다루는 문자열 관련된 기능들은 매우 비슷하다.
python에서 ""과 ''는 거의 같은 의미이다. ruby에서는 살짝 다르다.
간단하게 문자열을 출력해보겠다.

**python**

```python
# your code goes here
print('Hello')
print("Hello")
print("Hello 'world'")
print('Hello "world"')
```

**ruby**

{% highlight ruby %}
# your code goes here
puts('Hello')
puts("Hello")
puts("Hello 'world'")
puts('Hello "world"')
{% endhighlight %}

**result**
```
Hello
Hello
Hello 'world'
Hello "world"
```

다음은 문자열에 +와 \*를 사용한다. +는 두 문자열을 합쳐주고 \*는 문자열을 반복시켜준다.
그리고 python과 ruby에서 -와 /를 문자열에 사용할 수 없다.

**python**

```python
# your code goes here
print("Hello" + 'world')
print('Hello' + "wordl")
print("Hello" + "world")
print('Hello' + 'world')

print("Hello"*10)

print("Hello"[0])
print('asome'[2])
#print('kong'[4]) expect error!
```

**result**

```
Helloworld
Hellowordl
Helloworld
Helloworld
HelloHelloHelloHelloHelloHelloHelloHelloHelloHello
H
o
```

**ruby**

{% highlight ruby %}
# your code goes here
puts("Hello" + "world")
puts('Hello' + 'world')

puts("H H H" * 4)

puts("Hell"[1])
puts("Hool"[1])
puts('Hawi'[1])
{% endhighlight %}

**result**

```
Helloworld
Helloworld
H H HH H HH H HH H H
e
o
a
```
