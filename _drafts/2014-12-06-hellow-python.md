평소에 R 만 쓰다보니 점점 지식이 편협해 지는 것 같아 Python을 해보려 마음먹었다. 

나중에 까먹을 것이 분명하므로 일단 Mac에서 수치계산 및 머닝러신을 실행할 수 있는 환경을 구축하는 순서를 정리한다.

※ 이하 환경에서 정상 작동확인
OS X Mavericks (10.9)
OS X Yosemite (10.10)


## 시작하며

1. 간단히 순서를 정리하면 일단 pyenv 을 이용하여 Python을 설치

2. Miniconda을 이용하여 NumPy, SciPy, matplotlib, scikit-learn을 설치 

Python의 버전 2.xx과 3.xx의 사양은 큰 차이가 있다고 하나 자세한 것은 잘 모르므로 인터프리터 및 버전을 손쉽게 스위칭할 수 있도록 pyenvd를 이용한다. 

수치계산, 머신러닝을 위해 설치하는 라이브러리는 

* NumPy
数値計算を効率的に処理するためのライブラリです
配列の操作がとても簡単になるので、行列計算には必須っぽいです

* SciPy
様々な科学計算が実装されたライブラリです
内部でNumPyを利用しています

* matplotlib
グラフ描画のライブラリです
内部でNumPyを利用しています

* scikit-learn
機械学習に関する様々なアルゴリズムが実装されたライブラリです


上記のライブラリもバージョンの組み合わせが問題になることがあるようなので、Miniconda を使って切り替えられるようにしておきます

Anaconda を使うと最初からこれらのライブラリが全部入りで使えるようですが、今回は後からライブラリを入れるタイプの Miniconda を使おうと思います


IDE는 개인적으로 선호하는 Sublime Text를 이용한다. 



## Python と各ライブラリのインストール
まず、ホームディレクトリに pyenv のリポジトリをコピーしてきます

git clone git://github.com/yyuu/pyenv.git ~/.pyenv

~/.bash_profile にクローンしてきた pyenv へのパスを追加するコードを付け足します

export PYENV_ROOT="${HOME}/.pyenv"
if [ -d "${PYENV_ROOT}" ]; then
    export PATH=${PYENV_ROOT}/bin:$PATH
    eval "$(pyenv init -)"
fi

.bash_profile の内容を現在のシェルに反映します

source ~/.bash_profile

これは必須ではありませんが pyenv-pip-rehash をインストールします
pyenv で Python の環境を切り替えたときに pyenv rehash を自動で実行してくれます

brew install pyenv-pip-rehash

pyenv のインストールが終わったら、pyenv を利用して Python をインストールします
ここでは Miniconda をインストールします

# インスール可能なバージョンを確認する
$ pyenv install --list

# インストールする
$ pyenv install miniconda3-3.7.0

# 現在のシェルで使う python のバージョンを指定
$ pyenv shell miniconda3-3.7.0

# インストール済みのバージョンと現在有効になっている python のバージョンを確認する
$ pyenv versions

念のためシェルを再起動して、正しくバージョンが切り替えられることを確認します

# デフォルトの状態
$ python --version
Python 2.7.5

# バージョンの切り替え
$ pyenv shell miniconda3-3.7.0
$ python --version
Python 3.4.2 :: Continuum Analytics, Inc.

# pyenv-pip-rehash をインストールしていない場合は
# バージョンを切り替えた後に以下を必ず実行します
$ pyenv rehash

Miniconda を利用して NumPy、SciPy、matplotlib、scikit-learn をインストールします

$ conda install numpy=1.8 scipy matplotlib scikit-learn
特にバージョンを指定しなければ最新版がインストールされますが、現時点の最新版である SciPy 0.14.0 と NumPy 1.9.1 の組み合わせだと SciPy の test でエラーが大量に発生します
NumPy 1.8系 にするとエラーが出なくなったので、ここでは NumPy 1.8 をインストールします

