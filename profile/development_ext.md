# 확장된 fatima 프로세스 개발

## 목차

1. [메시지 처리](#1-메시지-처리)
1. [HA aware 처리](#2-ha-aware-처리)
1. [PS aware 처리](#3-ps-aware-처리)
1. [UI(User Interactive) 프로그램 만들기](#4-ui--user-interactive--프로그램-만들기)
   1. [Goland 에서 mytool 프로젝트 생성](#41-goland-에서-mytool-프로젝트-생성)
   1. [라이브러리 의존성 추가](#42-라이브러리-의존성-추가)
   1. [기본 코드 작성](#43-기본-코드-작성) 
   1. [ui.xml 파일 작성](#44-uixml-파일-작성)
   1. [빌드와 배포](#45-빌드와-배포)
   1. [실행](#46-실행)


## 1. 메시지 처리
   프로세스 개발시 알람이나 이벤트 메시지를 손쉽게 처리할 수 있다.

다음은 helloworld 예제에서 `maintain.go` 소스 부분이다.

```go
func (m *Maintain) Initialize() bool {
    log.Info("Maintain Initialize()")

    err := lib.RegistCronJob(m.fatimaRuntime, "sayHello", sayHelloJob)
    if err != nil {
        log.Error("fail to regist cron : %s", err.Error())
        m.fatimaRuntime.GetSystemNotifyHandler().SendAlarm(monitor.AlarmLevelWarn, "fail to regist cron...")
    }
 
    return true
}

func (m *Maintain) Bootup() {
   log.Info("Maintain Bootup()")
   m.fatimaRuntime.GetSystemNotifyHandler().SendEvent("I'm bootup : %s", time.Now())
}
```
7번 라인은 알람 메시지를 보내는 예제 코드 부분이다.

15번 라인은 이벤트 메시지를 보내는 코드이다.

이렇게 내가 작성하는 각 프로세스들의 메시지들은 saturn 프로세스로 자동 취합되어 일관되게 사용자에게 전달된다.

## 2. HA aware 처리
   Fatima 패키지는 Active/Standby 등의 HA 상태를 가질 수 있다. 내가 만드는 Fatima component가 이러한 HA 상태를 감지하는 역할을 하고 싶다면 아래와 같이 정의된 FatimaSystemHAAware 인터페이스를 추가한다. 
   
>FatimaSystemHAAware
>```go
>type FatimaSystemHAAware interface {
>   SystemHAStatusChanged(newHAStatus HAStatus)
>}
>```
다음은 `maintain.go`의 예제이다.

```go
type Maintain struct {
    fatimaRuntime   fatima.FatimaRuntime
}

func (m *Maintain) Initialize() bool {
   log.Info("Maintain Initialize()")
   log.Info("current system HA : %s", m.fatimaRuntime.GetSystemStatus().GetHAStatus())
   return true
}

func (m *Maintain) Bootup() {
    log.Info("Maintain Bootup()")
}

func (m *Maintain) SystemHAStatusChanged(newHAStatus monitor.HAStatus) {
    log.Warn("System HA changed to %s", newHAStatus)
}
```
7번 라인을 보면 기본적으로 fatimaRuntime을 통해 언제든지 현재의 패키지 HA 상태값을 구할 수 있다.

15번 라인을 보면 Maintain 에 SystemHAStatusChanged 함수를 추가해준 모습이 보인다. 패키지의 HA가 변경되면 호출되게 된다.

소스를 컴파일 한 후 배포하여 아래와 같이 시스템 상태를 변경해서 테스트 해 본다.

>현재 HA 상태 출력
>```go
>macbook:bin djinch$ lcha
>STANDBY
>```


>helloworld 프로세스 가동 로그
>```
>2023-06-23 16:21:17.636 DEBUG [onfig.checkFileAvailable():141] file [application.local.properties] does not exist
>2023-06-23 16:21:17.636 DEBUG [onfig.checkFileAvailable():141] file [helloworld.properties] does not exist
>2023-06-23 16:21:17.636 INFO  [g.NewPropertyConfigReader():54] using properties file : application.properties
>2023-06-23 16:21:17.636 INFO  [t.f.b.process.Initialize():272] change log level : INFO
>2023-06-23 16:21:17.637 INFO  [e.h.e.maintain.Initialize():35] Maintain Initialize()
>2023-06-23 16:21:17.637 INFO  [e.h.e.maintain.Initialize():36] current system HA : Standby
>2023-06-23 16:21:17.637 INFO  [    e.h.e.maintain.Bootup():41] Maintain Bootup()
>2023-06-23 16:21:17.637 INFO  [.v.t.f.i.component.func1():105] process start up successfully
>```
6번 라인 로그를 통해 내가 추가했던 소스에서 출력한 HA 상태 관련 메시지를 볼 수 있다.

>HA 상태 변경
>```shell
>macbook:bin djinch$ lcha set active
>set to ACTIVE
>```


>helloworld 로그
>```
>2023-06-23 16:21:17.637 INFO  [e.h.e.maintain.Initialize():35] Maintain Initialize()
>2023-06-23 16:21:17.637 INFO  [e.h.e.maintain.Initialize():36] current system HA : Standby
>2023-06-23 16:21:17.637 INFO  [    e.h.e.maintain.Bootup():41] Maintain Bootup()
>2023-06-23 16:21:17.637 INFO  [.v.t.f.i.component.func1():105] process start up successfully
>2023-06-23 16:21:17.637 INFO  [.t.f.i.core.pprofService():147] starting pprof service. address=:6060
>2023-06-23 16:21:18.638 WARN  [t.f.i.monitoring.Process():103] fatima proc log level : DEBUG
>2023-06-23 16:21:36.637 WARN  [ing.SystemHAStatusChanged():65] new HA Status detected : Active
>2023-06-23 16:21:36.637 WARN  [ain.SystemHAStatusChanged():45] System HA changed to Active
>```

7번 8번 라인을 보면 패키지 상태를 ACTIVE로 변경해서 출력된 로그임을 알 수 있다. 7번 라인은 기본적으로 fatima framework에서 출력한 것이고 8번은 내가 작성한 maintain.go 에서 출력한 로그이다.

## 3. PS aware 처리
   Fatima 패키지는 Primary/Secondary 등의 PS 상태를 가질 수 있다. 원리는 HA 원리와 동일하다. FatimaSystemPSAware 인터페이스를 추가한다.

>FatimaSystemHAAware
>```go
>type FatimaSystemPSAware interface {
>   SystemPSStatusChanged(newPSStatus PSStatus)
>}
>```
다음은 `maintain.go`의 예제이다.
```go
type Maintain struct {
    fatimaRuntime   fatima.FatimaRuntime
}

func (m *Maintain) Initialize() bool {
   log.Info("Maintain Initialize()")
   log.Info("current system PS : %s", m.fatimaRuntime.GetSystemStatus().GetPSStatus())
   return true
}

func (m *Maintain) Bootup() {
    log.Info("Maintain Bootup()")
}

func (m *Maintain) FatimaSystemPSAware(newPSStatus monitor.PSStatus) {
    log.Warn("System PS changed to %s", newPSStatus)
}
```

7번 라인을 보면 기본적으로 fatimaRuntime을 통해 언제든지 현재의 패키지 PS 상태값을 구할 수 있다.

16번 라인을 보면 Maintain 에 SystemPSStatusChanged 함수를 추가해준 모습이 보인다. 패키지의 PS가 변경되면 호출되게 된다.

소스를 컴파일 한 후 배포하여 아래와 같이 시스템 상태를 변경해서 테스트 해 본다.

>현재 PS 상태 출력
>```shell
>macbook:bin djinch$ lcps
>PRIMARY
>```

>helloworld 프로세스 가동 로그
>```
>2023-06-23 16:28:59.429 INFO  [e.h.e.maintain.Initialize():35] Maintain Initialize()
>2023-06-23 16:28:59.429 INFO  [e.h.e.maintain.Initialize():36] current system PS : Primary
>2023-06-23 16:28:59.429 INFO  [    e.h.e.maintain.Bootup():41] Maintain Bootup()
>2023-06-23 16:28:59.429 INFO  [.v.t.f.i.component.func1():105] process start up successfully
>```
2번 라인 로그를 통해 내가 추가했던 소스에서 출력한 PS 상태 관련 메시지를 볼 수 있다.

>PS 상태 변경
>```shell
>macbook:bin djinch$ lcps set secondary
>set to SECONDARY
>```

>helloworld 로그
>```
>2023-06-23 16:28:59.429 INFO  [.v.t.f.i.component.func1():105] process start up successfully
>2023-06-23 16:28:59.429 INFO  [.t.f.i.core.pprofService():147] starting pprof service. address=:6060
>2023-06-23 16:29:00.431 WARN  [t.f.i.monitoring.Process():103] fatima proc log level : DEBUG
>2023-06-23 16:30:46.428 WARN  [ing.SystemPSStatusChanged():72] new PS Status detected : Secondary
>```
4번 라인을 보면 패키지 상태가 Secondary로 변경된걸 감지하고 로그를 출력한걸 알 수 있다.



## 4. UI(User Interactive) 프로그램 만들기
   Fatima 에서 UI 프로그램이란 User Interactive 프로그램을 뜻한다.

즉, 패키지 내에서 항상 실행되어 있는 프로세스의 성격이라기 보다 backend 서버에서 일종의 툴처럼 그때그때 실행하고 종료하는 성격의 프로그램이다.

UI 프로그램은 메뉴를 XML 형태로 정의하고 사용자가 입력한 값에 따라서 관련된 함수(function)를 자동 실행(reflection) 해 준다.

### 4.1 Goland 에서 mytool 프로젝트 생성


### 4.2 라이브러리 의존성 추가
>라이브러리 의존성 추가
>```shell
>MN03:mytool djinch$ govendor init
>MN03:mytool djinch$ govendor fetch -tree throosea.com/fatima
>MN03:mytool djinch$ govendor fetch throosea.com/log
>```

### 4.3 기본 코드 작성

비어 있는 application.properties 파일을 생성해 둔다. 향후 각종 properties 를 설정하여 사용할 목적이다.

mytool 프로젝트에 `mytool.go` 파일을 생성하고 아래와 같이 main 코드를 작성한다.

```go
package main

import (
   "throosea.com/fatima/runtime"
   "throosea.com/fatima"
   "log"
)

func main() {
   ui := &UserInteraction{}
   ui.fatimaRuntime = runtime.GetUserInteractiveFatimaRuntime(ui)
   ui.fatimaRuntime.Run()
}

type UserInteraction struct {
    fatimaRuntime   fatima.FatimaRuntime
}

func (ui *UserInteraction) SayHello(name string) {
   log.Printf("hello %s !!!\n", name)
}
```
11번 라인이 그간 작성했던 코드와 약간 다른걸 볼 수 있다.

위의 코드에서 SayHello 함수는 최종적으로 reflection 되어 호출될 함수이다.

### 4.4 ui.xml 파일 작성
xml 파일명 규칙은 "**프로세스이름.ui.xml**" 이다. 따라서 파일 이름을 `mytool.ui.xml` 로 생성한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<prompt>
    <common>
        <keyword value="b">Back to upper stage</keyword>
        <keyword value="r">Re-execute last command</keyword>
        <keyword value="q">Quit Interaction</keyword>
    </common>

    <startup>
        <item category="text">도구 프로그램</item>
        <item category="menu" key="1" sig="menuSay">인사하기 메뉴</item>
    </startup>
 
    <menuSay>
        <item category="text">인사하기 메뉴</item>
        <item category="call" key="1" sig="SayHello">인사를 해 봅시다</item>
    </menuSay>
 
    <SayHello>
        <input type="string" default="your name">당신의 이름은?</input>
    </SayHello>
</prompt>
```
참고로 input type이 call 일 경우는 관련 함수를 호출한다는 의미이다.

함수 호출시 지원 파라미터는 string, int 등이 존재한다.

만약 함수 호출시 입력 파라미터가 필요 없다면 굳이 별도 정의하지 않아도 된다. 예를 들어 위의 예제에서 SayHello가 파라미터를 받지 않는다면 SayHello 태그를 정의하지 않아도 된다.

호출될 함수(SayHello)는 Fatima framework 에서 GetUserInteractiveFatimaRuntime()로 넘기는 파라미터를 통해 리플렉션 될 것이다.

여기까지 진행했다면 프로젝트 구조는 대략 아래와 같을 것이다.

### 4.5 빌드와 배포
```shell
SKTX1100282MN03:mytool 1100282$ go install
SKTX1100282MN03:mytool 1100282$ gofar mytool
binpath : /Users/1100282/IronForge/gowork/bin/mytool
target : /Users/1100282/IronForge/gowork/far/mytool/mytool.far
src : /Users/1100282/IronForge/gowork/bin/mytool
src : /Users/1100282/IronForge/gowork/src/example/mytool/application.properties
src : /Users/1100282/IronForge/gowork/src/example/mytool/mytool.ui.xml
srcDir : /Users/1100282/IronForge/gowork/src/example/mytool
Finished far zip : /Users/1100282/IronForge/gowork/far/mytool/mytool.far
SKTX1100282MN03:mytool 1100282$ rodeploy /Users/1100282/IronForge/gowork/far/mytool/mytool.far
2023-06-23 16:46:58 (Asia/Seoul)
far name : mytool.far (3486450 bytes). target : 1 juno enqueued
```
참고로 UI 프로그램은 항상 실행되는 형태가 아니기 때문에 roproc 등을 통해 패키지에 프로세스를 추가할 필요가 없다.

#### 4.5.1 Rollback

이전 형상으로 되돌리려면 아래와 같이 수행한다

```shell
rostop process_name
lcproc process_name version
lcproc process_name version R018
rostart process_name

> rostop batsupport
> lcproc batsupport version
batsupprot revisions...
R018
R017
R016
> lcproc batsupport version R018
> rostart batsupport
```

### 4.6 실행
패키지 배포는 $FATIMA_HOME/app/mytool 에 위치해 있을 것이므로 아래와 같이 실행한다.

>mytool 실행 모습
>```shell
>MN03:mytool djinch$ $FATIMA_HOME/app/mytool/mytool
> 
>================
>도구 프로그램
>[1] 인사하기 메뉴
> 
>-------------
>[b] Back to upper stage
>[r] Re-execute last command
>[q] Quit Interaction
>================
>Enter Menu : 1
> 
>================
>인사하기 메뉴
>[1] 인사를 해 봅시다
> 
>-------------
>[b] Back to upper stage
>[r] Re-execute last command
>[q] Quit Interaction
>================
>Enter Menu : 1
>Enter 당신의 이름은? (default : your name) = Dave
>2018/08/22 18:00:52 hello Dave !!!
> 
>================
>인사하기 메뉴
>[1] 인사를 해 봅시다
> 
>-------------
>[b] Back to upper stage
>[r] Re-execute last command
>[q] Quit Interaction
>================
>Enter Menu : q
>MN03:mytool djinch$
>```

