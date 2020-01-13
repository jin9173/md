---
title: pyenv와 virtualenv
date: 2019-12-18 08:57:50
categories: etc
---
[pyenv와 virtualenv 설치](https://lhy.kr/configuring-the-python-development-environment-with-pyenv-and-virtualenv)

**pyenv**: 파이썬 버전 관리 시스템

**virtualenv**, **pyenv-virtualenv**: 프로젝트별로 설치된 패키지들간의 충돌을 막아주기 위한 가상환경 (pyenv-virtualenv는 virtualenv보다 좀 더 밀접하게 사용 가능)

## pyenv와 virtualenv를 사용한 파이썬 개발환경 구성

#### `brew`설치

~~~bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
~~~

#### `pyenv`와 `pyenv-virtualenv`설치

~~~bash
$ brew install pyenv
$ brew install pyenv-virtualenv
~~~

~~~bash
# 설치 후 pyenv관련 설정을 shell설정 파일에 추가
$ vi ~/.zshrc

# 가장 아래쪽에 아래 문장 추가
export PYENV_PATH=$HOME/.pyenv
if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi
if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi
~~~

~~~bash
# 설치 후 pyenv 확인
$ pyenv
~~~

#### python설치

~~~bash
$ brew install readline xz
~~~

#### pyenv로 관리되는 파이썬 설치 후, 시스템 전역에서 사용할 파이썬 설정

~~~bash
# pyenv로 파이썬 설치
$ pyenv install --list
$ pyenv install 3.7.5
~~~

~~~bash
# pyenv로 관리되는 파이썬 버전 확인
$ pyenv versions
~~~

~~~bash
# 전역에서 사용할 파이썬 설정
$ pyenv global 3.7.5
$ pyenv versions
~~~

~~~bash
# 현재 사용되는 파이썬 버전 확인
$ python --version
~~~

#### `pyenv-virtualenv`를 사용한 가상환경 관리

~~~bash
# 가상환경 sample-env생성
$ pyenv virtualenv <version> <env_name>
$ pyenv virtualenv 3.7.5 sample-env
~~~
~~~bash
# 가상환경을 프로젝트 폴더에 지정
$ cd ~/
$ mkdir projects
$ cd projects
$ mkdir sample-project
$ cd sample-project
~~~

~~~bash
# 가상환경 적용
$ sample-project> pyenv local sample-env
# 로컬에서 가상환경이 적용되었는지 확인
$ (sample-env) ➜ sample-project> pyenv versions
# 가상환경의 패키지 목록 확인
$ (sample-env) ➜ sample-project> pip list
~~~

