XMPP(Extensible Messaging and Presence Protocol)는 [XML](XML)에 기반한 통신 프로토콜로, 둘 이상의 네트워크 상 개체(entity)간 구조화된 데이터의 근실시간(near real-time) 교환을 가능케 한다.
XMPP는 애플리케이션 계층에서 정의된 프로토콜로, XMPP 네트워크의 구조는 탈중앙화된 구조로, 여러 운영 주체의 XMPP서버가 서로 통신할 수 있다는 점에서 이메일과 비슷하다(federated open system).

XML은 text 기반이기 때문에, XMPP를 사용하는 통신 역시 binary 데이터를 전동하는 것보다 많은 네트워크 부하가 발생한다는 문제점이 있다.
따라서, 전송하는 binary 데이터의 양이 많아지면 OOB(out-of-band)로 전송하는 것이 바람직하다.

이를 담당하는 것이 P2P 세션 관리(Signaling)를 가능하게 하는 XMPP의 확장, Jingle이다. 
Jingle은 Google과 XMPP Standards Foundation에 의해 설계되었으며, Google Talk에 사용된 libjingle 라이브러리가 BSD 라이센스로 사용이 가능하다.

## Reference
[Wikipedia - XMPP](https://en.wikipedia.org/wiki/Presence_information)
[Wikipedia - Out-of-band data](https://en.wikipedia.org/wiki/Out-of-band_data)
[Wikipedia - Jingle](https://en.wikipedia.org/wiki/Jingle_(protocol))