---
layout: post
title: "Glide 4 - Transcoder"
tags: [Android, Glide]
comments: true
---

### Glide 3.x 에서 4.x 으로 마이그레이션 하기
기본적으로 Glide 4.x 의 사용방법은 Glide 3.x 와 크게 다르지 않다. [공식 페이지](http://bumptech.github.io/glide/doc/migrating.html) 에서도 migration 방법에 대해 자세히 안내하고 있다. 이 포스팅에서는 공식 가이드에서 자세히 설명되어 있지 않은 Transcoder, Transition, Option 등에 대해 기록해보고자 한다.

### Transcoder
Glide 3.x 에서는 Request를 생성할 때 .setTranscode() 메소드를 이용해 적용될 Transcoder 를 주입해주는 방식이었다. 하지만 Glide 4.x 에서는 .as([데이터타입]) 을 통해 최종적으로 생성할 결과물의 데이터 타입을 정하고 이 데이터 타입에 맞는 Transcoder이 런타임에 적용되는 방식으로 바뀌었다. 그렇기 때문에 Transcoder는 GlideModule에서 등록 되어야한다.

예를 들어 PaletteBitmap을 생성하는 Transcoder는 다음과 같이 등록한다. (https://github.com/bumptech/glide/issues/2417)


```java
public class PaletteBitmap {
    public final Bitmap bitmap;
    public final Palette.Swatch swatch;

    //생성자 생략
}
```

Transcoder 에서 사용될 Resource 객체

```java
public class PaletteBitmapResource implements Resource<PaletteBitmap> {
    private PaletteBitmap paletteBitmap;
    private BitmapPool bitmapPool;

    public PaletteBitmapResource(PaletteBitmap paletteBitmap, BitmapPool bitmapPool) {
        this.paletteBitmap = paletteBitmap;
        this.bitmapPool = bitmapPool;
    }

    @Override
    public Class<PaletteBitmap> getResourceClass() {
        return PaletteBitmap.class;
    }

    @Override
    public PaletteBitmap get() {
        return paletteBitmap;
    }

    @Override
    public int getSize() {
        return Util.getBitmapByteSize(paletteBitmap.bitmap);
    }

    @Override
    public void recycle() {
        bitmapPool.put(paletteBitmap.bitmap);
    }
}
```

 4.x에서는 options 파라미터가 추가됐다. 3.x에서는 런타임에 Transcoder를 생성해 주입하면서 Transcoder에 커스텀하게 옵션을 줄 수 있었지만 4.x 에서는 여기서 파라미터로 넘어오는 options를 이용해야한다. 커스텀 옵션은 GlideExtension을 이용해 구현하거나 options가 Key-Value 구조이기 때문에 GlideRequest 생성시 put 할 수 있다.

```java
public class PaletteBitmapTranscoder implements ResourceTranscoder<Bitmap, PaletteBitmap> {
    private final BitmapPool bitmapPool;

    public PaletteBitmapTranscoder(BitmapPool bitmapPool) {
        this.bitmapPool = bitmapPool;
    }

    @Override
    public Resource<PaletteBitmap> transcode(Resource<Bitmap> toTranscode, Options options) {
        Bitmap bitmap = toTranscode.get();
        Palette.Swatch swatch = PaletteUtils.getAverageSwatch(bitmap);
        return new PaletteBitmapResource(new PaletteBitmap(bitmap, swatch), bitmapPool);
    }
}
```

위에서 생성된 Transcoder는 GlideAppModule 초기화 시에 registry에 아래와 같이 등록돼야한다.

```java
@GlideModule
public class MyAppGlideModule extends GlideAppModule {
    @Override
    public void registerComponents(@Nullable Context context, @Nullable Glide glide, @Nullable Registry registry) {
        if (glide != null && registry != null) {
            registry.register(Bitmap.class, PaletteBitmap.class, new PaletteBitmapTranscoder(glide.getBitmapPool());
        }
    }
}
```

`.as(PaletteBitmap.class)` 요청을 하게 되면 GlideAppModule에서 주입한 PaletteBitmapTranscoder가 사용된다.

```java
GlideApp.with(fragment)
    .as(PaletteBitmap.class)
    .load(mediastoreUri)
    .into(new ImageViewTarget<PaletteBitmap>(holder.imageView) {
        @Override protected void setResource(PaletteBitmap resource) {
           // Set the bitmap and use the palette
        }
    });
```

### Reference
- [https://github.com/bumptech/glide](https://github.com/bumptech/glide)
- [http://bumptech.github.io/glide/doc/migrating.html](http://bumptech.github.io/glide/doc/migrating.html)