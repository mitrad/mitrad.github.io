---
title: "Cpp for R C++をRとつなぐ"
author: "ryamada"
date: "Monday, July 20, 2015"
output: html_document
---

# Steps to use c++ with R Rでc++を使うステップ

(1) Write c++ file(s) c++のファイルを書く

(2) Make a dynamic library that can be called from R ダイナミック・ライブラリを作ってRから使えるものを作る

(3) Write R wrapper functions that call function(s) defined in the library ライブラリで定義した関数を呼び出すRラッパー関数を書く

(4) Use R functions based on the c++ library c++ライブラリを使っているR関数を使う

# Preparation 準備

Among the four steps (1)-(4), (1), (3) and (4) are easy if your are using R already.

The step (2) might be a problem. (2)のステップは問題が出るかもしれない。

The following command should be used in command-line, which is a command-line version R program with an option making a dynamic library from c/c++ file(s).
以下のコマンドをコマンドラインで使う。このコマンドはRのコマンドライン版であり、そのダイナミック・ライブラリを作るオプションを指定しているものである。

```{}
R CMD SHLIB
```

This command calls g++ to compile c/c++ file(s), therefore g++, a compiler, should be appropriately installed.

このプロセスでc/c++ファイルを「コンパイル」する必要があるので、g++というコンパイラがきちんと動くようになっていることが必要である。

### For Windows ウィンドウズの場合

Rtools should be installed from https://cran.r-project.org/bin/windows/Rtools/ that makes g++ ready and "R CMD SHLIB" ready as well.
Rtoolsをhttps://cran.r-project.org/bin/windows/Rtools/ からインストールするとg++と"R CMD SHLIB"とが使えるようになる。

## Check your g++

Check g++ as below. g++を以下のコマンドで確認する。

```{}
g++ -v
```

"-v" is an option to show various information when you use "g++" command, such as the version of the compiler and the paths where g++ look for various information.
"-v" オプションは"g++"というコマンドを使うときに、その"g++"が呼び出すコンパイラのバージョンや各種参照情報の置き場へのパスなどが示される。

If you are seeing strings like below, you have gcc.
もし以下のような文字列が現れれば、gccが入っていることがわかる。
```{}
C:\Users\ryamada\Desktop\cppR>R CMD SHLIB hundred.c
gcc -m64 -I"C:/PROGRA~1/R/R-31~1.0/include" -DNDEBUG     -I"d:/RCompile/CRANpkg/
extralibs64/local/include"     -O2 -Wall  -std=gnu99 -mtune=core2 -c hundred.c -
o hundred.o
gcc -m64 -shared -s -static-libgcc -o hundred.dll tmp.def hundred.o -Ld:/RCompil
e/CRANpkg/extralibs64/local/lib/x64 -Ld:/RCompile/CRANpkg/extralibs64/local/lib
-LC:/PROGRA~1/R/R-31~1.0/bin/x64 -lR
...
...
gcc version 4.6.3 20111208 (prerelease) (GCC)
```


## Check "R CMD SHLIB"

Let's connect a small c.file to R. 小さなcファイルをRにつないでみる。

(i) c file
Make a c file and save as "hundred.c". 次のようなCファイルを書き、"hundred.c"と言う名前で保存する。

This defines a function named "hundred".
"hundred"という名前の関数を定義している。

```{}
void hundred(int *x){
 x = 100;
}
```

You may be concerned the difference between "*x" as an argument and "x" as an object in the function, but for now we ignore.
引数では"*x"と書き、関数内部では"x"と書いてあり、この違いが気になるが、ここでは無視する。

This function takes an integer object and assign the interge "100".
この関数は整数オブジェクトを受け取り、それに"100"を付値する。

This function return nothing as indicated by "void". この関数は返り値なしであることは"void"で表されている。

(ii) "R CMD SHLIB"

"R CMD SHLIB" is a command to compile c/c++ files to make dynamic libraries using g++. "R CMD SHLIB"はc/c++ファイルをg++を使ってコンパイルし、ダイナミック・ライブラリを作る。

```{}
R CMD SHLIB "hundred.c"
```

You will see the followings on your console. コンソールに以下のようなものが現れる。

