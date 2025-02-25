# Host
- scheme 제외임
- UnResolvedAddressException: 네트워크 주소 해석 문제(ex.프록시)


# Curl
- -x 또는 --proxy
  - 프록시 서버를 설정하는 옵션
- -w 또는 --write-out
  - curl이 요청을 수행한 후 출력할 내용을 정의하는 옵션
- -v 또는 --verbose
  - 자세한 정보를 출력 

# OIDC vs OAuth2
- Oauth +@가 OIDC
- Access Token(권한관리)뿐 아니라 Id Token(사용자 인증) 수행


# OIDC RP-Initiated logout
- 클라이언트(Relying Party, RP)가 사용자를 로그아웃시킬 때 Identity Provider(IdP)에 로그아웃 요청을 시작하는 방식
- 동작방식
  - 로그아웃 요청 생성: 사용자가 RP에서 로그아웃을 트리거하면 RP는 IdP의 로그아웃 엔드포인트로, 일반적으로 사용자의 id_token과 함께 요청
  - IdP는 요청을 수신한 후 해당 사용자의 세션을 종료합니다.
  - (선택 사항) 사용자가 여러 RP에 로그인한 상태라면 IdP는 해당 RP들에도 세션 종료를 알릴 수 있습니다.
  - (선택 사항) 로그아웃 완료 후, 사용자는 RP가 지정한 URL(post_logout_redirect_uri)로 리디렉션될 수 있습니다.
- 요청 구성
  - logout_endpoint (필수): IdP가 제공하는 로그아웃 엔드포인트 URL입니다.
  - id_token_hint (선택): RP에서 IdP로 전달하는 사용자의 id_token입니다. IdP는 이를 사용해 어떤 사용자가 로그아웃 중인지 확인할 수 있습니다.
  - post_logout_redirect_uri (선택): 로그아웃 완료 후 사용자를 리디렉션할 URL입니다. 이 URL은 미리 IdP에 등록되어 있어야 합니다.
  - state (선택): 로그아웃 요청에 대한 응답을 처리할 때 필요한 정보를 포함하는 임의 값입니다. CSRF 공격 방지 및 상태 추적에 유용합니다.
- post_logout_redirect_uri가 없이 로그아웃 요청이 오면?
  - 있는 거면 등록된 것중 해당 uri가 있는지 검사 후 리다이렉션
  - 클라이언트와 관련된 정보가 없거나 리다이렉트가 필요하지 않으면 IDP의 기본 로그아웃 완료 페이지를 보여줌. 



# Spring Security /login
- 존나 안 꺼짐, post_logout_redirect_uri 엔드포인트는 꼭 기본 설정 아닌 경로로..
  - GET /logout 하면 Confirm page뜨고, 여기서 POST /logout하면 기본 리디렉션 페이지(/login?logout)로 감
  - LogoutFilter는 스프링 기본 필터체인에 껴있어서 이 앞에 어떻게 필터를 껴넣을 수도 없다
  - /logout 이 경로를 못 가로채게 할 수가 없다ㅜㅅㅂ
- logoutHandler vs logoutSuccessHandler
  - 세션 만료, 쿠키 삭제 등 로그아웃 동작
  - 리디렉션 등 로그아웃 후 동작
- **기본 필터를 갈아끼기 할 수는 있다**
  - addFilterAt으로.
  - 모든 체인에서 기존 login disable해야함
  - logout disable 후 logout 설정하면 Logout 재활성됨 주의
- **별도 endpoint 사용시**
  - 기본적으로 POST 요구함,  리디렉션같이 GET인 경우 AntPathRequestMatcher 요청 매칭 필요
  - logoutSuccessHandler 등록한 게 호출됨, logoutHandler는 메소드 부터가 add(기본+@ 호출)
 
# State를 이용한 client post redirect uri 리뷰
- api-server -> auth-server 로 갈때는 state가 필요없고, 어디로 이동해야할지 정보인 postLogoutRedirectUri 만 전달
- state파라미터는 클라이언트 애플리케이션의 상태를 유지하기 위해 사용
- iris-auth-server 입장에서state 를 임의의 값으로 생성하고, 그 값을 이용하여 postLogoutRedirectUri를 저장한 다음. Logout 주소로 다시 돌아올때, state 값으로 저장된 postLogoutRedirectUri을 불러와서 이동
- state 없는 경우(잘못된 요청)
  - DEFAULT_REDIRECT_URL로 돌리기 보다는 return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("Invalid state parameter");


