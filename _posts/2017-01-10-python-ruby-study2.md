---
layout: post
title: "Python Ruby Study _ 조건문"
date: 2017-01-10
categories: [python, ruby]
---

python과 ruby의 조건문에 대해 보겠다. 다른 언어들과 크게 다르지 않지만 python의 경우는
`:` 를 꼭붙여야 함! 그리고 python은 `if` 이후 들여쓰기를 기준으로 한 그룹으로 인식하고
ruby는 `if` 이후 `end` 까지를 한 그룹으로 인식한다. 기본적인 사용법을 보겠다.

**python**

```python
# your code goes here
if True:
	print("code1")
	print("code2")
print("code3")
```

**ruby**

{% highlight ruby %}
# your code goes here
if true
	puts("code1")
	puts("code2")
end

puts("code3")
{% endhighlight %}

**result**

```
code1
code2
code3
```


변수와 else문을 사용하는 예제이다. python은 `else`에도 `:` 를 붙여야 한다. else if문의
경우 python은 `elif`를 쓰고 ruby는 `elsif`를 쓴다. else if 문을 사용하면 상황에 따라
else문 밑에 if문을 쓰는 조건문 중복을 제거할 수 있다.

**python**

```python
# your code goes here
input = 11
real = 11
if real == input:
    print("Hello!")
else:
    print("Who are you?")

if real == input - 1:
	print("Hello!")
else:
	print("Who are you?")
```

**ruby**

{% highlight ruby %}
# your code goes here
input = 11
real = 11
if real == input
  puts("Hello!")
else
  puts("Who are you?")
end

if real == input-1
  puts("Hello!")
else
  puts("Who are you?")
end
{% endhighlight%}

**result**

```
Hello!
Who are you?
```

**python**

```python
# your code goes here
input = 33
real_egoing = 11
real_k8805 = "ab"

if real_egoing == input:
  print("Hello!, egoing")
elif real_k8805 == input:
  print("Hello!, k8805")
else:
  print("Who are you?")
```

**ruby**

{% highlight ruby %}
# your code goes here
input = 33
real_egoing = 11
real_k8805 = "ab"
if real_egoing == input
  puts("Hello!, egoing")
elsif real_k8805 == input
  puts("Hello!, k8805")
else
  puts("Who are you?")
end
{% endhighlight %}

**result**

```
Who are you?
```
