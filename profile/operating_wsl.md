# Windows 환경에서 WSL을 이용하여 Fatima 환경 구축


현재 `Fatima` framework는 `Linux`와 `Mac`환경만 설치 할 수 있고 `Windows` 환경에서는 불가하여 `WSL(Windows Subsystem for Linux)`를 이용하여 `Windows` 환경에서도 개발 테스트 및 배포가 가능한 환경을 구성하기 위한 문서입니다. `GoLand` 등의 개발 IDE 및 git source는 `Windows` 환경 하에 있고 `Fatima` 실행 환경만 `WSL`을 이용하여 구축하는 방법에 대한 소개입니다.

*   1 [1 WSL 소개](#1-WSL-소개)
*   2 [2 WSL 설치](#[hardBreak]2-WSL-설치)
    *   2.1 [2-1 WSL 사용을 위한 WINDOWS 버전 확인](#2-1-WSL-사용을-위한-WINDOWS-버전-확인)
    *   2.2 [2-2 WSL 설치](#2-2-WSL-설치)
        *   2.2.1 [2-2-1 WSL 사용을 위한 Windows 설정을 활성화 합니다.](#2-2-1-WSL-사용을-위한-Windows-설정을-활성화-합니다.)
        *   2.2.2 [2-2-2 Microsoft Store 에서 배포판 설치](#2-2-2-Microsoft-Store-에서-배포판-설치)
        *   2.2.3 [2-2-3 계정 및 WSL 설정](#2-2-3-계정-및-WSL-설정)
*   3 [3 WSL Golang 설치](#3-WSL-Golang-설치)
    *   3.1 [3-1 GoLang 설치](#3-1-GoLang-설치)
    *   3.2 [3-2 GoLang 환경 설정](#3-2-GoLang-환경-설정)
*   4 [4 WSL Fatima 설치](#4-WSL-Fatima-설치)
    *   4.1 [4-1 Fatima 다운로드 및 설치](#4-1-Fatima-다운로드-및-설치)
    *   4.2 [4-2 Fatima 환경 설정](#4-2-Fatima-환경-설정)
    *   4.3 [4-3 Fatima 실행](#4-3-Fatima-실행)
*   5 [5 GoLand Terminal 실행을 WSL로 변경](#5-GoLand-Terminal-실행을--WSL로-변경)
*   6 [6 Nerd 간지의 WSL 사용을 위한 eDEX-UI 설치](#6-Nerd-간지의-WSL-사용을-위한-eDEX-UI-설치)

1 WSL 소개
========

`WSL` 은 `Windows Subsystem for Linux`의 약자로 윈도우의 가상 머신인 `Hyper-v` 를 이용하여 `Windows` native환경에서 Application을 구동하는 것처럼 리눅스를 사용할수 있게 도와줍니다.

기존의 가상머신에 `Linux` 를 설치하는 대비 오버헤드가 적고, `cmd / powershell / windows terminal` 등을 이용하여 별도의 `ssh` 접속 없이 `shell`에 접근 가능하므로 빠릿빠릿한 실행 환경을 제공합니다. 그리고 기본적으로 `Windows`의 file system이 mount 되어 `WSL` 환경에서 Windows file 을 native 환경 처럼 사용할 수 있습니다.


> WSL 공식 소개 페이지 : [https://docs.microsoft.com/ko-kr/windows/wsl/about](https://docs.microsoft.com/ko-kr/windows/wsl/about "https://docs.microsoft.com/ko-kr/windows/wsl/about")


2 WSL 설치
===========

해당 내용은 이미 `WSL`을 설치하여 사용중이신 부분이라면 스킵하시면 됩니다.

2-1 WSL 사용을 위한 WINDOWS 버전 확인
----------------------------

`WSL` 설치를 위해서는 아래와 같은 윈도우즈 환경이 필요합니다.

*   WSL1 : Windows 10 PRO 1607 이상

*   WSL2 : Windows 10 HOME 2004 이상



`Windows 10` `2004` 이 나오기 전까지는 `Hyper-v` 를 구동 할 수 있는 `PRO` 이상 버전에서만 사용 가능 했지만, `2004` 이후 `WSL2` 등장과 함께 `Hyper-v`를 활성화 하지 않아도 내부적으로 이용하는 것으로 변경되어 `HOME` 버전에서도 사용가능하게 되었습니다.

특히, `WSL2`를 이용하여 `Hyper-v` 없이 `docker` native 실행이 가능하므로( `WSL2` 지원 없을 경우 `HOME` 버전에서는 `Virtual box` 를 이용하여 구동) `HOME` 버전을 사용하는 개발자 분들은 `2004` 이상을 사용하는 것을 권장합니다.

> WSL1 vs WSL2 비교 : [https://docs.microsoft.com/ko-kr/windows/wsl/compare-versions](https://docs.microsoft.com/ko-kr/windows/wsl/compare-versions "https://docs.microsoft.com/ko-kr/windows/wsl/compare-versions")

2-2 WSL 설치
----------

### 2-2-1 WSL 사용을 위한 Windows 설정을 활성화 합니다.

![](https://api.media.atlassian.com/file/7545e72b-efc9-443f-a6dd-55c0b4dba292/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=547&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

위의 설정은 `cmd / PowerShell` 등 명령 Prompt를 관리자 권한 으로 실행하여 다음의 명령으로도 활성화 가능합니다. ([DISM 명령어 관련 공식 문서](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/what-is-dism "https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/what-is-dism"))

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart 
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

### 2-2-2 Microsoft Store 에서 배포판 설치

Microsoft Store를 실행하여 WSL로 검색합니다. ubuntu, centos 등부터 alpine 까지 다양한 Linux 배포판이 존재합니다. 원하는 배포판을 선택하여 설치합니다. 여기서는 `ubuntu 20.04 LTS`를 선택하여 진행합니다.

![](https://api.media.atlassian.com/file/d047c77c-0e02-4ae8-ab22-e623113a241f/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=849&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

### 2-2-3 계정 및 WSL 설정

![](https://api.media.atlassian.com/file/1cb6387a-40bd-418c-9af1-fe7537206448/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=452&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

설치가 완료 된 후 실행 버튼을 누르면 계정 설정을 진행합니다.

![](https://api.media.atlassian.com/file/85667974-73aa-418a-9621-01e0a73fdbd4/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=397&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

이 계정은 Windows와 별도의 계정이므로 원하는 값으로 설정합니다.

![](https://api.media.atlassian.com/file/7257b884-32e0-425e-bed1-1c6e0c25b48c/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=440&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

`Ubuntu 20.04 LTS` 설치가 완료되었습니다.

![](https://api.media.atlassian.com/file/e5d81708-cf42-41d0-9231-521085abac7c/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=421&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

위와 같이 `Windows`의 파일 시스템이 기본으로 마운트 됩니다.

`Windows prompt` 에서 다음 명령어로 설치된 배포 버전을 확인 할 수 있습니다.

```
wsl -l -v
```

![](https://api.media.atlassian.com/file/2c957383-e8b8-4a94-b1e2-cf3aa107216f/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=233&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

위 화면은 `docker` 가 미리 설치되어있을 경우 `docker`를 위한 `WSL` 배포판이 미리 설치된 화면입니다.`*` 표시는 `wsl.exe` 명령어 실행 시 기본으로 실행되는 배포판에 대한 표시입니다. `ubuntu`를 사용할 것이므로 `ubuntu`가 기본 배포판이 아니라면 다음 명령어를 실행하여 변경해 줍니다.

```
wsl --set-default Ubuntu-20.04
```

먄약 `ubuntu` 의 `WSL` 버전이 `1`로 설치되었다면 `2` 로 변경하고 변경하고 `default version`을 `2`로 변경해줍니다.

```
wsl --set-version Ubuntu-20.04 2 wsl --set-default-version 2
```

![](https://api.media.atlassian.com/file/a2f28b8f-7726-40c4-9eb6-3d91389a6afa/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=88&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

![](https://api.media.atlassian.com/file/b12eb8a8-2c92-44be-aa79-4c18fd9e0fd7/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=162&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

이제 window prompt 에서 `wsl.exe` 실행 시 `ubuntu` shell 이 실행됩니다.  
`apt-get` 을 이용하여 패키지를 최신으로 업데이트 해줍니다.

```
sudo apt-get update 
sudo apt-get -y upgrade
```

`WSL` 준비가 완료되었습니다.

WSL 커널을 최신 버전으로 update 하기 위해서는 아래 문서를 참조하면 됩니다.

[https://docs.microsoft.com/ko-kr/windows/wsl/wsl2-kernel](https://docs.microsoft.com/ko-kr/windows/wsl/wsl2-kernel "https://docs.microsoft.com/ko-kr/windows/wsl/wsl2-kernel")

3 WSL Golang 설치
===============

여기서 부터는 일반적인 Linux 환경에서의 구축과 동일합니다.

3-1 GoLang 설치
-------------

`ubuntu` 의 `apt-get`을 이용할 경우 현재 `GoLang` 버전이 `1.13`이므로 최신 버전(이 문서 작성 시점 `1.15.5`)을 사용하기 위해 [GoLonag 다운로드 사이트](https://Golang.org/dl/ "https://Golang.org/dl/")에서 직접 다운받아 사용합니다.

```
# GoLang 소스 다운로드 
wget https://dl.google.com/go/go1.15.5.linux-amd64.tar.gz 
# 압축 해제 
sudo tar -xvf go1.15.5.linux-amd64.tar.gz 
# 디렉토리 이동 
sudo mv go /usr/local
```

3-2 GoLang 환경 설정
----------------

`Golang` 사용시 필요한 환경 변수 설정울 위해 하고 shell profile에 수정합니다. `ubuntu` 의 경우 `.bash_profile` 이 아닌 `.profile` 을 사용합니다.

```
vi ~/.profile
```

제일 하단에 아래 내용을 붙여 넣습니다. `GOPATH` 에는 윈도우 마운트 경로( `/mnt/드라이브/~~~` )를 이용하여 fatima 관련 소스들이 위차한 경로로 설정합니다. 저같은 경우는 `D:\work\git\flo-backend` 에 소스들이 위치하고 있어 `/mnt/d/work/git/flo-backend` 로 설정하였습니다.

```
################################# 
# Go Env 
export GOROOT=/usr/local/go 
export GO111MODULE=on 
export PATH=$PATH:$GOROOT/bin 
export GOPATH={src path} <== fatima 소스가 위치한 경로를 지정 
export PATH=$PATH:$GOPATH/bin
```

해당 내용을 추가 했으면 `source` 명령어를 이용하여 현재 shell에 적용하고 go 명령어가 잘 동작 하는지 확인합니다.

```
source ~/.profile 

go version
```

다음과 같이 나오면 잘 설치 된 것입니다.

![](https://api.media.atlassian.com/file/de2e9899-e9dd-4565-a3fc-a2e3771662f9/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=123&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

4 WSL Fatima 설치
===============

다음 문서를 참고하여 `Fatima` 실행 환경을 구성합니다.[1\. Fatima 실행환경 구축](https://music-flo.atlassian.net/wiki/spaces/serverdevteam/pages/536182921)  
위 문서는 `macOS` 환경 기준이므로 `DARWIN AMD64`를 사용하지만 `WSL`은 순수 `Linux` 환경이므로 `LINUX AMD64`를 사용합니다.

4-1 Fatima 다운로드 및 설치
--------------------

```
# download 
wget https://github.com/fatima-go/fatima-download/blob/main/fatima-package.linux-arm64.tar.gz
# 압축해제 
gzip -cd fatima_linux_amd64.tar.gz | tar xvf -
```

4-2 Fatima 환경 설정
----------------

`Fatima` 사용시 필요한 환경 설정을 위해 다시 shell profile을 수정합니다.

```
vi ~/.profile
```

다음 내용을 붙여놓고 `source ~/.profile` 을 실행합니다. 제 경우는 FATIMA\_HOME 경로가 을 home 디렉토리 바로 아래에 설치 했으므로 `/home/purewsl/FATIMA_HOME` 와 같이 설정했습니다.

```
########################### 
# FATIMA 
export FATIMA_HOME={fatima home path} 
export FATIMA_PROFILE=local 
export PATH=.:$PATH:$FATIMA_HOME/bin 
export LD_LIBRARY_PATH=.:$LD_LIBRARY_PATH:$FATIMA_HOME/lib 

################################# 
# ALIAS 
alias gofa="cd $FATIMA_HOME" 
alias golog="cd $FATIMA_HOME/log" 
alias goapp="cd $FATIMA_HOME/app" 
alias goconf="cd $FATIMA_HOME/conf" 
alias godata="cd $FATIMA_HOME/data" 
alias gobin="cd $FATIMA_HOME/bin" 
alias golinux="GOOS=linux GOARCH=amd64 go install -ldflags='-s -w'" 
alias golinuxc="CC=x86_64-pc-linux-gcc GOOS=linux GOARCH=amd64 CGO_ENABLED=1 go install -ldflags='-s -w'" 
alias so="source ~/.profile"
```

`source` 명령어를 사용하여 shell 에 적용하고 `export` 명령을 사용하여 환경 변수가 잘 적용 되었는지 확인합니다.

```
source 
~/.profile export
```

![](https://api.media.atlassian.com/file/c19d6c12-6966-4a4c-9f4d-66febb5a6fa1/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=477&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

4-3 Fatima 실행
-------------

`startro` 를 이용하여 Fatima 프로세스를 실행하고 `rodis` 명령어로 프로세스를 확인합니다.  
아래와 같이 작동하면 성공적으로 설치가 된 것입니다.

![](https://api.media.atlassian.com/file/fc7a79b4-d2d5-436b-abc1-0ee6d4022b0b/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=425&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

5 GoLand Terminal 실행을 WSL로 변경
=============================

`GoLand` 환경 설정을 열어 `Terminal` 항목을 검색한 후 `Shell Path` 항목을 다음과 같이 변경합니다.

```
C:\Windows\System32\wsl.exe
```

![](https://api.media.atlassian.com/file/a9f88933-9706-42e7-badd-5d55a5b5aaf0/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=674&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

이제 `GoLand` 에서 `Terminal` 실행 시 `WSL shell`을 이용하게 됩니다.

![](https://api.media.atlassian.com/file/470f93f6-6a04-45a4-97f7-0d255b5e4789/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=607&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

이제 `golinux`, `gofar`, `rodeploy` 명령어등을 사용하여 배포를 진행하면 됩니다.

`Linux` 환경이므로 `gofar` 명령어 수행시 마지막에 `linux_amd64` 는 생략해도 됩니다.

```
# rocontext 명령어로 context 선택 
rocontext use dev 

# build 
golinux 

# far 패키징 
gofar evttrack 

# 대상 환경 배포 
rodeploy /mnt/d/work/git/flo-backend/far/evttrack/evttrack.far
```

![](https://api.media.atlassian.com/file/30689def-98c8-43dc-bb26-510c038049c9/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=840&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

> 다음 문서들을 참조하여 Fatima 개발 및 배포를 진행합니다.
>
> *   Fatima 명령어 및 관리 : [2\. 기본적인 Fatima 패키지 관리](https://music-flo.atlassian.net/wiki/spaces/serverdevteam/pages/263302364)
>
> *   배포를 위한 스크립트 : [https://music-flo.atlassian.net/wiki/spaces/serverdevteam/pages/263305293](https://music-flo.atlassian.net/wiki/spaces/serverdevteam/pages/263305293)



6 Nerd 간지의 WSL 사용을 위한 eDEX-UI 설치
================================

![](https://api.media.atlassian.com/file/da9ed883-ea7a-4980-908c-b5e9f3e0208f/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=427&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

![](https://api.media.atlassian.com/file/9aea25af-05a9-48f9-9e80-04905f748bba/image?allowAnimated=true&client=a2663cee-d7f5-4af0-b3ff-23e3ec2f926a&collection=contentId-877133939&height=957&max-age=2592000&mode=full-fit&token=eyJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJhMjY2M2NlZS1kN2Y1LTRhZjAtYjNmZi0yM2UzZWMyZjkyNmEiLCJhY2Nlc3MiOnsidXJuOmZpbGVzdG9yZTpjb2xsZWN0aW9uOmNvbnRlbnRJZC04NzcxMzM5MzkiOlsicmVhZCJdfSwiZXhwIjoxNjg3NDE5MzY3LCJuYmYiOjE2ODc0MTY0ODd9.sxcmZZ9nBDDblZ8aiYbr56eIccUrVcZ9FVGsEcePVcM&width=760)

[https://github.com/GitSquared/edex-ui](https://github.com/GitSquared/edex-ui)

시연 영상 : [https://www.youtube.com/watch?v=sYbuWzsfiv4](https://www.youtube.com/watch?v=sYbuWzsfiv4 "https://www.youtube.com/watch?v=sYbuWzsfiv4")
