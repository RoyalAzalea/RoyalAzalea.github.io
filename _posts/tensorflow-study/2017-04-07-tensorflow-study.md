---
layout: post
title: "Tensorflow Study _ 환경 구성"
date: 2017-04-07
categories: [tensorflow, machine_learning]
---

설치환경 Window 10

`Tensorflow`를 공부하기 위한 환경 구성 (`Python`, `PyCharm`, `Anaconda`)  

## 공부자료

* 모두를 위한 머신러닝/딥러닝 강의 : <https://hunkim.github.io/ml/>
* Tensorflow Docs 번역본 : <https://tensorflowkorea.gitbooks.io/tensorflow-kr/content/>

## 1. 설치

`Python` : <https://www.python.org/downloads/release/python-352/>  
(현재 tensorflow에서 python version 3.6.X 를 지원해주지 않는다. 그러므로 python
  version 3.5.X를 설치해야 한다. 참고로 3.5.2 설치함)  

`PyCharm` : <https://www.jetbrains.com/pycharm/download/#section=windows>  

`Anaconda` : <https://www.continuum.io/downloads>
(설치할 Anaconda는 python version과 사용할 bit(64 or 32)를 맞춰야한다.
tensorflow는 64 bit에서 지원하기 때문에 64 bit로 설치해야 한다. 참고로
python 3.5 version 3.5.X와 64 bit에 맞는 Anaconda3-4.2.X를 설치해야 한다.)  



## 2. Anaconda에 Tensorflow 설치  

`tensorflow`라는 이름으로 콘다환경을 구성한다.(python version 3.5.X 사용)  

```
C:> conda create -n tensorflow python=3.5
```  


이제 `tensorflow`라는 이름의 콘다환경을 활성화 시켜준다.  

```
C:> activate tensorflow
 (tensorflow)C:>  # Your prompt should change
```  


`tensorflow`라는 이름의 콘다환경에 **Tensorflow** 를 설치한다. (CPU-only version과
  GPU version 중 우선 CPU-only version만 설치)  

**CPU-only version**

```
(tensorflow)C:> pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/windows/cpu/tensorflow-1.0.1-cp35-cp35m-win_amd64.whl
```

**GPU version**

```
(tensorflow)C:> pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/windows/gpu/tensorflow_gpu-1.0.1-cp35-cp35m-win_amd64.whl
```




## 3. PyCharm에서 구성한 콘다환경 설정

우선 PyCharm에서 python Project를 만든다. 이후 Project Interpreter를 설정할 때
위에서 구성한 `tensorflow`라는 이름의 콘다환경에 들어있는 `python.exe`를 선택한다.  

![Install_1](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/tensorflow-study/Install_1.PNG)  

이제 **File -> Setting -> Project:XXX -> Project Interpreter** 를 보면 Package에
`tensorflow`가 있음을 확인할 수 있다. (`tensorflow`라는 콘다환경에 **TensorFlow**
  가 설치됨)  

![Install_2](https://raw.githubusercontent.com/RoyalAzalea/RoyalAzalea.github.io/master/static/img/_posts/tensorflow-study/Install_2.PNG)  



아래는 예제와 테스트 결과이다.

**test.py**

```python
import tensorflow as tf


hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

**result**

```
C:\DevProgram\Anaconda3\envs\tensorflow\python.exe C:/Users/RoyalAzalea/Documents/Pycharm/lab_01/test.py
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "BestSplits" device_type: "CPU"') for unknown op: BestSplits
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "CountExtremelyRandomStats" device_type: "CPU"') for unknown op: CountExtremelyRandomStats
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "FinishedNodes" device_type: "CPU"') for unknown op: FinishedNodes
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "GrowTree" device_type: "CPU"') for unknown op: GrowTree
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "ReinterpretStringToFloat" device_type: "CPU"') for unknown op: ReinterpretStringToFloat
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "SampleInputs" device_type: "CPU"') for unknown op: SampleInputs
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "ScatterAddNdim" device_type: "CPU"') for unknown op: ScatterAddNdim
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "TopNInsert" device_type: "CPU"') for unknown op: TopNInsert
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "TopNRemove" device_type: "CPU"') for unknown op: TopNRemove
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "TreePredictions" device_type: "CPU"') for unknown op: TreePredictions
E c:\tf_jenkins\home\workspace\release-win\device\cpu\os\windows\tensorflow\core\framework\op_kernel.cc:943] OpKernel ('op: "UpdateFertileSlots" device_type: "CPU"') for unknown op: UpdateFertileSlots
b'Hello, TensorFlow!'

Process finished with exit code 0
```
