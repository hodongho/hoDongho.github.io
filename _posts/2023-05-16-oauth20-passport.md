---
title: "[Node.js]OAuth2.0그리고 Passport란?"
last_modified_at: '2023-05-16'
categories:
- 개발지식
tags:
- 네트워크
- 계정
- 인가
- OAuth
- Passport
- OAuth2.0
- Node.js
---

![OAuth2.0](https://oauth.net/images/oauth-2-sm.png)
_출처 oauth.net_


## OAuth 그리고 Passport
&nbsp;&nbsp;&nbsp;&nbsp;과거 로그인을 하던 경험을 생각해봅시다. 과거 우리는 사이트마다 아이디와 비밀번호를 입력해서 계정을 만들어야만 했습니다. 그 결과 사이트마다 정책이 달라 다양한 아이디와 비밀번호를 사용하다보니 아이디와 비밀번호를 까먹어 틀리는 경우도 빈번했습니다. 더군다나 여러 사이트에 아이디와 비밀번호를 제공하기 때문에 보안적으로도 안전하지 못했습니다. 이 결과 사용자 입장에서는 이 방식은 상당히 불편하고 복잡했습니다.

![지그재그 로그인페이지](/assets/img/ZIGZAG_로그인페이지.png)
_출처 지그재그_

&nbsp;&nbsp;&nbsp;&nbsp;그러나 요즘 우리는 구글로 로그인, 카카오로 로그인과 같이 다양한 로그인 방식을 선택하고 한가지 계정으로 다양한 사이트에 접속할 수 있게 됐습니다. 이러한 편리한 기능을 가능하게 만들어준 것이 OAuth(Open Authorization)입니다. 사용자가 직접 계정을 생성하는 것이 아닌 기존의 계정정보를 활용하여 새로운 사이트에 간편하게 로그인을 할 수 있게 해줍니다. 사용자의 아이디와 비밀번호를 다양한 곳에 제공할 필요가 없어졌고 버튼 하나면 기존의 로그인되어 있던 서비스와 연동되어 계정을 아주 편하게 생성할 수 있게 되었습니다. 더 나아가 우리는 계정 연동을 통해 사용자의 다양한 정보(친구 목록 조회, 캘린더 정보 조회등)를 보다 편리하게 연동할 수 있게 되었습니다.

&nbsp;&nbsp;&nbsp;&nbsp;그리고 이러한 OAuth를 개발자가 구현할 때 조금 더 편리하게 해주기위한 미들웨어 Passport가 있습니다.

## OAuth
&nbsp;&nbsp;&nbsp;&nbsp;일단 OAuth에 대해서 알아봅시다. OAuth는 사용자들이 직접 아이디와 비밀번호를 제공하지 않고 다른 웹 사이트 혹은 애플리케이션의 접근 권한을 부여할 수 있는 프로토콜입니다.

&nbsp;&nbsp;&nbsp;&nbsp;사실 OAuth이전에도 여러 회사에서 각자의 프로토콜을 만들어 사용했습니다. Google의 AuthSub, 아마존의 웹서비스 API 등 그러나 이렇게 다양한 프로토콜을 사용하는 것은 상당히 피곤한 일이였습니다. 그래서 이를 통합한 표준 프로토콜 OAuth가 등장하게 됐습니다.

### OAuth 1.0 vs OAuth 2.0
&nbsp;&nbsp;&nbsp;&nbsp; 기존의 OAuth1.0에 여러가지 사항을 개선해서 OAuth 2.0이 탄생하게 되었습니다.

- 애플리케이션 지원 강화
	- 애플리케이션에서 OAuth1.0을 사용하려면 브라우저를 통해 토큰을 발급받아야만 하는 문제점이 있었는데 OAuth2.0은 이를 개선해 애플리케이션에서 바로 발급받을 수 있게 되었습니다.

- 암호화 단순화
	- OAuth1.0은 정보를 암호화 하는 과정이 필수적이였지만 OAuth2.0은 HTTPS를 사용하게 되면서 암호화의 과정이 필요 없어졌습니다

- 다양한 인가 방식 제공
	- Authorization Code Grant, PKCE, Client Credentials Grant 등등 다양한 방식을 제공하면서 여러 브라우저, 서비스에 대응할 수 있습니다.

- 토큰 유효기간 부여
	- 토큰에 유효기간을 부여하면서 토큰이 탈취 당하더라도 일정시간 후에는 다시 재발급 되도록 구현되었습니다.

-  리소스 서버와 인가 서버 분리
	- 리소스 서버에서는 단순히 자원만 제공하고 인가 서버에서 인가 과정 전체를 담당하면서 역할을 명확하게 분리하고 보안을 강화 할 수 있게 되었습니다.

해당 글에서는 OAuth1.0를 조금 더 보완한 OAuth 2.0에 대해서 자세히 다뤄보도록 합시다.

### OAuth 2.0의 용어

&nbsp;&nbsp;&nbsp;&nbsp;OAuth의 인가 방식을 이해하기 위해서는 몇가지 용어를 알아야합니다. 해당용어들은 OAuth2.0 기준이며 OAuth1.0과는 조금 다릅니다.

- Resource Server
	- 리소스 서버는 Google, 카카오, Facebook 등 우리가 필요로하는 자원을 가지고 있는 서비스의 서버입니다.

- Authorization Server
	- 인가 서버는 OAuth 인가 절차를 담당하는 서버입니다. 클라이언트에게 Access토큰을 발급해주는 서버가 인가 서버입니다.

- Resource Owner
	- 리소스 오너는 쉽게 말하면 사용자입니다. 리소스 서버에서 어떤 권한을 허용할지 결정하는 주체입니다.

- Client
	- 클라이언트는 리소스 서버의 자원을 활용하려는 서비스입니다. 리소스 오너에게 인가(Authorization)를 요청하고 권한을 획득하면 리소스 서버로 부터 정보를 받는 주체입니다.
<br /><br /><br />

>💡   Authorization(인가, 권한 부여) vs Authentication(인증)<br />
>비슷한 듯 다른 이 두 개념에 대해서 간략하게 설명하자면 Authorization(인가, 권한 부여)은 특정 사용자에게 권한의 범위를 설정하는 것이고 Authentication(인증)은 특정 사용자의 신원을 확인하는 것입니다.

### OAuth 2.0의 인가 방식

![OAuth 인가 방식](https://cloud.google.com/static/apigee/docs/api-platform/images/oauth-abstract.png)

&nbsp;&nbsp;&nbsp;&nbsp;OAuth2.0는 인가 방식마다 차이가 조금있지만 전체적인 인가 절차는 비슷합니다. 인가 방식의 차이는 어떻게 Access token을 획득할 것인가에 따라 다릅니다. 전체적인 흐름은 다음과 같습니다.

1. 리소스 오너가 클라이언트의 서비스를 이용하기위해 OAuth를 사용해 로그인 요청을 합니다.
2. 클라이언트가 리소스 오너에게 받은 요청을 토대로 인가 서버에 로그인 페이지를 요청합니다.
3. 로그인 페이지를 응답받은 리소스 오너가 로그인을 합니다.
4. 성공적으로 로그인이 된 것을 확인한 클라이언트는 인가 서버에게 Access token을 요청합니다.
5. 인가 서버로부터 Access token을 성공적으로 발급 받으면 해당 Access token을 사용해 리소스 서버에서 원하는 데이터를 요청하고 원하는 데이터를 응답 받습니다.

&nbsp;&nbsp;&nbsp;&nbsp;자주 사용되는 방식인 Authorization Code Grant(인가 코드 방식)에 대해서 조금 더 자세히 알아봅시다!

### Authorization Code Grant(인가 코드 방식)
![Authorization Code Grant](https://itwiki.kr/images/3/37/Authorization_Code_Grant_Type.png)
_출처 IT위키_

&nbsp;&nbsp;&nbsp;&nbsp;기본적인 흐름은 위에서 설명한 흐름과 동일합니다. 하지만 Access token을 획득할 때 인가 서버에게 바로 요청하는 것이 아닌 인가 코드라는 것을 획득하고 그 다음 인가 코드를 통해 Access token을 발급 받는 방식입니다. 클라이언트가 인가 서버에게 인가 코드를 요청하고 리소스 오너가 로그인을 통해 권한을 부여해주면 인가 서버에서 redirect_url을 통해 인가 코드를 발급해줍니다. 클라이언트는 발급받은 인가 코드를 기반으로 Access token을 발급받습니다.

&nbsp;&nbsp;&nbsp;&nbsp;이러한 인가 코드 방식을 사용하는 이유는 보안 때문입니다. 만약 인가 코드가 아닌 Access token을 바로 발급받는다고 하면 redirect_url의 쿼리값으로 전달 받게 될텐데 그 말은 Access token의 정보가 쉽게 노출된다는 이야기 입니다. 따라서 인가 코드를 사용해 비교적 안전하게 Access token을 보호할 수 있게 됩니다.

## Passport

![Passport](https://pbs.twimg.com/profile_images/599259952574693376/DMrPoJtc_400x400.png)
_출처 passport twitter_

### Passport란?
&nbsp;&nbsp;&nbsp;&nbsp;마지막으로 passport에 대해서 알아봅시다. passport는 Node.js용 미들웨어로 페이스북, 트위터, 구글 등 여러 서비스에 쉽게 인증할 수 있도록 도와줍니다. 직역하자면 여권이라는 뜻을 가지고 있는 것처럼 우리 미들웨어를 사용하면 다양한 서비스에 인증을 쉽게 받을 수 있다는 것을 강조하는 것 같습니다. 또한 passport는 Nest.js에서도 추천하는 검증된 모듈입니다. 사용자의 아이디, 패스워드 그리고 다양한 데이터를 주고 받아야하는 상황에서 검증되지 않은 모듈을 사용하는 것은 상당히 위험한데 그런 면에서 passport는 상당히 안전하다고 볼 수 있습니다.

>💡 미들웨어란?
> 미들웨어는 서로 다른 애플리케이션이 서로 통신하는데 사용되는 소프트웨어 입니다. 서로 다른 애플리케이션을 이어주는 다리라고 생각하시면 됩니다.

### Passport 사용법
&nbsp;&nbsp;&nbsp;&nbsp;일단 Passport를 사용하기전에 우리가 사용하고자하는 API를 가진 서비스(Google, Facebook 등)에 애플리케이션을 등록해야합니다.
- [Google API](https://console.developers.google.com/apis)
- [Facebook API](https://developers.facebook.com/)

&nbsp;&nbsp;&nbsp;&nbsp;사용하고자 하는 서비스에 애플리케이션을 등록하면 OAuth2.0의 경우 3가지 정보를 획득할 수 있습니다.
- Client ID
  - 클라이언트 ID는 우리가 등록한 에플리케이션의 식별자입니다. 해당 식별자를 통해 어떤 애플리케이션에서 요청을 보냈는지 확인할 수 있습니다. 클라이언트 ID는 공개되는 정보이며 애플리케이션 등록시 자동으로 생성되는 값입니다.
- Client Secret
  - 클라이언트 Secret은 인증에 사용되는 비공개 값으로 클라이언트 ID로 애플리케이션을 식별하고 클라이언트 Secret을 통해 요청을 보낸 클라이언트를 인증해주는 역할을 합니다.
- Redirect URL
  - Redirect URL은 인가과정이 종료 후 인가 서버 측에서 사용자를 리다이렉션할 URL입니다. 성공적으로 인가과정이 종료되었으면 해당 URL뒤에 인가코드 혹은 Access token을 쿼리값으로 넘겨줍니다.

&nbsp;&nbsp;&nbsp;&nbsp;다시 본론으로 넘어와서 Passport는 애플리케이션 등록을 통한 3가지 정보를 Strategy(전략)으로 사용하여 Passport 내부적으로 인가 서버와 인가 절차를 마친 뒤 우리가 원하는 정보인 Access token, Refresh token, Profile 등을 반환해줍니다.

![passport-google-oauth20](/assets/img/passport-google-oauth20.png)
_출처 Passportjs.org_

&nbsp;&nbsp;&nbsp;&nbsp;위 사진은 passport-google-oauth20의 예시인데 애플리케이션등록으로 획득한 정보들을 GoogleStrategy에 넣어주면 passport가 인가 과정을 진행하고 내부적으로 function 함수를 호출해 내부적으로 정의된 동작을 실행시켜줍니다. 이 떄 function 함수에서 Access token, Refresh token, Profile 정보를 검사하고 저장하는 함수를 구현하여 데이터를 가져오는 방식으로 구현 할 수도 있습니다.

&nbsp;&nbsp;&nbsp;&nbsp;OAuth와 Passport에 대해서 간략하게 알아봤습니다. Passport의 자세한 사용법(Nest.js에서 Passport)는 추후에 차차 알아보도록 합시다.

## Reference
<https://oauth.net/2/>
<https://ko.wikipedia.org/wiki/OAuth>
<https://cloud.google.com/apigee/docs/api-platform/security/oauth/oauth-introduction?hl=ko>
<https://d2.naver.com/helloworld/24942>
<https://itwiki.kr/w/OAuth>
<https://www.passportjs.org/>

잘못된 정보에 대한 피드백, 수정요청은 언제나 환영입니다!
