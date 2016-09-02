# `repo` 工具

介紹如何使用`repo`工具

## 製作snapshot

如果你在建立AOSP的manifest時使用的是指定的branch，則因為該branch是會不斷前進的，因此每次`repo sync`的結果可能會不一樣。在這種情況下你可能會希望能將你目前的`manifest`保存起來。

在根目錄下輸入以下指令就可以保存你的manifest

```shell
$ repo manifest -r -o my_manifest.xml # 名稱可以自己取
```

注意這個動作的原理是將當前所有git專案中的sha1存起來，所以如果你有本地的commit那也會被存起來。這種情況下自己用是沒差，但如果要將這份manifest給別人用那就會發生找不到git commit的問題。

## 使用製作出來的snpashot

當然啦，建立出了manifest就是要給人用的嘛！不管是自己的還是別人的manifest我們都可以使用。

請照以下步驟：

```shell
$ cd [path/to/aosp/root]
$ cp my_manifest.xml .repo/manifests/  # 烤貝你的manifest到進repo資料夾，這樣等等才找的到
$ repo init -m my_manifest.xml
$ repo sync -j4
```

這樣就會sync到該manifest建立時的狀態嘍！
