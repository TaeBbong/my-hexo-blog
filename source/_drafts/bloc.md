
## BloC 패턴

BloC(Business Logic Component) 패턴은 구글 개발자들이 추천하는, 플러터 개발에 적용되는 가장 대표적인 디자인 패턴이다.
안드로이드에서의 MVVM 패턴(Model - View - ViewModel)과 거의 동일하며 ViewModel 대신 BloC이 들어가는 구조이다.

지난번 내용이 여기까지였는데 이어서 공부해보았다.

## BloC 패턴의 구조

앞서 계속 말했듯 BloC은 패턴이다. 따라서 프로젝트 구조를 어떻게 잡고, 기능을 구현할 때 어떤 요소들을 어떤 단위로 나눠 흐름이 이어지도록 만드는가에 초점을 맞춰 공부해야한다. 이를 파악하기 위한 제일 쉬운 방법 중 하나는 데이터의 흐름(어디서 나와서 어디로 전달되는가)을 확인하는 것이다.

![BloC 패턴](https://miro.medium.com/max/1400/1*MqYPYKdNBiID0mZ-zyE-mA.png)

위의 그림을 보면 아주 간단한 형태로 BloC 패턴을 이해할 수 있다. 
1. UI: 말그대로 화면 관련 부분
2. BloC: Presenter 또는 ViewModel의 역할이라고 한다. 그게 무슨 역할이냐면 데이터를 다루는 부분과 화면을 다루는 부분 사이에서 
3. Repository:
4. Network/Local Data Provider: