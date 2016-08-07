# aosp-study

本項目為[Android讀書會](https://www.facebook.com/groups/523386591081376/)中，由大家自發性發起學習AOSP的教材。

AOSP為**Android Open Source Project**的縮寫，白話來說就是Android的原始碼。這份教材會以探討AOSP的設計架構為主軸。

由於大部份人都是使用Mac開發，因此本教材基於MacOS編寫。如果您用的是Windows或Ubuntu則建議參考其它教材。

***目前已經有人願意編寫Linux(Ubuntu)的教材了，還請耐心等待。或者可以直接參考[AOSP官方網站](https://source.android.com/index.html)***

## TODO

以下這些章節待完成，如果各位願意一起共筆的話不妨寫上吧！別忘了在Contributors上加上你的大名和聯絡方式喔！

* 確認SD卡的路徑設定沒問題
* [CH4: 燒入客製的AOSP image](/ch4_flash)
* [CH6: AOSP架構總覽](/ch6_aosp_overview)

## 目錄

### 基本設定篇

1. [環境設定](/ch1_setup)
2. [下載AOSP程式碼](/ch2_download)
3. [編繹AOSP原始碼](/ch3_build)
4. [燒入客製的AOSP image](/ch4_flash)
5. [使用adb工具開發AOSP](/ch5_adb)
6. [AOSP架構總覽](/ch6_aosp_overview)
7. [設定Android Studio](/ch7_android_studio_setup)

### Android核心知識篇 (TODO)

暫定主題，順序未定

* Android與Linux kernel的關係
* HAL
* Android的核心library (sp,wp,RefBase)
* Android執行的第一個程式：init與init.rc
* Android的IPC框架：servicemanager與binder driver
* Android開機流程
* Zygote
* framework IPC: IBinder
* System Server

* [附錄：終端機工具及指令](/appendix/cli-tools)

## Contributors(協作者們)

* [Charlie Tsai](https://github.com/chatea) - cha122977@gmail.com
* [erinus](https://github.com/erinus)

## License(版權聲明)

[![Created Commons License](https://i.creativecommons.org/l/by-sa/3.0/88x31.png)](http://creativecommons.org/licenses/by-sa/3.0/)
<br>
本項目採用[CC-BY-SA授權](http://creativecommons.org/licenses/by-sa/3.0/).
<br>
This work is licensed under a [Creative Commons Attribution-ShareAlike 3.0 Unported License](http://creativecommons.org/licenses/by-sa/3.0/).
