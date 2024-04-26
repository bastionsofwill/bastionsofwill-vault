## WebRTC
WebRTC(Web Real-Time Communication)는 말 그대로 Web에서의 실시간 통신(RTC)을 가능하게 하는 기술로, 주로 오디오/비디오를 사용한 RTC를 구현할 때 자주 고려된다. 

WebRTC는 아래와 같은 API를 구현함으로써 사용자가 기능을 구현하도록 한다. 
- `MediaStream`(a.k.a `getUserMedia`): 카메라, 마이크, 화면 캡처 등의 데이터 스트림에 접근한다.
- `RTCPeerConnection`: 암호화, 대역폭 관리 기능으로 오디오/비디오 통화를 가능케 한다.
- `RTCDataChannel`: P2P 통신을 가능케 한다.

이는 W3C에서 관리하는 [WebRTC API 명세](https://w3c.github.io/webrtc-pc/)와 [`getUserMedia`명세](https://www.w3.org/TR/mediacapture-streams)를 통해 정의된다.

`MediaStream` API로 송신할 데이터를 얻고, `RTCPeerConnection` API로 데이터를 송/수신하지만,  

단, P2P 통신이 성립되기 위해서는 연결하고자 하는 상대를 식별하고 연결 방식을 결정하는 Signaling이 선행되어야 하나, WebRTC API에서는 이를 명시하지 않는다. 
- ICE, STUN, TURN과 같은 NAT-traversal 기술을 사용하여 원격으로 peer간 연결을 수행

## P2P 통신과 NAT
public IP주소를 가지지 않는 클라이언트라도  요청을 보내고 응답을 받는 데에는 문제가 없다.
하지만, 어떤 라우터는 연결된 단말 통신을 제한할 수 있다.
즉, STUN을 통해 자신의 public 주소를 알아낸다고 해서 연결이 항상 성립할 수 있는 것은 아니다.

## ICE
Interactive Connectivity Establishment
브라우저의 peer 간 연결을 수행하는 프레임워크. STUN/TURN 서버를 사용한다.

## STUN
Session Traversal Utilities for NAT
public address를 찾고 peer 간 직접 연결을 방해하는 라우터의 제한을 결정한다.
client가 인터넷을 통해 stun 서버에 요청을 보내면, STUN 서버는 클라이언트의 public address와 라우터의 NAT 너머 접근 가능 여부를 응답한다.
![](webrtc-stun.png) 

## TURN
Traversal Using Relays around NAT
다양한 제약으로 인해 두 클라이언트 간 직접 연결이 어려울 수 있다.
이를 우회하여 두 클라이언트 간 통신을 수립하기 위해 두 클라이언트 사이에 TURN 서버를 두고 서로 통신 데이터를 TURN서버로부터 전달(forward)받는 방식을 사용할 수 있다.
STUN 방식에 비해 리소스가 많이 필요하므로, 대안이 없는 경우에만 사용하는 것이 좋다.
![](webrtc-turn.png) 

## References
[Wikipedia: WebRTC](https://en.wikipedia.org/wiki/WebRTC)
[webrtc.org](https://webrtc.org)
[MDN WebRTC API 문서-Protocols](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols)
[MDN WebRTC API 문서-Signaling](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Signaling_and_video_calling)
[STUN, TURN, and ICE](https://anyconnect.com/stun-turn-ice/)
[web.dev](https://web.dev/articles/webrtc-basics)