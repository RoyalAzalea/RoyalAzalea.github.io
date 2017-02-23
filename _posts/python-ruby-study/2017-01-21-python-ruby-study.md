---
layout: post
title: "Python Ruby Study _ 함수"
date: 2017-01-21
categories: [python, ruby]
---

함수를 사용하면 중복된 코드를 제거할 수 있고 코드의 재활용성과 가독성을 높힐 수 있다.  

아래는 python과 ruby에서 간단한 함수를 사용하는 예제이다. python의 경우에는 `def`를
쓴 이후 들여쓰는 부분이 함수의 영역이고 ruby에선 `def-end`로 함수의 영역을 알 수 있다.  

참고로 python은 `:`써야함! 그리고 함수의 `return`은 값을 반환하면서 함수의 실행을
종료시키므로 `return` 이후의 코드는 실행되지 않음!

**python**

```python
# your code goes here
def a3():
	print('aaa')
a3()

# use return
def a4():
	return 'aaaa'
print(a4())

# use input&return
def b(num):
	return 'b'*num
print(b(5))
```

**ruby**

{% highlight ruby %}
# your code goes here
def a3()
	puts('aaa')
end
a3()

# use return
def a3()
	return 'aaa'
end
puts(a3())

# use input&return
def b(num)
	return 'b' * num
end
puts(b(5))
{% endhighlight %}

**result**

```
aaa
aaa
bbbbb
```


함수에서 여러개의 입력값을 받는 예제이다.  

**python**

```python
# your code goes here
def mk_str(str, num):
	return str * num
print(mk_str('King',3))
```

**ruby**

{% highlight ruby %}
# your code goes here
def mk_str(str, num)
	return str * num
end
puts(mk_str('King',3))
{% endhighlight %}

**result**

```
KingKingKing
```


함수를 활용하여 간단한 로그인 시스템을 만든 예제이다.  

**python**

```python
# your code goes here
input_id = input("write your name \n")

def login(id):
	members = ['Royal','Azalea','Hong']
	for member in members:
		if member == id:
			return True
	return False

if login(input_id):
	print('Welcom, '+input_id)
else:
	print('Sorry, you are not member')
```

**ruby**

{% highlight ruby %}
# your code goes here
puts('write your name')
input_id = gets.chomp()

def login(id)
	members = ['Royal','Azaela','Hong']
	for member in members do
		if member == id
			return true
		end
	end
	return false
end

if login(input_id)
	puts('Welcom, '+input_id)
else
	puts('Sorry, you are not member')
end
{% endhighlight %}

**input**

```
Royal
```

**result**

```
write your name
Welcom, Royal
```

**input**

```
Yoma
```

**result**

```
write your name
Sorry, you are not member
```
