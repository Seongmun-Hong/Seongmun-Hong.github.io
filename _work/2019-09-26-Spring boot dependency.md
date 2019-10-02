---
title: Spring Boot에서 dependency를 관리하는 방법
layout: post
description: ""
headline: null
modified: '2019-09-26'
category: SpringBoot
tags: [SpringBoot]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-09-26 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Spring Boot에서 dependency를 관리하는 방법

Spring Boot 프로젝트를 생성한 후 pom.xml은 다음과 같다.


<br /> 

### pom.xml  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>springboot.com</groupId>
    <artifactId>spring-boot-getting-started</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

Dependency에는 groupId와 artifactId만 존재하며 버전에 대한 정보는 없다. 이렇듯 버전정보가 없음에도 불구하고 어떻게 버전 관리를 하고 있는 것일까 ?


dependencymanager

장점 : 수많은 디펜던시를 직접 적어놓은 후 스프링 버전을 올리게 되면 문제가 발생할 가능성이 높다. 서드파티 라이브러리들이 어떤 버전과 어떤버전이 호환되는지 알 수 없다.

pom에서 지원하지 않는 버전들은 버전을 명시해야한다. 혹은 특별히 원하는 버전이 있는경우에는 명시할 수 있다.

바꾸는 방법 2가지
1. parent에 artifactId를 수정
2. dependencymanager element를 사용하여 설정

하지만 parent 설정에서 java version, encording 방법, resource 등 여러가지 다른 설정도 포함하기 때문에
바꿀 경우 많은 설정을 따로 진행해야 한다. 

## pom.xml

