---
title: Android Paint 透明度
date: 2020-06-09 21:38:37
tags: android canvas paint
---
### paint 设置透明度是修改当前颜色的透明度值，后设置32位颜色会覆盖透明度设置
<!-- more -->

```
// right
Paint paint = new Paint();
paint.setColor(0xffffffff);
paint.setAlpha(123);

// wrong, alpha will be reset.
Paint paint = new Paint();
paint.setAlpha(123);
paint.setColor(0xffffffff);
```

## source code
```
    /**
     * Helper to setColor(), that only assigns the color's alpha value,
     * leaving its r,g,b values unchanged. Results are undefined if the alpha
     * value is outside of the range [0..255]
     *
     * @param a set the alpha component [0..255] of the paint's color.
     */
    public void setAlpha(int a) {
        ColorSpace cs = Color.colorSpace(mColor);
        float r = Color.red(mColor);
        float g = Color.green(mColor);
        float b = Color.blue(mColor);
        mColor = Color.pack(r, g, b, a * (1.0f / 255), cs);
        nSetAlpha(mNativePaint, a);
    }
```