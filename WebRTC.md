web.dev의 ['Get started with WebRTC'](https://web.dev/articles/webrtc-basics)를 표절하였음.
https://web.dev/articles/webrtc-infrastructure
https://codelabs.developers.google.com/codelabs/webrtc-web/#0

WebRTC(Web Real-Time Communication)는 말 그대로 Web에서의 실시간 통신(RTC)을 가능하게 하는 오픈 소스 기술로, 주로 오디오/비디오를 사용한 RTC를 구현할 때 자주 고려된다. 

![WebRTC Architecture](webrtc-public-diagram-for-website.png)
[이미지 출처](https://webrtc.github.io/webrtc-org/architecture/)

## Web API
보라색으로 표시된 영역인 Web API는 아래와 같은 API로 구성되어 있다. 
- `MediaStream`(a.k.a `getUserMedia`): 카메라, 마이크, 화면 캡처 등의 데이터 스트림에 접근한다.
- `RTCPeerConnection`: 암호화, 대역폭 관리 기능으로 오디오/비디오 통화를 가능케 한다.
- `RTCDataChannel`: P2P 통신을 가능케 한다.

이는 W3C에서 관리하는 [WebRTC API 명세](https://w3c.github.io/webrtc-pc/)와 [`getUserMedia`명세](https://www.w3.org/TR/mediacapture-streams)를 통해 정의된다.

### RTCPeerConnection API
`RTCPeerConnection`은 Peer 간 안정적이고 효과적인 스트리밍 데이터 통신을 담당한다.
WebRTC는 서버가 필요한데, 아래와 같은 기능을 수행하기 위함이다.
- User discovery and communication
- Signaling
- NAT/firewll traversal
- Relay 서버(P2P 통신 실패 시)

ICE 프레임워크는 STUN/TURN 프로토콜로 RTCPeerConnection이 NAT traversal과 다른 네트워크의 방해 요인을 극복하도록 해준다. UDP를 사용한 Peer 간 직접 연결을 할 경우, STUN서버의 유일한 역할은 각 Peer의 public IP 주소와 포트를 알려주는 역할 뿐이다. 이렇게만 해결된다면 간단하겠지만, UDP 연결이 실패하게 되면 TCP 연결을 시도한다. 직접 연결이 실패하면, relay 서버인 TURN 서버를 거치게 된다.

### RTCDataChannel API
오디오/비디오 데이터가 아닌, 임의의 데이터의 P2P 교환을 가능하게 하는 API이다.

## Transport / Session
본격적인 P2P 통신은 `RTCPeerConnection`으로 이루어지지만, 통신이 성립되기 위해서는 제어 메시지를 교환하면서 통신을 조정하는 절차, 즉 Signaling이 먼저 이루어져야 한다.
WebRTC는 Signaling 관련 내용을 명시하지 않기 때문에, WebRTC 앱 개발자는 기능을 구현할 때 [적절한 방안](https://github.com/muaz-khan/WebRTC-Experiment/blob/master/Signaling.md)을 선택하여야 한다.
[appr.tc](https://appr.tc)는 XHR과 ChannelAPI를, codelab에서는 Node server 위에서 Socket.io를 사용한다.

Signaling이 성공적으로 수행되었다는 것은 두 peer 간에 아래의 정보가 문제없이 교환되었다는 것이다.
- 네트워크 구성: 서로를 식별할 수 있는 IP 주소와 포트 정보. ICE 프레임워크로 교환
- 미디어 호환성: 서로의 브라우저에서 처리할 수 있는 [Codec](Codec.md), [Resolution](Resolution.md) 정보. SDP(Session Description Protocol)로 교환
성공적으로 Signaling 절차가 종료되면 비로소 RTCPeerConnection API를 사용한 peer 간(또는 relay 서버를 거쳐서) 통신이 가능하다.

## P2P 통신과 NAT
public IP주소를 가지지 않는 클라이언트라도  요청을 보내고 응답을 받는 데에는 문제가 없다.
하지만, 어떤 라우터는 연결된 단말 통신을 제한할 수 있다.
즉, STUN을 통해 자신의 public 주소를 알아낸다고 해서 연결이 항상 성립할 수 있는 것은 아니다.

## ICE
Interactive Connectivity Establishment
브라우저의 peer 간 연결을 수행하는 프레임워크. STUN/TURN 서버를 사용한다.
https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/

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
[web.dev](https://web.dev/articles/webrtc-basics)
[web.dev2](https://web.dev/articles/webrtc-infrastructure)
[Wikipedia: WebRTC](https://en.wikipedia.org/wiki/WebRTC)
[webrtc.org](https://webrtc.org)
[MDN WebRTC API 문서-Protocols](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Protocols)
[MDN WebRTC API 문서-Signaling](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Signaling_and_video_calling)
[STUN, TURN, and ICE](https://anyconnect.com/stun-turn-ice/)
[codelab](https://codelabs.developers.google.com/codelabs/webrtc-web/)
