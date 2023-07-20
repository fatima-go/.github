# fatima 프로세스 디버깅과 테스트

## 1. 개발 환경 정의

### 1.1 개발 환경 정의

> fatima 프로세스를 위해 다양한 개발 환경을 사용할 수 있습니다. 이 문서에서는 설명의 편의를 위해 다음 상황을 가정합니다.

- 사용 중인 운영 체제: Linux 또는 OS X
- 이미 [golang을 이용한 Fatima 프로세스 개발](/profile/development_start.md)을 통해 개발 환경 설정이 완료된 상태
- 개발 도구: GoLand

### 1.2 프로젝트 구조 정의

> fatima 프로세스는 여러 형태의 구조를 가질 수 있습니다. 이 문서에서는 아래 환경으로 가정합니다.

- 개발할 Fatima 프로젝트의 이름: `hello-world`
- 프로젝트 소스 위치 (`src`): `${GOPATH}/src/hello-world`
- Fatima app 기동 디렉토리 (`Fatima app home`): `${FATIMA_HOME}/app/hello-world` 
- Fatima 내부 `FolderGuide`에 따른 위치:
    - application 파일, 설정, 모니터링을 위한 proc 파일 및 로그 등은 `${FATIMA_HOME}` 하위에 위치
    - 테스트 시에도 동일하게 적용됨
    - 테스트를 위한 파일은 반드시 Fatima app home에 위치해야 함
  
### 개발 환경 설정

Fatima app home에 필요한 파일들은 `gofar`와 `rodeploy` 등을 통해 설치될 수 있습니다. 그러나 코드를 수정하고 매번 배포하는 것은 번거로우므로, 필요한 파일만 먼저 `symlink`를 사용하여 연결해두는 것이 좋습니다.

``` shell
${FATIMA_HOME}/app/hello-world$ ln -s $GOPATH/src/hello-world/cmd/hello-world/* . # 모든 설정 파일을 symlink로 연결합니다.
${FATIMA_HOME}/app/hello-world$ rm hello-world # 불필요한 main entry는 제거합니다.
```

Fatima에서 사용하는 설정은 `application.profile.properties` 외에도 `fatima-package-predefine.properties`가 있습니다.

이 파일은 `${FATIMA_HOME}/conf` 디렉토리에 위치하며, `application.profile.properties`에서 `${}` 문법을 사용하여 보간되는 값을 정의할 수 있습니다. 

예를 들어, `application.dev.properties`에서 `${var.hello.world}`를 참조한다면, `fatima-package-predefine.properties`를 다음과 같이 수정해야 합니다.

**fatima-package-predefine.properties**
``` yaml
# this is fatima package predefine configuration file

var.hello.world=Hello
```

이제 필요한 파일 배치는 완료되었습니다.

## 2. GoLand를 사용한 디버깅

Fatima 프로세스는 필요한 설정 파일을 Fatima app home에서 읽어야 하며, 이를 위해 프로세스 이름을 알아야 합니다. 기본적으로 이 값은 바이너리의 이름을 사용하며, 위 예제 상황에서는 `hello-world`가 됩니다. 그러나 GoLand에서 디버깅을 진행할 경우 바이너리 이름이 `___build_xxx` 형태로 만들어지기 때문에 Fatima 런타임은 app name을 혼동하여 설정 파일을 올바르게 읽지 못할 수 있습니다.

이 문제를 해결하기 위해 다음과 같은 방법을 사용할 수 있습니다:
- 프로그램 인자(`Program arguments`)에 `-debugapp=hello-world` 인자를 추가하여 app name을 올바르게 인식하도록 합니다.
- 필요한 경우 환경 변수(`Environment`)에서 `FATIMA_PROFILE`을 덮어씌울 수 있습니다. 로컬에서 모든 테스트 환경을 갖추지 못했다면 `dev`로 변경하여 테스트할 수도 있습니다.

모든 것이 제대로 설정되었다면 디버그 모드로 실행된 프로세스가 정상적으로 Fatima 런타임을 실행하는 것을 확인할 수 있어야 합니다. 만약 `application.properties` 파일을 찾지 못하거나 `${}` 값이 여전히 존재한다면, 심볼릭 링크가 올바르게 설정되었는지와 `fatima-package-predefine.properties`가 올바르게 구성되었는지 확인해야 합니다.

### 2.1. 로그 보기

Fatima 런타임은 초기화 단계에서 모든 로그를 fatima 로그 디렉토리(`$FATIMA_HOME/log`)에 기록합니다.

- `github.com/fatima-go/fatima-log`는 `${FATIMA_HOME}/log/hello-world/hello-world.log`에 기록됩니다.
- `stdout`으로 출력되는 대상은 `${FATIMA_HOME}/app/hello-world/proc/hello-world.PID.output`에 기록됩니다.

