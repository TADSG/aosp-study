# 使用adb工具開發AOSP

[上一章：燒入客製的AOSP image](/ch4_flash)

目前emulator裡面已經是你剛剛編好的AOSP image了。本章節我們會利用`adb`工具，將我們對AOSP的修改直接反應到emulator上。

## 簡介adb工具

`adb`工具全名`android debug bridg`，它是用來將電腦和android裝置連線的工具。它除了可以用如`adb logcat`的指令將`logcat`的內容從裝置讀出來之外，還可以從電腦將檔案上傳至android裝置(`adb push`)，或將android裝置上的檔案下載到電腦裡(`adb pull`)。

但`adb`最厲害的是，透過它我們可以將AOSP的改動直接*同步*到裝置上，而且不用重新編譯/燒錄整份AOSP。這將大大的增加我們開發AOSP的效率。

### TODO
