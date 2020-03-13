```
---
RestTemplate 
-스프링 3.0부터 지원
 스프링에서 제공하는 http 통신에 유용하게 쓸 수 있는 템플릿
 HTTP 서버와의 통신을 단순화하고 RESTful 원칙을 지킨다
 
 jdbcTemplate처럼 RestTemplate도 기계적이고
 반복적인 코드를 깔끔하게 정리해준다
 요청을 보내고 받을때 코드가 몇줄 되지 않는다
 
-특징
 기계적이고 반복적인 코드를 최대한 줄여줌
 RESTful 형식에 맞춤
 json, xml을 쉽게 응답받음
 
 ---
 HTTP 서버와의 다양한 통신방법
 -URLConnection
  jdk 1.2부터 내장/java.net 패키지 
  URL의 내용을 읽어오거나 URL 주소에
  GET, POST로 데이터 전달시 사용
  또한 http 프로토콜 이외에도 사용 가능(file 등) 
  
  사용예시
  1) new URL("http://...")
  2) openConnection()
  3) URLConnection
  4) getInputStream, getOutputStream
  5) InputStream, OutputStream 처리
  
  문제점
  
  
  
 -HttpClient
 
 
 참조
 
 https://sjh836.tistory.com/141
