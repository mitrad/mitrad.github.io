---
title: '[R] 중복된 Package를 삭제하는 방법'
layout: post
categories:
  - R-Tips
tags:
  - R-Tips
---
R을 사용하다 보면 package가 중복되어 설치되는 경우가 있다. 이러한 경우 중복된 package를 삭제하려면, 함수 remove.packages()를 이용해 R의 콘솔상에서 다음과 같이 입력한다.

{% highlight r %}
> remove.packages(installed.packages() [duplicated(rownames(installed.packages())), 1], lib=.libPaths()[.libPaths() != .Library])
{% endhighlight %}