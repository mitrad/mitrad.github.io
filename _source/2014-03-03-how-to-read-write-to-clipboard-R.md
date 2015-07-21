---
layout: post
title: "[R] 클립보드를 이용한 데이터 입출력"
date: 2014-03-03
categories: R
tags: [R, base, clipboard]
comments: true
---



클립보드에 저장된 데이터를 불러오거나 R에서 생성된 객체를 클립보드로 보내는 방법

### 클립보드에서 데이터 불러오기


```r
> # MS Windows
> mydf <- read.table("clipboard", header = TRUE, sep = ",")
> 
> # Mac OS X
> mydf <- read.table(pipe("pbpaste"), header = TRUE, sep = ",")
```

### 클립보드로 내보내기


```r
> data <- rbind(c(1,1,2,3), c(1,1, 3, 4), c(1,4,6,7))
> 
> # MS Windows
> write.table(data, "clipboard", row.names = FALSE,   sep = ",")
> 
> # Mac OS X
> clip <- pipe("pbcopy", "w")                       
> write.table(data, file=clip)                               
> close(clip)
```
            
Unix/Linux에서는 xclip을 이용하면 된다는데 그렇게까지 하느니 그냥 파일 만들어서 입출력하는 것이 편할 것 같다. 그래도 꼭 해야 한다면 다음 링크를 참고


* [How to write to clipboard on Ubuntu/Linux in R?](http://stackoverflow.com/questions/10959521/how-to-write-to-clipboard-on-ubuntu-linux-in-r)
