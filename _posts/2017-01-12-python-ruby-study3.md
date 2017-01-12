---
layout: post
title: "Python Ruby Study _ 주석, Cheat Sheet"
date: 2017-01-12
categories: [python, ruby]
---

python과 ruby는 주석으로 #을 사용한다. python의 경우는 특이하게 \`\`\`를 사용하여
다음 \`\`\`가 나올 떄 까지를 모두 문자열로 인식한다. 이 걸 사용해서 여러문장의 여러문
장의 주석처리를 한다. 참고로 " ", ' ' 이 둘은 한 문장에서만 문자열로 인식하고 열이
바뀌면 에러가 뜬다!

**python**

```python
# your code goes here
'''
test
test
'''

str = '''
test
test
'''

print(str)
```

**result**

```
test
test

```
