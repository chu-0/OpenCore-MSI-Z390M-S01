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