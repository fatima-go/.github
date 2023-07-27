# 설치하기

## 목차
1. [운영 패키지 다운로드](#1-운영-패키지-다운로드)
2. [환경변수 설정](#2-환경변수-설정)
3. [fatima 패키지 실행](#3-fatima-패키지-실행)
4. [fatima 운영 환경 맛보기](#4-fatima-운영-환경-맛보기)


## 1. 운영 패키지 다운로드

| Platform                                                                                                 | URL                                                                                           |
|----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| [linux-arm64](https://github.com/fatima-go/fatima-download/raw/main/fatima-package.linux-arm64.tar.gz)   | wget https://github.com/fatima-go/fatima-download/raw/main/fatima-package.linux-arm64.tar.gz  |
| [linux-amd64](https://github.com/fatima-go/fatima-download/raw/main/fatima-package.linux-amd64.tar.gz)   | wget https://github.com/fatima-go/fatima-download/raw/main/fatima-package.linux-amd64.tar.gz  |
| [darwin-arm64](https://github.com/fatima-go/fatima-download/raw/main/fatima-package.darwin-arm64.tar.gz) | wget https://github.com/fatima-go/fatima-download/raw/main/fatima-package.darwin-arm64.tar.gz |
| [darwin-amd64](https://github.com/fatima-go/fatima-download/raw/main/fatima-package.darwin-amd64.tar.gz) | wget https://github.com/fatima-go/fatima-download/raw/main/fatima-package.darwin-amd64.tar.gz |

(본 문서의 설명은 macos에서 진행하므로 Darwin arm64 버전을 다운로드 받아서 설치하고 설명한다)

다운로드 후 적당한 폴더에 압축 해제

```shell
macbook:throosea djinch$ wget https://github.com/fatima-go/fatima-download/raw/main/fatima-package.darwin-arm64.tar.gz
--2023-06-23 12:14:05--  https://github.com/fatima-go/fatima-download/raw/main/fatima-package.darwin-arm64.tar.gz
Resolving github.com (github.com)... 20.200.245.247
Connecting to github.com (github.com)|20.200.245.247|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://raw.githubusercontent.com/fatima-go/fatima-download/main/fatima-package.darwin-arm64.tar.gz [following]
--2023-06-23 12:14:05--  https://raw.githubusercontent.com/fatima-go/fatima-download/main/fatima-package.darwin-arm64.tar.gz
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.108.133, 185.199.109.133, 185.199.110.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.108.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 49513244 (47M) [application/octet-stream]
Saving to: ‘fatima-package.darwin-arm64.tar.gz’

fatima-package.darwin-arm64.tar.gz        100%[===================================================================================>]  47.22M  23.3MB/s    in 2.0s

2023-06-23 12:14:10 (23.3 MB/s) - ‘fatima-package.darwin-arm64.tar.gz’ saved [49513244/49513244]

macbook:throosea djinch$ gzip -cd fatima-package.darwin-arm64.tar.gz | tar xvf -
x fatima-package/
x fatima-package/app/
x fatima-package/bin/
... (생략 ) ...
macbook:throosea djinch$
```

## 2. 환경변수 설정
Fatima 환경은 $FATIMA_HOME을 비롯해서 몇 가지 사용하는 환경 변수가 존재한다. 아래와 같이 profile 에 적절한 환경 변수를 세팅한다
```shell
###########################
# FATIMA
export FATIMA_HOME=/Users/djinch/IronForge/throosea/fatima-package
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
alias so="source ~/.bash_profile"
```
참고로 환경변수 세팅 내용 중 alias 는 선택 사항이고, 중요한 환경변수 세팅 값들은 아래와 같다.

| 변수명         | 설명                                            |
|-------------|-----------------------------------------------|
| FATIMA_HOME | fatima가 설치된(압축이 풀린) 디렉토리                      |
| FATIMA_PROFILE | fatima가 실행되는 환경 (예, local, dev, stage, prod)  |

## 3. fatima 패키지 실행

Fatima 를 다운로드, 압축 해제 후 환경변수 설정까지 끝났다면 아래와 같이 실행을 해 본다.

`startro` 명령어를 통해 OPM 프로세스 그룹을 실행할 수 있다.

>Fatima 패키지 실행
>```shell
>macbook:throosea djinch$ source ~/.bash_profile
>macbook:throosea djinch$ startro
>STARTING OPM PROGRAMS...
>start all programs? (y/n) y
>check process jupiter
>process jupiter STARTED. pid=21694
>check process juno
>process juno STARTED. pid=21696
>check process saturn
>process saturn STARTED. pid=21697
>```

`rocontext` 명령어를 통해 context를 설정할 수 있다. 터미널에서 아래와 같이 명령어를 입력 후 현재 등록되어 있는 context를 확인해보자.

>```shell
>$ rocontext
>More usage : rocontext -h
>
>CURRENT CONTEXT_NAME    JUPITER                                         USER        TZ
>*       local           http://127.0.0.1:9190                           admin       Asia/Seoul
>```

최초 실행 시 기본적으로 `local` context가 등록되어 있으며, 아래 형태의 명령어를 통해 사용하는 환경에 맞게 개발 및 상용 등 context를 설정할 수 있다.

>```shell
>$ rocontext -l http://127.0.0.1:3000 add test_context
>new context test_context added
>
>$ rocontext
>More usage : rocontext -h
>
>CURRENT CONTEXT_NAME    JUPITER                                         USER        TZ
>*       local           http://127.0.0.1:9190                           admin       Asia/Seoul
>         test_context    http://127.0.0.1:3000                           
>```

다른 context를 사용하기 위해서는 context를 변경해야 한다. 아래 명령어를 통해 사용할 context를 변경할 수 있다.

>```shell
>$ rocontext use test_context
>context test_context actived
>
>$ rocontext
>More usage : rocontext -h
>
>CURRENT CONTEXT_NAME    JUPITER                                         USER        TZ
>         local           http://127.0.0.1:9190                           admin       Asia/Seoul
>*       test_context    http://127.0.0.1:3000        
>```

특정 context의 접속 계정은 아래와 같이 설정할 수 있다.

>```shell
>$ rocontext set test_context
>Enter Username (default admin):
>Enter Password:
>Enter Timezone (default : Asia/Seoul):
>
>----------------------------------------
>username : admin
>password : ......
>timezone : Asia/Seoul
>context test_context set successfully
>```

이후 `rodis` 명령어를 통해 패키지 프로세스 상태를 출력한다. (각 명령의 자세한 설명은 다른 챕터에서 확인할 수 있다.)

`rodis` 명령어는 현재 설정되어 있는 context의 패키지 프로세스들의 상태를 출력한다. 

>```shell
>macbook:throosea djinch$ rodis
>2023-06-23 08:50:22 (Asia/Seoul)
>[basic] macbook.local:default
>+---------+-------+--------+-----+-----+----+---------------------+----+-------+
>|  NAME   |  PID  | STATUS | CPU | MEM | FD |     START TIME      | IC | GROUP |
>+---------+-------+--------+-----+-----+----+---------------------+----+-------+
>| jupiter | 21694 | ALIVE  | 0.1 | 10M | 18 | 2023-06-23 08:50:18 | 0  | OPM   |
>| juno    | 21696 | ALIVE  | 2.0 | 11M | 17 | 2023-06-23 08:50:19 | 0  | OPM   |
>| saturn  | 21697 | ALIVE  | 0.0 | 11M | 18 | 2023-06-23 08:50:20 | 0  | OPM   |
>+---------+-------+--------+-----+-----+----+---------------------+----+-------+
>Total:3 (Alive:3, Dead:0), system is ACTIVE/SECONDARY
>```

Fatima 패키지가 모두 정상적으로 실행된걸 볼 수 있다.

## 4. fatima 운영 환경 맛보기

자, 이제 성공적으로 내 PC에서 fatima 패키지를 띄웠으니, 조금만 더 간단히 살펴보자

먼저 juno 프로세스의 로그를 살펴본다

>juno 프로세스 로그
>```shell
>macbook:throosea djinch$ cd $FATIMA_HOME/log/juno
>macbook:juno djinch$ ls -l
>total 24
>-rw-r--r--  1 djinch  staff  9820  8 23 09:42 juno.log
>macbook:juno djinch$ tail -f juno.log
>2023-06-23 09:42:27.288 INFO  [t.j.e.http_server.Bootup():114] Juno HttpServer Bootup()
>2023-06-23 09:42:27.288 INFO  [t.j.e.system_base.Bootup():122] SystemBase Bootup()
>2023-06-23 09:42:27.288 INFO  [.v.t.f.i.component.func1():105] process start up successfully
>2023-06-23 09:42:27.288 INFO  [tp_server.StartListening():127] called StartListening()
>2023-06-23 09:42:27.288 INFO  [tp_server.StartListening():136] start web guard listening...
>2023-06-23 09:42:28.293 INFO  [    t.j.s.juno.RegistJuno():49] packaging : default/macbook.local/basic
>2023-06-23 09:42:28.293 INFO  [    t.j.s.juno.RegistJuno():60] try to regist. gateway=http://0.0.0.0:9190/juno/regist/v1, endpoint=http://10.0.1.2:9180/FtnZMool/
>2023-06-23 09:42:28.294 INFO  [    t.j.s.juno.RegistJuno():69] response from jupiter : {"system":{"code":200,"message":"success"}}
>
>2023-06-23 09:42:30.773 INFO  [t.j.e.http_server.access():110] 10.0.1.2 -> /FtnZMool/package/dis/v1
>```

현재 실행중인 juno 프로세스의 메모리 상태를 살펴보자.

>juno 프로세스 상태
>```shell
>macbook:juno djinch$ cd $FATIMA_HOME/app/juno
>macbook:juno djinch$ ls -l
>total 18440
>-rw-r--r--  1 djinch  staff      111  3 24  2017 application.properties
>-rwxr-xr-x  1 djinch  staff  9197348  8 23 09:41 juno
>drwxr-xr-x  5 djinch  staff      160  8 23 09:42 proc
>macbook:juno djinch$ cd proc
>macbook:proc djinch$ ls -l
>total 8
>-rw-r--r--  1 djinch  staff    0  8 23 09:42 juno.24151.output
>-rw-r--r--  1 djinch  staff    5  8 23 09:42 juno.pid
>drwxr-xr-x  4 djinch  staff  128  8 23 09:42 monitor
>macbook:proc djinch$ cd monitor
>macbook:monitor djinch$ ls -l
>total 8
>drwxr-xr-x  5 djinch  staff   160  8 23 09:42 history
>-rw-------  1 djinch  staff  3759  8 23 09:44 juno.24151.monitor
>macbook:monitor djinch$ tail -f juno.24151.monitor
>-------------------------------------------------------------------
>
>[2023-06-23 09:44:07]
>[fatima process]
>:: Alloc=3M, Sys=10.6M, TotalGC=4, LastPause=0ms, LastGC=2023-06-23 09:42:30
>-------------------------------------------------------------------
>
>[2023-06-23 09:44:12]
>[fatima process]
>:: Alloc=3M, Sys=10.6M, TotalGC=4, LastPause=0ms, LastGC=2023-06-23 09:42:30
>^C
>macbook:monitor djinch$
>```