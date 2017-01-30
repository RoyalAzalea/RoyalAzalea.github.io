---
layout: post
title: "Python Ruby Study _ 컨테이너, 사용설명서"
date: 2017-01-19
categories: [python, ruby]
---

컨테이너는 무언가를 담는 그릇이다. 변수도 컨테이너의 일종이고 하나의 값을 담는 그릇이다.
여러개의 값을 담는 그릇도 있다. 그 중 python에서는 list, ruby에서는 array를 사용해보자.
python에서 list를 사용하는 예제이다. python에서 type의 괄호에 변수나 확인하고 싶은걸
넣고 print하면 데이터형이 무엇인지 확인할 수 있다. list를 `print`하면 담고있는 값들을
보여주고 array처럼 index를 이용해서 한개의 값에 접근가능함!

**python**

```python
# your code goes here
print(type('RoyalAzalea')) # show <class 'str'>
name = 'RoyalAzalea'
print(name) # show RoyalAzalea

print(type(['Royal','Azalea','25'])) # show <class 'list'>
info = ['Royal','Azalea','25']
print(info)

print(info[0])
print(info[1])
print(info[2])

print(type(['R','Y',222,True])) # show <class 'list'>
info2 = ['R','Y',222,True]
print(info2)
```

**result**

```
<class 'str'>
RoyalAzalea
<class 'list'>
['Royal', 'Azalea', '25']
Royal
Azalea
25
<class 'list'>
['R', 'Y', 222, True]
```


ruby에서 array를 사용하는 예제이다. ruby에서는 `.class`를 붙여 데이터형이 무엇인지 확인
할 수 있다. array에 `puts`를 사용하면 원소를 하나마다 출력하고 개행한다. `print`를 쓰면
쫘르륵 나열해주고 개행하지 않는다. ruby에서 개행하려면 `"\n"`를 사용하면 된다. `'\n'`는
문자열 그대로를 출력하므로 개행이 일어나지 않음!

**ruby**

{% highlight ruby %}
# your code goes here
info = ['Royal','Azalea','Seoul']
puts(info.class) # show Array
puts(info[0]) # show Royal
info[2] = 'Busan'
print(info) # show ['Royal','Azalea','Busan']

print("\n")
info[2] = true
print(info) # show ["Royal", "Azalea", true]
{% endhighlight %}

**result**

```
Array
Royal
["Royal", "Azalea", "Busan"]
["Royal", "Azalea", true]
```


python과 ruby 둘 다 각 홈페이지에서 사용설명서를 볼 수 있다. 사용설명서는 문서로 불리며
필요한 것들을 사용할 때 참고하자!

**python**

* language reference는 문법책 역할 (ex. 반복문)
* library reference는 사전 역할 (ex. print 사용법)

**ruby**

* core API는 기본적인 ruby 명령어와 사용법
* standard library API는 import해서 사용할 수 있는 정보


이제 문서를 사용해서 python의 list와 ruby의 array의 여러기술을 사용해보겠다.

**python**

```python
# your code goes here
list1 = ['A','B','C','D']
print(list1)

# check list's contents
print('A' in list1)
print('E' in list1)

# check list's length
print(len(list1))

# append contents to list
list1.append('E')

# delete list's contents
del(list1[0])
print(list1)
```

**result**

```
['A', 'B', 'C', 'D']
True
False
4
['B', 'C', 'D', 'E']
```

**ruby**

{% highlight ruby %}
# your code goes here
a1 = ['A','B','C','D']
print(a1)
print("\n")

# check array's length
puts(a1.length)

# append contents to array
a1.push('E')
print(a1)
print("\n")

# delete array's contents
a1.delete_at(0)
print(a1)
print("\n")

# check array's first contents and last contents
puts(a1.first)
puts(a1.last)
{% endhighlight %}

**result**

```
["A", "B", "C", "D"]
4
["A", "B", "C", "D", "E"]
["B", "C", "D", "E"]
B
E
```