[공식 다운로드 페이지](https://www.anaconda.com/download/)에서 파이썬 3.6 버전의 64비트 installer를 다운받은 뒤 아래의 명령어로 설치하시면 됩니다.
장문의 라이센스 소개가 나오면 **Enter**를 계속 입력해야 합니다. **Ctrl+c**를 입력하면 읽지 않을 수 있습니다.
선택지가 나오면 모두 **yes**로 답해주시면 됩니다.

```sh
$ bash Anaconda3-4.3.0-Linux-x86_64.sh
```

파이썬을 실행하시면 아래와 같은 결과가 출력됩니다.

```sh
$ python
Python 3.6.0 |Anaconda 4.3.0 (64-bit)| (default, Dec 23 2016, 12:22:00)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-1)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

공식문서에서 텐서플로는 **python3-numpy python3-dev python3-pip python3-wheel**가 필요하다고 하는데, **dev** 이외의 나머지 패키지는 이미 Anaconda에 설치되어 있습니다. **dev**는 아래와 같이 터미널에 입력하여 설치하시면 됩니다.


```sh
$ pip install dev
```

<br/><br/>
## 2. Bazel 설치
[바젤 설치문서](https://docs.bazel.build/versions/master/install.html)를 참고하였습니다. 먼저 JDK 8을 설치합니다.

```sh
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java8-installer
```

그 후 아래와 같이 바젤 배포 URI를 추가해주세요.

```sh
$ echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list
$ sudo apt install curl # if you did not install curl
$ curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -
```

URI를 추가했으니 업데이트 후 바젤을 설치해주시면 됩니다.

```sh
$ sudo apt-get update && sudo apt-get install bazel
$ bazel version
Extracting Bazel installation...
Build label: 0.4.4
```

> 혹시 다음 내용에서 **패키지 Build**시 이러한 오류를 접한다면, Bazel 최신 버전이 아닌 0.5.2버전을 설치해주시면 오류가 나지 않습니다.

<br/><br/>

## 3. Tensorflow 설치 (from Github)

먼저 텐서플로 저장소로부터 소스코드를 다운받습니다.
적당한 폴더 위치에서 터미널을 여신 후 아래와 같이 입력해주십시오. 파일복사가 끝나고 나면 새로 생기는 **tensorflow**라는 폴더로 이동해주시면 됩니다.

```sh
$ sudo apt-get install git
$ git clone https://github.com/tensorflow/tensorflow
$ cd tensorflow
```

> 만약 **master** 소스코드 대신 **r1.0** 버전 소스코드로 컴파일하고 싶으시다면 **git checkout r1.0**을 입력해주시면 됩니다.

<br/><br/>
### 3-1 설정 입력
**tensorflow** 폴더에서 아래와 같이 ./configure 라고 입력 하시면 파이썬이나 CUDA, cuDNN의 경로 등에 대해서 물을겁니다.  
이때 **y**나 **n** 입력에 주의가 필요하고, 이전에 설치한 경로와 다른 경로가 **Default** 설정으로 뜬다면 이전에 설치한 경로를 입력해주시면 됩니다. (예: /usr/local/cuda-8.0)

```sh
$ ./configure
Please specify the location of python. [Default is /home/dmlab/anaconda3/bin/python]:
Please specify optimization flags to use during compilation [Default is -march=native]:
Do you wish to use jemalloc as the malloc implementation? (Linux only) [Y/n] y
jemalloc enabled on Linux
Do you wish to build TensorFlow with Google Cloud Platform support? [y/N] n
No Google Cloud Platform support will be enabled for TensorFlow
Do you wish to build TensorFlow with Hadoop File System support? [y/N] n
No Hadoop File System support will be enabled for TensorFlow
Do you wish to build TensorFlow with the XLA just-in-time compiler (experimental)? [y/N] n
No XLA JIT support will be enabled for TensorFlow
Found possible Python library paths:
  /home/dmlab/anaconda3/lib/python3.6/site-packages
Please input the desired Python library path to use.  Default is [/home/dmlab/anaconda3/lib/python3.6/site-packages]
Using python library path: /home/dmlab/anaconda3/lib/python3.6/site-packages
Do you wish to build TensorFlow with OpenCL support? [y/N] n
No OpenCL support will be enabled for TensorFlow
Do you wish to build TensorFlow with CUDA support? [y/N] y
CUDA support will be enabled for TensorFlow
Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]:
Please specify the CUDA SDK version you want to use, e.g. 7.0. [Leave empty to use system default]:
Please specify the location where CUDA  toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]: /usr/local/cuda-8.0
Please specify the Cudnn version you want to use. [Leave empty to use system default]:
Please specify the location where cuDNN  library is installed. Refer to README.md for more details. [Default is /usr/local/cuda-8.0]:
Please specify a list of comma-separated Cuda compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size.
[Default is: "3.5,5.2"]:
```

<br/><br/>

### 3-2 pip 패키지 빌드

아래 명령어를 입력하여 텐서플로 GPU 버전 패키지를 빌드합니다.

```sh
$ bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
...
...
INFO: Elapsed time: 868.698s, Critical Path: 661.03s
```

위 빌드가 끝나면 **build_pip_package** 라는 이름의 스크립트가 생깁니다. 아래 명령어를 입력하여 스크립트를 실행하면 **/tmp/tensorflow_pkg** 폴더에 **.whl** 파일을 빌드합니다.

```sh
$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```

<br/><br/>

### 3-3 pip 패키지 설치

아래와 같이 터미널에 입력하되, 별표시 이전까지만 입력하고 **tab** 키를 누르자. 나머지 파일명이 자동완성된다. (빌드되는 파일명이 일정하지 않기 때문)

```sh
$ pip install /tmp/tensorflow_pkg/tensorflow*
```

아나콘다에서 한 가지 빠진 파일이 있어([#5017 issue](https://github.com/tensorflow/tensorflow/issues/5017) 참고) 아래의 명령어로 붙여넣어주십시오. (your_id = 우분투 로그인 계정명)

```sh
$ cp /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /home/{your_id}/anaconda3/lib/
```

<br/><br/>
### 3-4 테스트
파이썬에서 아래와 같이 설치 테스트를 해주십시오.
* 텐서플로 불러오기 : 아래와 같이 CUDA 라이브러리를 잘 불러오면 성공입니다.
```sh
>>> import tensorflow as tf
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcublas.so.8.0 locally
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcudnn.so.5 locally
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcufft.so.8.0 locally
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcuda.so.1 locally
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcurand.so.8.0 locally
```

* 간단한 연산 둘려보기 : GPU가 올바르게 인식되었고, 결과도 제대로 출력되는지 확인해주십시오
```sh
>>> a = tf.constant(1)
>>> b = tf.constant(2)
>>> with tf.Session() as sess:
...     print(sess.run(a+b))
...
I tensorflow/core/common_runtime/gpu/gpu_device.cc:885] Found device 0 with properties:
name: GeForce TITAN Xp COLLECTORS EDITION
major: 6 minor: 1 memoryClockRate (GHz) 1.582
pciBusID 0000:01:00.0
Total memory: 11.91GiB
Free memory: 11.21GiB
I tensorflow/core/common_runtime/gpu/gpu_device.cc:906] DMA: 0
I tensorflow/core/common_runtime/gpu/gpu_device.cc:916] 0:   Y
I tensorflow/core/common_runtime/gpu/gpu_device.cc:975] Creating TensorFlow device (/gpu:0) -> (device: 0, name: GeForce TITAN Xp COLLECTORS EDITION, pci bus id: 0000:01:00.0)
9
>>>
```