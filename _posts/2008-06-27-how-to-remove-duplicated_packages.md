---
title: '[R] 중복된 Package를 삭제하는 방법'
layout: post
permalink: /2008/06/r-tips-%ec%a4%91%eb%b3%b5%eb%90%9c-package%eb%a5%bc-%ec%82%ad%ec%a0%9c%ed%95%98%eb%8a%94-%eb%b0%a9%eb%b2%95/
dsq_thread_id:
  - 333573820

categories:
  - R-Tips
tags:
  - R-Tips
---
R을 사용하다 보면 package가 중복되어 설치되는 경우가 있다. 이러한 경우 중복된 package를 삭제하려면, 함수 remove.packages()를 이용해 R의 콘솔상에서 다음과 같이 입력한다.

{% highlight r %}
> remove.packages(installed.packages() [duplicated(rownames(installed.packages())), 1], lib=.libPaths()[.libPaths() != .Library])
{% endhighlight %}