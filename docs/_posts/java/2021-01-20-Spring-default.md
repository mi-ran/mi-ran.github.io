---
title: "Spring 기초"
categories:
   - java
tags:
   - java
---

### 컨테이너
- 프레임워크 안에서 인스턴스들의 생명 주기를 관리하며, 생성된 인스턴스들에게 추가적인 기능을 부여
- 스프링 컨테이너는 스프링 프레임워크 핵심에 위치하며, DI를 통해 어플리케이션을 구성하는 컴포넌트들을 관리

### Spring MVC 흐름
1. 요청된 URL을 dispatcher-servlet으로 전달
2. HandlerMapping은 해당 URL에 매핑된 컨트롤러가 있는지 검사 후 컨트롤러에 전달
3. 해당 컨트롤러가 로직을 처리
4. ModelAndView 객체를 생성 후 로직의 결과를 담아서 Dispatcher servlet에 전달
5. Dispatcher servlet은 전달 받은 뷰가 있는지 검사하기 위해 ViewResolver로 보냄
6. ViewResolver는 받은 뷰가 있는지 검사 후 뷰로 보냄
7. 모델과 같이 뷰를 그린 후 Dispatcher servlet으로 보냄
8. 최종적으로 컨텐츠를 클라이언트에 전달

- 꼭 뷰를 전달하지 않더라도 @ResponseBody를 붙이면 값을 그대로 클라이언트로 보낼수 있다.

### Dispatcher Servlet
`Servlet Container`에서 HTTP 프로토콜을 통해 들어오는 모든 요청을 프레젠테이션 계층의 제일 앞에 둬서 중앙집중식으로 처리해주는 프론트 컨트롤러.
서블릿 컨터이너의 한 종류로 Tomcat이 있다.
클라이언트로부터 요청이 들어오면 서블릿 컨테이너가 요청을 받는데, 이때 제일 앞에서 서버로 들어오는 모든 요청을 처리하는 프론트 컨트롤러를 Spring이 정의하였고 이를 Dispatcher servlet이라고 한다.
공통 처리 작업을 Dispatcher 서블릿이 하고 이후 세부 컨트롤러로 작업을 위임함
Dispatcher servlet이 처리하는 url패턴을 지정해주어야함.

### ApplicationContext
