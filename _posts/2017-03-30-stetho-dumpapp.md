---
layout: post
title: "Stetho 확장하여 사용하기, Dumpapp"
slug: "stetho-dumpapp"
date: 2017-03-25 10:00:00 +0900
categories: blog
---

오늘은 Stetho를 확장하여 추가적인 기능을 구현하는 방법에 대해 알아보려고 합니다.
좀 더 정확히 이야기 하자면, stetho의 dumpapp 기능을 이용해서 다음과 같은 일이 가능합니다.

- 실행중인 앱의 SharedPreference 값을 임의로 바꿔줄 수 있습니다
- 실행중인 앱에 Toast를 띄울 수 있습니다
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

dumpapp 플러그인 출력 `$ ./dumpapp -l`
현재 사용가능한 플러그인 리스트가 나타납니다. 
![플러그인_리스트]({{ site.url }}/images/dumpapp/dumpapp-list.png)

SharedPreference에 저장된 값들이 출력됩니다.
![pref_리스트]({{ site.url }}/images/dumpapp/dumpapp-pref.png)

pref 명령어를 통해 SharedPreference myname에 저장된 값을 junki에서 other로 바꿔보았습니다. 이같은 기능을 이용하면 앱 테스트중 SharedPreference의 값을 바꾸고 싶을 때 쉽게 테스트가 가능합니다.
![pref_리스트]({{ site.url }}/images/dumpapp/dumpapp-pref2.png)

chrome developer tool에서도 pref값이 제대로 바뀐걸 확인할 수 있습니다.
![pref_devtool]({{ site.url }}/images/dumpapp/dumpapp-devtool.png)

# 커스텀 플러그인 만들기

커스텀 dumpapp 플러그인은 DumperPlugin 인터페이스를 구현해서 만들 수 있습니다. 다음과 같이 MyDumperPlugin클래스를 구현합니다.

```java
public class MyDumperPlugin implements DumperPlugin {
    @Override
    public String getName() {
        //plugin 이름 설정
        return "my_plugin";
    }

    @Override
    public void dump(DumperContext dumpContext) throws DumpException {
        //outstream에 packgename을 출력한다
        PrintStream out = dumpContext.getStdout();
        out.println(BaseApplication.getContext().getPackageName());
    }
}
```

getName()은 plugin list에 보여질 이름, dump()는 plugin이 수행할 동작을 나타냅니다. dump()는 여기까지 했으면 Plugin준비는 완료됐습니다.

하지만 custom plugin을 dumpapp에 등록하기 위해선 PluginsProvider를 구현해야합니다. 다음과 같이 커스텀 플러그인 list를 반환해줌으로써 plugin 사용이 가능해집니다.

```java
public class MyDumperPluginProvider implements DumperPluginsProvider {
    @Override
    public Iterable<DumperPlugin> get() {
        // 플러그인 리스트 생성
        ArrayList<DumperPlugin> plugins = new ArrayList<>();
        // 하나 이상의 커스텀 플러그인을 추가
        plugins.add(new MyDumperPlugin());
        // MyDumperPluginProvider에는 기본 플러그인이 포함되어 있지 않으므로 포함시켜줘야한다
        for(DumperPlugin plugin: Stetho.defaultDumperPluginsProvider(BaseApplication.getContext()).get()){
            plugins.add(plugin);
        }
        // 리스트 반환
        return plugins;
    }
}
```

이제 마지막으로 새로 정의한 MyDumperPluginProvider를 stetho에 등록시켜줘야 합니다. 이렇게 custom plguin을 사용할 때는 stetho의 초기화 방법이 약간 달라집니다.

enableDumpapp의 인자로 방금 구현한 MyDumperPluginProvider를 주입해주면 됩니다.

```java
Stetho.initialize(Stetho.newInitializerBuilder(this)
                .enableDumpapp(new MyDumperPluginProvider())
                .enableWebKitInspector(Stetho.defaultInspectorModulesProvider(this))
                .build());
``` 



앱을 실행시키고 dumpapp을 통해서 확인해보면 plugin이 정상적으로 등록된 것과, app의 packagename을 제대로 보여주고 있는 것을 확인할 수 있습니다.
![dumpapp_custom]({{ site.url }}/images/dumpapp/dumpapp-custom.png)

# 어떤게 더 가능한가?
위와 같은 방식으로 다양한 plugin 제작이 가능하므로 앱 디버깅중 토스트 띄우거나, 특정 상황에서 테스트 코드를 수행시키는 등 정말 다양한 작업이 가능할 것 같습니다. 디버깅을 위한 코드가 앱 코드에 추가된다는 점이 조금 찝찝하지만 debug일 때만 빌드되게 설정한다면 크게 문제는 없을 것 같습니다.

# References

- https://www.slideshare.net/kingori/ss-68326596
- https://realm.io/kr/news/mobilization-gautier-mechling-the-2016-android-developer-toolbox/
- https://www.youtube.com/watch?v=iyXpdkqBsG8