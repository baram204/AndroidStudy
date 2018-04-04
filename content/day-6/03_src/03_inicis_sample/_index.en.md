---
title: 이니시스 샘플
weight: 15
pre: "<b>3. </b>"
chapter: true
---

### 소스 파일들

# 이니시스 샘플

아이엠포트 SDK 와 웹뷰 사용해서 결제 데모 시연 페이지

https://github.com/iamport/iamport-inicis-graddle

# 분석

### InicisWebViewClient


```java
public class MainActivity extends Activity {

 // 웹뷰 변수 생성
 // 앱 스킴을 "importtest://"로 설정
	private WebView mainWebView;
	private static final String APP_SCHEME = "iamporttest://";
```
액티비티 서브 클래싱

```java
    @SuppressLint("NewApi") @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 메인 뷰 호출
        setContentView(R.layout.activity_main);
```
@SuppressLint("NewApi")

옛 버전(minSdkVersion) 이후의 새 버전의 API 메소드를 호출할 경우.

오작동이나 크래시가 날 수도 있으니. 경고를 보내는데 이 린트 경고를 무시한다.


```java
       // xml 레이아웃에서 웹뷰 컴포넌트 가져오기.
        mainWebView = (WebView) findViewById(R.id.mainWebView);
        // 웹 클라이언트 지정
        mainWebView.setWebViewClient(new InicisWebViewClient(this));
```

> **WebView** 기본적으로 보여주기만 한다.

```
Uri uri = Uri.parse("https://www.example.com");
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);
```

https://developer.android.com/reference/android/webkit/WebView.html

```java
        // 웹뷰를 컨트롤 하기위해 사되는 웹셋팅 객체 가져온다.
        WebSettings settings = mainWebView.getSettings();
        // 이 설정객체에 웹뷰에서 자바스크립트 허용설정.
        settings.setJavaScriptEnabled(true);

```

https://developer.android.com/reference/android/webkit/WebViewClient.html

```java
        // 빌드 SDK 버전이 롤리팝 이상이면
        if (android.os.Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
          // http https 섞인 컨텐츠 모드 항상 허용 설정.
          settings.setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
          // 또한 쿠키 긴으도 허용.
        	CookieManager cookieManager = CookieManager.getInstance();
        	cookieManager.setAcceptCookie(true);
        	cookieManager.setAcceptThirdPartyCookies(mainWebView, true);
        }

```

> **setMixedContentMode** Configures the WebVie\` behavior when a secure origin attempts to load a resource from an insecure origin. By default, apps that target KITKAT or below default to MIXED_CONTENT_ALWAYS_ALLOW. Apps targeting LOLLIPOP default to MIXED_CONTENT_NEVER_ALLOW. The preferred and most secure mode of operation for the WebView is MIXED_CONTENT_NEVER_ALLOW and use of MIXED_CONTENT_ALWAYS_ALLOW is strongly discouraged.

PG 사 결제화면 및 카드사 인증화면간 여러 번 http, https 리다이렉션 되는 과정에서 웹뷰 설정을
해줘야 한다고 함.

https://developer.android.com/reference/android/webkit/WebSettings.html

> **setAcceptThirdPartyCookies** Sets whether the WebView should allow third party cookies to be set. Allowing third party cookies is a per WebView policy and can be set differently on different WebView instances.

Apps that target KITKAT or below default to allowing third party cookies. Apps targeting LOLLIPOP or later default to disallowing third party cookies.


그리고 카드사 인증 단계에서 쿠기기능 활용하는게 필요하다고.. 요것도 롤리팝 이상부터 허용 설정이 필요함.

https://developer.android.com/reference/android/webkit/CookieManager.html


```java
        // getIntent 는 Acrivity 메서드.
        // 인텐트를 가져온다.(있으면)
        Intent intent = getIntent();
        // 인텐트 자료를 가져와서 Uri 객체에 담는다.
        Uri intentData = intent.getData();

        // 인텐트 데이터가 없을 경우...
        // 처음 실행이 되었고 다른 앱에서 인텐트를 넘기지 않았을 경우에
        if ( intentData == null ) {
          // 웹뷰가 아이엠포트를 로드하게 한다.
        	mainWebView.loadUrl("http://www.iamport.kr/demo");
//        	mainWebView.loadUrl("http://192.168.0.77:8888");
        } else {
          // 그게 아니라면.. 그니까 다른 곳에서 다시 메인 액티비티를 호출했을 때.
        	//isp 인증 후 복귀했을 때 결제 후속조치
        	String url = intentData.toString();
          // importtest:// 스킴으로 시작하는지 검사해서
        	if ( url.startsWith(APP_SCHEME) ) {
            // 거기로 리다이렉트 시켜버린다.
        		String redirectURL = url.substring(APP_SCHEME.length()+3);
                mainWebView.loadUrl(redirectURL);
        	}
        }
    }

```



```java

    @Override
    protected void onNewIntent(Intent intent) {
      // 액티비티의 인스턴스가 시작될 때, 인텐트 url 받아와서
    	String url = intent.getDataString();
      // importtest:// 스킴으로 시작되는지 검사해서
    	if ( url.startsWith(APP_SCHEME) ) {
        // 리다이렉트 시켜버린다.
    		String redirectURL = url.substring(APP_SCHEME.length()+3);
    		mainWebView.loadUrl(redirectURL);
    	}
    }

}
```

> **onNewIntent** This is called for activities that set launchMode to "singleTop" in their package, or if a client used the FLAG_ACTIVITY_SINGLE_TOP flag when calling startActivity(Intent). In either case, when the activity is re-launched while at the top of the activity stack instead of a new instance of the activity being started, onNewIntent() will be called on the existing instance with the Intent that was used to re-launch it.

An activity will always be paused before receiving a new intent, so you can count on onResume() being called after this method.

https://developer.android.com/reference/android/app/Activity.html#onNewIntent(android.content.Intent)
