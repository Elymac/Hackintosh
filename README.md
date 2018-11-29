# Hackintosh镜像制作、安装、、常用Kext驱动、config配置、常见失败原因及解决办法。

##### 前期准备工作

1. 准备8G及以上U盘。
2. 准备装有PE的U盘一个（Window下无法读取ESP分区，以便后期双系统时出现引导丢失无法修复的情况）。
3. 关闭Bios快速启动（闪速启动等）。
4. 开启BIOS AHCI。
5. 将要待安装的硬盘调整为GPT格式（ＤＧ）。

## 镜像制作(推荐使用白果或者虚拟机，这里已10.13.6为例）：

1. Windows：下载要安装的macOS镜像及最新版的Clover，插入U盘，下载安装TransMac，打开后右键点击U盘，选择“Format Disk for Mac”，将U盘格式化成Mac下的格式，将U盘格式化成Mac下的格式，名称随意，右键U盘选择“Restore with Disk Image”，选择下载好的macOS镜像，等待写入完成后，我的电脑中会自动出现名为EFI的磁盘，将整下载好的Clover中的EFI文件夹放入EFI磁盘的根目录（如果我的电脑中没有自动出现EFI磁盘，则可以在PE下进行此操作）。

2. Mac：在Appstore搜索macOS下载，插入U盘并打开磁盘工具，将U盘抹成APFS或者macOS扩展（日志式），在Finder中找到下载好的＂安装macOS High Sierra＂，右键选择查看包内容，在Contents/Resources下找到名为createinstallmedia的脚本文件，然后用终端调用这个文件。

   > Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinnstallmedia --volume /Volumes/ABC --applicationpath /Applications/Install\ macOS\ High\ Sierra.app



   对照自己的U盘名及安装文件修改（其中ABC为U盘名，注意空格）。回车以后输入y确认，等待完成后，打开官网下载的Clover安装程序
