# Mi Pay Extractor

Extract Mi Pay from MIUI China Rom

# 这个分支已经和原分支有了较大改动，原分支的使用方法这里不再适用。

这个分支只提取国内版 ROM 并进行反编译修改，不需要 eu 包，最后打包成 magisk 模块。  
magisk 模块的本地化内容如下

- 日历的农历
- 天气改用彩云天气
- 信息助手微信扫一扫、支付宝、小米钱包公交车等顶部磁铁（不是所有卡片都能恢复，快递查询可以恢复，部分卡片需要有相应的应用）
- 闹钟添加工作日选项
- 小米钱包
- 短信智能分类和验证码快速复制（设置里的选项无法关闭这个功能，都搞本地化了不会有人要关这个吧）
- mipush (刷入本模块后删除/data/data/com.xiaomi.xmsf文件夹重启即可恢复)
- 通知聚合(如果设置中没有通知聚合选项，见下面解决方法)

## 请注意
刷入本模块后将不能通过safetyNet测试，卸载模块也不能恢复，原因不明  
有使用google pay或看netflix需求的请慎重刷入

## 关于safetyNet测试
刷入后无法通过应该是修改了build.prop引起的  
需要修改build.prop是因为恢复mipush的需要  
假如不需要mipush而需要通过safetyNet测试的话  
可以清空`/eufix-base/system.prop`文件内容(不要删除文件)再运行一次即可

## 使用方法(任选其一)

一、使用我提取的K20 pro plus 模块，一般来应用是不分机型的，这个模块按理也是可以用在其他机型上  
直接下载然后使用magisk刷入 https://github.com/kooritea/mipay-extract/releases

二、使用自己的机型的rom包提取

1、克隆项目(或者http下载)  

```bash
git clone git@github.com:kooritea/mipay-extract.git
```

2、下载对应的国内版 ROM[https://xiaomifirmwareupdater.com/miui12/](https://xiaomifirmwareupdater.com/miui12/)  
如果是miui11的话直接把连接的路径改成miui11  

把下载的国内版ROM放入项目文件夹(注意不要重命名了)

3、依赖(一般linux发行版都有这两个了，版本不对问题也不大，执行不了再说)
```
apt-get install -y openjdk-8-jre python2.7
```

4、执行
```bash
chmod +x ./start
./start
```

不支持windows抱歉，我没有改bat和没有windows测试环境

最后将生成的 eufix-magisk-model-ver.zip 使用 magisk 刷入即可

---

[![Build Status](https://travis-ci.org/linusyang92/mipay-extract.svg)](https://travis-ci.org/linusyang92/mipay-extract)

**Use at your own risk!**

## 通知聚合未恢复问题

如果在设置中未出现通知聚合选项，在终端中输入下面的命令，adb shell 或者终端应用都可以

```bash
su

setprop persist.sys.notification_rank 6

#miui12 通知聚合
setprop persist.sys.notification_ver 2

#miui11 通知过滤(miui12开发版已经强制使用通知聚合，如果想在miui12开发版使用通知过滤请使用edxposed hook)
setprop persist.sys.notification_ver 1

killall com.miui.notification
```



## Usage

Put MIUI 9 China Rom (OTA zip package) in the directory and double-click `extract.bat` (Windows) or run `./extract.sh` (macOS and Linux) to generate the flashable zip.

Additionally, if you want to recover Chinese functions, e.g. bus card shortcut or quick payments for WeChat/Alipay in "App Vault" (i.e. the leftmost shortcut page on home screen), you can add an option `--appvault`: `extract.bat --appvault` (Windows) or `./extract.sh --appvault` (macOS and Linux). An extra flashable zip file with prefix `eufix-appvault` will be generated.

Support Windows, Linux and macOS (10.10 or above). Windows version has all dependencies included. For macOS, you need Java 8 runtime. For Linux, you may need both Java 8 and Python 2.7. For example, you can install all dependencies using `apt-get`:

```bash
apt-get install -y openjdk-8-jre python2.7
```

**Note**:

- It is recommended to run `extract.bat` on Windows. WSL (Windows Subsystem for Linux) is **not supported** due to issues of the emulated file system in WSL. You need a real Linux VM on Windows to run `./extract.sh`.
- To avoid line-ending issues, Windows users should **directly** [download the repo](https://github.com/linusyang92/mipay-extract/archive/master.zip) through the Github's "Clone or Download" button, instead of using a Windows version's Git command. If you clone the repo using a MinGW version's Git, the line endings may be incorrectly converted to CR/LF, which makes the generated packages invalid to use.

Automatic builds for selected devices are available in [releases](https://github.com/linusyang92/mipay-extract/releases).

## Recover Chinese Functions

For users in China, you can also download the **xiaomi.eu rom** and run `cleaner-fix.sh` for creating a flashable zip with prefix `eufix`. It contains patches to

- Show Lunar dates in Calendar app.
- Fix FC of cleaner app.
- Show payment monitor options in setting page of Security app.
- Use Chinese weather sources in Weather app.
- Allow alarm of legal workday repeat mode.

## (Optional) Encryption for xiaomi.eu ROMs

It is recommended to enable encryption if you plan to use Mi Pay. Official MIUI roms (China or International) has encryption enabled by default. But xiaomi.eu roms **remove encryption** by default (check in "Settings-Privacy-Encryption"). Some early versions of EU roms have bootloop bugs when enabling encryption in Settings (**Backup before trying!**).

If your device cannot be encrypted normally in Settings, you can completely **format `/data/` partition** and flash the additional zip file `eufix-force-fbe-oreo.zip` after flashing xiaomi.eu ROM.

**Warning**:

- Do not try to flash `eufix-force-fbe-oreo.zip` when your device is **decrypted**. It will cause bootloop. Only flash it when your device has a freshly formatted `/data` partition, or already encrypted.
- Formatting `/data` will destroy **EVERYTHING**, including all your personal data and external storage (`/sdcard`). Remember to backup before formatting.
- Once your `/data` partition is encrypted, it will be kept encrypted. But when you flash a new EU rom, the force encryption will be removed. You need to flash this file every time you flash a new EU rom.

## Credits

- sdat2img
- progress (by @verigak)
- smali/baksmali
- SuperR's Kitchen
- vdexExtractor
- Google Android SDK
- p7zip
- [flinux](https://github.com/wishstudio/flinux)
- [Apk-Changer](https://github.com/Furniel/Apk-Changer/tree/b3680c496169278079d7b23814d3c448f9853f81/other/cdexconv/linux)

## Disclaimer

This repository is provided with no warranty. Make sure you have legal access to MIUI Roms if using this repository. If any files of this repository violate your copyright, please contact me to remove them.

## License

[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