이로 인해 디버깅 과정에서 IDE 콘솔에서는 아무 메시지도 표시되지 않습니다. 따라서 위의 두 파일에 적절하게 `tail`을 사용하여 디버깅을 진행하면 상황을 더 쉽게 파악할 수 있습니다.

다음 명령을 사용하여 tail을 연결할 수 있습니다.

``` shell
${FATIMA_HOME}$ tail -f app/hello-world/proc/*.output log/hello-world/hello-world.log
```

위 명령을 실행하면 `app/hello-world/proc/*.output` 파일과 `log/hello-world/hello-world.log` 파일을 실시간으로 모니터링할 수 있습니다.

### 2.2. deployment.json

프로그램은 정상적으로 실행되지만 기동 시 `deployment.json` 파일을 찾을 수 없다는 경고 메시지가 계속해서 출력될 수 있습니다. 이 파일은 `gofar`를 통해 패키징될 때 빌드 정보를 추적하기 위해 자동으로 생성되는 파일입니다. 디버깅을 진행하는 상황에서는 한 번도 배포하지 않은 경우에도 이 경고 메시지가 지속적으로 나타날 수 있습니다.

이러한 경고 메시지는 프로그램의 동작에는 영향을 주지 않지만, 해당 메시지를 노출시키고 싶지 않다면 다음과 같은 `가짜 파일`을 Fatima app home 디렉토리 하위에 생성합니다.

``` shell
${FATIMA_HOME}/app/hello-world$ echo '{"Process":"hello-world"}' > deployment.json
```

위 명령을 실행하면 `fatima/app/hello-world` 디렉토리에 `deployment.json` 파일이 생성됩니다. 이렇게 `가짜 파일`을 생성하면 경고 메시지가 더 이상 나타나지 않습니다.

## 3. Go 테스트 사용하기

### 3.1. Fatima를 사용하지 않는 단위 테스트

테스트 대상이 Fatima 런타임을 사용하지 않는다면, 기존 방식과 동일하게 `testing` 패키지를 사용할 수 있습니다.

