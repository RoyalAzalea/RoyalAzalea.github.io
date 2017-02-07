---
layout: post
title: "Python Ruby Study _ 객체와 모듈"
date: 2017-02-07
categories: [python, ruby]
---

규모가 큰 프로젝트를 진행하다보면 파일정리가 필요하다. 이때 객체를 모듈화를 하여
필요한 곳에서 필요한 것만 쓸 수 있다. 아래 예제는 모듈안에 객체를 만들어서 다른곳에서
사용하는 예제이다. python의 경우 `lib.py`에 `A_class`를 만들었고 `1.py`에서 `import`하여
`obj = lib.A()`를 통해 인스턴스화 한다. 이때 `lib.A()`처럼 모듈안에 있는 클래스를 `.`로
가져온다.

**1.py**

```python
import lib
obj = lib.A()
print(obj.a())
```

**lib.py**

```python
class A:
    def a(self):
        return 'a'
```

**result**

```
a
```


ruby의 경우도 마찬가지로 `lib.rb`에 `A_class`를 만들었고 `1.rb`에서 `require_relative`를
통해 `lib.rb`를 가져온다. 그리고 `obj = Lib::A.new()`를 통해 인스턴스화 한다. 이때
`Lib::A.new()`처럼 모듈안에 있는 클래스를 `::`로 가져온다.

**1.rb**

```ruby
require_relative 'lib'
obj = Lib::A.new()
p obj.a()
```

**lib.rb**

```ruby
module Lib
  class A
    def a()
      return 'a'
    end
  end
end
```

**result**

```
a
```