```{}
gcc -m64 -I"C:/PROGRA~1/R/R-31~1.0/include" -DNDEBUG     -I"d:/RCompile/CRANpkg/
extralibs64/local/include"     -O2 -Wall  -std=gnu99 -mtune=core2 -c hundred.c -
o hundred.o

gcc -m64 -shared -s -static-libgcc -o hundred.dll tmp.def hundred.o -Ld:/RCompil
e/CRANpkg/extralibs64/local/lib/x64 -Ld:/RCompile/CRANpkg/extralibs64/local/lib
-LC:/PROGRA~1/R/R-31~1.0/bin/x64 -lR
```

They look dizzy but they are two scentences starting with "gcc".
ごちゃごちゃしているが、２つの"gcc"で始まるコマンドが動いていることがわかる。

They compile "hundred.c" and make the dynamic library, "hundred.dll" in case of Windows or "hundred.so" in case of Linux/Mac.

"hundred.c"ファイルをgccがコンパイルし、"hundred.dll"(Windowsの場合)、"hundred.so"(Linux/Macの場合)というダイナミック・ライブラリを作っている。

The first gcc command takes "hundred.c" as an argument and the second gcc command outputs "hundred.dll" in case of Windows or "hundred.so" in case of Linux/Mac.
１つ目のgcc文は"hundred.c"を引数として受け取っており、２つ目のgcc文は出力するダイナミック・ライブラリ名を"hundred.dll"(Windowsの場合)、"hundred.so"(Linux/Macの場合)とするようにしている。

Other dizzy strings are somethings to specify condtions of gcc.
ただし、Rで使うためなので、R関連のもろもろを使うようにオプション指定している。

(iii) dyn.load()

"dyn.load()" function is a R function to LOAD a DYNamic library file. "dyn.load()"関数はRの関数で、ダイナミック・ライブラリ(DYNamic library)のファイルをロード(LOAD)する関数である。

Run the command below in R. Of course you should know the location of your "hundred.dll" or "hundred.so" file, which you just made in the same directory with "hundred.c".
以下のコマンドをRで発行する。もちろん今作ったばかりの"hundred.dll"または"hundred.so"ファイルの置き場所("hundred.c"と同じディレクトリ)に注意して読み込む。

```{}
dyn.load("hundred.dll")
# dyn.load("hundred.so")
```

(iv) Wrapper function. ラッパー関数

Now R knows that there is a C function named "hundred".
この時点でRは"hundred"という名前のC関数があることを知っている。

Let's make a R function that call out the C function and hand an argumet fitting to double type.
さて、R関数を作る。そのR関数はC関数を呼び出し、double型に合う引数を渡す。

The C function only changes the value assinged to the object and you should take out the changed value.
C関数は与えた引数の値を書き換えるだけなので、それを取り出せばよい。

{% highlight r %}
hundredR <- function(){
  result <- .C("hundred",x=100.0)
  return(result$x)
}
{% endhighlight %}

The following R function, hundredR(), works as below.
以下が出来上がったR関数 hundredR()の挙動である。
```{}
> hundredR()
[1] 100
```

# Some basic tasks いくつかの基礎課題

The example above is easy.
上の例は簡単である。

When you start connecting C++ and R, troubles come out and the troubles are related with something we ignored above or some extensions the example above did not use.

うまく行かなくなるのは、上の例で無視したり、実施していないことになる。

List up such pitfalls as below.
そんな注意事項を挙げておく。

## Differences in data types データタイプの違い

When we use R, we can handle integer and real numbers in the same way. However in C/C++, they should be handled separately.
Similar to this, R is lazy with data types in general and C/C++ is strict.

Rを使うとき整数と実数は同じように扱うが、C/C++は区別する。同様に、Rはデータ型に関して全般にルーズであり、C/C++は厳密である。

To overcome this difference between R and C/C++, R has files such as R.h in its basic file set.
このためにRはそれを定義したファイルをR.hなどと言う名前で、元々備えている。

## Memory management メモリの管理

Rはオブジェクトを作ったり使わなくなったりするにあたって、操作が不要だが、C/C++は不要になったオブジェクトはきちんと始末しないといけない作りになっている。

