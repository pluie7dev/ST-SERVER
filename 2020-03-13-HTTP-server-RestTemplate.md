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
  -응답코드가 4xx 거나 5xx이면 IOException 발생
  -타임아웃을 설정할 수 없다
  -쿠키제어 불가 
  
 -HttpClient
  3.x apache commons의 프로젝트에서
  4.x부터 Apache 재단 탑 프로젝트 승급
  4.x부터는 Apache HttpComponents로 불린다 
  maven dependency에서 설정가능/org.apache.http 패키지
  
  사용예제
  1) CloseableHttpClient httpclient = HttpClients.createDefault();
  2) 메소드에 따라 new HttpGet("http://...");
  3) CloseableHttpResponse response = httpclient.execute(httpget);
  4) HttpEntity entity = response.getEntity();
  5) Stream 으로 entity.getContent() 처리 등 
  
  URLConnection 와 비교시 장점
  -모든 응답코드를 읽을 수 있다
   httpResponse.getStatusLine().getStatusCode()   
  -타임아웃 설정 가능
  -쿠키제어 가능
  
  문제점
  -URLConnection 을 이용한 방식보다 코드가 간결해졌지만
   여전히 반복적이고 코드들이 길다
  -스트림 처리 로직을 별도로 짜야한다
   (EntityUtils 사용)
  -응답의 컨텐츠타입에 따라 별도 로직이 필요하다
   (이 경우 RestTemplate 사용)
   
---
RestTemplate 의 동작원리
 -org.springframework.http.client 패키지
 -HttpClient는 HTTP를 사용하여 통신하는 범용 라이브러리
 -RestTemplate은 HttpClient 를 추상화
  (HttpEntity의 json, xml 등)
 -내부 통신(HTTP 커넥션)에 있어서는 Apache HttpComponents 를 사용
  (만약 RestTemplate 가 없었다면, 직접 json, 
   xml 라이브러리를 사용해서 변환해야 했을 것)

    1) 어플리케이션이 RestTemplate를 생성하고, 
    URI, HTTP메소드 등의 헤더를 담아 요청한다.
    
    2) RestTemplate 는 HttpMessageConverter 를
    사용하여 requestEntity 를 요청메세지로 변환한다.
    
    3) RestTemplate 는 ClientHttpRequestFactory 로
    부터 ClientHttpRequest 를 가져와서 요청을 보낸다.
    
    4) ClientHttpRequest 는 요청메세지를 만들어 HTTP 
    프로토콜을 통해 서버와 통신한다.
    
    5) RestTemplate 는 ResponseErrorHandler 로 오류를 
    확인하고 있다면 처리로직을 태운다.
    
    6) ResponseErrorHandler 는 오류가 있다면 
    ClientHttpResponse 에서 응답데이터를 가져와서 
    처리한다.
    
    7) RestTemplate 는 HttpMessageConverter 를 이용해서 
    응답메세지를 java object(Class responseType) 
    로 변환한다.
    
    8) 어플리케이션에 반환된다

---
RestTemplate 사용
1) 생성하기
-기본 생성 : RestTemplate restTemplate = getRestTempalte();
2) 설정생성
  HttpComponentsClientHttpRequestFactory factory = 
  new HttpComponentsClientHttpRequestFactory();
  
  factory.setReadTimeout(5000);    // 읽기시간초과, ms 
  factory.setConnectTimeout(3000); // 연결시간초과, ms
   HttpClient httpClient = HttpClientBuilder.create()
        .setMaxConnTotal(100)     // connection pool 적용    
        .setMaxConnPerRoute(5)    // connection pool 적용
        .build();
  factory.setHttpClient(httpClient); // 동기실행에 사용될 
                                     //HttpClient 세팅
  RestTemplate restTemplate = new RestTemplate(factory);

---
connection pool 적용

RestTemplate : 기본적으로 connection pool 을 사용하지 않는다
연결할 때 마다, 로컬 포트를 열고 tcp connection 을 맺는다
이때 문제는 close() 이후에 사용된 소켓은 TIME_WAIT 상태가 되는데
요청량이 많다면 이런 소켓들을 재사용하지 못하고 
소켓이 오링나서 응답이 지연될 것이다  
이런 경우 connection pool 을 사용해서 해결할 수 있는데
DBCP처럼 소켓의 갯수를 정해서 재사용

RestTemplate 에서 connection pool 을 적용

위와 같이 HttpClient 를 만들고 setHttpClient() 를 해야한다

setMaxConnPerRoute : IP,포트 1쌍에 대해 수행 할 연결 수를 제한한다.
setMaxConnTotal : 최대 오픈되는 커넥션 수를 제한한다.

---
주요 메소드
RestTemplate Method	      HTTP Method	      설명
execute	                  Any	
exchange	                 Any	          헤더세팅해서 HTTP Method로 
                                        요청보내고 ResponseEntity로 
                                        반환받음
                                        
getForObject	             GET	          get 요청을 보내고 java object로 
                                        매핑받아서 반환받음
getForEntity	             GET	          get 요청을 보내고 ResponseEntity로
                                        반환받음
postForLocation	          POST	         post 요청을 보내고 java.net.URI로 
                                        반환받음
postForObject	            POST	         post 요청을 보내고 ResponseEntity로
                                        반환받음
put	                      PUT	
delete	                   DELETE	
headForHeaders	           HEAD	
optionsForAllow	          OPTIONS	

---
요청할 URL
    -UriComponentsBuilder 로 파라미터를 붙이거나 
     String.format 로 붙이거나 등등
    -(/user/{id}, ... , "redboy") 처럼 rest하게 
     넘길 수도 있다.
    -map 을 이용해서 더 깔끔하게 할 수도 있다.

Object 로 받기
    -ForObject 를 사용할때, 응답 xml이나 json 에 
    맞는 java object(Class responseType)가 필요하다. 
    @XmlElement 를 사용하거나 @JsonProperty 등을 
    사용하여 매핑해줘야한다.
    
에러 처리
   -DefaultResponseErrorHandler를 사용하여 
   HTTP Error 를 제어한다. 
   -restTemplate.setErrorHandler 를 통해 
   커스텀 핸들러를 등록할 수 있다.
   
비동기 처리

RestTemplate 는 동기처리에 사용된다. 
비동기 처리는 org.springframework.web.client.
AsyncRestTemplate 를 사용해야 한다

---
예제
import org.apache.http.client.HttpClient; 
import org.apache.http.impl.client.HttpClientBuilder; 
import org.springframework.http.client.
HttpComponentsClientHttpRequestFactory; 
import org.springframework.web.client.RestTemplate;

public class RestTemplateEx {

public static void main(String[] args) { HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory(); 

factory.setReadTimeout(5000); // 읽기시간초과, ms factory.setConnectTimeout(3000); // 연결시간초과, ms 

HttpClient httpClient = HttpClientBuilder.create()
.setMaxConnTotal(100) // connection pool 적용 
.setMaxConnPerRoute(5) // connection pool 적용 
.build(); 

// 동기실행에 사용될 HttpClient 세팅 
factory.setHttpClient(httpClient); 

RestTemplate restTemplate = new RestTemplate(factory); 
String url = "http://testapi.com/search?text=1234"; 

Object obj = restTemplate.getForObject
("요청할 URI 주소", "응답내용과 자동으로 매핑시킬 java object");
System.out.println(obj); } }

---
참조문서 : https://stackoverflow.com/questions/31869193/using-spring-rest-template-either-creating-too-many-connections-or-slow/
  
 
 참조 
 https://sjh836.tistory.com/141
