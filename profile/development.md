# fatima 프로세스 개발

TODO-AUTHOR : @dave
프로그램은 기본적으로 프로세스 형태로 실행되며 공통적으로 관심을 가지고 처리해야 하는 부분들이 존재합니다

- 프로세스의 시작과 종료 : 비즈니스 로직의 시작과 종료
- 프로그램의 로깅
- 프로세스의 모니터링

fatima framework을 이용해 프로그램을 개발한다면, 이러한 공통 관심 부분에 대해서 framework에서 인터페이스를 제공하기에 필요한 부분에 인터페이스를 통해 관련 코드를 넣어주면 됩니다.<BR>
즉 fatima framework은 프로세스의 기본적인 동작에 관여하므로 개발자는 비즈니스 로직에만 집중할 수 있으며, 
본인이 원하는 다양한 다른 package 를 사용해 개발을 진행할 수 있습니다. 
예를 들어 http service 를 제공하기 위해 gin 이나 gorilla 등을 사용할 수도 있고, grpc 서버를 띄울수도 있으며 각종 배치 프로그램을 원하는대로 작성할 수 있습니다.


## 프로세스 개발과 관련된 repository
| Repository                                              | 내용                             |
|---------------------------------------------------------|--------------------------------|
| [fatima-core](https://github.com/fatima-go/fatima-core) | fatima framework               |
| [fatima-log](https://github.com/fatima-go/fatima-log)   | fatima framework에서 사용하는 logger |
| [gofar](https://github.com/fatima-go/gofar)        | 배포를 위한 fatima 프로세스 패키징         |


## 개발 방법
golang 언어를 기반으로 fatima framework을 사용해 손쉽게 프로세스를 개발하는 방법을 알아봅니다

- [golang을 이용한 fatima 프로세스 개발](./development_start.md)
- [확장된 fatima 프로세스 개발](./development_ext.md)
- [fatima 프로세스 디버깅과 테스트](./development_debug.md)
- [fatima properties](./development_prop.md)
