---
layout: post
title:  "mecab-ko analysis for elasticsearch 맥에 설치하기"
categories: elasticsearch
---
이 글은 꽤나 활발하게 업데이트되고 퀄리티가 준수한 한글 형태소 분석기인 [은전한닢](http://eunjeon.blogspot.kr/) 의 elasticsearch 플러그인을 맥에 설치하는 방법에 관한 글이다. mecab은 원래 일본어 형태소 분석기인데, 이를 한국어도 분석할 수 있도록 만든 것이 은전한닢 프로젝트인듯 하다. 리눅스에서는 프로젝트 페이지에 있는 [가이드](https://bitbucket.org/eunjeon/mecab-ko-lucene-analyzer/raw/master/elasticsearch-analysis-mecab-ko/)를 따라하면 큰 무리가 없는데 맥에서 사용하기 위해서 몇 가지 해결해야 하는 부분이 있다.

우선 elasticsearch 플러그인을 설치하기 전에 mecab-ko(형태소 분석 엔진)과 mecab-ko-dic(사전)을 설치해야 한다. 이 과정은 맥에서 특별히 문제되는 부분이 없기 때문에 [이 페이지](https://bitbucket.org/eunjeon/mecab-ko-dic)를 보고 설치하면 된다.

다음은 elasticsearch 플러그인을 깔아야 하는데, es에서 mecab을 사용하기 위해선 동적 라이브러리인 libMeCab.so가 필요하다. [mecab-java-XX.tar.gz](https://code.google.com/p/mecab/downloads/list)를 설치해야 한다. 맥에서 빌드하기 위해서 Makefile을 조금 손봐야 한다.

{% highlight bash %}
# Makefile

TARGET=MeCab
JAVAC=javac
JAVA=java
JAR=jar
CXX=c++
# 해당 맥의 java home을 찾아서 경로를 바꿔줘야 한다.(es 최신 버전은 java 1.7 이상이 필요)
# INCLUDE=/usr/local/jdk1.6.0_41/include
INCLUDE=/Library/Java/JavaVirtualMachines/jdk1.8.0_25.jdk/Contents/Home/include
PACKAGE=org/chasen/mecab

LIBS=`mecab-config --libs`
# linux -> darwin으로 변경
# INC=`mecab-config --cflags` -I$(INCLUDE) -I$(INCLUDE)/linux
INC=`mecab-config --cflags` -I$(INCLUDE) -I$(INCLUDE)/darwin

all:
        # -fno-strict-aliasing 옵션 추가
        # $(CXX) -O3 -c -fpic $(TARGET)_wrap.cxx  $(INC)
        $(CXX) -O3 -c -fno-strict-aliasing -fpic $(TARGET)_wrap.cxx  $(INC)
        # 기본으로 명시돼 있는 타겟명 뒤에 Java 추가.
        # $(CXX) -shared  $(TARGET)_wrap.o -o $(libMeCab).so $(LIBS)
        $(CXX) -shared  $(TARGET)_wrap.o -o libMeCabJava.dylib $(LIBS)
        $(JAVAC) $(PACKAGE)/*.java
        $(JAVAC) test.java
        $(JAR) cfv $(TARGET).jar $(PACKAGE)/*.class

test:
        env LD_LIBRARY_PATH=. $(JAVA) test

clean:
        rm -fr *.jar *.o *.so *.class $(PACKAGE)/*.class

cleanall:
        rm -fr $(TARGET).java *.cxx
{% endhighlight %}
