# 使用adb工具開發AOSP

[上一章：燒入客製的AOSP image](/ch4_flash)

目前emulator裡面已經是你剛剛編好的AOSP image了。本章節我們會利用`adb`工具，將我們對AOSP的修改直接反應到emulator上。

## 簡介adb工具

`adb`工具全名`android debug bridg`，它是用來將電腦和android裝置連線的工具。它除了可以用如`adb logcat`的指令將`logcat`的內容從裝置讀出來之外，還可以從電腦將檔案上傳至android裝置(`adb push`)，或將android裝置上的檔案下載到電腦裡(`adb pull`)。

但`adb`最厲害的是，透過它我們可以將AOSP的改動直接*同步*到裝置上，而且不用重新編譯/燒錄整份AOSP。這將大大的增加我們開發AOSP的效率。

## adb sync 初體驗

首先我們先來修改`Activity`當成實驗。

請先將**設定好環境的終端機**移動到`$TOP/framework/base/core/`資料夾，再打開`android/view/ViewRootImpl.java`

```shell
$ cd framework/base/core
$ vim android/view/ViewRootImpl.java  # 或用你喜歡的編輯器打開這個檔案
```

接著找到`public ViewRootImpl(Context context, Display display)`，大概在360行附近:

```java
...
    public ViewRootImpl(Context context, Display display) {
        mContext = context;
        mWindowSession = WindowManagerGlobal.getWindowSession();
        mDisplay = display;
        mBasePackageName = context.getBasePackageName();
...
```

...
在該function的第一行裡面加上**`Log.i("===TAG===", "Hello, AOSP!");`**，變成這樣:

```java
...
    public ViewRootImpl(Context context, Display display) {
        Log.i("===TAG===", "Hello, AOSP!");
        mContext = context;
        mWindowSession = WindowManagerGlobal.getWindowSession();
        mDisplay = display;
        mBasePackageName = context.getBasePackageName();
...
```

接著回到終端機上，輸入`mm`

```shell
$ mm
```

`mm`指令只會在做過環境設定(請參考[編繹AOSP原始碼](/ch3_build)章節)的終端機上生效，它的效果是做部份編譯(partial build)。細節之後會提到，這邊先了解它可以避免重新編譯整個AOSP，而只改掉其中的一部份。這一步可能會需要一點時間，因為我們現在改的是AOSP中最大的library之一`framework.jar`。筆者大約花了六分鐘才完成這一步。如果覺得久也請不要擔心，這個已經是最久的library了，絕大部份library的輸入`mm`都是十幾秒至一分鐘內會有結果。

完成後，應該會看到類似如下的結果

```shell
...
Starting build with ninja
ninja: Entering directory `.'
[ 54% 6/11] Ensure Jack server is installed and started
Jack server already installed in "/Users/cha122977/.jack-server"
Server is already running
[100% 11/11] Install: out/target/produ..._x86_64/system/framework/framework.jar

#### make completed successfully (02:36 (mm:ss)) ####
```

注意到當中的`Install: out/target/produ..._x86_64/system/framework/framework.jar`就是指有一個`jar`檔被更新了(更新成我們加上一行log的版本)

接下來就是要將新的`framework.jar`送進你的裝置。常見的狀況是你不知道到底這個檔案要放在Android裝置的哪個地方，這時候就可以利用`adb sync`來幫你：

```shell
$ adb root # 如果你用的是 userdebug就需要加這行，確保adb有root權限。
$ adb remount
$ adb sync
```

*(注意這必需在做過環境設定的終端機上做才有用喔)*

大致上會看到如下結果

```
 /Volumes/android/aosp/frameworks/base/core/java/android/view/ [remotes/aosp/master*] adb remount
remount succeeded
 /Volumes/android/aosp/frameworks/base/core/java/android/app/ $ adb sync
/system/: 1 file pushed. 1605 files skipped. 2.2 MB/s (5873646 bytes in 2.584s)
/data/: 0 files pushed. 31 files skipped.
```

`adb root`是要讓`adb`能以root權限執行，因為我們要做`adb remount`所以需要root權限。如果是userdebug的image就需要這步，而eng build的adb預設就是以root權限執行，這行指令就可有可無。

預設android開機時系統檔案都是唯讀的，所以要透過`adb remount`來重新設定檔案系統為可修改的模式。`adb sync`則會將你電腦中AOSP*有改動的檔案*自動更新到裝置上。

這樣一來就大功告成了……嗎？

還差一步，再來要將Android系統重新啟動

```
$ adb shell stop
$ adb shell start
```

為什麼要重新啟動系統呢？因為我們放進去的`frameowrk.jar`在系統開機時就已經載入到memory了，也就是說這個檔案在當時就已經被讀入記憶體，之後要用就從記憶體找而不會再開檔案了。單單更新`framework.jar`並不會使Android重新讀取這個檔案，因此我們要重新啟動Android系統。

`adb shell stop`會將Android系統給關掉(但Linux kernel還在)，`adb shell start`則會將Android系統重新打開。因為Android系統重開的關係，`framework.jar`就會重新被讀入記憶體，那麼我們的改動就生效嘍！

接著輸入

```shell
$ adb logcat
```

`adb logcat`指令會不斷的將android的log輸出到營幕上。除非你喊停`(用CTRL-C)`否則是不會停的。將logcat的內容顯示在營幕上後，隨便打開一個app，你就會看到我們剛加的Log出現了！

以筆者自己的logcat來說，打開通話app後出現的是

```logcat
...
08-05 08:23:06.160  3789  3928 W System  : ClassLoader referenced unknown path: 
08-05 08:23:06.160  3789  3928 W System  : ClassLoader referenced unknown path: /system/priv-app/Dialer/lib/x86_64
08-05 08:23:06.160  3789  3928 I ===TAG===: Hello, AOSP!
08-05 08:23:06.250  4595  4595 W CountryDetector: No location permissions, not registering for location updates.
08-05 08:23:06.270  4595  4595 I ===TAG===: Hello, AOSP!
08-05 08:23:06.340  4595  4595 I ContactPhotoManager: Cache adj: 1.0
08-05 08:23:06.500  4595  4606 I art     : Background sticky concurrent mark sweep GC freed 13654(1613KB) AllocSpace objects, 12(200KB) LOS objects, 0% free, 1679KB/1680KB, paused 0 total 120ms
...
```

可以看到我們成功修改AOSP並且讓我們的改動生效了！以後所有開發AOSP的行為都和現在的作法幾乎一樣，只要靠`adb sync`就能幫助我們開發大部份的AOSP了！

# 完成！

到這裡你已經可以自由自在的修改AOSP程式碼並使之真正發揮作用。下一章將進入正題，正式開始學習AOSP！

[下一章：AOSP架構總覽](/ch6_aosp_overview)