테스트를 만드는 방법은 [여기](https://pkg.go.dev/testing)를 참고하세요.

### 3.2. Fatima를 사용하는 단위 테스트

`go test`는 stdout에 출력된 PASS/FAIL 메시지를 기준으로 성공/실패 여부를 판단합니다. 

하지만 Fatima는 `builder/process.go:Initialize`에서 호출하는 `prepareProcFolder` 함수에서 stdout/stderr을 `proc` 하위의 파일에 redirection하기 위해 `dup`을 수행하므로 테스트의 정상 종료를 제대로 판단하지 못하게 됩니다. 

이로 인해 `runtime.GetFatimaRuntime()` 함수를 사용하여 모든 초기화를 수행한 `FatimaRuntime`을 반환할 수 없으며, 필요한 부분만 직접 구현하여 `runtime` 모의 객체를 만들어야 합니다.

이제 `mock.NewFatimaRuntime()`을 사용하여 테스트에 안전한 `FatimaRuntime`을 생성할 수 있습니다. 

만약 일반적인 Fatima에 대한 테스트 프레임워크를 개발하는 경우, 이와 같은 `mock` 코드를 공유하여 사용하는 것도 좋은 방법일 수 있습니다.

GoLand에서 이러한 테스트를 실행하려면, 앞서 언급한 디버깅 항목과 같이 `FATIMA_PROFILE`과 `debugapp`을 설정해야 합니다. 

터미널에서 직접 실행하려면 다음과 같이 수행하면 됩니다.

``` shell
FATIMA_PROFILE=local \ 
go test -args debugapp=hello-world
```

Go Build로 실행하는 경우 `-debugapp` 인자를 사용하고, Go Test로 실행하는 경우 `debugapp` 인자를 사용해야 합니다.

### 3.3. Fatima를 사용하는 통합 테스트 

생성된 Fatima 런타임을 사용하여 구성되는 `FetchHelper`는 일반적으로 같은 기능을 갖습니다.

- `github.com/fatima-go/queryman`을 사용한 데이터베이스 접근
- `text_profiles.xml`로 작성된 텍스트 접근
- `encdec` 패키지를 사용하여 데이터베이스에서 조회한 값의 암/복호화

이 중 일부 기능을 사용하여 통합 테스트를 진행해야 할 경우, 각각에 대한 모의 객체(mocks)를 구성해주면 됩니다. 
이때, Fatima 런타임 모의 객체로부터 생성되므로 외부 시스템과 강하게 연동하는 부분은 가능한 완전한 모의 객체를 사용하는 것이 좋습니다.

**3.3.1. text profile mocking**

`TextProfileMap`은 `Env`의 `FolderGuide`의 Fatima app home을 참조하므로, 모의 객체를 간단히 생성할 수 있습니다.

``` go
fatimaMock := NewFatimaRuntime()
textProfile, err := infra.LoadTextProfileMapImpl(fatimaMock)

if err != nil {
  return nil, err
}
```

**3.3.2. 데이터베이스 mocking**

데이터베이스는 `Encdec`와 함께 초기화되어야 합니다. 이렇게 초기화해야 조회한 테이블의 암호화된 항목을 읽을 수 있습니다. 
두 기능 모두 `config`와 `env`를 참조하므로, 다음과 같이 간단히 생성할 수 있습니다.

``` go
fatimaMock := NewFatimaRuntime()
database, err := infra.LoadDatasource(fatimaMock, "servicedb")

if err != nil {
  return nil, err
}

codec, err := encdec.NewEncdec(fatimaMock)

if err != nil {
  return nil, err
}
```

만약 `infra` 패키지 내의 이런 모듈이 외부에서 범용적으로 사용될 가능성이 있다면, 이러한 모의(mock) 코드도 별도의 패키지로 분리하는 것이 바람직할 수 있습니다.

## 3.4. Fatima의 log redirection 방어

Fatima 패키지는 `init` 함수에서 `github.com/fatima-go/fatima-log` 패키지를 Fatima 로그 디렉토리(`$FATIMA_HOME/log`)에 쓰도록 설정하고 있습니다.

따라서 다른 비즈니스 로직에서 로그를 fatima-log를 통해 출력하고 있다면 `github.com/fatima-go/fatima-log` 패키지는 초기화를 한 번만 수행할 수 있기에 로그를 `stdout`으로 보내기 위해서는 **다음과 같은 `init` 함수를 Fatima 패키지보다 먼저 실행해야 합니다.**

``` go
package polyfill

import "github.com/fatima-go/fatima-log"

func init() {
  // It should be called before "fatima/builder.init"
  log.SetLevel(log.LOG_TRACE)
  
  // This makes the logger prints all things into stdout.
  logPref := log.NewPreference("")
  
  // This makes the logger prints directly without any LogEvent consumer.
  logPref.DeliveryMode = log.DELIVERY_MODE_SYNC
  
  // Do Initialize first than any other modules such as Fatima.
  log.Initialize(logPref)
}
```

해당 모듈을 적절한 위치에 두고 import하여 사용하면 됩니다.

```go
import (
  _ "mmate/hello-world/polyfill"
  "github.com/fatima-go/fatima-core"
  ...
)
```

> **golang의 `init` 함수는 import되는 패키지의 순서대로 실행됩니다. 따라서 golang 스펙에서는 import 패키지의 lexical order(사전식 순서)를 지키도록 가이드하고 있습니다.**
> 
> - https://golang.org/ref/spec#Package_initialization
>
> Package initialization—variable initialization and the invocation of `init` functions—happens in a single goroutine, sequentially, one package at a time. An `init` function may launch other goroutines, which can run concurrently with the initialization code. However, **initialization always sequences the init functions: it will not invoke the next one until the previous one has returned.**
> 
> To ensure reproducible initialization behavior, build systems are encouraged to present multiple files belonging to the **same package in lexical file name order to a compiler.**

**3.4.1. Log redirection 주의점**

위에서 만든 polyfill을 이용해도 다음 두 가지에 의해 의도치 않은 결과가 나올 수 있습니다.

1. `${FATIMA_HOME}/conf/fatima-packages.yaml` 파일에 테스트할 프로세스의 로그 레벨이 `TRACE`보다 높게 설정되어 있을 경우, 실행 중에 해당 값을 덮어쓸 수 있습니다.

2. 첫 번째 상황은 원래의 Fatima 런타임에 절대적으로 접근하지 않으면 문제가 없습니다. **그러나 Fatima 런타임은 글로벌 스코프에서 `GetFatimaRuntime()`으로 접근할 수 있기 때문에** 실수로 어디서든 접근할 수 있습니다. 만약 이런 코드가 하나라도 존재하고 해당 코드가 테스트 대상이 되는 순간, Mock Fatima 런타임의 존재 여부와 관계없이 Fatima 런타임의 초기화 과정이 진행되고 로그 리디렉션도 수행되는 문제가 발생합니다.

따라서 테스트를 위해 Fatima 런타임을 목업으로 사용할 때는 **절대로 실행되는 코드 내에서 `GetFatimaRuntime()`을 사용하지 않아야 합니다.** 

가급적이면 `GetFatimaRuntime()`은 `main` 함수에서 딱 한 번만 사용하는 것이 가장 좋습니다.
