本配置基于[github.com/gorquan/OC-XPS-7590](url)修改以支持4K机型
**仅支持Dell XPS 15 7590 4K 机型**
OpenCore 版本 0.6.4
macOS 版本 11.0.1-11.1 **不支持10.15及以下！**

### 配置信息
| 型号       | XPS 15 7590                     |
| ---------- | ------------------------------- |
| CPU        | Intel Core i7 9750H             |
| 核芯显卡   | Intel Graphics UHD 630          |
| 内建显示屏 | 15.6" 4K SHP14B9 带触摸         |
| 内存       | 8G*2                            |
| 固态硬盘   | TOSHIBA KXG60ZNV512G NVMe 512GB |
| 板载声卡   | Realtek ALC298                  |
| 无线网卡   | DW1820a                         |
### 使用前注意
请先参考该文章：[XPS 7590 1.6.0 UEFI: unlock undervolting and remove CFG lock](https://www.reddit.com/r/Dell/comments/fzv599/xps_7590_160_uefi_unlock_undervolting_and_remove/)，对CFG Lock进行解锁再使用该OpenCore！
### 工作情况
- CPU：
正常工作
正常变频,最低频率800MHz
温度正常
- 板载声卡：
正常工作
支持耳机、内置扬声器和HDMI音频输出
支持内建麦克风
- 核芯显卡：
正常工作
支持HDMI输出
- 内建显示屏：
正常工作
能正常输出4K 60Hz
- 蓝牙：
正常工作
能够与其他设备正常连接
- 电池：
正常工作
续航时间可以长达5小时（电池健康情况下）
- 无线网卡：
正常工作
隔空投送能够正常使用
- 键盘：
正常工作
键盘灯能够正常显示
快捷键正常工作
- 触控板：
正常工作
- 睡眠：
正常工作
盒盖睡眠正常工作
- 读卡器：
正常工作

**存在问题的设备**
- NVIDIA Geforce GTX 1650
无法进行驱动，已经屏蔽
- 读卡器
无法使用只读模式（内存卡加锁）
- 触摸屏
因为触摸屏会导致CPU占用率异常，导致续航一塌糊涂，因此屏蔽
- 蓝牙
无法接收从其他设备发送来的文件（发送文件正常）

### 说明
- 由于采用了PNP0C0D睡眠，因此Fn+Insert在外接HDMI情况下将关闭内屏而不是睡眠，当不外接HDMI时电脑将进行睡眠

### 使用后优化
- 对于睡眠部分，请参考睡眠设置
- 对于升级11.0.1后sech进程一直占用CPU，请打开iCloud密钥串同步开关，即可解决

### 睡眠处理
1. 检查hibernatemode是否为0或3

``` shell
pmset -g | grep hibernatemode
```

2. 在终端执行以下命令

``` shell
sudo pmset -a standby 0
sudo pmset -a proximitywake 0
sudo pmset -a hibernatemode 3 # 如果hibernatemode 不为3或0 执行此条命令
sudo pmset -a tcpkeepalive 0 # 如果仍然睡不着可以尝试一下睡眠期间断开网络连接
```

3. 除了“当显示器关闭时，防止电脑自动进入睡眠”是可选的外，请关闭设置-节能器里的所有其他选项。

### 声卡问题处理
> 以下方法待修复

板载声卡如果在电池供电状态下使用耳机，并从睡眠中唤醒会出现无声/爆音问题

* 原因

唤醒前`nid = 0x18 --> result 0x00000024`，唤醒后`nid = 0x18 --> result 0x00000000`，更改`nid = 0x18`的`result`为`0x00000024`即可正常发生

* 解决方式

有两种解决方式
  - 使用[ALCPlugFix](https://github.com/gorquan/ALCPlugFix),出现问题后**插拔耳机**
  - 执行以下命令：`hda-verb 0x18 SET_PIN_WIDGET_CONTROL 0x24`，需要参考**hda-verb安装**步骤

* hda-verb安装

`hda-verb`放置于`software`文件夹下，下载后请将其**放置**在`/usr/local/bin`目录下面

* 注意

如果采用两者，则**不要**再将`hda-verb`安装在`/usr/local/bin`目录下面，因为`ALCPlugFix`已经安装`hda-verb`到系统。

系统截图
[upl-image-preview url=https://windowsvistabar.s3.ap-southeast-1.amazonaws.com/2021-01-02/1609579687-971914-image.png]