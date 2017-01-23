---
layout: post
title: "Python Ruby Study _ 모듈"
date: 2017-01-24
categories: [python, ruby]
---

모듈은 필요한 기능들을 모아둔 부품상자처럼 생각해도 된다. python과 ruby에서 수학관련
모듈을 사용하는 예제이다. python에서 math를 import한 뒤 그 안에있는 기능들을 사용했다.
참고로 python에서는 Library reference에 들어가면 모듈에 대한 내용을 볼 수 있음!

**python**

```python
# your code goes here
import math
print(math.ceil(2.9))
print(math.floor(2.9))
print(math.sqrt(16))
```

**result**

```
3
2
4.0
```

**ruby**

{% highlight ruby %}
# your code goes here
puts(Math.sqrt(16))
{% endhighlight %}

**result**

```
4.0
```


위 처럼 언어가 기본으로 제공하는 모듈이 있으면 사용하면 된다. 모듈이 없으면 코드내에서
함수들을 관리하기 힘들고 정리정돈이 안된다. 만약 프로젝트의 크기가 커진다면 참여한 사람들
사이에 함수이름이 중복되는 문제나 다른 여러문제가 생긴다. 이때 모듈을 사용하여 해결할 수
있다. 아래 예제는 많은 코드들 사이에서 함수의 정의를 덮어씌운 실수를 보여주는 예제이다.

**python**

```python
# your code goes here
def a():
	return 'a'

# many code
# many code
# many code

def a():
	return 'B'

print(a())
```

**result**

```
B
```


이제 python에서 모듈을 직접 만들어 사용하는 방법을 보겠다. 각기 다른 파일에 모듈들을
정의하고 사용할 모듈을 import하면 된다. one.py와 two.py를 만들고 main1.py에서 동시에
두 모듈을 import 하고있다. 이제 main에서 one.a()로 one.py의 a 함수를 실행 할 수 있다.

**one.py**

```python
def a():
    return 'a'

def b():
    return 'b'

def c():
    return 'c'

```

**two.py**

```python
def a():
    return 'B'
```

**main1.py**

```python
import one, two
print(one.a())
```

**result**

```
a
```


이번엔 모듈안에서 사용할 함수를 하나 가져오는 예제이다. main2.py에서 from을 이용해
one.py의 함수 a를 가져왔다. 이렇게 가져오면 따로 one.a()를 쓰지않고 바로 함수를 호출
할 수 있다.

**main2.py**

```python
from one import a
print(a())
```


main3.py를 보면 as를 사용했다. 이처럼 import할 때 as를 사용해서 가져온 모듈의 이름이나
함수의 이름을 수정해서 사용할 수 있다.

**main3.py**

```python
import two as tw
from one import a as z
print(z())
print(tw.a())
```

**result**

```
a
B
```


이제 ruby에서 모듈을 직접 만들어 사용하는 방법을 보겠다. ruby에서는 모듈이 파일로
분리되어 쓰이기도 하고 분리되지 않은채 쓰이기도 한다. 아래의 one.rb는 한 파일 내에서
모듈을 생성하고 사용하는 예제이다. ruby에서 모듈의 이름은 반드시 대문자로 시작해야된다.
그리고 모듈의 영역은 module-end로 구분할 수 있다. 그리고 module_function()을 모듈안에
써야지 "모듈이름.함수명"으로 사용할 수 있다.

**one.rb**

{% highlight ruby %}
#루비에서의 모듈은 파일로 분리될수도 있고 분리되지 않을 수도 있음! 독특한 특성이 있다!

module One
  module_function()
  def a()
    return 'a'
  end
end

puts(One.a())
{% endhighlight %}

**result**

```
a
```


아래 예제는 ruby에서 모듈을 따로 분리하여 사용하는 예제이다. main.rb에서 one.rb의
One모듈을 가져올 때 require를 사용한다. require 뒤에는 모듈의 경로를 적어준다. 이때
경로문제가 많이 발생하니까 주의하자! 참고로 require_relative를 사용하면 상대경로로
모듈을 가져오니 유용히 사용하자!

**one.rb**

{% highlight ruby %}
module One
  module_function()
  def a()
    return 'a'
  end
end
{% endhighlight %}


**main.rb**

{% highlight ruby %}
# require './One' 써도 터미널에선 잘되지만 atom에선 경로설정이 프로젝트로 되있어서 에러남!
require_relative 'One'
puts(One.a())
{% endhighlight %}

**result**

```
a
```
