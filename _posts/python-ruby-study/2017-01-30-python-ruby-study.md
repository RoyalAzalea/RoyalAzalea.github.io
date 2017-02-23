---
layout: post
title: "Python Ruby Study _ 객체"
date: 2017-01-30
categories: [python, ruby]
---

객체(`Object`)는 `Class + Instance`이다. **`Class`는 객체와 관련된 변수나 기능등을 담는
일종의 모듈 또는 수납공간으로 생각하면 된다. `Instance`는 `Class`를 통해 객체를 실체화
시킨것으로 `Class`의 틀을 가지고 있지만, 안에 들어있는 내용은 조금씩 다르다.**  

쉽게 생각하면 붕어빵틀(`Class`)에서 붕어빵(`Instance`)를 찍어내는 것과 같다.
이때, 붕어빵의 속에 단팥이 들었거나 슈크림이 들은것처럼 각 `Instance`가 가지고 있는
Data는 다를 수 있다.

**ruby**

{% highlight ruby %}
# your code goes here
# String Class
name1 = 'RoyalAzalea'
name2 = String.new('Seoul')
puts(name1.reverse())
puts(name2.reverse())

# Array Class
arr1 = Array.new()
arr2 = Array.new
arr3 = [1,2,3]
arr1.push('Royal')
arr2.push('Royal')
arr1.push('Azalea')
arr2.push('Azalea')
puts(arr1.join('-'))
puts(arr2.join('--'))
puts arr3.join '\*'
{% endhighlight %}


위 예제는 ruby를 통해 `String_Class`와 `Array_Class`에 대해 간략히 알아본다. 객체를 생성
하는 방법이 조금씩 다를 뿐 `name1`과 `name2`는 둘 다 문자열 객체를 생성한다.

마찬가지로 `arr1, arr2, arr3`도 배열 객체를 생성한다.
배열 객체는 `push`를 통해 원소를 추가할 수 있고 `join`을 통해 `join`다음에 나오는
문자열로 원소들을 연결한다. ruby에서 제공하는 객체에 대한 내용은 문서를 참고하도록 하자!
