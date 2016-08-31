# Android 開機流程概觀

[上一章：Android 的 `Makefile` -- `Android.mk`](/ch9_android_makefile)


本章節將介紹Android開機過程中所發生的事情。

首先要有一個認知，Android系統本身就帶有非常多的服務(system service)，而這些服務大多運行在某個叫做xxx server的process內(如`mediaserver`，`system_server`，不過有一部份的名稱沒有server這個字眼)。這些服務有的是C++層提供有的是Java層提供。C++層提供的服務大多是一個服務一個process(如`mediaserver`，`surfaceflinger`，而Java層則將所有Java層的服務都跑在`system_server`這個process裡了。

大體上來說，Android的開機過程如下：

1. `Linux Kernel` 準備完成
2. 執行`init` (第一個userspace程式，也是Android的第一行)，過程中init會成為`property service`。
3. `init`過程中叫起大部份的C++層服務，並啟開機動畫。
4. `init`最後執行`app_process64`，正式完成`init`的工作。
5. `app_process64`會準備好一台`JVM`，並用`JVM`載入`com.android.internal.os.ZygoteInit`這個class(以下簡稱`zygote`)。(`ZygoteInit`是java的第一行，執行的是`main()`)
5. `zygote`會在過程中啟動`SystemServer`，內含Java層的系統服務。
6. 系統準備好後結束開機動畫，啟動`launcher`。`zygote`會留下來負責產生的Android App。

## Linux Kernel

// TODO

## `init`

`init`是Linux系統執行的第一行usersapce程式，而Android使用自製的init。Android的init會讀取`init.rc`檔案來執行動作，所以我們不用真的修改它的原始碼便能為你的系統增加一些功能。

`init`在執行過程中會變成`property service`來處理Android系統中的屬性。我們可以在`adb shell`內用`getprop`和`setprop`工具來取得/修改系統中的property資訊。

`init`為`init.rc`量身訂製了一套`Android Init Language`，用來充當`init`的設定檔。`Andorid Init Language`中也有`service`一詞，記得別和Android App的Service搞混嘍！

另外要注意的是，有些`*.rc`的檔案不會放在`$TOP/system/core/rootdir/`內，而是改採放在自己專案下並在`Android.mk`中宣告`LOCAL_INIT_RC := xxx.rc`的方式。比如說如`mediaserver`就是放在`$TOP/framework/av/media/mediaserver/mediaserver.rc`

// TODO

### 參考檔案

* `$TOP/system/core/init/*`
* `$TOP/system/core/rootdir/*`

## `app_process64`

非常小的一支程式，會啟動一台`JVM`(用一個叫`AppRuntime`的class封裝)，並且用這台`JVM`載入`com.android.internal.os.ZygoteInit`這個class。接著`ZygoteInit`內的`main(String[] argv)`就會被執行。

`AppRuntime`繼承了`AndroidRuntime`，這個class主要處理從C++到Java的JNI流程。大致上是：
1. 用C++產生一台`JVM`
2. 設定好`JVM`
3. 告訴`JVM`要執行的`main(String[] argv)`是哪個。

### 參考檔案

* `$TOP/framework/base/cmds/app_process`
* `$TOP/framework/base/include/android_runtime/AndroidRuntime.h`
* `$TOP/framework/base/core/jni/AndroidRuntime.cpp`
* `$TOP/framework/base/core/java/com/android/internal/os/ZygoteInit.java`

## `ZygoteInit`

`ZygoteInit`會註冊一個socket，之後會用來接收別人要它產生新app的需求。在開機流程中執行的`ZygoteInit`會帶`start-system-server`這個參數，而收到這個參數時`ZygoteInit`會自我fork來產生Java層的系統服務(`system_server`)。

在自我fork的過程中，會利用`ZygoteConnection`來產生參數，最終則執行`Zygote.forkSystemServer(...)`，從此這個process就fork成了parent(`Zygote.forkSystemServer()`回傳child的id)及child(`Zygote.forkSystemServer()`回傳的id為0)。其中child prcess會變成`system_server`，而原本的`ZygoteInit`則會繼續它自己的使命--接收參數並產生新的process(包括app的process)。

child process首先會關掉zygote socket，接著開始準備自己。會先產生一個西為`class loader`的Java類別，並在裡面設定好`service.jar`的位置(一般來說會是`/system/framework/services.jar`)，然後執行`RuntimeInit.zygoteInit(...)`。`RuntimeInit.zygoteInit`會再調用`RuntimeInit.applicationInit(...)`並最終執行`class loader`中class的`main()`--也就是`system_server`的`main()`，程式碼會是`$TOP/frameworks/base/services/java/com/android/server/SystemServer.java`。


### 參考檔案

* `$TOP/frameworks/base/services/java/com/android/server/SystemServer.java`

## SystemServer

在SystemServer開始執行後，他會先準備好需要用到的jni library(叫做`libandroid_servers`)，並開始將Java層的系統服務準備好。
接下來會建立一個系統用的`Context`(`createSystemContext()`，產生一個新的`SystemServiceManager`(`mSystemServiceManager = new SystemServiceManager(mSystemContext);`)，然後在LocalServices內加入`SystemServiceManager`。

接著依序執行`startBootstrapServices()`、`startCoreServices()`、`startOtherServices()`，完成後進入`Looper.loop()`狀態，等待其它程式送指令交待給它。

## 完成！

到此我們對Android的啟動流程就有初步的認識了！
