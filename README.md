# aosp-study

本項目為 [Android 讀書會](https://www.facebook.com/groups/523386591081376/)中，由大家自發性發起學習 AOSP 的教材。

AOSP 為 **Android Open Source Project** 的縮寫，白話來說就是 Android 的原始碼，這份教材會以探討AOSP的設計架構為主軸。

由於大部份人都是使用 Mac 和 Linux 進行開發，因此本教材基於 Mac OS X 和 Ubuntu 14.04 LTS 編寫。如果您用的是其他 Linux 系統則建議參考教材進行調整。

***或可直接參考[AOSP官方網站](https://source.android.com/index.html)***

## TODO

以下這些章節待完成，如果各位願意一起共筆的話不妨寫上吧！別忘了在 Contributors 上加上你的大名和聯絡方式喔！

* 確認 SD 卡的路徑設定沒問題
* [CH4: 燒入客製的 AOSP Image](/ch4_flash)
* [CH6: AOSP 架構總覽](/ch6_aosp_overview)

## 目錄

### 基本設定篇

1. [環境設定](/ch1_setup)
2. [下載 AOSP 程式碼](/ch2_download)
3. [編繹 AOSP 原始碼](/ch3_build)
4. [燒入客製的 AOSP Image](/ch4_flash)
5. [使用 adb 工具開發 AOSP](/ch5_adb)
6. [AOSP 架構總覽](/ch6_aosp_overview)
7. [設定 Android Studio](/ch7_android_studio_setup)

### Android 核心知識篇 (TODO)

暫定主題，順序未定

* Android 與 Linux Kernel 的關係
* HAL
* Android 的核心 Library (sp, wp, RefBase)
* Android 執行的第一個程式：init 與 init.rc
* Android 的 IPC 框架：ServiceManager 與 Binder Driver
* Android 開機流程
* Zygote
* Framework IPC: IBinder
* System Server

* [附錄：終端機工具及指令](/appendix/cli-tools)

## Contributors (協作者們)

* [Charlie Tsai](https://github.com/chatea) - cha122977@gmail.com
* [Cheng-Yi, Yu](https://github.com/erinus) - erinus.startup@gmail.com
* [Richard Chang](https://github.com/chiel99)
* [Lex Chien](https://github.com/LexChien)

## License (版權聲明)

[![Created Commons License](https://i.creativecommons.org/l/by-sa/3.0/88x31.png)](http://creativecommons.org/licenses/by-sa/3.0/)
<br>
本項目採用 [CC-BY-SA授權](http://creativecommons.org/licenses/by-sa/3.0/).
<br>
This work is licensed under a [Creative Commons Attribution-ShareAlike 3.0 Unported License](http://creativecommons.org/licenses/by-sa/3.0/).