※この issue と似たようなエラーなんですが、今回は Python3 で動かしているのでこれとは別のエラーかもしれません
Many scipy.sparse test errors/failures with numpy 1.9.0b2 · Issue #3853 · scipy/scipy · GitHub

さっそく Miniconda にしたメリットがありました


インストール済みのパッケージを確認します

$ conda list
# packages in environment at /Users/[ユーザー名]/.pyenv/versions/miniconda3-3.7.0:
#
conda                     3.7.3                    py34_0  
dateutil                  2.1                      py34_2  
freetype                  2.4.10                        1  
libpng                    1.5.13                        1  
matplotlib                1.4.0                np18py34_0  
nose                      1.3.4                    py34_0  
numpy                     1.8.2                    py34_0  
openssl                   1.0.1h                        1  
pip                       1.5.6                    py34_0  
pycosat                   0.6.1                    py34_0  
pyparsing                 2.0.1                    py34_0  
python                    3.4.2                         0  
python-dateutil           2.1                       <pip>
pytz                      2014.9                   py34_0  
pyyaml                    3.11                     py34_0  
readline                  6.2                           2  
requests                  2.4.3                    py34_0  
scikit-learn              0.15.2               np18py34_0  
scipy                     0.14.0               np18py34_0  
setuptools                7.0                      py34_0  
six                       1.8.0                    py34_0  
sqlite                    3.8.4.1                       0  
tk                        8.5.15                        0  
xz                        5.0.5                         0  
yaml                      0.1.4                         1  
zlib                      1.2.7                         1  

最後に python を起動して NumPy、SciPy、matplotlib のテストを実行します

>>> import numpy
>>> numpy.test('full')
>>> import scipy
>>> scipy.test('full')
>>> import matplotlib
>>> matplotlib.test()
scikit-learn のテストは nose でテストが用意されているようなので bash シェルから以下を実行します

nosetests --exe sklearn

ずらーーーーーーーーーーっと warning や実行中の出力が表示されます
最後に以下のように "OK" と表示されれば問題なく動作します

OK (KNOWNFAIL=6, SKIP=8)
ここで以下のように "FAILED" となる場合は、Python やライブラリのバージョンを確認してください

FAILED (KNOWNFAIL=276, SKIP=922, errors=326, failures=42)
matplotlib のテストは 2つエラーとなって fail になりました
グラフの描画をやるだけなので、とりあえずここは無視しても差し支えないかと思います


※おまけ
Miniconda の存在を知る前に NumPy、SciPy を手動でインストールしてしまったので、一応その際の手順も残しておきます

・NumPy のインストール

pip3 install numpy
・SciPy のインストール

pip3 install git+http://github.com/scipy/scipy/
※pip3 install scipy でもOKです


SciPy のインストール時には恐らくいろいろエラーが出るはずです
fortran のコンパイラと cython が無いことが原因でエラーとなっていました

gfortran をインストール
※現在は gcc に含まれているそうなので gcc をインストールします

brew install gcc
・cython のインストール

easy_install-3.3 cython
これで再度、SciPy のインストールを試みます
以下の記事を参考に、コンパイラを指定してインストールしてください
エラーを出さずにScipyをインストールする - PASL


・matplotlib のインストール

pip3 install matplotlib
・scikit-learn のインストール

pip3 install scikit-learn 
pip はソースからコンパイルしてインストールしているようなので、SciPy でエラーが発生するのはMac にデフォルトで入っているコンパイラが原因のようです

Miniconda でインストールする場合はコンパイル済みのバイナリを取得してくるだけなので、この点については問題ありません
こういったライブラリの管理ツールはとても便利ですね


(参考リンク)
S.7.12. SciPyの設定 | R Financial & Marketing Library
Errors in tests of Scipy · Issue #12 · Homebrew/homebrew-python · GitHub

