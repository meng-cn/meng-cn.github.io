---
title: Android Q 文件存储
date: 2021-11-09 00:47:30
tags: 
- android 
- file
- "open failed: EACCES (Permission denied)"
categories: 
- android
- file
---
Q 之前，申请了文件权限可以随意在 external 存储读写数据。
从 Q 开始，即使申请了文件权限 在外部存储 保存文件、创建文件夹等操作仍然会报 android.system.ErrnoException: open failed: EACCES (Permission denied) 权限错误。
<!-- more -->

Q 之后，存储文件需要以下几个步骤:
- 新建 ContentValues 对象，设置文件名、文件类别等信息
- 通过 MediaStore 请求一个新的 Uri。使用 ContentResolver 把 content values 插入到库，并返回一个 Uri。使用这个 Uri 保存数据。
- cv.put(MediaStore.Audio.Media.IS_PENDING, 0), 更新数据库

以保存图片为例:
```
	// Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q
	void saveBitmap(Context context, Bitmap bitmap, String fileName){
		ContentValues contentValues = new ContentValues();
		contentValues.put(MediaStore.Images.Media.DISPLAY_NAME, fileName);
		contentValues.put(MediaStore.Images.Media.RELATIVE_PATH, Environment.DIRECTORY_DCIM);
		contentValues.put(MediaStore.Images.Media.MIME_TYPE, "image/jpeg");
		contentValues.put(MediaStore.Images.Media.IS_PENDING, 1);

		ContentResolver resolver = context.getContentResolver();
		Uri insertUri = resolver.insert(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, contentValues);
		try(OutputStream outputStream = resolver.openOutputStream(insertUri)){
			bitmap.compress(Bitmap.CompressFormat.PNG, 100, outputStream);
		}catch (Exception e){
			e.printStackTrace();
		}

		contentValues.clear();
		contentValues.put(MediaStore.Images.Media.IS_PENDING, 0);
		resolver.update(insertUri, contentValues, null, null);
	}
```

详见 [保存到共享的存储空间](https://developer.android.google.cn/training/data-storage/shared)