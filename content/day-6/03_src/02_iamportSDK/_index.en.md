---
title: 아이엠포트SDK
weight: 10
pre: "<b>2. </b>"
chapter: true
---

### 소스 파일들

# 아이엠포트 SDK

아이엠포트에서 안드로이드용으로 개발할 때 쓰라고 만들어둔 개발 키트.

https://github.com/iamport/iamport-inicis-graddle

# 대충 실행과정

1. 아이엠포트 관리자 페이지에서 PG사 지정 후 저장.
2. 안드로이드 웹뷰를 통해서 아임포트를 실행한다.
  - 인텐트에 import 라는 스킴 설정
  - 결제 완료 후 리다이렉트 될 곳 설정
3. 웹뷰에서 각 카드사 등의 ISP 앱연결을 위한 네이티브 코드 작성
  - WebViewClient.shouldOverrideUrlLoading() 으로 특정 url 스킴에 따라 각각의 앱 실행(intent)
4. 외부 앱으로 이동할 경우 이용 후 다시 원래 주소로 리다이렉트 되도록 네이티브 코드 처리
  - onNewIntent 메소드 오버라이딩 해서, 새 인텐트 생성시 담고 있는 스킴주소를 웹뷰에 로드하도록 함.
5. 결제 완료되면 웹뷰가 리다이렉트되며 성공한 결제 정보가 쿼리 스트링으로 전달된다.
  - m_redirect_url : 'https://www.yourdomain.com/payments/complete'
  - 실제 이동 : https://www.yourdomain.com/payments/complete? imp_uid=imp_12345678&merchant_uid=oid_987654321&imp_success=true
http://www.iamport.kr/getstarted
  - API 로 실제 주문금액과 일치하는지 최종 검증을 한다. https://api.iamport.kr/

# 분석

### InicisWebViewClient

```java

// 웹뷰 클라이언트 서브클래싱(상속 혹은 확장)
public class InicisWebViewClient extends WebViewClient {

  // 빈 액티비티 변수 선언
	private Activity activity;

  // 클래스 생성자 - 액티비티를 받아서 내부 변수에 할당
	public InicisWebViewClient(Activity activity) {
		this.activity = activity;
	}
```

여기까지는 뭐... 통상의 웹뷰 클라이언트의 작동이 아닌 우리가 원하는 작동을 하는
웹뷰 클라이언트가 필요하기 때문에 상속하고 오버라이딩 하는 거.

https://developer.android.com/reference/android/webkit/WebViewClient.html

```java

	@Override
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
```

현재 웹뷰에 새로운 url 이 막 불러와질 때 호스트 어플리케이션에게 컨트롤을 빼앗아 올 수 있는 기회를 준다.
웹뷰 클라이언트가 제공되지 않으면, 기본적으로 웹뷰가 액티비티 매니저에게 해당 url 에 적절한 핸들러를 선택하도록 요청한다.
웹뷰 클라이언트가 제공되면, `true` 면 호스트 어플리케이션이 url 을 다루고, `false` 면 현재 웹뷰가 url 을 다룬다.
POST 메소드를 사용한 리퀘스트에는 이 메소드가 호출될 수 없다.

https://developer.android.com/reference/android/webkit/WebViewClient.html#shouldOverrideUrlLoading(android.webkit.WebView,%20java.lang.String)
레벨 24부터 디프리케이티드 됐다고... 쩝. string 대신에 웹리소스리퀘스트 사용하게 한다.


```java

    // 만약 불러와질 url 이
    // http:// 혹은 https:// 혹은 javascript:// 로 시작하지 않는다면
		if (!url.startsWith("http://") && !url.startsWith("https://") && !url.startsWith("javascript:")) {

      // 인텐트 변수를 생성하고 비워버린다.
      Intent intent = null;

			try {
        // 웹 관련 url 를 파싱해서 인텐트를 생성한다.
        // Intent.URI_INTENT_SCHEME 는 처리 플래그인데 결과가 `intent:`를 포함하게 한다.
        // 안그럼 그냥 raw Uri 로 처리되면 VIEW 액션이 실행됨.
				intent = Intent.parseUri(url, Intent.URI_INTENT_SCHEME); //IntentURI처리

        // 인코딩된 문자열 URI 를 얻어온다.
        // 'intent:~~~'
        Uri uri = Uri.parse(intent.getDataString());

        // 엉? 기껏 만들어 놓은 인텐트에서 uri 만 뽑고 버리네? ㅡㅡ;;

        // ACTION_VIEW 로 uri 를 실행하는 인텐트를 만들어서 해당 액티비티를 시작(호출)한다.
				activity.startActivity(new Intent(Intent.ACTION_VIEW, uri));

        // 웹뷰가 아닌, 액티비티를 호스트로 시작했으므로, 이 호스트 어플리케이션(액티비티)가 웹뷰 대신 해당 페이지를 컨트롤 한다.
				return true;

      // URI 구문 오류가 있다면 (어차피 아무 액티비티도 실행 안 되니까) 걍 웹뷰가 해당 페이지를 컨트롤 하게 둔다.
      } catch (URISyntaxException ex) {
        return false;
```

> **parseURI uri** toUri(int)로 반환되었다면, 이 Uri 로부터 인텐트를 생성. URI 는 액션, 카테고리 그리고 다른 인텐트 필드를 인코드 할 수 있다.
만약 인텐트가 toUri() 로 생성되지 않았다면, 인텐트의 자료는 풀 URI 가 될 것이고
액션은 ACTION_VIEW 가 될 것이다.
https://developer.android.com/reference/android/content/Intent.html?hl=ko#parseUri(java.lang.String,%20int)

