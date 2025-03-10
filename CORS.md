Origin이란 domain, scheme, port로 구성된, 네트워크상에서 식별되는 무언가로 해석된다.

브라우저는 요청을 보내고 이에 대한 응답을 표시하기 위해 많은 일을 수행하지만, 이러한 응답이 정상적이지 않을 경우 사용자에게 피해가 발생하는 공격의 수단이 될 수 있다.
이를 방지하기 위해, 대다수의 브라우저에서는 일반적으로 same-origin 정책을 적용하여 다른 origin에서 전달된 자원인 cross-origin 자원은 차단해버린다.
따라서, 특정 cross-origin 접근을 허용하여야 할 경우 이를 위한 예외 처리가 필요하다.

**CORS** (Cross-Origin Resource Sharing)는 HTTP header에 기반한, cross-origin 접근을 허용하는 체제(mechanism)이다.

CloudFront에는 origin request policy라는 policy가 있는데, 말 그대로 origin에 request를 전달할 때 어떻게 전달하는지를 결정하는 policy이다.

그 중 `Managed-CORS-S3Origin` policy는 S3 bucket을 origin으로 가지는 CloudFront distribution의 CORS를 허용하기 위한 정책으로, origin으로 전달되는 request에 cookie나 query string은 모두 제외하고 아래 header들을 추가한다.
- Origin
- Access-Control-Request-Headers
- Access-Control-Request-Method

이를 통해 아래 목적을 수행할 수 있다
1. Set up CORS between CloudFront and an S3 bucket
2. Handle preflight OPTIONS requests automatically
3. Support cross-origin requests from web applications


CloudFront가 받은 요청에 대한 Cache miss가 발생하는 경우, 요청을 아래와 같이 origin으로 전달(forward)한다.
- URL path (단, query string은 제외)
- request body (존재할 경우)
- 기본적인 HTTP header (Host, User-Agent, and X-Amz-Cf-Id)

CloudFront는 사용자와 Origin 사이 중간자(Intermediary) 역할을 하기 때문에, Origin에서 CORS 관련 작업을 수행해주는 로직이 있다면 CloudFront도 Origin에서 적절한 CORS header를 받아와서 사용자(의 브라우저)가 요구하는 CORS를 해결해야 한다.
이렇게 Origin에서 의도한 CORS header를 받아오기 위해 CloudFront에서 Origin으로 향하는 request에 기본적인 header외에 추가로 전달하여야 하는 내용이 있을 경우, 이를 origin request policy로 설정할 수 있다.

Ah, now I understand your question better! You're right - CORS is fundamentally a browser security feature, not something that should matter for server-to-server communication between CloudFront and your origin. [1]

However, there's a specific reason why the Origin header is sometimes forwarded to the origin

1. Dynamic CORS Response Headers:
• If your application needs to return different CORS headers based on the requesting origin 

• Instead of allowing all origins (Access-Control-Allow-Origin: *), you might want to validate and return specific origins
  
For example:

**javascript**

```
// Origin server logic
if (request.headers.origin === 'https://trusted-site.com') {
    response.headers['Access-Control-Allow-Origin'] = 'https://trusted-site.com';
} else if (request.headers.origin === 'https://another-trusted.com') {
    response.headers['Access-Control-Allow-Origin'] = 'https://another-trusted.com';
} else {
    response.headers['Access-Control-Allow-Origin'] = null; // deny
}
```
  
You have two options:
1. If you want to serve all origins (less secure):
• Don't forward the Origin header to your origin
• Configure CloudFront to add Access-Control-Allow-Origin: * in the response
• This can be done using a Response Headers Policy in CloudFront

2. If you need origin-specific CORS (more secure):
• Forward the Origin header to your origin
• Let your origin decide which origins to allow
• Return appropriate CORS headers based on the Origin

You're absolutely correct that if you're using a simple CORS policy (like allowing all origins), there's no need to forward the Origin header to your origin - CloudFront can handle this entirely by adding the appropriate CORS headers in the response to the browser.
