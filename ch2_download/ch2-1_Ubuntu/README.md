[Download]

```shell
cd ~
mkdir aosp
mkdir aosp/src
mkdir aosp/bin

PATH=~/aosp/bin:$PATH

curl https://storage.googleapis.com/git-repo-downloads/repo > ~/aosp/bin/repo
chmod a+x ~/aosp/bin/repo

git config --global user.email "user@USER"
git config --global user.name "user"

cd ~/aosp/src

repo init -u https://android.googlesource.com/platform/manifest -b android-2.3.7_r1
repo init -u https://android.googlesource.com/platform/manifest -b android-4.0.4_r2.1
repo init -u https://android.googlesource.com/platform/manifest -b android-4.1.2_r2.1
repo init -u https://android.googlesource.com/platform/manifest -b android-4.2.2_r1.2
repo init -u https://android.googlesource.com/platform/manifest -b android-4.3_r3.1
[v] repo init -u https://android.googlesource.com/platform/manifest -b android-4.4.4_r2

sudo sysctl -w net.ipv4.tcp_window_scaling=0
repo sync -j1
```