# ResponseEntity vs. HttpServletResponse
```
 ResponseEntity.status(HttpStatus.FOUND)
                                 .header(HttpHeaders.LOCATION, clientPostRedirectUri)
                                 .build();
 HttpServletResponse.sendRedirect(clientPostRedirectUri);
```
- Spring MVC 컨트롤러 처리 vs. 필터, 인터셉터 등 서블렛 처리
- @ControllerAdvice 같은 Spring 예외 처리와 자연스럽게 연동, 테스트 용이 vs. 즉시 종료


# 상태코드 302, 307, 308 차이
- 302 Found (임시 리디렉션)
  - POST 요청을 보내면 브라우저가 자동으로 GET 요청으로 바꿔서 리디렉션할 수도 있음.
  - 서버가 "일단 다른 URL로 이동해, 근데 나중에는 다시 원래 URL을 써도 돼" 라고 하는 경우.
  - OIDC 인증 후 클라이언트로 리디렉션할 때 사용. 보통 브라우저에서 302를 사용하고,302 Found와 함께 Location 헤더를 사용하면 브라우저가 GET 요청을 다시 보내면서 쿼리 파라미터를 유지할 수 있음.
- 307 Temporary Redirect (임시 리디렉션)
  - 302와 달리, 요청 메서드를 변경하지 않고 유지함.
  - 서버가 "이 URL이 임시로 이동했으니 그대로 요청을 다시 보내라" 라고 하는 경우.
- 308 Permanent Redirect (영구 리디렉션)
  - 서버가 "이 URL이 영구적으로 이동했으니 앞으로 새로운 URL만 사용해라" 라고 하는 경우.
 

# HTTP Status code
HTTP 상태 코드 HTTP 상태 코드(HTTP Status code)란 응답 메시지의 첫 번째 줄에 나타나는 세 자리 숫자의 코드로 요청에 대한 상태 정보(성공 또는 실패)를 나타냅니다. 자세한 정보는 RFC 2616을 참고합니다.
아래는 API 요청에 대해 응답하는 상태 코드의 종류와 의미입니다.

| 상태코드    | STRATUS               | 상태             | 설명                                                                                        |
|:--------|:----------------------|:-----------------|:------------------------------------------------------------------------------------------|
| 200     | OK                    | 성공               | 서버가 클라이언트의 요청을 성공적으로 수행한 상태입니다 응답 내용의 경우 각 API별로 형식이 다를 수 있으므로, 각 API별 상세 설명을 참고합니다.      |
| 400     | Bad Request           | 실패               | API에 필요한 파라미터가 잘못 되어 요청을 처리하지 못한 상태입니다.                                                   |
| 401     | Unauthorized          | 실패 (인증 오류)       | 해당 리소스에 유효한 인증 자격 증명이 없어 요청에 실패한 상태입니다. 응답은 OAuth 2.0 RFC6750 표준 규격에 따라 헤더에 오류 정보를 포함합니다. |
| 403     | Forbidden             | 실패 (권한 오류)       | 권한이 없어 거절된 상태입니다.                                                                         |
| 429     | Too Many Request      | 실패 (쿼터 초과)       | 정해진 사용량이나 초당 요청 한도를 초과한 상태입니다.                                                            |
| 500     | Internal Server Error | 실패 (시스템 오류)      | 서버 에러로서 요청을 처리하는 과정에서 서버가 예상하지 못한 상황에 놓인 상태입니다.                                           |
| 502     | Bad Gateway           | 실패 (시스템 오류)      | 서로 다른 프로토콜을 연결해주는 게이트웨이가 잘못된 프로토콜을 연결하거나 연결된 프로토콜에 문제가 있어 통신이 제대로 되지 않는 상태입니다.            |
| 503     | Service Unavailable   | 실패 (서비스 점검중)     | 서버가 요청을 처리할 준비가 되지 않는 상태입니다.                                                              |


