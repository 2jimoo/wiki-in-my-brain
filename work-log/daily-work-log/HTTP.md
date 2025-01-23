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
