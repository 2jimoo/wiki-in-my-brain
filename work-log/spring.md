- Custom Req/Res Mapping
  - HandlerMethodArgumentResolver
  - WebMvcConfigurer
  - HttpMessageConverter
  - Jackson Serializer/Deserializer

---
# Custom Req/Res Mapping
### 요약
  - Converter<S,T> : Java <-> Java
  - Jackson Serializer/Deserializer: Java <-> JSON, XML
  - HttpMessageConverter: Java <-> Http
  - HandlerMethodArgumentResolver: @RequestParam,@PathVariable <-> Controller parameter

### HandlerMethodArgumentResolver
- **Spring MVC Controller에서 요청 처리 메서드의 파라미터**를 커스텀 방식으로 해석하고 바인딩하는 데 사용
- HandlerMethodArgumentResolver를 구현하여 @PathVariable에서 받은 값을 record로 변환하는 로직을 작성
  - supportsParameter
    - 파라미터 타입, 어노테이션 등 확인
  - resolveArgument
    - 조건이 만족되면 이 메서드에서 값을 변환하여 컨트롤러 메서드로 전달
- WebMvcConfigurer 통해 Spring MVC에 등록

### WebMvcConfigurer
- Spring MVC의 기본 설정을 유지하면서 특정 기능을 추가하거나 기존 동작을 재정의
  - addArgumentResolvers
    - 컨트롤러 메서드의 파라미터 처리 방식
  - addInterceptors
    - 요청을 가로채어 요청 전후 처리
  - configureMessageConverters(List<HttpMessageConverter<?>> converters)
    - HTTP 요청과 응답을 변환하는 커스텀 메시지 컨버터
  - addCorsMappings(CorsRegistry registry)
    - 특정 도메인에서의 요청을 허용하거나 차단
  - addResourceHandlers(ResourceHandlerRegistry registry)
    - 정적 자원(이미지, CSS, JavaScript 파일 등)의 핸들러를 등록하여 특정 경로의 자원을 어떻게 제공할지 설정  

### HttpMessageConverter
- **Spring MVC에서 HTTP 요청과 응답의 본문을 객체로 변환**하는 데 사용되는 인터페이스
-  @RequestBody와 @ResponseBody 어노테이션을 사용하여 JSON, XML, 텍스트 등 다양한 형식의 데이터를 객체로 변환하거나 객체를 특정 형식으로 변환

### Jackson Serializer/Deserializer
- Converter와 다른 점?
  - **Converter<S, T>는 Spring의 타입 변환 API의 일부**
  - **Jackson은 좀 더 넓게 Json과 java 객체사이**
- 컨트롤러에서 @RequestBody를 사용하여 자동으로 매핑된 객체를 처리
