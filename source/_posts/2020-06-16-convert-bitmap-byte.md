---
title: Android Bitmap 转 opencv Mat data
date: 2020-06-16 23:06:13
tags: 
- android convert mat data
categories:
- android
- cv
- bitmap
---
---

opencv Mat 中保存的 data， 8uc3 是 bgr 序列，8uc4 是 bgra 序列。而安卓中，生成 Bitmap ARGB_8888 或从 Bitmap ARGB_8888 获取的 byte 数组都是 rgba 序列数据，接口调用如果传 opencv 读取的 byte 数组时需要换一下 r/b 位置。
<!-- more -->
处理图片像素计算量较大，一般写入 jni 处理。以下是相关代码。

```
Bitmap bitmap = ...; // ARGB_8888 format
ByteBuffer buffer = ByteBuffer.allocate(bitmap.getByteCount());
bitmap.copyPixelsToBuffer(buffer);
byte[] array = buffer.array(); // rgba 序列
```

```
cv::Mat M1(3, 3, CV_8UC3);  // bgr
cv::Mat M1(3, 3, CV_8UC4);  // bgra
```


```
// 8uc3 to bitmap bytes
void convert_8uc3(unsigned char* bitmap, unsigned char* src, int len){
    // len(bitmap) = len(src) / 3 * 4
    int pos_src, pos_dst;
    for (int index = 0; index < len / 3; ++index) {
        pos_src = index * 3;
        pos_dst = index << 2;
        bitmap[pos_dst] = src[pos_src + 2];  // r
        bitmap[pos_dst + 1] = src[pos_src + 1];  // g
        bitmap[pos_dst + 2] = src[pos_src];  // b
        bitmap[pos_dst + 3] = 255;  // a
    }
}

// 8uc4 to bitmap bytes
void convert_8uc4(unsigned char* bitmap, unsigned char* src, int len){
    // len(dst) = len(src)
    int pos;
    for (int index = 0; index < len / 4; ++index) {
        pos = index << 2;
        bitmap[pos] = src[pos + 2];  // r
        bitmap[pos + 1] = src[pos + 1];  // g
        bitmap[pos + 2] = src[pos];  // b
        bitmap[pos + 3] = src[pos + 3];  // a
    }
}

// bitmap bytes to 8uc3
void convert_to_8uc3(unsigned char* bitmap, unsigned char* dst, int len){
    // len(dst) = len(src) / 4 * 3
    int pos_src, pos_dst;
    for (int index = 0; index < len / 4; ++index) {
        pos_src = index << 2;
        pos_dst = index * 3;
        dst[pos_dst] = bitmap[pos_src + 2];  // b
        dst[pos_dst + 1] = bitmap[pos_src + 1];  // g
        dst[pos_dst + 2] = bitmap[pos_src];  // r
    }
}

// bitmap bytes to 8uc4
void convert_to_8uc4(unsigned char* bitmap, unsigned char* dst, int len){
    // len(dst) = len(src)
    int pos;
    for (int index = 0; index < len / 4; ++index) {
        pos = index << 2;
        dst[pos] = bitmap[pos + 2];  // b
        dst[pos + 1] = bitmap[pos + 1];  // g
        dst[pos + 2] = bitmap[pos];  // r
        dst[pos + 3] = bitmap[pos + 3];  // a
    }
}
```