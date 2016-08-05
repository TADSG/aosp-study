# 下載AOSP程式碼

[上一章：環境設定](/ch1_setup)

由於AOSP太過龐大，不能只用一個`git`專案管理，所以AOSP其實被切成約500個`git`專案。而為了確保這500個專案間的版本能協調一致，Google官方就用python寫了一個工具叫`repo`來協調各個專案。
<TODO 有更好的說法嗎？>

`repo`的設計是用一張`manifest.xml`描述所有`git`專案的位置和版本號。這樣就可以確保不同`git`專案之間不會發生彼此版本不相容的問題。

## 安裝 repo 工具

官方文件是將`repo`安裝在`~/bin/`下，但放在這裡就必需要改`$PATH`，所以我推薦是放在`/usr/local/bin/`下。

```shell
$ curl https://storage.googleapis.com/git-repo-downloads/repo > /usr/local/bin/repo
$ chmod a+x /usr/local/bin/repo
```

## 裝上Android磁區

若是使用分割磁區請執行

```shell
$ mountAndroid
```

使用SD記憶卡則插上便可(已經插好的話略過這步)

## 建立工作目錄

在Android磁區內建立放aosp source code的資料夾。接在來我們會將整份aosp程式碼下載進去。

```shell
$ cd /Volumes/android/ # 如果你是用外接SD卡，這邊請移動到你外接的SD卡根目錄
$ mkdir aosp
```

## 下載AOSP原始碼
終於到了下摙AOSP原始碼的時候了，請在`aosp`資料夾下執行

```shell
$ repo init -u https://android.googlesource.com/platform/manifest
```

這樣做會直接取得目前最新的AOSP程式碼的`repo`描述檔。
***請注意要在`/Volumes/android/aosp`(用外接SD記憶卡則為`[SD記憶卡路徑]/aosp`)資料夾內做`repo init`的動作，而不是在`/Volumes/android/`喔***（這邊是為了避免未來如果想要再下載一份AOSP時產生問題）

`repo`的描述檔會放在`/Volumn/android/aosp/.repo/manifest.xml`(外接SD卡則是`[SD下根目錄]/aosp／.repo/manifest.xml`)，有興趣的可以自己打開來看。

有了描述檔以後我們就可以真正開始下載AOSP了！請執行

```shell
$ repo sync
```

這樣就會把整份AOSP程式碼給下載下來。一般來說如果網路狀況良好，大概花*四五個小時*就會下載完了。如果下載過程失敗，可以直接再打一次`$ repo sync`，一直失敗就只好請你打到成功為止嘍囧>

由於下載AOSP原始碼真的很久，如果放整晚又怕電腦休眠的話可以用[`caffeinate`](/appendix/cli-tools/caffeinate.md)指令避免電腦休眠

```shell
$ caffeinate repo sync
```

## 完成！

現在你已經有AOSP的整份原始碼了，其實已經可以開始研究嘍！不過在那之前我們還是先來將這份AOSP原始碼給編譯起來吧！請見下一章！

[下一章：編繹AOSP原始碼](/ch3_build)

## 參考資料
* [官方文件(英)](https://source.android.com/source/downloading.html)
