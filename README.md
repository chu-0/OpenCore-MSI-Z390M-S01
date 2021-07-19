# OpenCore-MSI-Z390M-S01
OpenCore Files for MSI Z390M S01

# 这一周的心得
1. 需要耐心和计划，黑果小兵提醒的熬夜警示不是空穴来风；
2. 接上，真的会上瘾；
3. 很多教程自己都不知道是怎么回事；
4. 核显感觉比独显难。
5. 到现在没有见过苹果；
6. 应该买个容易点的主板。

# 新的0.7.1debug版本配置
1. 使用的perbulid的四个aml文件，得到错误文件如下
```
03:396 00:036 OCA: Inserted ACPI table has length mismatch 132869 vs 557582858, ignoring
03:427 00:030 OC: Failed to add ACPI SSDT-AWAC.aml - Invalid Parameter
03:467 00:039 OCA: Inserted ACPI table has length mismatch 134074 vs 557582858, ignoring
03:498 00:031 OC: Failed to add ACPI SSDT-EC-USBX-DESKTOP.aml - Invalid Parameter
03:534 00:036 OCA: Inserted ACPI table has length mismatch 133024 vs 557582858, ignoring
03:563 00:028 OC: Failed to add ACPI SSDT-PLUG-DRTNIA.aml - Invalid Parameter
03:599 00:036 OCA: Inserted ACPI table has length mismatch 132848 vs 557582858, ignoring
03:626 00:027 OC: Failed to add ACPI SSDT-PMC.aml - Invalid Parameter
```
2. 准备使用工具获取的四个文件替代

# 20210718 安装系统成功
1. 启动安装过程成功，安装成功。

2. 关键在于，安装时候，AAPL,ig-platform-id=0x12345678，得这么设置才能跳过现在如下信息后屏幕闪黑
```
IOConsoleUsers: gISOcreenLockState 3, hs 0, bs 0, nov 0, sm 0x0
```
这个时候txt的Log里的是Stuck on [EB|#LOG:EXITBS:START]。

3. 想把硬盘启动里的AAPL,ig-platform-id改为00009B3E，启动后黑屏。又改回了0x12345678

4. 现在EFI071debug目录里的EFI，可以启动i5 9400+MSI Z390m s01。当前的问题是
```
intel uhd graphics 630 7mb
```

5. 系统 catalina 19H15

# 20210719 iGPU显存配置正常

1. 这些值以前是用过的，安装系统不行。但是现在放在已经安装好系统的硬盘EFI分区，可以进入系统，现存正常。

2. 主要是以下这些值
```
AAPL,ig-platform-id 		07009B3E
device-id					923E0000
framebuffer-patch-enable 	01000000
framebuffer-stolenmem		00003001
framebuffer-fbmem			00009000
```

3. 其中 AAPL,ig-platform-id, framebuffer-patch-enable, framebuffer-stolenmem 三值来自于教程dortania的OpenCore-Install-Guide. 我一开始用的 AAPL,ig-platform-id 是 00009B3E，后面两个值没有设。就是进不了系统，后面看几个论坛的方案都是改0x12345678，可以安装，可以进系统，但是显存不正常，1080只有7M，2K是14M

4. device-id的值来自于 WhateverGreen的手册文件 FAQ.IntelHD.en.md . 我的系统现在是10.15.7，按照手册里说的，应该这个值可以删除了。我并没有删除。

5. framebuffer-fbmem的值来自于 dortania的OpenCore-Install-Guide教程，锚点 #Patching VRAM. 

6. 这些值设完，VRAM可以达到1536 MB。

7. 下面说说时间节点和心路历程

	- 7月4日开始的，利用的时间就是晚上这点时间。不熬夜，12点怎么都结束。

	- 7月11日之前尝试了很多别人的EFI，都没有成功过，还看过一些视频，照着做也没有成功过，我都准备放弃，准备装Manjaro去了。

	- 7月11日决定再尝试一次，这次自己配，opencore 0.7.1 debug，按照教程一步一步来dortania的OpenCore-Install-Guide，用的英文版。然后准备参考几个国内大牛的博客。

	- 这一遍的心态特别好，慢慢看，需要修改的健值全部记笔记。中间有几天晚上也忙，就都没有做。

	- 7月17日，第一个配置版本出炉，启动之后屏幕上卡在
		```		
		SMCSuperIO: ssio @ detected device Nuvoton NCT6797D
		```

	- 这个以前就发生过，记得当时使用了PerBuild的ACPI文件，改用SSDTTime-master自己生成，新版的会生成很多aml文件，dortania的OpenCore-Install-Guide里面说Desktop Coffee Lake需要四个文件，那就做这四个文件。

	- 然后Stuck on [EB|#LOG:EXITBS:START]，这个在教程里面可是Kernel Issues的第一块，log里面最后几乎全部卡在了这里，但是仔细看屏幕，在闪黑前，实际上是卡在这一句
		```
		IOConsoleUsers: gISOcreenLockState 3, hs 0, bs 0, nov 0, sm 0x0
		```

	- 问题应该出在核显，boot-args增加了-igfxmlr igfxonln=1这两个参数，来自于教程里的Kernel Issues下的锚点 Stuck on or near IOConsoleUsers: gIOScreenLock.../gIOLockState (3...

	- 问题没有解决， 然后就用了这个野方案，AAPL,ig-platform-id改0x12345678，网上有很多提到这个方案的帖子。

	- 可以装系统了，7月18日，系统是使用工具gibMacOS-master做的10.15.7（19H15），recovery版本。

	- opencore 0.7.1其实很简单，该做的教程里都写了。偏离主线教程的操作并不多，ACPI算一次，boot-args增加两个参数算第二次，AAPL,ig-platform-id改0x12345678算第三次，今天修复好UHD630的缓存算第四次。

	- 还有一处没有按照主线教程来，Drivers使用了闭源的HfsPlus.efi，而不是主线教程的开源OpenHfsPlus.efi。我看有大牛替换回去了。

	- 前期试的多，记录很少，很不科学。11日之后，有详细的记录，也就有了耐心。