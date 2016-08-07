# [Flash Image]

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
