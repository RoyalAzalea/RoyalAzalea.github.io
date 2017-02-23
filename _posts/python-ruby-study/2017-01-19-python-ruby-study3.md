---
layout: post
title: "Python Ruby Study _ 반복문과 컨테이너"
date: 2017-01-19
categories: [python, ruby]
---

반복문과 컨테이너를 같이 사용하면 시너지 효과를 낼 수 있다. 중복되는 부분을 줄여서
코드길이도 줄어들고 깔끔해진다. 또 코드수정과 유지보수 등에 편리한 점이 많다.  

다음은 while문과 컨테이너를 사용한 예제이다. 컨테이너의 길이를 활용한다!

**python**

```python
# your code goes here
members = ['Royal','Azalea','Seoul']
i = 0

while i < len(members):
	print(members[i])
	i += 1
```

**ruby**

{% highlight ruby %}
# your code goes here
members = ['Royal','Azalea','Seoul']
i = 0

while i < members.length do
	puts(members[i])
	i += 1
end
{% endhighlight %}

**result**

```
Royal
Azalea
Seoul
```


이제 반복문 중 for문에 대해 보겠다. for문은 컨테이너와 찰떡궁합이다. while문 보다
편하게 사용할 수 있다.  

아래는 for문과 컨테이너를 사용한 예제이다. 위 while문에서는 `i`의 값을 증가시켜
주면서 반복조건을 확인했다. **하지만 for문은 자동으로 `value`가 `members`의
원소를 받고 더이상 원소가 없으면 끝난다.**
참고로 python은 `:`를 ruby는 `do-end`를 써줘야함!

**python**

```python
# your code goes here
members = ['Royal','Azalea','Seoul']

for value in members:
	print(value)
```

**ruby**

{% highlight ruby %}
# your code goes here
members = ['Royal','Azalea','Seoul']
for value in members do
	puts(value)
end
{% endhighlight %}

**result**

```
Royal
Azalea
Seoul
```


for문의 활용예제를 보겠다. python은 `in` 다음에 list를 사용할 수 있다. 그리고 `range`를
사용해서 원하는 반복횟수와 원하는 숫자를 출력할 수 있다.  

ruby도 `in` 다음에 array를 사용할 수 있다. 그리고 `..`을 사용해서 원하는 반복횟수와
원하는 숫자를 출력할 수 있다. 참고로 `range`과 `..`사용시 범위가 약간 다르니 주의할 것!!

**python**

```python
# your code goes here
for item in [0,1,2,3]:
	print("Hello")

# use range
for item in range(5):
	print("King")

# use range
for item in range(5, 11):
    print(item)
```

**result**

```
Hello
Hello
Hello
Hello
King
King
King
King
King << 총 5번
5
6
7
8
9
10 << python에서 range(5, 11)은 5부터 10까지 출력됨
```

**ruby**

{% highlight ruby %}
# your code goes here
for item in [0,1,2,3] do
	puts("Hello")
end

# use ()
for item in (0..5) do
	puts("King")
end

# use ()
for item in (5..11) do
    puts(item)
end    
{% endhighlight %}

**result**

```
Hello
Hello
Hello
Hello
King
King
King
King
King
King << 총 6번
5
6
7
8
9
10
11 << ruby에서 (5..11)은 5부터 11까지 출력됨
```


이제 컨테이너와 반복문을 활용한 예제를 보겠다. python은 `import sys`와 `sys.exit()`을
통해 실행을 종료한다. ruby는 `exit`을 통해 실행을 종료한다.  

참고로 python과 ruby 둘 다 반복문과 컨테이너에 해당하는 내용이 많으니 문서를
꼭 참고해서 더 공부를 해야한다!

**python**

```python
# your code goes here
input_id = input("write your name. \n")
members = ['Royal','Azalea','King']
for member in members:
	if member == input_id:
		print("Hello!, "+member)
		import sys
		sys.exit()
print("Sorry, you are not member")
```

**ruby**

{% highlight ruby %}
# your code goes here
puts("write your name.")
input_id = gets.chomp()
members = ['Royal','Azalea','King']
for member in members do
	if member == input_id
		puts("Hello!, "+member)
		exit
	end
end
puts("Sorry, you are not member")
{% endhighlight %}

**input**

```
Royal
```

**result**

```
write your name.
Hello!, Royal
```

**input**

```
KingKong
```

**result**

```
write your name.
Sorry, you are not member
```
