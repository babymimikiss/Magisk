#開發人南。。

##。。。 箱。。。

Magisk附帶功能完整的BusyBox二進製文件(包括完整的SELinux支持)。. 可執行文件位於。 `/數據/ adb / magisk /忙箱。。`.  Magisk的BusyBox運載時換成全切變的“ ASH佐外變模”。. wa種與合意的意向行。。. `‐。。` 商業票據外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外,外。 `路。`.  例,令喜。. `ls。。`, `rm。。`, `chmod。。` 王。 **。。不是。。。**。。.  利用面朝的東西。。。 `路。` ( 【Android b。。y默認會是。 `/系統/垃圾箱/ ls。。`, `/系統/箱/ rm。。`,和。。 `/系統/ bin / chmod。。` 分項),但直連調用部BusyBox小程序。。. 【樣可保hip首頁首頁可待的,配,配,配,配,配,配,配,配,配,配飾。。. 蘇聯解軍。。 _不是。_ 要使用BusyBox,您必須使用完整路徑調用可執行文件。.

在Magisk上下文中運行的每個shell腳本都將在BusyBox中執行。 `‐。。` 外用語。。. 【第三紀發發布者,包包和本安鬆餅。。.

對於那些想要在Magisk之外使用此“獨立模式”功能的人,有2種啟用它的方法:。

1。。. 設計場所。 `ASH_STANDALONE。。。` 至。。。 `1`<br。。>例:。 `ASH_STANDALONE=1 /data/adb/magisk/busybox sh <script>`
2。. 切換命令行選項:。<br。>`/data/adb/magisk/busybox sh -o standalone <script>`

確保所有後續操作。 `sh。。` (Shell)也是,(Shell)也是,(Magisk和Magisk應付款用法),因此,繼續承諾進軍。。.

##。 魔術模塊。

Magisk模塊是放置的文件夾。 `/數/ adb / mo。。` 和下結:。

```。。
/數/ adb / mo。。
├─────。。 .
├─────。。 .
|
├── $MODID                  <--- The folder is named with the ID of the module
│。。。。。。
││***同標識***。。
│。。。。。。
│   ├── module.prop         <--- This file stores the metadata of the module
│。。。。。。
││***主任務發布***。。
│。。。。。。
│   ├── system              <--- This folder will be mounted if skip_mount does not exist
│├───────。。 ...
│├───────。。 ...
│────────。。 ...
│。。。。。。
│   ├── zygisk              <--- This folder contains the module's Zygisk native libraries
│├──arm64-v8a.so。。
│├──armeabi-v7a.so。。
│├────x86.so。。
│├───x86_64.so。。
│   │   └── unloaded        <--- If exists, the native libraries are incompatible
│   │
│   │      *** Status Flags ***
│   │
│   ├── skip_mount          <--- If exists, Magisk will NOT mount your system folder
│   ├── disable             <--- If exists, the module will be disabled
│   ├── remove              <--- If exists, the module will be removed next reboot
│   │
│   │      *** Optional Files ***
│   │
│   ├── post-fs-data.sh     <--- This script will be executed in post-fs-data
│   ├── service.sh          <--- This script will be executed in late_start service
|   ├── uninstall.sh        <--- This script will be executed when Magisk removes your module
│   ├── system.prop         <--- Properties in this file will be loaded as system properties by resetprop
│   ├── sepolicy.rule       <--- Additional custom sepolicy rules
│   │
│   │      *** Auto Generated, DO NOT MANUALLY CREATE OR MODIFY ***
│   │
│   ├── vendor              <--- A symlink to $MODID/system/vendor
│   ├── product             <--- A symlink to $MODID/system/product
│   ├── system_ext          <--- A symlink to $MODID/system/system_ext
│   │
│   │      *** Any additional files / folders are allowed ***
│   │
│   ├── ...
│   └── ...
|
├── another_module
│   ├── .
│   └── .
├── .
├── .
```

#### module.prop

This is the **strict** format of `module.prop`

```
id=<string>
name=<string>
version=<string>
versionCode=<int>
author=<string>
description=<string>
updateJson=<url> (optional)
```

- `id` has to match this regular expression: `^[a-zA-Z][a-zA-Z0-9._-]+$`<br>
  ex: ✓ `a_module`, ✓ `a.module`, ✓ `module-101`, ✗ `a module`, ✗ `1_module`, ✗ `-a-module`<br>
  This is the **unique identifier** of your module. You should not change it once published.
- `versionCode` has to be an **integer**. This is used to compare versions
- `updateJson` should point to a URL that downloads a JSON to provide info so the Magisk app can update the module.
- Others that weren't mentioned above can be any **single line** string.
- Make sure to use the `UNIX (LF)` line break type and not the `Windows (CR+LF)` or `Macintosh (CR)`.

Update JSON format:

```
{
    "version": string,
    "versionCode": int,
    "zipUrl": url,
    "changelog": url
}
```

#### Shell scripts (`*.sh`)

