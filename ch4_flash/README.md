# 燒錄客製的AOSP Image

[上一章：編繹AOSP原始碼](/ch3_build)

第一次完成 AOSP 編譯時，emulator裡面的就是你自剛剛編譯的AOSP image了。其實我們可以跳過這章直接進行AOSP的開發。

但如果你是用實機開發，或者有重灌emulator內Android系統的打算，那麼這章節的內容就可以參考。

標題的「燒入」指的是"flash"，由於這個術語並沒有很適當的翻譯，以下皆用"flash"一調代替。而我們編譯 AOSP 的結果會是一個*.img檔案，也就是標題中的"image"。所以"flash image"一詞指的便是將編譯好的 AOSP 系統放入到裝置（不論是模擬器或實機）上。

## Flash Image

### 設定 fastboot tool

* fastboot command line tool 可以在 Androidsdk/sdk/platform-tools/ 找到

### 讓 device 進入 fastboot mode (bootloader的燒錄模式)

在開機狀態使用 adb 

```sh

$ adb reboot bootloader

```

或是在關機狀態按住特別的 combo key

(以 Nexus 5 為例: 長按 Volume Down + Power Key 可以進入 fastboot mode)

### 測試 fastboot 是否正常抓到 device

```sh

$ fastboot devices

```

如果出現 no permission, 請參考 udev rules setting [TODO]

### 解除 bootloader lock (執行一次就好)

一般手機都會把 bootloader 鎖住, 不讓使用者自行燒錄 image. 所以需要先 unlock

每一間手機廠解鎖 bootloader 的方式都不相同, 這邊是以 Google 出的 Nexus 系列為例子

```sh

$ fastboot oem unlock

```

### 燒錄 images, 通常只需要燒 system/boot/userdata 這三個 partition, 請參考 android partition [TODO]

```sh

$ cd <AOSP_TOP>/out/target/product/<product_name>/

$ fastboot flash system system.img

// boot.img 包含了 kernel and ramdisk
$ fastboot flash boot boot.img

$ fastboot flash userdata userdata.img

// 如果要更新 recovery partition
$ fastboot flash recovery recovery.img

$ fastboot reboot

```

重開機就是新的 ROM image 了

# 完成

到此你已經了解如何將自己編譯的 AOSP 系統裝設在你的裝置上了，接著就讓我們來聊聊 AOSP 的開發方式吧！

[下一章：使用adb工具開發AOSP](/ch5_adb)
