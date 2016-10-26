Android framework本身提供一系列的系統服務，並提供Java/C++的API來呼叫這些服務。這提供給Android App開發者一個很大的方便：只要
使用這些API就可以直接調用系統的功能，App開發者甚至不用自己準備能調用這些API的環境。

舉例來說，如果你想播放音效，只要使用Audio系列的API就可以了，而實際處理音訊檔到驅動硬體播出的部份則是由系統服務中的`AudioFlinger`來處理，不需要讓App開發者自己處理。

諸如6軸感硬器、照相機、閃光燈、震動裝置、網路連線等等的功能，如果要讓每個App開發者自己處理也太辛苦了。而且有些功能系統不願意提供太多權限給App開發者修改。這些功能適合由系統處理，但同時提供適當的API，讓開發者也能使用部份的功能。

為了達成這個目的，Android將系統服務放在由系統控制的許多Process內，再提供一套IPC(Inter Processes Communication)框架來讓其它程式可以呼叫系統服務。Android本身的系統服務也會利用這套IPC來互相呼叫。比如說，負責播打電話的系統服務也會用到震動和喇叭的系統服務，也是透過同樣的IPC框架來實現。

在Android內這個IPC框架主要由`Binder`構成。寫過像是bound Service或者用過AIDL的App開發者對這個詞應該不莫生。接下來就讓我們來看看這個`Binder`到底是怎麼實現的吧。

# Kernel Binder Driver

檔案位置：

* `KERNEL_ROOT/include/uapi/linux/android/binder.h`
* `KERNEL_ROOT/drivers/android/binder.c`

Binder Driver負責在IPC過程中能夠找到目標process。利用`ioctl`並代入一個`cmd`(command)參數的方試來操作Binder Driver。像是加入一個Binder節點或者是取得特定編號的Binder，都是在這裡實作。

## Context Manager

由於Android系統中允許切換不同的系統實作，也允許廠商及App開發者登錄自己的系統服務(像是允許用Intent開啟別的App)，但我們總要有個方法找到目標Process吧？這就是由`Context Manager`負責。

系統中有一個負責管理Binder的process，在開機過程中會在Binder Driver中將自己設定為`Context Manager`。這個Process中會記錄一張表，其中記錄了所有Process在Binder Driver中的`binder_node`編號(在程式碼中為叫做`handle`，是一個u32的值)。如果Target編號是0，則會回傳Context Manager本身。

大致上找到目標Process的方式是：先設目標handle為0，先找到Context Manager。接著送給Context Manager一個名稱，Context Manager會回答你這個名稱的對應編號是多少。接著再利用這個編號問Binder Driver，Binder Driver就會告訴你目標編號，再來就可以把資料寫進該編號的Process內，實現IPC。


Kernel中實現IPC是是用shared memory來使資料共用，這部份在實作上比較複雜，且比較偏向Kernel的範疇，因此這邊不討論太多。如果想要深入了解Binder Driver，在對岸有很多分析文，像是[這篇](/http://gityuan.com/2015/11/01/binder-driver/)就很值得參考。

# Native (c++) 層的Binder




# Java 層的Binder

# 參考資料

* [Gityuan的blog：Binder系列](http://gityuan.com/tags/#binder)
