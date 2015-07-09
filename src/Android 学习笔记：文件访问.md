#Internal Storage 内部存储空间
内部存储空间也就是userdata分区（/data/data/`application package`/）,这个文件夹下的内容只允许改应用访问，安全性高。在应用被卸载的时候，数据会同时被删除。

##context.getFileDir（）
/data/data/`application package`/files
openFileOutput()函数创建的文件也在这个文件夹下。
##context.getCacheDir()
/data/data/`application package`/cache  缓存


#External Storage  外部存储空间
外部存储空间，即sd卡分区（/mnt/sdcard/），可以被任意应用访问，一般空间比较大。
##context.getExternalFilesDir(String type)    
 /mnt/sdcard/Android/data/`application package`/files/ 

##context.getExternalCacheDir()
/mnt/sdcard/Android/data/`application package`/cache/

##Environment.getExternalStoragePublicDirectory(String type)

type值：
 - DIRECTORY_ALARMS                作为闹钟的声音
 - DIRECTORY_DCIM                     照相机拍摄的照片
 - DIRECTORY_DOCUMENTS       文档
 - DIRECTORY_DOWNLOADS     其他下载的内容
 -  DIRECTORY_MOVIES              所有的电影
 - DIRECTORY_MUSIC                音乐
 - DIRECTORY_NOTIFICATIONS   通知声音
 - DIRECTORY_PICTURES              所有的图片（不包括那些用照相机拍摄的照片）
 - DIRECTORY_PODCASTS          音/视频的剪辑片段
 - DIRECTORY_RINGTONES        铃声


```java
void createExternalStoragePublicPicture() {
    // Create a path where we will place our picture in the user's
    // public pictures directory.  Note that you should be careful about
    // what you place here, since the user often manages these files.  For
    // pictures and other media owned by the application, consider
    // Context.getExternalMediaDir().
    File path = Environment.getExternalStoragePublicDirectory(
            Environment.DIRECTORY_PICTURES);
    File file = new File(path, "DemoPicture.jpg");

    try {
        // Make sure the Pictures directory exists.
        path.mkdirs();

        // Very simple code to copy a picture from the application's
        // resource into the external file.  Note that this code does
        // no error checking, and assumes the picture is small (does not
        // try to copy it in chunks).  Note that if external storage is
        // not currently mounted this will silently fail.
        InputStream is = getResources().openRawResource(R.drawable.balloons);
        OutputStream os = new FileOutputStream(file);
        byte[] data = new byte[is.available()];
        is.read(data);
        os.write(data);
        is.close();
        os.close();

        // Tell the media scanner about the new file so that it is
        // immediately available to the user.
        MediaScannerConnection.scanFile(this,
                new String[] { file.toString() }, null,
                new MediaScannerConnection.OnScanCompletedListener() {
            public void onScanCompleted(String path, Uri uri) {
                Log.i("ExternalStorage", "Scanned " + path + ":");
                Log.i("ExternalStorage", "-> uri=" + uri);
            }
        });
    } catch (IOException e) {
        // Unable to create file, likely because external storage is
        // not currently mounted.
        Log.w("ExternalStorage", "Error writing " + file, e);
    }
}

void deleteExternalStoragePublicPicture() {
    // Create a path where we will place our picture in the user's
    // public pictures directory and delete the file.  If external
    // storage is not currently mounted this will fail.
    File path = Environment.getExternalStoragePublicDirectory(
            Environment.DIRECTORY_PICTURES);
    File file = new File(path, "DemoPicture.jpg");
    file.delete();
}

boolean hasExternalStoragePublicPicture() {
    // Create a path where we will place our picture in the user's
    // public pictures directory and check if the file exists.  If
    // external storage is not currently mounted this will think the
    // picture doesn't exist.
    File path = Environment.getExternalStoragePublicDirectory(
            Environment.DIRECTORY_PICTURES);
    File file = new File(path, "DemoPicture.jpg");
    return file.exists();
}
```