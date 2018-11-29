# Hackintosh镜像制作、安装、、常用Kext驱动、config配置、常见失败原因及解决办法。

#### 一.前期准备工作

1. 准备8G及以上U盘。
2. 准备装有PE的U盘一个（Window10下无法读取ESP分区，以便后期双系统时出现引导丢失无法修复的情况）。
3. 关闭Bios快速启动（闪速启动等）。
4. 开启BIOS AHCI。
5. 将要待安装的硬盘调整为GPT格式（推荐DG分区工具）。

#### 二.镜像制作(推荐使用白果或者虚拟机，这里以10.13.6为例)

1. Windows：下载要安装的macOS镜像及最新版的Clover引导，插入U盘，下载安装TransMac，打开后右键点击U盘，选择“Format Disk for Mac”（将U盘格式化为Mac下的格式），名称随意，右键U盘选择“Restore with Disk Image”，选择下载好的macOS镜像，等待写入完成后，我的电脑中会自动出现名为EFI的磁盘，将下载好的Clover引导中的EFI文件夹放入EFI磁盘的根目录（如果我的电脑中没有自动出现EFI磁盘，则可以在PE下进行此操作）。

2. Mac：在Appstore搜索macOS下载，插入U盘并打开磁盘工具，将U盘抹成APFS或者macOS扩展（日志式），在Finder中找到下载好的＂安装macOS High Sierra＂，右键选择查看包内容，在Contents/Resources下找到名为createinstallmedia的脚本文件，然后打开终端输入以下命令，或者直接拖动文件到终端窗口。

   > Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinnstallmedia --volume /Volumes/ABC --applicationpath /Applications/Install\ macOS\ High\ Sierra.app

   对照U盘名及安装文件修改（其中ABC为U盘名，注意空格）。回车以后输入y确认，等待完成后，打开官网下载的Clover安装程序，更改安装位置为目标U盘，点击自定按钮，勾选一下选项，其余选项默认就好。

   > 仅安装UEFI开机版本
   >
   > 安装Clover到EFI系统区

3. 到这里U盘的安装镜像制作完毕，接下来需要对U盘的Clover进行设置以引导我们顺利安装。

#### 三.Clover文件配置

1. config.plist设置：下载并打开Clover Configurator，点击Mount EFI，选择目标U盘并挂载，这里会提示输入密码，完成以后自动进入EFI文件夹，在EFI/Clover文件夹中找到config.plist(相当于Clover引导的设置文件)，选择使用Clover Configurator打开，会看到Clover引导的详细设置，这里我们的原则是能不填尽量不填，能留空尽量留空，以防止引导安装时出现错误，安装完成后再根据自己的情况完善设置。

   1.ACPI

   ​	点击list of patches，选择以下选项添加，其余选项可以删除。

   > change EHC2 to EH02
   > change EHC1 to EH01

   勾选PluginType 剩余部分全部留空。

   2.Boot

   ​	勾选以下选项（开机时显示debug，方便出错时了解问题）。

   > Verbose (-v)
   >
   > debug=0x100
   >
   > keepsyms=1

   ​	Custom Flags（启动参数）中填写以下参数，中间用空格隔开。

   > alcid=1 -lilubeta -wegbeta -alcbeta 

   ​	alcid=1为音频ID，可以自行尝试其他数字

   3.Devices

   ​	去掉所有已勾选的选项，全部留白。

   4.Graphics

   ​	去掉所有已勾选的选项，全部留白（独显N卡可以勾选Nv选项）。

   5.Kernel and Kext Patches

   ​	删除所有选项，全部留白。

   6.SMBIOS

   ​	点击右下角箭头，选择和自己设备最接近的型号。

2. 驱动设置：安装前添加必要kext以驱动我们的硬件

   1.FakeSMC:<https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek/downloads/>

   ​	让macOS识别我们的硬件（必用）。

   3.Lilu：<https://github.com/acidanthera/Lilu/releases>

   ​	一款开源的内核扩展补丁，可以为任何kext驱动提供补丁，用于配合其他驱动工作。

   4.WhateverGreen：<https://github.com/acidanthera/WhateverGreen/releases>

   ​	修复黑苹果AMD/NVIDIA显卡花屏、黑屏、睡眠黑屏等各种问题的显卡驱动补丁，依赖lilu.kext。

   5.AppleALC：<https://github.com/acidanthera/AppleALC/releases>

   ​	一款开源的黑苹果声卡驱动，主要作用是加载原生AppleHDA声卡驱动，依赖lilu.kext。

   6.网卡驱动：<https://bitbucket.org/RehabMan/os-x-intel-network/downloads/>（Intel网卡）
               <https://bitbucket.org/RehabMan/os-x-realtek-network/downloads/>（Realtek网卡）

   ​	用于驱动网卡，选择对应型号安装。

   ## 以上只是一些必备驱动，后期根据电脑硬件自行完善。

#### 四.安装macOS

1. 插入U盘，开机BIOS选择从U盘启动,进入Clover引导界面，选择Boot Install macOS from……。
2. 进入macOS安装界面后选择磁盘工具，选择安装的硬盘进行抹盘，格式为APFS或者macOS扩展（日志式）。
3. 退出磁盘工具，选择安装macOS。
4. 选择Boot macOS from……进入macOS开机设置，完成以后进入桌面。
5. 使用Clover Configurator挂载硬盘的EFI分区，复制U盘中的EFI文件夹到硬盘中的EFI分区中。

#### 五.完善驱动及常见问题

1. 根据情况添加自己需要的驱动

2. 常见问题：

   Q：无法进入macOS安装界面？

   A：关闭BIOS安全模式，并选择ACHI。



   Q：安装软件时提示未知开发者？

   A：打开终端输入以下命令后回车并输入密码。

   > sudo spctl --master-disable



   Q：安装双系统后发现Windows和macOS时间不同步？

   A：在Windows下Win+R，输入cmd回车开启命令提示符并输入以下命令。

   > Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1



   Q：macOS安装时，使用磁盘工具抹盘失败？

   A：因为硬盘上的EFI分区小于200M，在PE或Windows下使用DG工具删除目标硬盘上的所有分区，新建分区时选择同时建立ESP分区并设置分区大小（建议500M，方便安装双系统）。



   Q：安装完成后BIOS没有出现Clover启动项？Clover没有出现macOS引导项？

   A：进入Windows下载安装EasyUEFI，管理员身份运行并新建启动项，启动文件选择目标硬盘中的EFI/CLOVER/BOOTX64.efi 设置名称为Clover并重启。或者在Clover引导界面选择Clover boot options回车，选择Add Clover Boot Options。



   Q：先安装macOS，然后安装Windows到同一块硬盘Clover引导丢失？先安装Windows，然后安装macOS引导也会丢失？

   A：安装Windows时会将硬盘的ESP分区中的数据抹掉，解决方法是先安装Windows再安装macOS，如果不行就是因为ESP分区小于200M，macOS也会自动抹去ESP分区，一种方法是扩大ESP分区到500M，第二种方法是在PE下备份第一个系统的引导文件，安装完第二个系统后，将备份的引导文件放入ESP分区。
