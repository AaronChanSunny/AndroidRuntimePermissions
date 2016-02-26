# Android 6.0 运行时权限

## 1. 引子

Android自诞生以来，其安全性一直被人们所诟病。加之，国内`安卓`App生态环境恶劣，只要是个应用，恨不得申请所有权限。谷歌或许也意识到这个问题，因此在[Marshmallow](https://www.android.com/versions/marshmallow-6-0/)包含了期待已久的运行时权限管理。以下是在Nexus6P（系统版本6.0.1）下的效果图：

![Runtime gif](screenshot/clip.gif)

## 2. M时代之前
在Android 6.0之前，一个应用所需要的权限会在安装的时候列出，如果用户想安装这个应用，只能全盘接受。国内应用一般都是权限毒瘤，这一直以来都是安卓用户的痛中之痛。主要体现在两点：

- 无法动态授权
- 无法知道应用什么时候使用了权限

如果M之前的代码不加修改，直接跑在M手机上，可能导致应用崩溃。

## 3. M时代
Android中的权限分为两大类：普通权限和危险权限，具体可以参考[开发文档](http://developer.android.com/intl/zh-cn/guide/topics/security/permissions.html#normal-dangerous)。在M手机上，对于敏感权限，需要在程序运行时进行动态申请。对于非敏感权限，即[Normal Permissions](http://developer.android.com/guide/topics/security/normal-permissions.html)，和M之前的使用相同。

### 3.1 请求权限

这里以最简单的电话权限举例。首先，需要在`AndroidManifest.xml`中声明`android.permission.CALL_PHONE`权限：

```
<uses-permission android:name="android.permission.CALL_PHONE"/>
```

接下来，在Activity中，使用[ContextCompat.checkSelfPermission()](http://developer.android.com/reference/android/support/v4/content/ContextCompat.html#checkSelfPermission(android.content.Context, java.lang.String))检查该权限是否授权，如果没有被授权，使用[requestPermissions()](http://developer.android.com/reference/android/support/v4/app/ActivityCompat.html#requestPermissions(android.app.Activity, java.lang.String[], int))进行权限申请。需要为每一个权限指定一个id，当系统返回授权结果时，应用根据id拿到授权结果。

### 3.2 授权回调

当应用申请权限后，Activity将触发一个回调[onRequestPermissionsResult()](http://developer.android.com/reference/android/support/v4/app/ActivityCompat.OnRequestPermissionsResultCallback.html#onRequestPermissionsResult(int, java.lang.String[], int[]))，告诉应用用户的授权结果。

```
@Override
public void onRequestPermissionsResult(int requestCode,
        String permissions[], int[] grantResults) {
    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
            // If request is cancelled, the result arrays are empty.
            if (grantResults.length > 0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {

                // permission was granted, yay! Do the
                // contacts-related task you need to do.

            } else {

                // permission denied, boo! Disable the
                // functionality that depends on this permission.
            }
            return;
        }

        // other 'case' lines to check for other
        // permissions this app might request
    }
}
```

## 参考

- [Working with System Permissions](http://developer.android.com/training/permissions/index.html)
- [Requesting Permissions at Run Time](http://developer.android.com/training/permissions/requesting.html)
