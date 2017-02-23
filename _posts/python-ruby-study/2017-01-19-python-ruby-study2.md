---
layout: post
title: "Python Ruby Study _ 반복문"
date: 2017-01-19
categories: [python, ruby]
---

python과 ruby의 반복문 중 while문에 대해 보겠다. while문 사용시 반복조건 다음에
python은 `:`를 써야되고 ruby는 `do-end`를 써서 실행영역을 구분한다.  

**python**

```python
# your code goes here
while False:
	print("false parts")
print("After while")
```

**ruby**
{% highlight ruby %}
# your code goes here
while false do
	puts("false parts")
end
puts("After while")
{% endhighlight %}

**result**

```
After while
```


while문의 반복조건을 변화시킨 예제이다. python은 `str`을 이용해서 정수의 값을 문자열로
바꿔준다. ruby는 `.to_s()`를 이용해서 정수의 값을 문자열로 바꿔준다.  

**python**

```python
# your code goes here
i = 0
while i < 10:
	print(" 9 x " + str(i) + " = " + str(i*9))
	i += 1
```

**ruby**

{% highlight ruby%}
# your code goes here
i = 0
while i < 10 do
	puts(" 9 x " + (i).to_s() + " = " + (i*9).to_s())
	i += 1
end
{% endhighlight %}

**result**

```
 9 x 0 = 0
 9 x 1 = 9
 9 x 2 = 18
 9 x 3 = 27
 9 x 4 = 36
 9 x 5 = 45
 9 x 6 = 54
 9 x 7 = 63
 9 x 8 = 72
 9 x 9 = 81
```


while문과 조건문을 같이 활용한 예제이다. python과 ruby둘다 `break`를 통해 반복문을
빠져나갈 수 있다!  

**python**

```python
# your code goes here
i = 0
while i < 10:
    if i == 4:
        break
    print(i)
    i = i + 1
print('after while')
```

**ruby**

{% highlight ruby %}
# your code goes here
i = 0
while i < 10 do
    if i == 4
        break
    end
    puts(i)
    i = i + 1
end
print('after while')
{% endhighlight %}

**result**

```
0
1
2
3
after while
```