Please read the [Boot Scripts](#boot-scripts) section to understand the difference between `post-fs-data.sh` and `service.sh`. For most module developers, `service.sh` should be good enough if you just need to run a boot script. If you need to wait for boot completed, you can use `resetprop -w sys.boot_completed 0`.

In all scripts of your module, please use `MODDIR=${0%/*}` to get your module's base directory path; do **NOT** hardcode your module path in scripts.
If Zygisk is enabled, the environment variable `ZYGISK_ENABLED` will be set to `1`.

#### The `system` folder

All files you want to replace/inject should be placed in this folder. This folder will be recursively merged into the real `/system`; that is: existing files in the real `/system` will be replaced by the one in the module's `system`, and new files in the module's `system` will be added to the real `/system`.

If you place a file named `.replace` in any of the folders, instead of merging its contents, that folder will directly replace the one in the real system. This can be very handy for swapping out an entire folder.

If you want to replace files in `/vendor`, `/product`, or `/system_ext`, please place them under `system/vendor`, `system/product`, and `system/system_ext` respectively. Magisk will transparently handle whether these partitions are in a separate partition or not.

#### Zygisk

Zygisk is a feature of Magisk that allows advanced module developers to run code directly in every Android applications' processes before they are specialized and running. For more details about the Zygisk API and building a Zygisk module, please checkout the [Zygisk Module Sample](https://github.com/topjohnwu/zygisk-module-sample) project.

#### system.prop

This file follows the same format as `build.prop`. Each line comprises of `[key]=[value]`.

#### sepolicy.rule

If your module requires some additional sepolicy patches, please add those rules into this file. Each line in this file will be treated as a policy statement. For more details about how a policy statement is formatted, please check [magiskpolicy](tools.md#magiskpolicy)'s documentation.

## Magisk Module Installer

A Magisk module installer is a Magisk module packaged in a zip file that can be flashed in the Magisk app or custom recoveries such as TWRP. The simplest Magisk module installer is just a Magisk module packed as a zip file, in addition to the following files:

- `update-binary`: Download the latest [module_installer.sh](https://github.com/topjohnwu/Magisk/blob/master/scripts/module_installer.sh) and rename/copy that script as `update-binary`
- `updater-script`: This file should only contain the string `#MAGISK`

The module installer script will setup the environment, extract the module files from the zip file to the correct location, then finalizes the installation process, which should be good enough for most simple Magisk modules.

```
module.zip
│
├── META-INF
│   └── com
│       └── google
│           └── android
│               ├── update-binary      <--- The module_installer.sh you downloaded
│               └── updater-script     <--- Should only contain the string "#MAGISK"
│
├── customize.sh                       <--- (Optional, more details later)
│                                           This script will be sourced by update-binary
├── ...
├── ...  /* The rest of module's files */
│
```

#### Customization

If you need to customize the module installation process, optionally you can create a script in the installer named `customize.sh`. This script will be _sourced_ (not executed!) by the module installer script after all files are extracted and default permissions and secontext are applied. This is very useful if your module require additional setup based on the device ABI, or you need to set special permissions/secontext for some of your module files.

If you would like to fully control and customize the installation process, declare `SKIPUNZIP=1` in `customize.sh` to skip all default installation steps. By doing so, your `customize.sh` will be responsible to install everything by itself.

The `customize.sh` script runs in Magisk's BusyBox `ash` shell with "Standalone Mode" enabled. The following variables and functions are available:

##### Variables

- `MAGISK_VER` (string): the version string of current installed Magisk (e.g. `v20.0`)
- `MAGISK_VER_CODE` (int): the version code of current installed Magisk (e.g. `20000`)
- `BOOTMODE` (bool): `true` if the module is being installed in the Magisk app
- `MODPATH` (path): the path where your module files should be installed
- `TMPDIR` (path): a place where you can temporarily store files
- `ZIPFILE` (path): your module's installation zip
- `ARCH` (string): the CPU architecture of the device. Value is either `arm`, `arm64`, `x86`, or `x64`
- `IS64BIT` (bool): `true` if `$ARCH` is either `arm64` or `x64`
- `API` (int): the API level (Android version) of the device (e.g. `23` for Android 6.0)

##### Functions

```
ui_print <msg>
    Print <msg> to console
    Avoid using 'echo' as it will not display in custom recovery's console

abort <msg>
    Print error message <msg> to console and terminate the installation
    Avoid using 'exit' as it will skip the termination cleanup steps

set_perm <target> <owner> <group> <permission> [context]
    If [context] is not specified, the default is "u:object_r:system_file:s0"
    This function is a shorthand for the following commands:
       chown owner.group target
       chmod permission target
       chcon context target

set_perm_recursive <directory> <owner> <group> <dirpermission> <filepermission> [context]
    If [context] is not specified, the default is "u:object_r:system_file:s0"
    This function is a shorthand for the following psuedo code:
      set_perm <directory> owner group dirpermission context
      for file in <directory>:
        set_perm file owner group filepermission context
      for dir in <directory>:
        set_perm_recursive dir owner group dirpermission context
```

For convenience, you can also declare a list of folders you want to replace in the variable name `REPLACE`. The module installer script will create the `.replace` file into the folders listed in `REPLACE`. For example:

```
REPLACE="
/system/app/YouTube
/system/app/Bloatware
"
```

The list above will result in the following files being created: `$MODPATH/system/app/YouTube/.replace` and `$MODPATH/system/app/Bloatware/.replace`.

#### Notes

- When your module is downloaded with the Magisk app, `update-binary` will be **forcefully** replaced with the latest [`module_installer.sh`](https://github.com/topjohnwu/Magisk/blob/master/scripts/module_installer.sh). **DO NOT** try to add any custom logic in `update-binary`.
- Due to historical reasons, **DO NOT** add a file named `install.sh` in your module installer zip.
- **DO NOT** call `exit` at the end of `customize.sh`. The module installer script has to perform some cleanups before exiting.

## Boot Scripts

In Magisk, you can run boot scripts in 2 different modes: **post-fs-data** and **late_start service** mode.

- post-fs-data mode
  - This stage is BLOCKING. The boot process is paused before execution is done, or 10 seconds have passed.
  - Scripts run before any modules are mounted. This allows a module developer to dynamically adjust their modules before it gets mounted.
  - This stage happens before Zygote is started, which pretty much means everything in Android
  - **WARNING:** using `setprop` will deadlock the boot process! Please use `resetprop -n <prop_name> <prop_value>` instead.
  - **Only run scripts in this mode if necessary.**
- late_start service mode
  - This stage is NON-BLOCKING. Your script runs in parallel with the rest of the booting process.
  - **This is the recommended stage to run most scripts.**

In Magisk, there are also 2 kinds of scripts: **general scripts** and **module scripts**.

- General Scripts
  - Placed in `/data/adb/post-fs-data.d` or `/data/adb/service.d`
  - Only executed if the script is set as executable (`chmod +x script.sh`)
  - Scripts in `post-fs-data.d` runs in post-fs-data mode, and scripts in `service.d` runs in late_start service mode.
  - Modules should **NOT** add general scripts during installation
- Module Scripts
  - Placed in the module's own folder
  - Only executed if the module is enabled
  - `post-fs-data.sh` runs in post-fs-data mode, and `service.sh` runs in late_start service mode.

All boot scripts will run in Magisk's BusyBox `ash` shell with "Standalone Mode" enabled.

## Root Directory Overlay System

Since `/` is read-only on system-as-root devices, Magisk provides an overlay system to enable developers to replace files in rootdir or add new `*.rc` scripts. This feature is designed mostly for custom kernel developers.

Overlay files shall be placed in the `overlay.d` folder in boot image ramdisk, and they follow these rules:

1. Each `*.rc` file (except for `init.rc`) in `overlay.d` will be read and concatenated **AFTER** `init.rc` if it does not exist in the root directory, otherwise it will **REPLACE** the existing one.
2. Existing files can be replaced by files located at the same relative path
3. Files that correspond to a non-existing file will be ignored

To add additional files which you can refer to in your custom `*.rc` scripts, add them into `overlay.d/sbin`. The 3 rules above do not apply to anything in this folder; instead, they will be directly copied to Magisk's internal `tmpfs` directory (which used to always be `/sbin`).

Starting from Android 11, the `/sbin` folder may no longer exists, and in that scenario, Magisk uses `/debug_ramdisk` instead. Every occurrence of the pattern `${MAGISKTMP}` in your `*.rc` scripts will be replaced with the Magisk `tmpfs` folder when `magiskinit` injects it into `init.rc`. On pre Android 11 devices, `${MAGISKTMP}` will simply be replaced with `/sbin`, so **NEVER** hardcode `/sbin` in the `*.rc` scripts when referencing these additional files.

Here is an example of how to setup `overlay.d` with a custom `*.rc` script:

```
ramdisk
│
├── overlay.d
│   ├── sbin
│   │   ├── libfoo.ko      <--- These 2 files will be copied
│   │   └── myscript.sh    <--- into Magisk's tmpfs directory
│   ├── custom.rc          <--- This file will be injected into init.rc
│   ├── res
│   │   └── random.png     <--- This file will replace /res/random.png
│   └── new_file           <--- This file will be ignored because
│                               /new_file does not exist
├── res
│   └── random.png         <--- This file will be replaced by
│                               /overlay.d/res/random.png
├── ...
├── ...  /* The rest of initramfs files */
│
```

Here is an example of the `custom.rc`:

```
# Use ${MAGISKTMP} to refer to Magisk's tmpfs directory

on early-init
    setprop sys.example.foo bar
    insmod ${MAGISKTMP}/libfoo.ko
    start myservice

service myservice ${MAGISKTMP}/myscript.sh
    oneshot
```
