# Overview
https://prometheus.io/docs/introduction/overview/#components
## Components
- Prometheus server: scrapes and stores time series data
- client libraries: for instrumenting application code
- a push gateway: for supporting short-lived jobs
- special-purpose exporters: for services like HAProxy, StatsD, Graphite, etc.
- an alertmanager: to handle alerts
- various support tools

instance: scrape 대상 엔드포인트(주로 단일 프로세스)
job: 같은 목적을 가지는 instance의 모임


>job: `api-server`
>- instance 1: `1.2.3.4:5670`
>- instance 2: `1.2.3.4:5671`
>- instance 3: `5.6.7.8:5670`
>- instance 4: `5.6.7.8:5671`



rules: recording rule과 alerting rule이 있음.
- recording rule: allow you to precompute frequently needed or computationally expensive expressions and save their result as a new set of time series.



https://medium.com/codex/prometheus-blackbox-what-why-how-28290dbb22ce

Promtheus는 아래 요소들로 구성되는 오픈소스 metric-based 모니터링 시스템이다.
- Prometheus server
- Alert Manager
- Promethues Exporters: 다른 application으로부터 metric을 수집해서 Prometheus Server가 특정 endpoint(주로 특정 path와 port)에서 이를 수집하도록 제공한다.

이러한 Exporter에는 많은 종류가 있는데, 대표적으로는 `Node Exporter`와 `Blackbox Exporter`가 있다.
- Node exporter: 하드웨어 리소스 관련 정보를 export한다.
- Blackbox exporter: HTTP/S, DNS, TCP, ICMP endpoint의 가용성 정보를 export한다.