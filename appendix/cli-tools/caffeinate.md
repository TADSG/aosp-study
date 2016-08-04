# `caffeinate`

## 使用caffeinate指令防止電腦休眠

不論是下載或是編譯AOSP都需要很長的時間，由於實在是太久了，可以考慮放一整個晚上。但若你的Mac系統設定一段時間沒操作就會進入休眠，如果不想改設定的話，可以用`caffeinate`指令來防止電腦進入睡眠狀況。

使用方式有兩種，一種是單獨使用：
```
$ caffeinate
```
而單純止只打`caffeinate`的話，在你按下`CTRL-C`強制結束`caffeinate`指令之前都是不會停的

另一個方式是搭配其它指令使用：
```
$ caffeinate <your_command> <arg1> <arg2> ...
```
在你給的指令完成前電腦都不會進入休眠


請注意當用了`caffeinate`(咖啡因)指令後，你就算蓋上電腦也不會休眠，因此如果你要移動你的Mac還請小心。
