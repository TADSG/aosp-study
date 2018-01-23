# 進階`adb`用法

關於使用`adb`來燒入(flash)自製的aosp image，請參考[第四章：燒入客製的AOSP image](/ch4_flash)
使用`adb`替換已改過的檔案，請參考[第五章：使用adb工具開發AOSP](/ch5_adb)

## `adb shell service`

Android系統內有許多的`service`一直在觀察系統的狀態，而且這些`service`的資訊可以利用`dumpsys`指令取出。

要調查系統內有哪些`service`，可以利用

```shell
$ adb service list
```

來取得`service`列表。


如果你有想用指術操作一些系統或自製的`service`，`service`有提供對應的API可以達成。可以利用

```shell
$ adb service call [SERVICE] [CODE] [VALUE]
```

來傳值。其中\[SERVICE是該\]`service`的名字(比如說`SurfaceFlinger`)，\[CODE\]是該`service`內自訂的某個值，該值對應`service`內的某些動作。具體對應的動作要查閱原始碼。\[VALUE\]則是該動作需要的參數，也必需查閱原始碼來獲得。

### TODO：提供一個寫入值的例子

另外這些`service`都是可以自由停止的，只要輸入

```shell
$ adb shell stop [SERVICE]
```

就可以終止運它的運行，反之

```shell
$ adb shel start [SERVICE]
```

則可以啟動它。

大部份的`service`都可以在`init.rc`內找到，畢竟`service`主要都是在開機過程被啟動的。關於`init.rc]`請參考[第十章：Android 開機流程概觀](/ch10_android_bootup_progress)。


## `adb shell dumpsys`

`adb shell dumpsys`指令可以印出系統內的各種資訊。需要搭配`adb service list`的列表使用。
舉例來說，`adb shell service list`的列表內可以看到有個`service`叫做`cpuinfo`，那麼我們就可以輸入

```shell
$ adb shell dumpsys cpuinfo
```

來得到系統當前cpu的資訊。


如果都不帶任何參數，只輸入

```shell
$ adb shell dumpsys
```

的話，所有的系統資訊都會被顯示。
這個指令在某些場合下這個指令非常有用，比如說自動化測試。在自動化測試的過程中如果程式出問題了，這個指令可以保留當下系統的資訊，進而協助debug。

如果是一般的場合，由於提供的資訊實在太多，多到要好幾十秒才能顯示完，因此一般的場合盡量針對幾個重點`service`調查就可以了(比如說`cpuinfo`和`meminfo`)。


### 參考資料

官方的Android Developer網站也有提供[`dumpsys`指令的說明](https://developer.android.com/studio/command-line/dumpsys.html)
[這篇stack overflow的解答](https://stackoverflow.com/a/11201711)也提供了一些操作後的畫面
