---
layout: post
title: "Stetho 확장하여 사용하기, Dumpapp"
slug: "stetho-dumpapp"
date: 2017-03-25 10:00:00 +0900
categories: blog
---
# Stetho 확장하여 사용하기, Dumpapp

오늘은 Stetho를 확장하여 추가적인 기능을 구현하는 방법에 대해 알아보려고 합니다.
좀 더 정확히 이야기 하자면, stetho의 dumpapp 기능을 이용해서 다음과 같은 일이 가능합니다.

- 디버깅 중인 앱의 SharedPreference 값을 임의로 바꿔줄 수 있습니다
- 디버깅 중인 앱에 Toast를 띄울 수 있습니다
- Dumpapp plugin을 구현한다면 다른 작업들도 추가적으로 구현할 수 있습니다

Stetho의 강력한 기능들에 대해선 얼마전에 Android Yobi에 간략한 소개와 함께 공유한 적이 있었는데요, 오늘은 dumpapp 기능에 대해 조금 자세히 다뤄보려고 합니다.

(Stetho 설정 방법이나 사용법은 아래 링크에서 확인가능합니다~ )
> http://yobi.navercorp.com/Mobile/Android/post/127

# dumpapp?

dumpapp은 stetho사용 시 chrome developer tool의 기능을 확장할 수 있게 해주는 커맨드라인 기반의 기능입니다. 기본적으로 stetho에서 제공해주는 기본적인 몇가지 plugin이 있고, 개발자가 추가적으로 dumpapp plugin을 만들어 기능을 확장할 수 있습니다.

- 커맨드라인에서 stetho를 이용해 미리 구현한 로직을 호출하는 방식입니다
- python script가 stetho를 설정할 때 등록한 플러그인을 호출해줍니다
- 실행하려면 python script가 필요합니다
- python 3.x가 설치되어있어야합니다 ( https://www.python.org/downloads/ )

Stetho 기본 설정
```java
public class MyApplication extends Application {
    public void onCreate() {
        super.onCreate();
        Stetho.initializeWithDefaults(this);
    }
}
```

dumpapp 사용 설정
```
$ git clone https://github.com/facebook/stetho.git
$ cd stetho/scripts
```

dumpapp 플러그인 출력
```
$ ./dumpapp -l
```
![플러그인_리스트]({{ site.url }}/images/dumpapp/dumpapp-list.png)
현재 사용가능한 플러그인 리스트가 나타납니다. 

![pref_리스트]({{ site.url }}/images/dumpapp/dumpapp-pref.png)
SharedPreference에 저장된 값들이 출력됩니다.

![pref_리스트]({{ site.url }}/images/dumpapp/dumpapp-pref2.png)
pref 명령어를 통해 SharedPreference myname에 저장된 값을 junki에서 other로 바꿔보았습니다. 이같은 기능을 이용하면 앱 테스트중 SharedPreference의 값을 바꾸고 싶을 때 쉽게 테스트가 가능합니다.

![pref_devtool]({{ site.url }}/images/dumpapp/dumpapp-devtool.png)
chrome developer tool에서도 pref값이 제대로 바뀐걸 확인할 수 있습니다.


# References

- https://www.slideshare.net/kingori/ss-68326596
- https://realm.io/kr/news/mobilization-gautier-mechling-the-2016-android-developer-toolbox/
- https://www.youtube.com/watch?v=iyXpdkqBsG8