---
title: AndroidManifests.xml
weight: 5
pre: "<b>1. </b>"
chapter: true
---

### 소스 파일들

# AndroidManifests.xml ?

Manifest 는 보통 외부의 다른 것과 상호작용하는 방식을 기술하는 명세서라고 볼 수 있다.

구체적으로, 안드로이드 시스템과 앱이 상호작용할 수 있도록 앱의 필수 정보를 시스템에 제공한다.

https://developer.android.com/guide/topics/manifest/manifest-intro.html?hl=ko#filestruct

# 분석

`<manifest>` 및 `<application>` 요소만 필수입니다.

### Manifest 요소

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.siot.inicissample"
    android:versionCode="1"
    android:versionName="1.0" >
```

- com.siot.inicissample은 manifest 파일 내에서 참조하는 모든 클래스들의 상대 경로가 된다.
- 아래 `<application android:name=".MainActivity"`면 `com.siot.inicissample.MainActivity` 다.
- 기본 프로세스가 된다.
  - `<application android:name="` 혹은 `<service android:name="` 처럼 서브 클래스
  지정하지 않으면 이게 프로세스에서 돌아간다.

버전 코드나 이름.. 이거 gradle 에서 오버라이드 할 수 있고..

여튼 안드로이드 시스템은 이 앱을 읽게되면, com.siot.inicissample 을 베이스로 알아 먹는다.

https://developer.android.com/guide/topics/manifest/manifest-element.html?hl=ko

### user-sdk

```xml
    <uses-sdk
        android:minSdkVersion="8"
        android:targetSdkVersion="21" />
```

이것도 gradle 설정 볼 때 본 변수. 최소 버전을 만족시키지 못하는 안드로이드 시스템에는 설치를 막는다.

타겟은 테스팅이 된 버전을 적는다. 이거보다 높은 버전이면 추가 기능이 활성화되지 않게 막는다.

maxSdkVersion 도 있는데... 이것도 이 버전 이상이면 설치 막는 것.

레퍼런스에서는 하위호환 되도록 새 버전을 출시하므로 max 는 사용하지 말 것을 권고한다.

https://developer.android.com/guide/topics/manifest/uses-sdk-element.html?hl=ko

### uses-permission

애플리케이션이 올바르게 작동하기 위해 안드로이드 시스템으로부터 부여받아야 하는 권한을 요청한다.

android 로 시작하는 건, 시스템에 미리 정의된 권한이기 때문이다.

```xml
    <uses-permission android:name="android.permission.INTERNET" />
```

인터넷 권한의 경우 네트워크 소켓을 열 수 있게 허용해준다.

즉 네트워크 소켓을 열고 안드로이드 시스템을 통해서 외부와 통신할 수 있게 해준단 말이다.

- [인터넷 권한](https://developer.android.com/reference/android/Manifest.permission.html?hl=ko#INTERNET)
- [시스템 권한 목록](https://developer.android.com/reference/android/Manifest.permission.html?hl=ko)
- [유저퍼미션](https://developer.android.com/guide/topics/manifest/uses-permission-element.html?hl=ko)

### application

요소는 모든 컴포넌트에 영향을 미칠 수 있는 속성을 가지고 있고 어플리케이션의 컴포넌트를
각각 정의하는 서브엘리먼트를 포함하는 엘리먼트다.

```xml
    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
```

https://developer.android.com/guide/topics/manifest/application-element.html?hl=ko#aff

### 액티비티

시각적 사용자 인터페이스 요소를 구현하는 액티비티 클래스 선언. 모든 액티비티는 선언되어야 함.
 여기에 선언되지 않는다면 안드로이드 시스템이 표시하지 않고 실행도 안 된다.

```xml
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name" >
            <!-- 사용될 액티비티(화면 & DB 컨트롤) 이름과 라벨 설정 -->
