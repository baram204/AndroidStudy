---
title: 구조분석
weight: 5
pre: "<b>1. </b>"
chapter: true
---

### 구조분석 기본 내용

# 안드로이드 관점

![](https://developer.android.com/images/tools/projectview-p1.png?hl=ko)

* manifests
  * AndroidManifest.xml 파일을 포함합니다.
* java
  * JUnit 테스트 코드를 비롯한 Java 소스 코드 파일을 포함합니다. 이들 파일은 패키지 이름별로 구분됩니다.
* res
  * 코드가 아닌 모든 리소스(예: XML 레이아웃, UI 문자열, 비트맵 이미지 등)를 포함합니다. 이들 리소스는 해당 하위 디렉토리로 나뉩니다. 가능한 모든 리소스 유형에 대한 자세한 내용은 리소스 제공을 참조하세요.

구체적인 소스는 소스 분석에서 할 예정

{{% notice info %}}
더 자세히 https://developer.android.com/studio/projects/index.html?hl=ko#ProjectFiles
{{% /notice %}}

# 그래들 설정

![](https://developer.android.com/images/tools/studio/project-structure_2x.png?hl=ko)

* MyApp 이라는 프로젝트 상위 설정은 하위 폴더 설정에 영향을 미친다.
  * app 이라는 하위 폴더에 정의한 설정은 상위 설정을 기본으로 포함한다.

{{% notice info %}}
더 자세히 https://developer.android.com/studio/build/index.html?hl=ko
{{% /notice %}}
