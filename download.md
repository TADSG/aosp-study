# 下載AOSP程式碼

[上一章：環境設定](#setup.md)

由於AOSP太過龐大，不能只用一個`git`專案管理，所以AOSP其實被切成約500個`git`專案。而為了確保這500個專案能協調，Google官方就用python寫了一個工具叫`repo`來協調各個專案。
<TODO 有更好的說法嗎？>

`repo`的設計是用一張`manifest.xml`描述所有`git`專案的位置和版本號。這樣就可以確保不同`git`專案之間不會發生彼此版本不相容的問題。

## 安裝 repo 工具

官方文件是將`repo`安裝在`~/bin/`下，但放在這裡就必需要改`$PATH`，所以我推薦是放在`/usr/local/bin/`下。

```shell
$ curl https://storage.googleapis.com/git-repo-downloads/repo > /usr/local/bin/repo
$ chmod a+x /usr/local/bin/repo
```

## 裝上Android磁區

插上你的Android磁區

```shell
$ mountAndroid
```

## 建立工作目錄

在Android磁區內建立放aosp source code的資料夾。接在來我們會將整份aosp程式碼下載進去。

```shell
$ cd /Volumes/android/
$ mkdir aosp
```

## 下載AOSP原始碼
終於到了下摙AOSP原始碼的時候了，請到你的工作目錄下執行

```shell
$ cd /Volume/android/aosp
$ repo init -u https://android.googlesource.com/platform/manifest
```

這樣做會直接取得目前最新的AOSP程式碼的`repo`描述檔。
***請注意一定要在`/Volumes/android/aosp`資料夾內做`repo init`的動作，而不是在`/Volumes/android/`喔***

`repo`描述檔會放在`/Volumes/android/aos/.repo/manifest.xml`，有興趣的可以自己打開來看。

有了描述檔以後我們就可以真正開始下載AOSP了！請執行

```shell
$ repo sync
```

這樣就會把整份AOSP程式碼給下載下來。一般來說如果網路狀況良好，大概花四五個小時就會下載完了。如果下載過程失敗，可以直接再打一次`$ repo sync`，一直失敗就只好請你打到成功為止嘍囧>

下載完成後你就有AOSP的整份原始碼了，接著就可以開始研究嘍！不過在那之前我們還是先來將這份AOSP原始碼給編譯起來吧！請見下一章！

[下一章：編繹AOSP原始碼](#build.md)

## 參考資料
* [官方文件(英)](https://source.android.com/source/downloading.html)
