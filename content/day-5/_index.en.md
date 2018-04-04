---
title: 일차
weight: 6
pre: "<b>5 </b>"
chapter: true
---

### 5 일차

# Gradle

https://medium.com/@goinhacker/%EC%9A%B4%EC%98%81-%EC%9E%90%EB%8F%99%ED%99%94-1-%EB%B9%8C%EB%93%9C-%EC%9E%90%EB%8F%99%ED%99%94-by-gradle-7630c0993d09

# 웹에서 안드로이드 앱 실행

### 커스텀 URL 스키마로 안드로이드 액티비티 실행하기

AAA.class 라는 액티비티 실행하려면

```java
Intent intent = new Intent(this,AAA.class);
startActivity(intent);
```

그럼 특정 Uri 를 통해서 인텐트를 실행하려면

```java
String url ="http://gun0912.tistory.com";
Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
startActivity(intent);
```

https://medium.com/@gun0912/android-start-activity-by-custom-url-scheme-eada62fdf9eb

# 웹뷰

http://g-y-e-o-m.tistory.com/28
