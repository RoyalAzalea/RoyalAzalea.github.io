---
layout: post
title: "Python Ruby Study _ 입력과 출력"
date: 2017-01-12
categories: [python, ruby]
---

python의 경우 사용자의 입력을 받기위해 `input`을 사용하고 ruby의 경우는 `gets.chomp()`를
사용한다. python은 경우는 `input`을 쓰면서 사용자에게 출력해줄 문자열도 입력이 가능하다.
ruby는 따로 `puts`를 이용해서 출력을 해줘야 함. 이제 간략한 예제를 보겠다. 참고로 문자의
대문자화를 위해 python은 `upper`를 ruby는 `upcase`를 사용한다!

**python**

```python
# your code goes here
in_str = input("plz write your name: \n")
print("your name : "+in_str)
print("your name (upper) : "+in_str.upper())
```

**ruby**

{% highlight ruby %}
# your code goes here
puts("plz write your name: ")
in_str = gets.chomp()
puts("your name : " + in_str)
puts("your name (upper) : " + in_str.upcase())
{% endhighlight %}

**input**

```
RoyalAzalea
```

**result**

```
plz write your name:
your name : RoyalAzalea
your name (upper) : ROYALAZALEA
```


이제 사용자의 입력을 받아 그에 해당하는 출력문을 띄워주는 예제를 보겠다. 아직 반복문을
적용하지 않았으니 매번 실행 해야되는 불편함이 있다. 참고로 input처럼 키워드의 경우는
변수명으로 사용하지 않는게 좋다. 나중에 헷갈리고 코드가 꼬일 수 있음!

**python**

```python
# your code goes here
in_str = input("plz write number ( 1 or 2 or some number ) : \n")
hint1 = "1"
hint2 = "2"
cake = "4"
if hint1 == in_str:
	print("wow! you are great, but you can't get it! try try again!")
elif hint2 == in_str:
	print("almost... you reach this goal... try try hard!!")
elif cake == in_str:
	print("congratulation!!! you got it!! enjoy your victory! eat cake :D")
else:
	print("sorry, you need to more try")
```

**ruby**

{% highlight ruby %}
# your code goes here
puts("plz write number ( 1 or 2 or some number ) : ")
in_str = gets.chomp()
hint1 = "1"
hint2 = "2"
cake = "4"
if hint1 == in_str
	puts("wow! you are great, but you can't get it! try try again!")
elsif hint2 == in_str
	puts("almost... you reach this goal... try try hard!!")
elsif cake == in_str
	puts("congratulation!!! you got it!! enjoy your victory! eat cake :D")
else
	puts("sorry, you need to more try")
end
{% endhighlight %}

**input**

```
1
```

**result**

```
plz write number ( 1 or 2 or some number ) :
wow! you are great, but you can't get it! try try again!
```

**input**

```
2
```

**result**

```
plz write number ( 1 or 2 or some number ) :
almost... you reach this goal... try try hard!!
```

**input**

```
4
```

**result**

```
plz write number ( 1 or 2 or some number ) :
congratulation!!! you got it!! enjoy your victory! eat cake :D
```

**input**

```
1,2,4를 제외한 다른 숫자 혹은 비정상정인 입력(ex. 문자열)
```

**result**

```
plz write number ( 1 or 2 or some number ) :
sorry, you need to more try
```
