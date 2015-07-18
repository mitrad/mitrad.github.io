---
layout: post
title: "Mac OS X에 Python 개발환경 만들기"
---

평소에 R 만 쓰다보니 점점 지식이 편협해 지는 것 같아 Python을 해보려 마음먹었다. 

나중에 까먹을 것이 분명하므로 일단 Mac에서 수치계산 및 머닝러신을 실행할 수 있는 환경을 구축하는 순서를 정리한다.

* 이하 환경에서 정상 작동확인
    * OS X Mavericks (10.9)
    * OS X Yosemite (10.10)


## 시작하며

1. 간단히 순서를 정리하면 일단 pyenv 을 이용하여 Python을 설치
2. Miniconda을 이용하여 NumPy, SciPy, matplotlib, scikit-learn을 설치 

Python의 버전 2.xx과 3.xx의 사양은 큰 차이가 있다고 하나 자세한 것은 잘 모르므로 인터프리터 및 버전을 손쉽게 스위칭할 수 있도록 pyenvd를 이용한다. 

수치연산, 머신러닝을 위해 설치하는 라이브러리는 

* NumPy
    * 수치연산을 효율적으로 처리하기 위한 라이브러리
    * 행렬 조작이 매우 간단해지므로 행렬계산을 위해서는 필수 

* SciPy
    * 다양한 과학계산법이 담겨있는 라이브러리 
    * 내부적으로 NumPy를 이용

* matplotlib
    * 그래프 작성 라이브러리
    * 내부적으로 NumPy를 이용

* scikit-learn
    * 머신러닝에 관한 알고리즘을 담고 있는 라이브러리 

위 라이브러리도 버전 구성이 문제가 되는 경우가 있다고 하니 Miniconda을 사용하여 전환 할수 있게 만들어 둔다. 

Anaconda을 사용하면 처음부터 이들 라이브러리가 전부 설치되지만 이번에는 나중에 라이브러리를 설치하는 Miniconda을 사용하기로 한다. 

IDE는 개인적으로 선호하는 Sublime Text를 이용한다. 

## Python과 각 라이브러리 설치
먼저 홈 디렉토리에 pyenv 리포지토리를 복제한다. 
```
git clone git://github.com/yyuu/pyenv.git ~/.pyenv
```
~/.bash_profile에 복제한 pyenv의 패스를 추가

```
export PYENV_ROOT="${HOME}/.pyenv"
if [ -d "${PYENV_ROOT}" ]; then
    export PATH=${PYENV_ROOT}/bin:$PATH
    eval "$(pyenv init -)"
fi
```

.bash_profile의 내용을 현재 쉘에 반영

```
source ~/.bash_profile
```

필수는 아니지만 *pyenv-pip-rehash*를 설치한다. 
pyenv으로 Python 환경을 스위칭할때 `pyenv rehash`를 자동으로 실행시켜 준다. 

```
brew install pyenv-pip-rehash
```

pyenv의 설치가 끝나면 pyenv을 이용하여 Python을 설치한다. 
여기서는 Miniconda를 설치

```
# 설치 할수 있는 버젼을 확인 
$ pyenv install --list

# 설치
$ pyenv install miniconda3-3.7.0

# 현재 쉘에서 사용할 python 버전 지정
$ pyenv shell miniconda3-3.7.0

# 설치한 버전과 현재 유효한 python 버전을 확인 
$ pyenv versions
```
만약을 위해 쉘을 재기동하여 올바르게 버전 스위칭이 되는가를 확인한다. 

```
# 기본 설정 상태
$ python --versions
Python 2.7.5

# 버전 스위칭
$ pyenv shell miniconda3-3.7.0
$ python --version
Python 3.4.2 :: Continuum Analytics, Inc.

# pyenv-pip-rehash를 설치하지 않은 경우에는 
# 버전 스위칭 후 다음 명령어를 반드시 실행 
$ pyenv rehash
```
Miniconda을 이용하여 NumPy, SciPy, matplotlib, scikit-learn을 설치

```
$ conda install numpy=1.8 scipy matplotlib scikit-learn
```
특별히 버전을 지정하지 않으면 최신 버전이 설치되지만 SciPy 0.14.0와 NumPy 1.9.1의 조합이라면 SciPy의 test에서 대량의 에러가 발생한다. 
NumPy 1.8 버전으로 하면 에라가 에러가 발생하지 않는다. 

※ 이 issue와 비슷한 에러인데 이번엔 Python3에서 실행하고 있으므로 이 에러와는 별도의 에러 일지도 모른다. 

```
Many scipy.sparse test errors/failures with numpy 1.9.0b2 · Issue #3853 · scipy/scipy · GitHub
```
さっそく Miniconda にしたメリットがありました


설치된 패키지를 확인 
```
$ conda list
# packages in environment at /Users/username/.pyenv/versions/miniconda3-3.7.0:
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
```
마지막으로 python을 실행하여 NumPy, SciPy, matplotlib의 테스트를 실행한다.  

```
>>> import numpy
>>> numpy.test('full')
>>> import scipy
>>> scipy.test('full')
>>> import matplotlib
>>> matplotlib.test()
```

scikit-learn의 테스트는 bash에서

```
nosetests --exe sklearn
```

마지막에 다음과 같이 "OK" 메시지가 떨어지면 문제 없이 동작한다.
```
OK (KNOWNFAIL=6, SKIP=8)
```
그러나 "FAILED" 메지지가 나온다면 Python 및 라이브러리의 버전을 확인해보자. 

```
FAILED (KNOWNFAIL=276, SKIP=922, errors=326, failures=42)
```


### 참고 링크 
* [S.7.12. SciPyの設定 | R Financial & Marketing Library](http://itbc-world.com/home/rfm/r%E3%81%AE%E8%A8%AD%E5%AE%9A/scipy%E3%81%AE%E8%A8%AD%E5%AE%9A/)(일본어)
* [Errors in tests of Scipy · Issue #12 · Homebrew/homebrew-python · GitHub](https://github.com/Homebrew/homebrew-python/issues/12)

