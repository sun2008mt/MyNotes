Android
===

### 1.Android手机标识

#### (1) DEVICE_ID:
Android系统为开发者提供的用于标识手机设备的串号,根据不同的手机设备返回IMEI，MEID或ESN码，需要READ_PHONE_STATE权限
```
TelephonyManager tm = (TelephonyManager)getSystemService(Context.TELEPHONY_SERVICE);
String DEVICE_ID = tm.getDeviceId();
```

#### (2) Mac Address
可以使用手机Wifi或蓝牙的MAC地址作为设备标识,可能需要打开wifi和蓝牙权限

#### (3) Sim Serial Number
Android系统2.3版本以上可以获取Serial Number
```
String SerialNumber = android.os.Build.SERIAL;
```

#### (4) Installation ID
这种方式的原理是在程序安装后第一次运行时生成一个ID，该方式和设备唯一标识不一样，不同的应用程序会产生不同的ID，同一个程序重新安装也会不同。所以这不是设备的唯一ID，但是可以保证每个用户的ID是不同的。可以说是用来标识每一
份应用程序的唯一ID（即Installtion ID），可以用来跟踪应用的安装数量等。
```
public class Installation {
    private static String sID = null;
    private static final String INSTALLATION = "INSTALLATION";

    public synchronized static String id(Context context) {
        if (sID == null) {
            File installation = new File(context.getFilesDir(), INSTALLATION);
            try {
                if (!installation.exists())
                    writeInstallationFile(installation);
                sID = readInstallationFile(installation);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
        return sID;
    }

    private static String readInstallationFile(File installation) throws IOException {
        RandomAccessFile f = new RandomAccessFile(installation, "r");
        byte[] bytes = new byte[(int) f.length()];
        f.readFully(bytes);
        f.close();
        return new String(bytes);
    }

    private static void writeInstallationFile(File installation) throws IOException {
        FileOutputStream out = new FileOutputStream(installation);
        String id = UUID.randomUUID().toString();
        out.write(id.getBytes());
        out.close();
    }
}
```

### 2.Android多CPU架构支持
Android系统目前支持以下七种不同的CPU架构：ARMv5，ARMv7 (从2010年起)，x86 (从2011年起)，MipS (从2012年起)，ARMv8，MIPS64和x86_64 (从2014年起)，每一种都关联着一个相应的ABI。ABI是指应用基于哪种指令集来进行编译。 如果项目中使用到了NDK，它将会生成.so文件，Android应用支持的ABI取决于APK中位于lib/ABI目录中的.so文件，其中ABI可能是上面说过的七种ABI中的一种。 Android包管理器安装APK时，如果在对应的lib／ABI目录中存在.so文件的话，会自动选择APK包中为对应系统ABI预编译好的.so文件。当一个应用安装在设备上，只有该设备支持的CPU架构对应的.so文件会被安装。在x86设备上，libs/x86目录中如果存在.so文件的 话，会被安装，如果不存在，则会选择armeabi-v7a中的.so文件，如果也不存在，则选择armeabi目录中的.so文件（因为x86设备也支 持armeabi-v7a和armeabi）。

如果我们平时开发过程中没有很好注意这些，那么就会带来一些兼容性的问题。 比如：你的App支持armeabi-v7a和x86架构，然后使用 Android Studio 新增了一个函数库依赖，这个函数库包含.so文件并支持更多的CPU架构，那么在某些进行上可能会发生Crash，会出现"UnsatisfiedLinkError"，"dlopen: failed"等等问题。这是因为，你添加的一些库可能里面做了更多的架构适配，因为只要出现了这个目录，系统就只会在这个目录里找.so文件而不会遍历其他的目录，所以就出现了找不到so文件的情况（因为其他目录没有这个so文件）。 举个例子来详细说明一下：我们的APP只支持armeabi-v7a和x86架构，然后我们的APP使用了一个第三方的Library，而这个Library提供了AMR64等更多类型CPU架构的支持，构建APK的时候，这些ARM64的SO库依然会被打包进APK里面，也就是说我们自己的SO库没有对应的ARM64的SO库，而第三方的Library却有。这时候，某些ARM64的设备安装该APK的时候，发现我们的APK里带有ARM64的SO库，会误以为我们的APP已经做好了AMR64的适配工作，所以只会选择安装APK里面ARM64类型的SO库，这样会导致我们自己项目的SO库没有被正确安装（虽然armeabi-v7a和x86类型的SO库确实存在APK包里面）。
