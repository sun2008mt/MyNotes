1. 在Android 6.0之后，危险权限需要动态申请(权限被分为基本权限和动态权限)，比如读取联系人信息(Manifest.permission.READ_CONTACTS)等；

2. 危险权限：

   <pre>
    (1) 日历(Calendar)： READ_CALENDAR，WRITE_CALENDAR

    (2) 相机(Camera)： CAMERA

    (3) 联系人(Contacts)： READ_CONTACTS，WRITE_CONTACTS，GET_ACCOUNTS

    (4) 位置(Location)： ACCESS_FINE_LOCATION，ACCESS_COARSE_LOCATION

    (5) 麦克风(Microphone)： RECORD_AUDIO

    (6) 手机(Phone)： READ_PHONE_STATE，READ_PHONE_NUMBERS，CALL_PHONE，ANSWER_PHONE_CALLS (must request at runtime)，READ_CALL_LOG，WRITE_CALL_LOG，ADD_VOICEMAIL，USE_SIP，PROCESS_OUTGOING_CALLS

    (7) 传感器(Sensors)： BODY_SENSORS

    (8) 短信服务(SMS)： SEND_SMS，RECEIVE_SMS，READ_SMS，RECEIVE_WAP_PUSH，RECEIVE_MMS

    (9) 存储(Storage)： READ_EXTERNAL_STORAGE，WRITE_EXTERNAL_STORAGE
   </pre>

3. 检查是否拥有某权限，使用ContextCompat.checkSelfPermission()方法，该方法返回int类型的状态(PackageManager.PERMISSION_GRANTED和PackageManager.PERMISSION_DENIED)；

4. 可以使用shouldShowRequestPermissionRationale()方法，返回true表示应用之前请求过某权限但是被用户拒绝了(没有选择Don't ask again选项)；

5. 使用requestPermissions()方法动态请求权限

```
if (ContextCompat.checkSelfPermission(thisActivity,Manifest.permission.READ_CONTACTS) != PackageManager.PERMISSION_GRANTED) {

    // Should we show an explanation?
    if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,Manifest.permission.READ_CONTACTS)) {
        // Show an explanation to the user *asynchronously* -- don't block this thread waiting for the user's response! After the user sees the explanation, try again to request the permission.

    } else {
        // No explanation needed, we can request the permission.
        ActivityCompat.requestPermissions(thisActivity,
        new String[]{Manifest.permission.READ_CONTACTS},
        MY_PERMISSIONS_REQUEST_READ_CONTACTS);

       // MY_PERMISSIONS_REQUEST_READ_CONTACTS is an app-defined int constant. The callback method gets the result of the request.

    }
}
```

6.处理权限请求的回应，系统会返回权限请求结果在onRequestPermissionsResult()方法中
```
@Override
public void onRequestPermissionsResult(int requestCode,String permissions[], int[] grantResults) {

    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {

            // If request is cancelled, the result arrays are empty.
                if (grantResults.length > 0&& grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    // permission was granted, yay! Do the contacts-related task you need to do.

                } else {
                    // permission denied, boo! Disable the functionality that depends on this permission.

                }
                return;
        }
         // other 'case' lines to check for other permissions this app might request.

    }
}
```
