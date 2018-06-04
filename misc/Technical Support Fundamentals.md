


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
- CPU Clock Cycle：CPU 每次操作一个指令， 一个clock tick。一秒有多少个tick，表示CPU的处理速度上限，但并非绝对极限，实际上可以超过这个上限。Overclocking。


### Components


#### CPU

- LGA 封装
- PGA 封装
- 架构，指令集。 这是最重要的，很多程序通常只能在各自特定的CPU下工作， 例如 intel处理器或者 Apple A8处理器。 有些程序是跨平台（CPU）的，意味着在某种架构之上的程序，例如 java程序，运行于虚拟机之上。
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

- NorthBridge， 北桥， 连接 CPU与 RAM，高速器件。 现代CPU集成了 北桥的一些chipset。
- SouthBridge， 南桥， 连接 CPU与 I/O，  Peripherals， 例如 键盘、鼠标、显示器。
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
- Mb/s， Megabits per seconds， 数据传输速率单位。
- USB（Universal Serial Bus， 参考 <https://en.wikipedia.org/wiki/USB>) ，很多种
    - USB 2.0 黑色， 480Mb/s
    - USB 3.0 蓝色， 5Gb/s
    - USB 3.1  青色， 10Gb/s。 颜色由制造商决定。
- USB type C
- DVI 接口
- HDMI: audio and video