> **URI_INTENT_SCHEME** URI 가 언제나 "intent:" 스킴을 포함하도록 한다.
인텐트와 raw URI 를 구분하기 위해서 사용하며, raw URI 의 경우는 일반 VIEW 액션으로 실행이 된다.

> **URI(통합 자원 식별자)** URL 은 `http://`` 로 시작하는 경우만 해당하고, URI 는 더 상위의 개념이다. Web-oriented architecture (웹 기반의 구조문법) `kakaotalk://sendmsg?text=hello!`
`facebookmsg://like?url=mysite.com` 각각의 프로토콜을 해석할 수 있는 프로그램이 URI 를 핸들링한다.

```java

      // URI 구문은 잘 맞았는데 해당하는 액티비티가 (안드로이드 시스템 상에)없을 때...
			} catch (ActivityNotFoundException e) {
        // 인텐트 내부에 담긴 게 없을 때는 뭐.. 웹뷰가 해당 페이지 로드하게 하고..
				if ( intent == null )	return false;

        // 인텐트 있고, 인텐트에서 URI 도 뽑아냈는데 현재 휴대폰에 설치된 액티비티가 없다면
        // 결제스킴없음담당자에게 스킴을 찾아서 해당 스킴에 맞는 앱페이지로 마켓을 인텐트로 호출한 후
        // true 를 반환한다.
        // ISP 나 BANKPAY 인지 확인하고 맞으면 마켓 이동.
				if ( handleNotFoundPaymentScheme(intent.getScheme()) )	return true;

        // 인텐트도 있는데 액티비티가 없어서 찾아봤더니 우리가 가진 목록에 마켓이 패키지 정보가 없을 때는
        // 직업 패키지 정보를 추출한다음에
				String packageName = intent.getPackage();
		        if (packageName != null) {
                // 해당 패키지 설치 마켓 실행
		            activity.startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse("market://details?id=" + packageName)));
                // 그럼 ISP 앱도 다 설치됐으니 호스트 어플리케이션에서 해당 웹 페이지 컨트롤 빼앗음.
                // 그래야 결제 후 원하는 url 을 강제로 불러올 것 아니야.
                return true;
		        }

            // 패키키지 이름마저 없다면 포기.
		        return false;
			}
		}

    // 인텐트 뽑고 패키지 찾아서 마켓 이동하고 별지랄 다 해도 안 되면 그냥 포기
		return false;
	}

```

try catch 부분을 보면... handleNotFoundPaymentScheme 호출을 하는데..

jdk 1.8 부터 람다나 고차함수가 가능하지만, .. 고차함수라기 보다는 메소드 참조가
`System.out::println` 요런 형태를 써야 가능하다고 함.

여튼, 마켓에 가서 설치를 시켜놓고 백버튼 누르면 다시 들어오면, 호스트가 웹뷰의 해당 주소를 컨트롤하는 상태로 된다.

다른 소스를 보면 이렇게 컨트롤을 뺐어온 다음에 redirect 를 강제로 시켜벌임. 음... 아직 정확히는 모르겠다.

```java

	/**
	 * @param scheme
	 * @return 해당 scheme에 대해 처리를 직접 하는지 여부
	 *
	 * 결제를 위한 3rd-party 앱이 아직 설치되어있지 않아 ActivityNotFoundException이 발생하는 경우 처리합니다.
	 * 여기서 handler되지않은 scheme에 대해서는 intent로부터 Package정보 추출이 가능하다면 다음에서 packageName으로 market이동합니다.
	 *
	 */
	protected boolean handleNotFoundPaymentScheme(String scheme) {
		//PG사에서 호출하는 url에 package정보가 없어 ActivityNotFoundException이 난 후 market 실행이 안되는 경우
    // ISP 모바일 상수와 글자 하나씩 비교하는 string 메서드를 사용해서 같은지 다른지 검증
    if ( PaymentScheme.ISP.equalsIgnoreCase(scheme) ) {
      // 맞으면 마켓 이동
			activity.startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse("market://details?id=" + PaymentScheme.PACKAGE_ISP)));
      // 컨트롤 으라고 if 통과시키게 하기
      return true;
      // 뱅크페이도 검사
		} else if ( PaymentScheme.BANKPAY.equalsIgnoreCase(scheme) ) {
			activity.startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse("market://details?id=" + PaymentScheme.PACKAGE_BANKPAY)));
			return true;
		}

    // ISP 랑 뱅크페이 아니면 false..
		return false;
	}

}

```

왜 ISP 랑 뱅크페이만 체크할까?

### PaymentScheme

```java
public class PaymentScheme {

	public final static String ISP = "ispmobile";
	public final static String BANKPAY = "kftc-bankpay";
	public final static String HYUNDAI_APPCARD = "hdcardappcardansimclick"; //intent:hdcardappcardansimclick://appcard?acctid=201605092050048514902797477441#Intent;package=com.hyundaicard.appcard;end;
	public final static String KB_APPCARD = "kb-acp"; //intent://pay?srCode=5681318&kb-acp://#Intent;scheme=kb-acp;package=com.kbcard.cxh.appcard;end;


	public final static String PACKAGE_ISP = "kvp.jjy.MispAndroid320";
	public final static String PACKAGE_BANKPAY = "com.kftc.bankpay.android";

}
```