# Use package "Rcpp"" or not パッケージ"Rcpp"を使うか使わないか

We can overcome the above-mentioned tasks by mastering how to appropriate C/C++ codes.
上に述べた解決課題をC/C++コードの書き方を学ぶことで克服することができる。

Another way is to let the package "Rcpp" do the tasks.
もう１つの方法がRcppパッケージにやってもらうことである。

# Let's use package "Rcpp" パッケージ"Rcpp"を使う

Because "R CMD SHLIB" does not know what "Rcpp" yet, we have to tell it to the system as below.
"R CMD SHLIB"は"Rcpp"が何なのかを知らないので、システムにそれを教えておく必要がある。

Set conditions when using "Rscript" command, a one of R command-line commands.
"Rscript"と言うコマンドライン用のRの実行条件に関する設定を以下のようにすればよい。

Windowsではコマンドラインで次のように設定し
```{}
set PKG_CXXFLAGS=`Rscript -e 'Rcpp:::CxxFlags()'`
set PKG_LIBS=`Rscript -e 'Rcpp:::LdFlags()'`
```

Linx/Macでは次のようにする。
```{}
export PKG_CPPFLAGS=`Rscript -e 'Rcpp:::CxxFlags()'`
exprot PKG_LIBS=`Rscript -e 'Rcpp:::LdFlags()'`
```

参考サイト：
http://stackoverflow.com/questions/19140348/rcpp-first-compilation-trouble
http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.186.9878&rep=rep1&type=pdf


## Example of Rcpp Rcppの実例

See the following C++ file named "hello.cpp"
以下に示すのはC++ファイル"hello.cpp"である。

Using Rcpp, it includes the header file "Rcpp.h".
Rcppを使うので"Rcpp.h"というヘッダファイルをインクルードしている。

The function does not take any argument and returns something called SEXP being handled by Rcpp-way. 関数は引数なしで、返り値はSEXPをRcpp方式で取り扱った型である。

SEXP is one of C/C++ data types. It is good that whenever we combine R and C++, we always use this SEXP data type only.

SEXPというのはC/C++の型の一つだがRとやり取りするときはいつもSEXPなので、これだけを覚えておけばよい。

Inside of the function, a data type "Rcpp::Stringvector", that is a data type defined by Rcpp package for R's character string.
関数の内部では、Rcpp::StringVectorというRcppが定義したデータ型を使っている。
これは、Rの文字列ベクトル(String)に対応する型としてRcppが定義したものである。

```{}
#include <Rcpp.h>
RcppExport SEXP hello(){
  Rcpp::StringVector result(1);
  result[0] = "Hello World!";
  return(result);
}
```

Once we write this SEXP-way with Rcpp.h included, making the dynamic library and writing the wrapper function are both easy.
Be careful, the wrapper use ".Call()" function instead of ".C()".

SEXPを使ってRcpp.hをインクルードしてC++ファイルを書いておけば、これ以降のステップは簡単である。ただし".C()"関数の代りに".Call()"関数を使っていることに注意する。

```{}
R CMD SHLIB hello.cpp
```
```{}
dyn.load("hello.dll")
hello_R <- function(){
  result <- .Call("hello") #.Cではなく .Call
  return(result)
}
hello_R()
```

```{}
> hello_R <- function(){
+ result <- .Call("hello")
+ return(result)
+ }
> hello_R()
[1] "Hello World!"
```

# Go easier with Rcpp package, Rcppパッケージを使ってもっと楽に

## Benefits and drawbacks of usage of sourceCPP() in Rcpp package 関数sourceCpp() を使うことの利点と欠点

There is much easier way to connect Cpp and R using Rcpp package using a function sourceCpp().
RcppパッケージのsourceCpp()関数を使えばもっと簡単にCppとRとをつなぐことができる。

There are two benefits to do it; (i) you don't have to do "R CMD SHDLIB" or "dyn.load()" or "wrapper function" and (ii) your c++ codes are easier.

There is one drawback to do it; (i) you can handle only one c++ file at one time.

# Easy way with one Cpp file and sourceCpp() function １ CppファイルとsourceCpp()関数で手軽に

