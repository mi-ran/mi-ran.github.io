---
title: "Web Server와 WAS"
categories:
   - web
tags:
   - web
---

### 웹서버
- 하드웨어 측면에서 보면 website의 컴포넌트 파일(html, css, image, javascript)들을 저장하고 있는 컴퓨터이다.
- 소프트웨어 측면 예시, Apache, Nginx, IIS, GWS ...

### WAS
- 보통 웹서버는 정적인 데이터만 제공한다. 하지만 점점 동적인 데이터를 취급해야하는 경우가 생김 그래서 WAS가 탄생
- WAS는 웹서버 기능이 내장되어 있기 때문에 문제없이 잘 동작
  BUT 현업에서는 웹서버와 WAS를 분리해서 사용
  WHY was에 문제가 생기면 그 부분만 접속을 차단하여 정적데이터는 계속 제공해야하기 때문
 
### 통신 순서
Client -> Web Server -> WAS
Client -> Web Server -> html, image, css, js...

### Forward Proxy vs Reverse Proxy

#### Forward Proxy
Client -> Forward Proxy -> Internet -> Server
- 클라이언트가 직접 웹서버에 요청하는 것이 아니라 프록시 서버가 대신 요청하고 응답을 클라이언트에 전달해줌, 캐시기능이 있다.

#### Reverse Proxy
Client -> Internet -> Reverse Proxy -> Server
- 클라이언트가 인터넷에 요청을 하면 리버스 프록시가 이 요청을 받아서 내부 서버에 데이터를 받은 후 클라이언트에 전달
- 클라이언트는 내부 서버에 대한 정보를 알 필요 없이 리버스 프록시에만 요청하면됨
- WAS(내부서버)에 직접적으로 접근하면 DB접근이 가능하기 때문에 중간에 리버스 프록시를 두고 클라이언트와 내부 서버 사이의 통신을 답당한다.
- 내부서버에 대한 설정으로 로그 밸런싱이나 서버 확장 등에 유리하다.

#### 차이점
- Forward Proxy는 클라이언트가 요청하는 End Point가 실제 서버 도메인이고 프록시는 둘 사이의 통신을 담당
  (클라이언트가 감춰짐, 서버는 클라이언트의 정보를 알 수 없음)
- Reverse Proxy는 클라이언트가 요청하는 End Point가 프록시 서버의 도메인이고 실제 서버 정보는 알 수 없다.
  (서버가 감춰짐, 클라이언트는 실제 서버의 정보를 알 수 없다.)