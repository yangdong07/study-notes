


## Technical Support Fundamental


## Hardware


- Motherboard， 主板，  body， 连接各主要器件： CPU、Memory等等
- CPU， Central Processing Unit，中央处理单元
    - Register，寄存器，存储处理数据
    - Cache，缓存， L1/L2/L3 缓存， 存放经常使用的数据
    - L1是最小的，最快的Cache
- Memory， 内存， 快速存取（Short-term memory）
- Power Supply，电源
- Storage，存储器件
- MCC： Memory Controller Chip，用于根据地址读取内存中的数据或指令。
- EDB： Extend Data Bus， 8线、16线、32线、64线，每一线一个bit。高电压表示1， 低电压表示0。
- Address Bus：地址线
- CPU Clock Cycle：CPU 每次操作一个指令， 一个clock tick。一秒有多少个tick，表示CPU的处理速度上限，但并非绝对极限，实际上可以超过这个上限。Overclocking。


### Components


#### CPU

- LGA 封装
- PGA 封装
- 架构，指令集。 这是最重要的，很多程序通常只能在各自特定的CPU下工作， 例如 intel处理器或者 Apple A8处理器。 有些程序是跨平台（CPU）的，意味着在某种架构之上的程序，例如 java程序，运行于虚拟机之上。
- 32bit、64bit。

散热问题


#### RAM

- RAM，random access memory
- DRAM， Dynamic RAM
- SDRAM，Synchronous DRAM
- Double Data Rate SDRAM， DDR
    - DDR1
    - DDR2
    - DDR3
    - DDR4 （now）


#### Motherboard 主板

- NorthBridge， 北桥， 连接 CPU与 RAM，高速器件。 现代CPU集成了 北桥的一些chipset。
- SouthBridge， 南桥， 连接 CPU与 I/O，  Peripherals， 例如 键盘、鼠标、显示器。
- Expansion slots ， 扩展槽。可以扩展CPU功能，例如 Graphics Card，显卡。
- PCI， Peripheral Component Interconnect Express， PCIE
- form factor， 主板的大小、尺寸标准。 ATX、ITX


#### Storage

- HDD， Hard Disk Drives
- SSD， Solid State Drive， 固态硬盘
- Serial ATA，  SATA （串行）数据接口， 一个cable数据传输。
- NVM Express， NVMe，另一种接口标准，并行数据接口， 提高数据吞吐（throughout)


#### Peripherals

- MB， MegaByte， 数据存储单位
- Mb/s， Megabits per seconds， 数据传输速率单位。
- USB（Universal Serial Bus， 参考 <https://en.wikipedia.org/wiki/USB>) ，很多种
    - USB 2.0 黑色， 480Mb/s
    - USB 3.0 蓝色， 5Gb/s
    - USB 3.1  青色， 10Gb/s。 颜色由制造商决定。
- USB type C
- DVI 接口
- HDMI: audio and video


### BIOS

- BIOS, Basic Input Output Service
- BIOS程序储存在一个 ROM chip
- UEFI, Unified Extensible Firmware Interface
- POST, Power-On Self Test， 上电自检，检查所有可用硬件
- beep code， 如果自检正常，1次beep； 其他beep表示错误，可以查看手册，定位错误。


### 组装

- 防静电手腕带，防止人体静电损害贵重器件
- CPU
    - thermal paste， 导热硅胶
    - heat sink， 热传导
    - CPU fan， 散热


## Operating System

- Windows，现在PC多指 Windows操作系统
- Mac OS
- Linux， 开源操作系统（distributions）
    - Ubuntu
    - RedHat
    - CentOS


### Kernel Space/ User Space

- User Space，用户应用程序
    - Applications
- Kernel Space 管理操作硬件资源
    - File Manager
    - Process Manager
    - Memory Manager
    - I/O Manager

### Files and File System

不同OS 有不同文件系统，用于管理文件。

- File Handling：
    - Data
    - Metadata
    - File System

- Windows： NTFS / ReFS
- Mac： APFS
- Linux： ext4

### Memory Management

内存管理

- programs： 分成 pages
- pages存储在Virtual Memory中，这是结合 硬盘和实际内存的一个概念。
- 实际运行某一个page的程序

### Process Management

进程管理： 

- Time Slice，时间分片。操作系统会将CPU计算时间分片，给不同进程使用。


### I/O Management

kernel 还要管理 Input/Output devices 以及 devices之间的通信。

### Logs

注意观察日志。

### Boot 过程

1. 电源开关
2. BIOS/UEFI POST， 上电自检
3. Boot Device： Bootloader，一个小程序，加载OS。

### 作业练习

使用 Google Cloud Platform， 废了九牛二虎之力。。因为网太慢，翻墙不容易。

遇到的问题：

1. 用自己的google账号登录GCP，结果用其他账号google也管我要电话号码。实际上quiwklab是个第三方平台，可以自动在GCP上创建VM，进行实验。 如果跳转登录不行，直接去GCP用quiwklab提供的用户名密码登录即可。
2. windows 是 RDP远程桌面登录，卡的要死。。还好IP不用翻墙。
3. linux 是 SSH登录， easy。

**主要原因是用了路由器做VPN， google无法识别device，所以才要求verify。换成mac用vpn连接就好使了！！！！**

有点玄学了。。。，满足以下几个条件：

1. 选择 hongkong4的 vpn
2. 用一个新的person，在chrome上登录coursera 跳转到 GCP（无历史浏览记录）
3. 然后输入用户名、密码，这样似乎才不要求verify。。
