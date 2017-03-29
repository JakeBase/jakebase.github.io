---
layout: post
title: "Stetho로 SharedPreference 제어하기"
slug: "stetho-dumpapp"
date: 2017-03-25 10:00:00 +0900
categories: blog
---
# Stetho로 SharedPreference 값 변경하기

오늘은 짧지만 유용한 팁을 하나 가져와봤습니다. 결론부터 말씀드리면 Stetho를 이용해 SharedPreference 값을 임의로 바꿔줄 수 있습니다.

그게 유용해?라고 하실수도 있지만.. SharedPreference 값을 바꿔가면서 개발해야 할 때는 꽤 유용할 것 같습니다.

최근에 최초 실행 시 한번만 노출되는 튜토리얼 페이지 작업을 할 때 shown flag로 SharedPreference를 사용했는데

수정 사항이 제대로 반영됐는지 보려면 앱관리자에서 캐시데이터를 삭제하거나, flag가 false이게 코드를 잠시 수정한다던지 하는 귀찮은 부분이 조금 있었습니다.

Stetho의 강력한 기능들에 대해선 얼마전에 Android Yobi에 간략한 소개와 함께 공유한 적이 있었는데요, 오늘은 dumpapp 기능에 대해 조금 자세히 다뤄보려고 합니다.

(Stetho 설정 방법이나 사용법은 아래 링크에서 확인가능합니다~ )

> http://yobi.navercorp.com/Mobile/Android/post/127

# dumpapp?

공식 사이트의 설명에 따르면 dumpapp은 chrome의 developer tool 보다 더 확장된 기능을 원하는 개발자들에게 

제공되는 커맨드 라인 기반의 확장 기능입니다. 기본적으로 몇가지 기능이 제공되고 더 중요한 점은 직접 dumpapp plugin을 만들 수 있다는 것입니다.

