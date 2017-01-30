---
layout: post
title: "Python Ruby Study _ ruby에서 함수의 특징"
date: 2017-01-21
categories: [ruby]
---

ruby에서만 해당되는 함수의 특징들을 보겠다. ruby에서 함수를 정의할때 `()`를 생략할 수
있다. 또 함수를 사용할때 `()`를 생략할 수 있다.

**ruby**

{% highlight ruby %}
# normal
def f1()
	return 'f1'
end

# erase ()
def f2
	return 'f2'
end

# erase ()
puts(f1())
puts(f2)
{% endhighlight %}

**result**

```
f1
f2
```


입력값이 있는 함수를 정의할때도 `()`를 생략할 수 있고, 사용할때 `()`에 입력값을 담지않고
한칸 띄우고 입력값을 써도 된다.

**ruby**

{% highlight ruby %}
#normal
def f3(a1)
	return a1
end

def f4 a1
	return a1
end

# erase
puts(f3 'f3')
puts f4 'f4'
{% endhighlight %}

**result**

```
f3
f4
```


ruby에서 함수를 정의할 때 마지막 값이 자동으로 `return`으로 인식된다. 따로 `return` 선언을
안해도 되지만 헷갈릴 수 있으니 `return` 선언은 꼭 해주도록 하자!

**ruby**

{% highlight ruby %}
# retrun check
def f5
	a = 1
	b = 2
	a + b
end
puts f5
{% endhighlight %}

**result**

```
3
```


ruby의 코드블록 `{}`에 대해 보겠다. 이 부분은 문서를 많이 참고하는게 도움이 될 것이다.
우선 ruby에서 integer의 함수인 `times`는 `{}` 안에 코드가 반복된다. `upto`함수는 `()` 안 숫자가
될때까지 반복된다. 참고로 `times`에서 `||`를 사용하여 안에 변수를쓰면 자동으로 0으로 초기화됨!
그리고 변수에 정수(=integer)를 할당한 후 `times`함수를 사용해도 동일하게 동작한다.

**ruby**

{% highlight ruby %}
# your code goes here
5.times() {|i| puts i}
2.times() {puts '2times'}
3.upto(5) {|item| puts item}

# use variable
a = 4
a.times() {puts 'vv'}

# execute is ok, but can't show anything because 5 is alreay higher then 4
# 5.upto(4) {|item| puts item}
{% endhighlight %}

**result**

```
0
1
2
3
4 << 5.times() end
2times
2times << 2.times() end
3
4
5 << 3.upto(5) end
vv
vv
vv
vv
```


아래 예제를 보자. `A`와 `B`의 실행결과는 동일하다. `A`는 `B`보다 직관적이고 코드의 수도
적고 간결하다. `B`는 `A`보다 불필요한 작업을 하였지만 while문은 다른 것들과 함께 많이
쓰일 수 있어서 자유롭다. 이처럼 `A`와 `B`는 같은 일을 하지만 각각 장단점이 있다.
사용시 상황에따라 각각의 장단점을 생각하여 사용하자!!!

**ruby**

{% highlight ruby %}
# your code goes here
# A times사용
5.times() {|i| puts i}

# B while문
i = 0
while i < 5
	puts i
	i += 1
end
{% endhighlight %}

**result**

```
0
1
2
3
4 << A end
0
1
2
3
4 << B end
```


코드블록과 배열을 같이쓰는 예제이다. 배열의 `each`는 원소를 하나씩 반복적으로 꺼낸다.
for문을 사용한 효과가 있다. `delete_if()`를 사용하면 편리하게 배열의 원소를 제거할 수
있다. `arr2`에서 `delete_if()`를 통해 `item > 7` 이 `True`가 되면 그 때의 원소를 제거한다.
그리고 코드블록대신 `do-end`를 써도 똑같이 동작한다. 참고로 코드블록에 들어갈 내용이
한줄인 경우에는 `{}`를 사용하고 여러줄이면 보통 `do-end`를 사용함!

**ruby**

{% highlight ruby %}
# your code goes here
arr = ['A','B','C']
# downcase는 알파벳을 소문자화 해줌
arr.each() {|item| puts item.downcase()}

for value in arr
	puts value
end

# use delete_if()
arr2 = [1,3,56,7,13,52]
arr2.delete_if() do |item|
	item > 7
end
puts arr2

arr3 = [1,3,56,7,13,52]
arr3.delete_if() { |item|
	item < 7
}
puts arr3
{% endhighlight %}

**result**

```
a
b
c << each end
A
B
C << for end
1
3
7 << puts arr2 end
56
7
13
52 << puts arr3 end
```
