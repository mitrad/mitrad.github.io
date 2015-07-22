---
title: '[SAS] 복수의 문자변수의 값을 연결해 하나의 변수로 만드는 방법'
layout: post
permalink: /2008/08/sas-%eb%b3%b5%ec%88%98%ec%9d%98-%eb%ac%b8%ec%9e%90%eb%b3%80%ec%88%98%ec%9d%98-%ea%b0%92%ec%9d%84-%ec%97%b0%ea%b2%b0%ed%95%b4-%ed%95%98%eb%82%98%ec%9d%98-%eb%b3%80%ec%88%98%eb%a1%9c-%eb%a7%8c/
dsq_thread_id:
  - 306726064
categories:
  - SAS
tags:
  - data step
  - SAS
---
<span style="font-size: 24px;">Q</span>. 복수의 문자변수 값을 컴마(,)로 연결해 하나의 변수로 만드는 방법은?

<span style="font-size: 24px;">A</span>. SAS 9 부터 추가된 CATX 함수를 이용하면 구분문자를 지정하여 문자열로 만들 수 있다.

{% highlight r %}
DATA _NULL_;
   dlm=",";
   char1="Hong";
   char2="GilDong";
   char3="15";
   char4="A";
   results=CATX(dlm, OF char1-char4);
 
   PUT results;
RUN;
{% endhighlight %} 

출력결과 : Hong,GilDong,15,A