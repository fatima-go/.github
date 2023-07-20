# fatima 프로세스 개발
프로그램은 기본적으로 프로세스 형태로 실행되며 공통적으로 관심을 가지고 처리해야 하는 부분들이 존재하며 예를 들면 아래와 같은 것들이 있습니다.

- 프로세스의 시작과 종료 : 비즈니스 로직의 시작과 종료
- 프로그램의 로깅
- 프로세스의 모니터링

fatima 프레임워크를 이용해 프로그램을 개발한다면, 이러한 공통 관심 부분에 대해서 프레임워크에서 인터페이스를 제공하기에 필요한 부분에 인터페이스를 통해 관련 코드를 넣어주면 됩니다.<BR>
즉 fatima 프레임워크는 프로세스의 기본적인 동작에 관여하므로 개발자는 비즈니스 로직에만 집중할 수 있으며, 본인이 원한다면 다양한 다른 package 를 사용해 개발을 진행할 수 있습니다.<BR> 
예를 들어 http service 를 제공하기 위해 gin 이나 gorilla 등을 사용할 수도 있고, grpc 서버를 띄울수도 있으며 각종 배치 프로그램을 원하는대로 작성할 수 있습니다.


## 프로세스 개발과 관련된 repository
| Repository                                              | 내용                             |
|---------------------------------------------------------|--------------------------------|
| [fatima-core](https://github.com/fatima-go/fatima-core) | fatima framework               |
| [fatima-log](https://github.com/fatima-go/fatima-log)   | fatima framework에서 사용하는 logger |
| [gofar](https://github.com/fatima-go/gofar)        | 배포를 위한 fatima 프로세스 패키징         |


## 개발 방법
golang 언어를 기반으로 fatima 프레임워크를 사용해 손쉽게 프로세스를 개발하는 방법은 아래 링크를 살펴보세요.

- [golang을 이용한 fatima 프로세스 개발](/profile/development_start.md)
- [확장된 fatima 프로세스 개발](/profile/development_ext.md)
- [fatima 프로세스 디버깅과 테스트](/profile/development_debug.md)
- [fatima properties](/profile/development_prop.md)
