---
layout: post
title: "Python Ruby Study _ 문자열"
date: 2017-01-04
categories: [python, ruby]
---

python과 ruby에서 다루는 문자열 관련된 기능들은 매우 비슷하다.
python에서 " "과 ' '는 거의 같은 의미이다. ruby에서는 살짝 다르다.
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
print('Hello' + "world")
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
Helloworld
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

python과 ruby에 문자열 관련된 여러기능이 있다. 둘은 비슷하기도 하지만 다른점도 많으니
각 문서를 참고하는게 좋다.

**python**

```python
# your code goes here
print('hello world'.capitalize())
print('asome test'.upper())
print("hello world".__len__())
print(len("hello world"))
print("Hello world".replace('world', 'hell'))
```

**result**

```
Hello world
ASOME TEST
11
11
Hello hell

```

**ruby**

{% highlight ruby %}
# your code goes here
puts('hello world'.capitalize())
puts('hello world'.upcase())
puts('hello world'.length())
puts('hello world'.sub('world', 'hell'))
{% endhighlight %}

**result**

```
Hello world
HELLO WORLD
11
hello hell
```

이제 python과 ruby의 특수문자를 사용하는 법을 보겠다. 특수문자를 사용할때는
역슬레쉬 \\ 를 이용해서 사용하면된다.

**python**

```python
# your code goes here
print("egoing's \"tutorial\"")  # "출력
print("\\")                     # \출력
print("Hello\nworld")           #  개행
print("Hello\t\tworld")         # 탭문자 2번 삽입
print("\a")                     # 알람소리
print('hello\nworld')           # ""와 동일
```

**result**

```
egoing's "tutorial"
\
Hello
world
Hello		world

hello
world

```

**ruby**

{% highlight ruby %}
# your code goes here
puts("egoing's \"tutorial\"")
puts("\\")
puts("Hello\nworld")
puts("Hello\t\tworld")
puts("\a")
# ruby에서 ''는 문자열을 그대로 출력한다.
puts('Hello\nworld')
{% endhighlight %}

**result**

```
egoing's "tutorial"
\
Hello
world
Hello		world

Hello\nworld
```
