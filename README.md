# Windows 10 Update 驱动更新实测

本文是一个实验记录，实验测试了 Windows 10 在多种不同设置下，对硬件驱动更新的不同行为，包括是否自动更新，以及更新的驱动版本等。

## TL;DR

Windows 10 的驱动升级机制似乎是某种双轨制。 分别受“指定设备驱动程序源位置的搜索顺序”和“Windows 更新不包括驱动程序”两个组策略项控制。

* 开启前者会使某些硬件总是自动更新驱动，不受 Windows Update 设置的限制。
* 关闭前者会使驱动像一般的 Windows 补丁一样随着正常的升级机制升级。
* 两个选项都关闭就会彻底关闭驱动程序通过 Windows Update 途径的升级。

## 测试环境

* **KVM**：Linux 5.5.2
* **QEMU**：4.2.0
* **libvirt**：5.10.0
* **虚拟机配置**：[ZLZD-driver.xml](ZLZD-driver.xml)
* **Windows**：Windows 10 LTSC 2019，接入测试设备前 Windows Update 升级到最新（2020.02.10，KB4534321，版本号17763.1012）

## 测试设备

* **9560BT**：Intel AC9560 无线网卡的蓝牙部分(USB通信)，直通到虚拟机
* **GTX1050**：NVIDIA GTX 1050 Mobile 显卡 ，直通到虚拟机
* **M570**：Logitech Unifying Receiver 无线接收器，直通到虚拟机
* **RTL8152**：Realtek USB 百兆网卡，没有实际硬件，是修改 QEMU 的 USB 虚拟网卡的源代码中的硬件 ID 得到的“假”设备，所以并不能工作，要在设备管理器中禁用

默认安装的驱动：
* **9560BT**：ID 为`USB\VID_8087&PID_0AAA&REV_0002`，默认无驱动
* **GTX1050**：ID 为`PCI\VEN_10DE&DEV_1C8D&SUBSYS_10441D05&REV_A1`，默认安装“Microsoft 基本显示适配器”驱动，匹配 ID 为`PCI\CC_0300`，驱动日期为`2006/6/21`，驱动版本号为`10.0.17763.1`
* **M570**：这个一个 USB Composite Device，我们关注的是它的第一个子设备，ID 为`USB\VID_046D&PID_C52B&REV_2411&MI_00`，默认安装“USB 输入设备”驱动，匹配 ID 为`USB\Class_03&SubClass_01`，驱动日期为`2006/6/21`，驱动版本号为`10.0.17763.719`
* **RTL8152**：ID 为`USB\VID_0BDA&PID_8152&REV_2000`，默认安装操作系统自带的“Realtek USB FE Family Controller”驱动，匹配 ID 为`USB\VID_0BDA&PID_8152`,驱动日期为`2015/9/20`，驱动版本号为`10.5.920.2015`，驱动文件`rtux64w10.sys`提供商为 Realtek

每个设备都对多种状态进行了测试，分别用不同的后缀来表示。
无后缀代表正常设置；
`-o`后缀表示安装较老版本的驱动后测试；
`-r`表示移除设备后测试（Windows 仍然会记住设备的信息，设备管理器中表现为隐藏的设备，叫做“ghosted device”）；
`-d`表示禁用设备后再测试。

“老”版本的驱动信息如下：
* **9560BT**：73d5ad64-132b-4bd6-87d4-51d38cbc579c_035a3067ecc07979a632a7d866064c5ae58ce406.cab，来自 Microsoft Update Catalog，dpinst.exe 安装，驱动日期为2017/10/15，驱动版本号为20.10.0.5，匹配设备 ID 为`USB\VID_8087&PID_0AAA&REV_0002`
* **GTX1050**：397.31-notebook-win10-64bit-international-whql.exe，来自 guru3d.com，驱动日期为2018/4/22，驱动版本号为24.21.13.9731，匹配设备 ID 为 `PCI\VEN_10DE&DEV_1C8D&SUBSYS_10441D05`
* **M570**：20403361_968e64cad47777002bfa6f2995f05b12b6716fc8.cab，来自 Microsoft Update Catalog，dpinst.exe 安装，驱动日期为2010/12/6，驱动版本号为1.0.3.0，匹配设备 ID 为`USB\VID_046D&PID_C52B&MI_00`
* **RTL8152**：20545443_0d41603d67c51aa85769544ea492f06ee32909ea.cab，来自 Microsoft Update Catalog，dpinst.exe 安装，驱动日期为2013/2/27，驱动版本号为7.10.227.2013，匹配设备 ID 为`USB\VID_0BDA&PID_8152&REV_2000`

## 不同的更新方式

## 实验结果记录

## 分析和结论

