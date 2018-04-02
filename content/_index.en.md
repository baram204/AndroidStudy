---
title: "안드로이드 7일 공부"
---

# 안드로이드 7일 공부

 안드로이드를 모르는 스타트업 사장님을 위한 속성 강좌용 웹사이트. 아이엠포트 연동 정도만
 목표로 하고 있습니다.

 {{% notice info %}}
 현재 작성자가 안드로이드를 공부 중입니다.
 {{% /notice %}}

 {{<mermaid>}}
gantt
        dateFormat  YYYY-MM-DD
        title 진도표
        section 개발자 공부 현황
        안드 개요              :done,        des1, 2018-03-31,1d
        헬로월드               :done,        des2, 2018-04-01, 1d
        뷰와레이아웃            :done,       des3, 2018-04-02, 1d
        고급위젯               :done,       des4, after des3, 1d
        인텐트                 :crit, done, des5, after des4, 1d
        웹뷰                   :crit, active, des6, after des5, 1d
        네트워크                : des7, after des6, 1d

{{< /mermaid >}}

## 주요 주제

* 안드 액티비티 - 뷰 기본
* 인텐트 - 자료를 담아서 호출되는 뷰에 보내기
* 웹브라우저 - 내부 웹브라우저에서 호출
* 아이엠포트 결제(브라우저 사용)
* [Automatic Search]({{%relref "basics/configuration/_index.md#activate-search" %}})

{{% notice info %}}
info
{{% /notice %}}

{{% notice tip %}}
팁
{{% /notice %}}

{{% notice note %}}
note
{{% /notice %}}
