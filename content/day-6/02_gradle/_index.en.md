---
title: Gradle 설정
weight: 10
pre: "<b>2. </b>"
chapter: true
---

### Gradle 설정

# 실제 구조

```
C:.
│  build.gradle
│  settings.gradle
├─app
│  │  build.gradle
│  └─src
└─gradle
    └─wrapper
```

# buid.gradle

탑 폴더에서 정의된 빌드 설정이기 때문에 모든 하위 폴더에 적용되는 내용이다. 즉 모든
하위프로젝트에서 공통으로 사용될 내용을 기술해 놓는 용도다.

```groovy
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    /**
       * Gradle 리포지토리와 종속성을 정의하기 위해 buildscript {} 블록을 사용한다.
       * 빌드 라이브러리들을 검색하거나 다운로드할 때 사용될 저장소들을 정의한다.
       * Gradle pre-configures support for remote
       * repositories such as JCenter, Maven Central, and Ivy. You can also use local
       * repositories or define your own remote repositories.
       * 아래 코드는 해당 의존성을 찾을 때 jcenter 와 google 저장소를 찾아야 한다는
       * 의존성을 설정하는 것
       */
    repositories {
        jcenter()
        google()
    }
    /**
      * 프로젝트 빌드 시 gradle 이 필요로 하는 의존성을 정의한다.
      * 아래는 gradle 3.1.0 을 classpath 의존성으로 정해놓는다.
      */
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.0'
    }
}

/**
  * 프로젝트 내부의 모든 모듈에게 사용될 의존성을 정의한다. 예를 들자면 3rd 플러그인..
  * 모든 모듈이 아니라 특정 하위 모듈이 사용할 의존성은 여기에 기술하지 않는다.
  * 아래는 저장소만 기술하고 의존성은 기술하지 않았다.
  */
allprojects {
    repositories {
        jcenter()
    }
}
```

# settings.gradle

빌드 시 포함될 앱(폴더명)

```
// 즉, 여기에 적히지 않은 폴더는 빌드시 제외된다는 말.
include ':app'
```

구체적으로 app 폴더 내부의 설정을 알아보자.

# app 폴더 하위의 build.Gradle

```groovy
/**
  * 아래의 anroid{} 블록의 빌드와 작성을 가능하게 하는 플러그인을 정의한다.
  */
apply plugin: 'com.android.application'

/**
  * 모든 안드로이드 특정 설정이 담기는 곳이다.
  * android 말고 java, scala, groovy 등의 설정이 담기는 곳도 존재할 수 있다는 말인듯.
  */
android {
    /**
      * 컴파일 시 사용할 SDK 버전과, 그래들이 빌드할 때 사용할 SDK 버전 정의
      */
    compileSdkVersion 21
    buildToolsVersion "26.0.0"

    /**
     * 기본 설정 블록은,모든 빌드 기본 설정을 캡슐화 하고 모든 빌드 변수들을 등재한다.
     * 그러면, main/AndroidManifest.xml 의 몇몇 속성을 동적으로 대체 할 수 있다.
     * 다른 버전의 앱을 위해서 제품 기호에 맞게 아래의 값들을 변경할 수 있다.
     */
    defaultConfig {
        /**
          * 배포를 위한 유일한 ID 를 정의한다.
          * 그런데 main/AndroidManifest.xml 에서 정의한 패키지명을
          * 소스에서 참조할 수 있다.
        applicationId "com.siot.inicissample"

        // 앱을 실행하기 위한 최소 API
        minSdkVersion 8
        // 앱을 테스트하는데 사용될 API 레벨
        targetSdkVersion 21

        // 버전 넘버
        // versionCode 1

        // 사용자 친화적인 버전 넘버
        // versionName "1.0"
    }

    /**
     * 다중 빌드 타입을 설정할 수 있다.
     * 기본 : debug 와 release.
     * 디버그 빌드는 기본 빌드 설정 내부에서 명시적으로 보이지 않지만 디버그 도구와
     * 디버그 키로 사인되어 있다.
     * 릴리즈는 프로카드 설정파일을 지정하며 기본적으로 사인되어 있지 않다.
     */
    buildTypes {
        release {
            minifyEnabled false // 릴리즈 빌드 타입에서는 코드를  축소시키지 않는다.
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.txt'
        }
    }

    /**
     * The productFlavors {} 다중 제품 선호를 설정할 수 있다. 제품 별로 defaultConfig{}
     * 블록의 설정을 대체할 수 있음. 이것은 기본이 아니다.
     * 아래는 free 와 paid 에 따른 어플리케이션 ID 를 지정해 준 것이다.
     */
    /* productFlavors {
    free {
      applicationId 'com.example.myapp.free'
    }

    paid {
      applicationId 'com.example.myapp.paid'
    } */


  /**
   * The splits {} block is where you can configure different APK builds that
   * each contain only code and resources for a supported screen density or
   * ABI. You'll also need to configure your build so that each APK has a
   * different versionCode.
   */

  /* splits {
    // Screen density split settings
    density {

      // Enable or disable the density split mechanism
      enable false

      // Exclude these densities from splits
      exclude "ldpi", "tvdpi", "xxxhdpi", "400dpi", "560dpi"
    }
  } */
}

/**
  * 의존성 블록은 모듈 레벨의 빌드 설정 파일이며, 모듈 자신을 빌드할 때 요구되는
  * 의존성만 지정한다.
  */
dependencies {
    compile 'com.android.support:support-v4:21.0.3'
    compile 'com.android.support:appcompat-v7:21.0.3'
}
```

# Gradle 속성 파일

* gradle.properties
  - 이 위치에서는 프로젝트 범위의 Gradle 설정을 구성할 수 있습니다(예: Gradle
   데몬의 최대 힙 크기). 자세한 내용은 빌드 환경을 참조하세요.
* local.properties
  - 빌드 시스템의 로컬 환경 속성을 구성합니다(예: SDK 설치의 경로).
   이 파일의 내용은 Android Studio에 의해 자동으로 생성되고 로컬 개발자
   환경에만 해당하므로, 이 파일을 직접 수정하거나 **버전 제어 시스템에 체크인해서는 안됩니다.**
