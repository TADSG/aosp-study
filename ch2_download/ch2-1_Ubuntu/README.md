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

repo init -u https://android.googlesource.com/platform/manifest -b android-6.0.1_r63

sudo sysctl -w net.ipv4.tcp_window_scaling=0
repo sync -j4
```