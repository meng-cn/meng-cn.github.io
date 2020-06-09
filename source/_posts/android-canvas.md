---
title: Android Paint 透明度
date: 2020-06-09 21:38:37
tags:
- android
categories: 
- android
- canvas
- paint
---
paint.setAlpha() 实际修改了当前 paint 颜色的透明度值，所以 paint.setColor() 操作也会覆盖事先设置的透明度。
<!-- more -->

Usage
------
```
// alpha will be reset.
paint.setAlpha(alpha);
...
paint.setColor(color);

// keep alpha
paint.setColor(color);
...
paint.setAlpha(alpha);

or 

paint.setColor((paint.getAlpha() << 24) | (color & 0x00ffffff));
```

Android Source
------
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

    /**
    * Set the paint's color. Note that the color is an int containing alpha
    * as well as r,g,b. This 32bit value is not premultiplied, meaning that
    * its alpha can be any value, regardless of the values of r,g,b.
    * See the Color class for more details.
    *
    * @param color The new color (including alpha) to set in the paint.
    */
    public void setColor(@ColorInt int color) {
        nSetColor(mNativePaint, color);
        mColor = Color.pack(color);
    }
```