```

액티비티를 구현하는 서브 클래스 이름, `패키지+클래스` 여야 하는데 매니패스트에 패키지 적어 놓은 경우에
`.클래스명`으로 표시할 수 있다.


https://developer.android.com/guide/topics/manifest/activity-element.html?hl=ko#aff

### 인텐트 필터 (엔트리 포인트가 되는)

액티비티, 서비스, 혹은 브로드캐스트 리시버가 반응할 수 있는 (호출 요청의) 인텐트 타입을 지정한다.
인텐트 필터는 그것의 부모 컴포넌트의 능력을 선언한다. - 액티비티와 서비스가 무엇을 할 수 있는지,
어떤 브로드캐스트 리시버를 다룰 수 있는지. 컴포넌트에게 의미없는 것들을 걸러내면서, 광고된 타입의
인텐트를 수신하는 컴포넌트를 연다.

```xml
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
```

표준 액티비티 행동이 있다.

- xml에서 : android.intent.action.MAIN
- 액티비티 내에서 : Intent.Action_MAIN

> **Action_MAIN**  메인 엔트리 포인트 역할을 맡긴다. 안드로이드 시스템이 실행 엔트리 포인트를
찾고 있다면, 인텐트 필터로, "여기가 엔트리 포인트야, 이 액티비티를 실행해"라고 알려주는 것이다.

> **CATEGORY_LAUNCHER** 탑레벨 런처에서 이 액티비티가 보연다.

https://developer.android.com/guide/topics/manifest/intent-filter-element.html?hl=ko

### 엔트리 포인트 - 추가적인 액션

웹브라우저에서 "importtest://" 스킴을 가진 링크가 클릭될 때 MainActivity 가 안전하게 호출될 수 있고,
이것이 묵시적인 인텐트로 호출될 수 있음을 선언한다.

```xml
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />

                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />

                <data android:scheme="iamporttest" />
            </intent-filter>
        </activity>
```

> **ACTION_VIEW** 데이터를 사용자에게 보여준다. 이것은 자료에 대해 수행되는 가장 공통적인 액션이다.
이것은 어떤 자료의 조각이 가장 합리적으로 일어나야 하는 일을 일어나게 하는데 사용할 수 있다.
예를 들면, conatact 엔트리는 엔트리를 보여줄 것이고, mailto: 는 해당 URI 가 채워진 작성 윈도우를 가져올 것이다.
tel: 은 다이얼러를 호출할 것이고

> **CATEGORY_DEFAULT** Context.startActivity() 와 startActivityForResult() 에 의해서 발견되기 위해서 매우 자주 필요하다.
묵시적 인텐트 호출 시, 즉 거꾸로 말하면 안드로이스 시스템이 앱으로 묵시적 인텐트를 날릴 때 카테고리가 기본이어야 묵시적 인텐트를 수신할 수 있다.
다른 아무 카테고리가 없어도, default 로 선언되면 실행이 가능하다.

https://stackoverflow.com/questions/5727828/what-is-the-purpose-of-android-intent-category-default

> **CATEGORY_BROWSABL** 액티비티가 브라우저로부터 안전하게 호출되게 하려면 반드시 필요한 카테고리다.
예를 들자면, 사용자가 사용자가 웹페이지나 이메일을 보는 도중에 문자열에 있는 링크를 클릭한다면,
그 링크를 생성한 인텐트는 BROWSABLE 카테고리를 필요로 한다. 그러므로, 이 카테고리를 지원하는 액티비티만이
가능한 액션으로 고려될 것이다. 이 카테고리를 지원함으로써, 당신은 사용자의 개입 없이 아무 손상 없이 맞는 인텐트 호출이 일어날 거라 약속할 수 있다.

- `<data>` 는 인텐트 필터에 구체적인 데이터를 지정한다. mimeType, URI, type 과 URI 둘 다...
  - scheme 지정이 안 되면 모든 다른 URI 속성이 무시된다. 최소한 하나라도 정의가 되어 있어야 한다.
  - host 지정이 안 되면 port 속성과 다른 경로 속성이 무시된다.

  https://developer.android.com/guide/topics/manifest/data-element.html?hl=ko

예시

```
  ACTION_VIEW content://contacts/people/1 -- Display information about the person whose identifier is "1".
  ACTION_VIEW tel:123 -- Display the phone dialer with the given number filled in. Note how the VIEW action does what is considered the most reasonable thing for a particular URI.
  ACTION_VIEW content://contacts/people/ -- Display a list of people, which the user can browse through. This example is a typical top-level entry into the Contacts application, showing you the list of people. Selecting a particular person to view would result in a new intent { ACTION_VIEW content://contacts/people/N } being used to start an activity to display that person.
```

###  서브 액티비티를 만들어서 처리하는 방법

위에서는 메인 액티비티가 실행되고, 웹브라우저에서 특정 스킴 클릭 시 메인 액티비티에서 인텐트 수신이 되도록 했지만.
아래는 별도의 액티비티가 실행되도록 하는 설정이다.

```xml
        <!-- <activity
            android:name=".SubActivity"
            android:label="@string/app_name" >

            <intent-filter>
                <action android:name="android.intent.action.VIEW" />

                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />

                <data android:scheme="iamporttest" />
            </intent-filter>
        </activity> -->
    </application>

</manifest>
```
