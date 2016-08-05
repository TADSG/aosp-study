# Build

[上一章：下載AOSP程式碼](#download.md)

這章節將正式編譯AOSP原始碼並打開模擬器。

## 設定環境變數

由於我們的環境是Mac，所以環境變數的設定要改其中兩個路徑：

1. `$ANDROID_JAVA_HOME` 要正確選到Java8的版本
2. 要選我們用homebrew自己安裝的`curl`，而不要用預設的`curl`

再加上原本就需要的`source build/envsetup.sh`也要做。這一個動作會將AOSP預設所有環境變數設好。總體來說我們要做的就是：

```shell
$ export ANDROID_JAVA_HOME=$(/usr/libexec/java_home -v1.8)
$ source build/envsetup.sh
$ export PATH=$(brew --prefix curl)/bin:$PATH # Use newer curl
```

這樣就算完成環境變數的設定了。請注意這個設定會在你關掉終端機頁面後消失，如果開了新的終端機頁面就要重新設定。

***完成環境變數設定後，`$TOP`會被設定成AOSP原始碼的根目錄，以下如果提到`$TOP`這個位置，則皆指AOSP原始碼根目錄。(以目前為止的範例來說，會是在`/Volumes/android/aosp`)***

## 選擇要build的img類型

在已經設定好環境變數的shell內輸入

```
$ lunch
```

會跳出如下的選項

```
 /Volumes/android/aosp/ lunch

You're building on Darwin

Lunch menu... pick a combo:
     1. aosp_arm-eng
     2. aosp_arm64-eng
     3. aosp_mips-eng
     4. aosp_mips64-eng
     5. aosp_x86-eng
     6. aosp_x86_64-eng
     7. aosp_deb-userdebug
     8. aosp_flo-userdebug
     9. full_fugu-userdebug
     10. aosp_fugu-userdebug
     11. mini_emulator_arm64-userdebug
     12. m_e_arm-userdebug
     13. mini_emulator_mips-userdebug
     14. mini_emulator_x86-userdebug
     15. mini_emulator_x86_64-userdebug
     16. aosp_flounder-userdebug
     17. aosp_angler-userdebug
     18. aosp_bullhead-userdebug
     19. aosp_hammerhead-userdebug
     20. aosp_hammerhead_fp-userdebug
     21. hikey-userdebug
     22. aosp_shamu-userdebug

Which would you like? [aosp_arm-eng] 
```

這邊我推薦使用`mini_emulator_x86_64-userdebug`這個選項，`mini_emulator_x86_64-userdebug`約需要30G，出來的結果是一台營幕很小很小的emulator，如圖：
![mini_emulator_x86_64-userdebug](emulator_mini_emulator_x86_64-userdebug.png)


如果是用Mac系統的話，在此強力推薦使用x86_64類型的選項，因為x86_64有支援HAXM，可以讓你的模擬器跑的很快。不過如果是用Ubuntu就沒辦法了，HAXM並不支援Linux，這時就看喜好了。

三種build type:

|Build Type| 說明
|----------|:---
| user     |正式的產品會用這個Build Type，也因為是正式產品要用的所以權限上有限制(不能root之類的)
| userdebug|同user，但將權限全開。最推薦的Build Type
| eng      |開發用的Build Type，有追加許多不存在於userdebug版本中的除錯工具。

顯然的，因為我們是要開發/研究AOSP，所以就選userdebug或eng嘍。

## 修改部份程式碼以通過編譯

### 問題：Max Property Name

作者在2016/8/5時進行編譯有碰到一些問題，所以這邊先把這個問題處理掉。在底下的TroubleShooting也可以找到一樣的問題。

* 修改`AOSP_TOP/build/tools/post_process_props.py`這個檔案，找到`PROP_VALUE_MAX = 91`並把它改成`128`，如下：

```python
PROP_NAME_MAX = 31
# PROP_VALUE_MAX = 91
PROP_VALUE_MAX = 128
```

* 修改`AOSP_TOP/bionic/libc/include/sys/system_properties.h`這個檔案，找到`#define PROP_VALUE_MAX 92`並把它改成`128`，如下：

```c
#define PROP_NAME_MAX   32
// #define PROP_VALUE_MAX  92
#define PROP_VALUE_MAX  128
```

完成！

## 編譯AOSP原始碼

在完成上述步驟後的shell內輸入

```shell
$ make -j8
```

就會開始編AOSP了。大概2至3個小時會完成。當中的`-j8`表示最高可以同時開8個平行的Thread來做編譯的動作，建議這邊輸入和你的CPU核心數一樣的數字。

同樣的，如果要放整晚的話，請加上[`caffeinate`](/appendix/cli-tools/caffeinate.md)指令，例如

```shell
$ caffeinate make -j8 | tee build.log
```


## 打開emulator

只要在設定好環境變數的shell輸入

```shell
$ emulator
```

就可以打開emulator(模擬器)了。

## TroubleShoot
### fingerprint

```
error: ro.build.fingerprint cannot exceed 91 bytes: Android/mini_emulator_x86_64/mini-emulator-x86_64:6.0.1/MASTER/cha12208042258:userdebug/test-keys (97)
```
[Stack Overflow](http://stackoverflow.com/questions/28776970/android-build-error-ro-build-fingerprint-cannot-exceed-91-bytes)

### CURL

```
Unsupported curl, please use a curl not based on SecureTransport
```
[Stack Overflow](http://stackoverflow.com/questions/33318756/while-i-make-the-source-of-android-6-0-it-failed)
