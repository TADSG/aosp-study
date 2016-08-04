# `tee`

## 使用`tee`指令同時顯示log並存入檔案

在Mac上log只會被導到一個地方，要嘛就是營幕上(stdout)，要嘛就是某個檔案內。那如果我需要將log顯示在畫面上又希望它能被保存呢？這時候可以利用`tee`這個pipeline工具

使用方式為

```shell
$ <some_command> | tee [file_to_save_log]
```

以`adb logcat`為範例：

```shell
$ adb logcat | tee ~/my_logcat_output.txt
```

這個範例中`adb logcat`的輸出除了會顯示在畫面上外，也會被保存到`~/my_logcat_output.txt`這個檔案裡面。
