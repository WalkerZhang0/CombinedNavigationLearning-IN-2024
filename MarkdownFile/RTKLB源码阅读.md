# RTKLB源码阅读

### Manual阅读

**来自3.2节：Real-Time Positioning With RTKNAVI**

该组件能够提供实时的定位解算，但是在数据流的传输过程中，有很多传输协议或者服务。

1. 什么是RTCM？

参考文章：https://www.tersus-gnss.com/tech_blog/what-is-rtcm-why-does-your-rtk-firmware-updating-matter

在RTK技术之中，实时传输的数据流对于该系统的正常运行相当重要。不同的数据都有其生成、存储和交换的标准格式，称之为协议。在全球范围内使用的差分系统的标准协议也逐渐标准化，其中最常使用的差分信息格式就是RTCM SC-104（Radio Technology Commission for Maritime Services）和CMR。

RTCM2.x由于固定宽度包和延伸的错误矫正，该协议在具有较高的信息负担的RTK应用中表现一般。Trimble于1996年引入了自己的CMR格式，并于次年更新CMR+，这项协议满足了低带宽通信的要求。

RTCM3发布于2004年，并且为RTK服务引入了一个新的信息格式，多星座，该项服务使用一个可变长度格式提升效率并提高可发送信息的字符数量，这对于实时差分相当重要。RTCM3也已经更名为RTCM1040.1。

2. 

重新树立了一下学习的基本流程，我觉得应该先将软件的基本用法学习以下：具体一些包括命令行使用（CUI）以及界面使用GUI；

**CUI：**来自rtklib的操作文档中Appendix A CUI Command Reference

1. RTKCRV：该控件用于实时定位解算，在我的理解上，实时的定位解算要求实时输入数据流，这需要在接收机和计算机之间建立实时的数据通信，因此需要输入外界设备的信息传输接口等信息，具体的配置文件可以做更改。该模块的使用命令如下，成功建立起通信链路之后，计算机终端将进入rtkcrv控制台，在控制台中可以设置输出部分解算信息，或者结束解算等操作。

   ```bash
   rtkcrv [-s][-p port][-d dev][-o file][-t level]
   ```

2. RNX2RTKP：该控件通过读取导航观测文件、星历文件，计算接收机位置并输出定位结果。具体包括七种定位模式，不同的模式之间的解算配置信息可以通过命令行的输入修改，如下：

   ```bash
   rnx2rtkp [option ...] file file[...]
   ```

3. POS2KML：读取定位解算输出的成果文件，并将其转换成Google Earth KML file。

4. CONVBIN：实现RTCM、接收机原始文件和RINEX文件的转换。具体支持的协议可以参见操作文档之中。

5. STR2STR：实现数据流之间的转换。

**GUI：**GUI文件仅仅支持在Win下面进行编译；

