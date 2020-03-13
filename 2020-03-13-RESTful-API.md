
```
---
RESTful API
-REST(Representational State Transfer)는
웹서비스에서 통신을 위한 아키텍처를 뜻하며
이 용어는 로이 필딩(ROY Fielding)의 2000년
박사학위 논문에서 소개되었다.

REST API는 리소스 중심으로 설계하고,
기능에 맞게 GET,POST,PUT,PATCH,DELETE 등의
메서드를 정의한다.

GET     : 지정된 URI에서 리소스의 표현을 조회
POST    : 지정된 URI에서 신규 리소스를 생성
PUT     : 지정된 URI에서 리소스를 생성하거나
          업데이트          
PATCH   : 리소스의 부분 업데이트
DELETE  : 지정된 URI에서 리소스 제거

---
REST API 아키텍처에 적용되는 6가지 제한조건은
아래와 같다

1) 클라이언트/서버 구조
2) 무상태(Stateless)
3) 캐시처리가능(Cacheable)
4) 계층화
5) Code on demand(optional)
6) 인터페이스 일관성
---
1) 일관적인 인터페이스로 분리되어야 한다
2) 각 요청간 클라이언트의 콘텍스트가 서버에 저장되어서는
   안된다
3) WWW에서와 같이 클라이언트는 응답을 캐싱할 수 있어야
   한다. 잘 관리되는 캐싱은 클라이언트-서버간 상호작용을
   부분적으로 또는 완전하게 제거하여 scalability와
   성능을 향상한다
4) 클라이언트는 보통 대상 서버에 직접 연결되었는지,
   또는 중간 서버를 통해 연결되었는지를 알 수 없다.
   중간 서버는 로드밸런싱 기능이나 공유 캐시 기능을
   제공함으로써 시스템 규모 확장성을 향상하는데
   유용하다
5) 자바 애플릿이나 자바스크립트의 제공을 통해
   서버가 클라이언트가 실행시킬 수 있는 로직을
   전송하여 기능을 확장시킬 수 있다
6) 아키텍처를 단순화시키고 작은 단위로 분리(decouple)
   함으로써 클라이언트-서버의 각 파트가 독립적으로
   개선될 수 있도록 해준다

---
리소스 중심 RESTful API uri(End Point) 설계
-REST API 개발시 가장 중요한 것은 URI 설계
 URI 설계는 엔드포인트라고 부른다 
 
 일반적인 Rest API에서의 URI는 아래와 같은 규칙으로
 설계한다 
 
 1)동일한 URI(End Point)에 행위에 맞게 
   POST,GET,DELETE,PATCH 등의 메서드를 사용한다
 2)명사를 사용한다
 3)리스트를 표현할 때는 복수형을 사용한다
 4)URI Path에 불필요한 파라미터를 넣지 말자
   (3 Depth 이상 길어지면 안된다)
---
1) ex. 커피관련 API 구축시 

잘못된 사례
-커피리스트 조회
-커피 중 특정 하나의 커피 조회
-신규 커피를 메뉴에 등록
-특정 커피를 메뉴에 삭제

-[GET] http://domain:1234/api/coffee/list
-[GET] http://domain:1234/api/coffee/list?name=아메리카노
-[POST] http://domain:1234/api/coffee/creater-coffee
-[DELETE] http://domain:12234/api/coffee/delete/latte
   
수정된 사례

-[GET] http://domain:1234/api/coffees
-[GET] http://domain:1234/api/coffees/아메리카노
-[POST] http://domain:1234/api/coffees
-[DELETE] http://domain:12234/api/coffees/latte
   
   *불필요한 Path 제거됨
---
2,3 ) 위의 coffees라는 복수형 명사를 사용
   http://domain:1234/api/getCoffees 처럼 동사형 사용 x
   
4) 불필요한 파라미터 URI를 Path에 추가하지 말 것.
   ex. 커피 메뉴 리스트를 조회하는 API에서
   우유가 추가된 메뉴만 조회할 경우
   
   잘못된 사례
   http://domain:1234/api/coffees/milk
   
   수정된 사례
   http://domain:1234/api/coffees?type=milk
   
   
   
   
   
   
   
   
   
   
   
   
   
   















