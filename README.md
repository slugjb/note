JWT(Json Web Token)

유저를 인증하고, 식별하기 위한 토큰(Token)기반 인증이다.

세션은 서버에 저장되지만, 토큰은 클라이언트에 저장되기 때문에 메모리나, 스토리지 등을 통해 세션을 관리했던 서버의 부담을 덜 수 있다.

토큰 자체에 사용자의 권한 정보 or 서비스를 사용하기 위한 정보가 포함(Self-contained)된다.



JWT 구조





JWT Structure

JWT는 dot(.)을 구분자로 3파트로 구분되어 있으며 각각의 파트를 Header, Payload, Signature라 부르며 각각 필요한 정보들을 담아 보관한다.



Header : 토큰의 타입과 해시 암호화 알고리즘으로 구성되어 있다. 첫 째는 토큰의 유형을 나타내고 두 번째는 HMAC, SHA256또는 RSA와 같은 해시 알고리즘을 나타낸다. Header는 typ와 alg 두 정보로 구성되는데, 여기서 alg는 Signature를 해싱하기 위한 알고리즘을 의미한다.

typ: 토큰의 타입 지정(ex: JWT)

alg: 알고리즘 방식을 지정하며, Signature와 토큰 검증에 사용한다.

{
  "alg" : "HS256",      // 해싱 알고리즘
  "typ" : JWT           // 고정값
}



Payload : 토큰에 담을 Claim 정보를 포함하고 있으며, Payload에 담는 정보의 한 조각을 Claim이라 부르고 이는 key/value pair로 이루어져 있다. 여러 조각(claim)을 넣을 수 있다.



Claim은 Registered Claim, Public Claim, Private Claim 세 종류가 있다.

1. Registrered Claim : 토큰 정보를 표현하기 위해 이미 정해진 종류의 데이터이다.

iss : 토큰 발급자(issuer)

sub : 토큰 제목(subject)

aud : 토큰 대상자(audience)

exp : 토큰 만료 시간(expiration), NumericData 형식

nbf : 토큰 활성일(not before), 이 날이 지나기 전의 토큰은 활성화 안됨

iat : 토큰 발급 시간(issued at): 토큰 발급 이후의 경과 시간을 알 수 있음

jti : JWT 토큰 식별자(JWT ID), 중복 방지를 위해 사용하며 일회용 토큰(Access Token)등에 사용된다..



2. Public Claim : 사용자 정의 Claim, 공개용 정보를 위해 사용된다. 충돌 방지를 위해 URI 포맷을 이용한다.

{
    "https://sanhait.com/jwt_claims/example": true
}

3. Private Claim: 사용자 정의 Claim, 서버와 클라이언트 사이의 임의로 지정한 정보를 저장한다.

등록된 클레임도 아니고, 공개된 클레임도 아니다.

클라이언트 ↔︎ 서버 협의간에 사용되는 클레임 이름이다.

공개 클레임과 달리 이름이 중복되어 충돌이 일어날 수 있으니,  사용시 유의해야 한다.

{
   "username": "sanhaitOMA"
}

예제 Payload

{
    "iss": "sanhait.com",
    "exp": "1485270000000",
    "https://sanhait.com/jwt_claims/example": true
    "userId": "11028373727102",
    "username": "sanhaitOMA"
}

위 예제 payload 는 2개의 등록된 클레임, 1개의 공개 클레임, 2개의 비공개 클레임으로 이루어져 있다.



Structure : Secret key를 포함하여 암호화되어 있다.

Header와 Payload의 데이터 무결성과 변조 방지를 위한 서명이다.

이 서명은 헤더의 인코딩값과, 정보의 인코딩값을 합친후 주어진 비밀키로 해쉬를 하여 생성합니다.

해쉬는 서버만 알고 있다.(비밀키를 통해 해킹 방지)

서명 값과 계산값이 일치하고 유효기간이 지나지 않았다면 인가한다.



※ Base64 인코딩의 경우 “+”, “/”, “=”이 포함되지만, JWT는 URI에서 파라미터로 사용할 수 있도록 URL-Safe 한  Base64url 인코딩을 사용한다.



JWT Process





[장점]

토큰 자체에 인증에 필요한 정보가 모두 있기에 별도의 인증 저장소가 필요 없다.

별도의 사용자 정보를 요청할 필요가 없기에 데이터 요청이 좀 더 가벼워 질 수 있다.