上で使ったcppファイルは次のように書くことができる。"helloRcpp.cpp"と名付けることにする。

You can rewrite the same function as below.
```{}
#include <Rcpp.h>
// [[Rcpp::export]]
Rcpp::StringVector hello(){
  Rcpp::StringVector result(1);
  result[0] = "Hello World!";
  return(result);
}
```
何が違うかと言うと
```{}
// [[Rcpp::export]]
```
が挿入されて、返り値データ型からC/C++特有のSEXPが消えたことである。
The difference is a comment out line inserted.

このようなファイルであると、コマンドラインは一切不要で、以下のようにするだけで使える。

Now you can just read the cpp file from R and use the new function immediately.
```{}
> sourceCpp("helloRcpp.cpp")
> hello()
[1] "Hello World!"
```

# Multiple Cpp files with R CMD SHLIB 複数CppファイルをR CMD SHLIBで

さて、複数ファイルを以下のように作ってみる。
Now we make a set of cpp files as below.

(i) Rで使う関数を定義しているファイル　"testjisaku2.cpp"

(ii) cppの関数を定義をしたファイルを２つ。それらの関数は、(i)の関数で使われたり、お互いに使いあったりする。　"jisakuf1.cpp" と "jisakuf2.cpp".

(iii) 上記の(i),(ii)の関数同士を組織化するためのヘッダーファイル　"jisaku2.h"


(i) A main file that defines a function that should be used in R; "testjisaku2.cpp"

(ii) Two files that define cpp functions which are called from the function in (i) and are called each other; "jisakuf1.cpp" and "jisakuf2.cpp".

(iii) The header file that organizes the three files in (i) and (ii). This header file is included by those three files; "jisaku2.h".

話を簡単にするために、上記の４ファイルはすべて同じディレクトリに置いておく。そうすれば、(i),(ii)のファイルでincludeするべきヘッダファイルは以下のように書けばよくなる。

For simplicity we place all four files in the same directory, so that the files in (i) and (ii) can include the header file by
```{}
#include "jisaku2.h"
```


さて。まず、"testjisaku2.cpp"。
Fist, "testjisaku2.cpp".
```{}
// this is testmain.cpp
#include <Rcpp.h>
#include "jisaku2.h"


RcppExport SEXP testjisaku(){
  int n = 3;
  int n2 = jisakuf1(n);
  int n3 = jisakuf2(n);
  int n4 = n2 + n3;
  Rcpp::NumericVector result(n4);
  for(int i=0;i<n4;i++){
    result[i] = i;
  }
  return(result);
}
```

次に、関数２ファイル。"jisakuf1.cpp" と "jisakuf2.cpp"

Two function files.
```{}
//jisakuf1.cpp
#include "jisaku2.h"

int jisakudouble(int x){
  return(x*2);
}

int jisakuf1(int x){
  int y = jisakudouble(x);
  int z = y + 3;
  return(z);
}
```
```{}
//jisakuf2.cpp
#include "jisaku2.h"

int jisakuf2(int x){
  int y = x + 4;
  int z = jisakudouble(y);
  return(z);
}
```

最後にヘッダファイル "jisaku2.h"。Finally the header file.

ヘッダファイルには、ファイル群が複雑になったときに定義重複が起きないようなおまじないの行 "ifndef"とかがついている(ifndef = "if not defined yet")。
It has some lines that work to avoid duplication of definitions in case we have many files compiled altogether.

```{}
//jisaku2.h
#ifndef R_Y_20150723
#define R_Y_20150723

int jisakudouble(int x);
int jisakuf1(int x);
int jisakuf2(int x);

#endif //R_Y_20150723
```

これに対して、行う処理は以下のように簡単である。

Commands are easy for them.

```{}
R CMD SHL testjisaku2.cpp jisakuf1.cpp jisakuf2.cpp
```

```{}
> dyn.load("testjisaku2.dll")
> require("Rcpp")
 要求されたパッケージ Rcpp をロード中です
> testjisakuR <- function(){
+ result <- .Call("testjisaku")
+ return(result)
+ }
>
> testjisakuR()
 [1]  0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22
```
