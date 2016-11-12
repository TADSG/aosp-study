# 下載AOSP程式碼

[上一章：環境設定](/ch1_setup)

請依據您的作業系統前往操作指導：[Mac OS X](#macosx)、[Ubuntu 14.04 LTS](#ubuntu1404)

由於 AOSP 太過龐大，不能只用一個 `git` 專案管理，所以 AOSP 其實被切成約 500 個 `git` 專案。而為了確保這 500 個專案間的版本能協調一致，Google 官方就用 Python 寫了一個工具叫 `repo` 來協調各個專案。
<TODO 有更好的說法嗎？>

`repo` 的設計是用一張 `manifest.xml` 描述所有 `git` 專案的位置和版本號。這樣就可以確保不同 `git` 專案之間不會發生彼此版本不相容的問題。

# <a name="macosx">Mac OS X</a>
## 安裝 repo 工具

官方文件是將 `repo` 安裝在 `~/bin/` 下，但放在這裡就必需要改 `$PATH`，所以這份教學推薦是放在 `/usr/local/bin/` 下。

```shell
$ curl https://storage.googleapis.com/git-repo-downloads/repo > /usr/local/bin/repo
$ chmod a+x /usr/local/bin/repo
```

有關`repo`工具的一些使用方法，可以參考[附錄：repo工具](/appendix/repo/)

## 掛載 Android 磁區

若是使用分割磁區請執行

```shell
$ mountAndroid
```

使用 SD 記憶卡則插上便可（已經插好的話略過這步）

## 建立工作目錄

在 Android 磁區內建立放 AOSP Source Code 的資料夾，接下來我們會將整份 AOSP 程式碼下載進去。

```shell
$ cd /Volumes/android/   # 如果你是用外接 SD 卡，這邊請切換到你掛載的 SD 卡根目錄
$ mkdir aosp
```

## 下載 AOSP 原始碼
終於到了下載 AOSP 原始碼的時候了，請在 `aosp` 資料夾下執行

```shell
$ cd /aosp/
$ repo init -u https://android.googlesource.com/platform/manifest
```

這樣做會直接取得目前最新的 AOSP 程式碼的 `repo` 描述檔。
***請注意要在 `/Volumes/android/aosp`（用外接SD記憶卡則為 `[SD 記憶卡路徑]/aosp`）資料夾內做 `repo init` 的動作，而不是在 `/Volumes/android/` 喔***（這邊是為了避免未來如果想要再下載一份 AOSP 時產生問題）

### 指定 branch
如果怕編譯不過， 可以看 [已知能編譯過的manifest](https://github.com/TADSG/aosp-study/blob/master/ch3_build/known_good_manifests) 抓取特定版本下載，此外 [官網](https://source.android.com/source/build-numbers.html#source-code-tags-and-builds) 有對應的表格。

```shell
# Android 6.0.1 for Nexus 5
# repo init -u https://android.googlesource.com/platform/manifest -b android-6.0.1_r60
```

### 如果初始化步驟位置錯了
如果已經在 `/Volumes/android/` 初始化 `init` ，移除 `.repo` 即可。

```shell
$ rm -r /Volumes/android/.repo

```

`repo` 的描述檔會放在 `/Volumn/android/aosp/.repo/manifest.xml`（外接 SD 卡則是 `[SD下根目錄]/aosp/.repo/manifest.xml`），有興趣的可以自己打開來看。

有了描述檔以後我們就可以真正開始下載 AOSP 了！請執行

```shell
$ repo sync
```

這樣就會把整份 AOSP 程式碼給下載下來。一般來說如果網路狀況良好，大概花*四五個小時*就會下載完了。如果下載過程失敗，可以直接再打一次 `$ repo sync`，一直失敗就只好請你打到成功為止嘍囧

由於下載AOSP原始碼真的很久，如果放整晚又怕電腦休眠的話可以用 [`caffeinate`](/appendix/cli-tools/caffeinate.md) 指令避免電腦休眠

```shell
$ caffeinate repo sync
```

## 完成！

現在你已經有 AOSP 的整份原始碼了，其實已經可以開始研究嘍！不過在那之前我們還是先來將這份 AOSP 原始碼給編譯起來吧！請見下一章！

# <a name="ubuntu1404">Ubuntu 14.04 LTS</a>

## 建立目錄

```shell
$ mkdir ~/aosp
$ mkdir ~/aosp/bin   # 放置 repo 工具
$ mkdir ~/aosp/src   # 存放 AOSP 原始碼
```

## 安裝 repo 工具

```shell
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/aosp/bin/repo
$ chmod a+x ~/aosp/bin/repo
```

## 更新 bash 啟動設定

```shell
$ vi .bashrc
# 加入以下內容
export PATH=~/aosp/bin:$PATH
```

請重新啟動 Terminal 讓 PATH 環境變數配置生效，才能順利呼叫 `repo` 命令

## 設定 git 資訊

```shell
$ git config --global user.name "user"
$ git config --global user.email "user@user"
```

## 切換目錄

```shell
$ cd ~/aosp/src
```

## 取得 branch 清單

```shell
$ repo init -u https://android.googlesource.com/platform/manifest
```

## 指定 branch

```shell
$ repo init -u https://android.googlesource.com/platform/manifest -b [branch]
# Android 6.0.1 for Nexus 5
# repo init -u https://android.googlesource.com/platform/manifest -b android-6.0.1_r60
```

## 開始下載

```shell
# 自動調整同時下載執行緒數量
$ repo sync

# 指定同時下載執行緒數量
$ repo sync -j[Number]
# repo sync -j4   # 4 執行緒同時下載
```

[下一章：編繹 AOSP 原始碼](/ch3_build)

## 參考資料
* [官方文件（英）](https://source.android.com/source/downloading.html)