XML보다 덜 장황해서 인코딩 시 크기도 작아져 SAML보다 컴팩트하다.

분산/클라우드 기반 infra-structure에 대응하기 쉽다

URL 파라미터와 헤더로 사용한다.

수평 스케일이 쉽다.

트래픽에 대한 부담이 적다.

내장된 만료가 존재한다.

REST 서비스 제공이 가능하다.



[단점]

저장 할 필드 수에 따라 토큰이 커질 수 있다.

토큰이 클라이언트에 저장되어 데이터베이스에서 사용자 정보를 조작해도 토큰에 직접 적용할 수 없다.

stateless앱 환경에서는 토큰이 대부분의 요청에 전송되기에 트래픽 크기에 영향을 줄 수 있다.

토큰 자체 정보 저장(보안 취약)

정보가 많을수록 토큰 길이로 인한 네트워크 부하가 일어난다.

payload 암호화 필요하다.(보안)

Stateless (서버에서 제어 불가능, 임의 삭제 불가)


----


단축 URL이란?

짧은 URL을 통해 긴 URL로 이동할 수 있도록 해주는 서비스입니다.

SMS을 전송하거나 SNS을 이용할 때 글자 수 제한에 걸리지 않고, 링크 공유가 쉽다.

URL Redirection 기법을 이용해서 서비스된다.



URL Redirection 기법

특정 URL을 요청하면 다른 URL로 응답하는 기법이다. 이는 잘 알려진 URL의 변경으로 인해 링크를 유지하려는 목적 또는 한 URL에 대해 여러 URL을 가지는 경우에 사용되며, 피싱 공격을 위해 악용될 수도 있다.



단축 URL 생성 원리

해당 URL을 직접적으로 인코딩하는 방식이 아닌 URL을 DB에 삽입하고 해당 Index를 인코딩하는 방식이다.

인코딩에는 [0-9], [A-Z], [a-z]의 총 62개의 문자로 인코딩이 되어 Base62 인코딩이라 부른다.

Seq No

Name

Comments

1

index (Auto Increment)

인덱스

2

url

접속하고자 하는 URL 주소

3

encoded (NULL)

index를 Base62로 인코딩 한 결과





단축 URL 설계

 중복 허용 vs 비허용

 DB를 이용

 8자리 이내의 짧은 URL(논의 필요)







중복

중복은 허용하지 않는다.

긴 URL과 단축URL 집합 사이 중복 없이 모두 일대일로 대응 시키는 bijective function 관계여야 한다. 





인덱스

인덱스 개수가 많을수록 입력 시간이 길고, 테이블 사이즈가 크다

PK가 지정된 상태에서 PK로 조회할 때 가장 빠르다

조회 시간은 PK, 보조 인덱스, 일반 필드 순으로 빠르다

인덱스가 있으면 데이터 삭제가 느리다.



가장 속도가 빠른 PK를 최대한 이용한다.

단축 URL 링크 클릭시 : DB에 해당 데이터가 있는지 찾고, 매칭되는 원본 URL을 응답한다.

단축 URL로 조회를 하기 때문에, 단축 URL에 인덱스를 걸어주는 것이 좋다.

원본 URL 입력 : DB에 해당 데이터가 있는지 찾고, 있으면 매칭되는 단축 URL을 응답하고, 아니면 새로운 단축 URL을 생성 후 응답한다.

원본 URL에 인덱스를 걸거나 Unique를 걸면 확인을 위해 엄청난 String Compare 비용 발생한다.

DB에서 직접 원본 URL으로 조회할 때도 String Compare 비용 발생한다.

서버단에서 원본 URL을 단축 URL로 바꾸고, 원본 URL이 아니라 단축 URL로 DB를 조회하는 것이 바람직하다.

결론 : 단축 URL에 인덱스를 걸어서 [원본] [단축] 필드로 DB를 설계한다.

Seq No

Name

Comments

1

url

접속하고자 하는 URL 주소

2

encoded (NULL)

index를 Base62로 인코딩 한 결과





HTTP Response code

오류 시 404 응답을 통해 해당 페이지가 존재하지 않음을 나타낸다. 

정상의 경우 200 응답을 통해 해당 페이지로 이동한다. 

정상의 경우 302 응답을 통해 해당 페이지로 이동한다. 

임시 링크가 변경 혹은 만료될 경우를 대비하여 임시 이동을 의미하는 302를 사용
