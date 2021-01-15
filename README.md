# Part1 自我介绍

很有幸参与到这次Digi-key和硬禾学堂联合举办的这次开发板体验活动。我叫Argon，来自浙江杭州是一名从事~~安防行业~~ 物联网行业（嘿嘿，领导不听话我跳槽了）的嵌入式网络工程师。从大学开始就对微电子充满了兴趣，在闲暇的时间也会利用手头的元器件自己搭建一些好玩的DIY产品。

# Part2 硬件介绍

本期我们要介绍的是Silicon Labs 公司的Thunderboard EFR32BG22，这是一款十分小巧的Demo板子，PCB做工也及其的扎实。

![img](https://i.loli.net/2021/01/11/oZhHd6lONuecKy2.png)

板载了加速度、温湿度、霍尔、光强UV以及双声道麦克风等诸多传感器，并且支持蓝牙5.2以及蓝牙mesh，提供硬件加密解密，硬件上也做了低功耗设计可以满足开发者原型开发时候的需求。

对于学生朋友来说可能对EFR系列不是那么的熟悉，其实这个系列的SOC在蓝牙模组上算得上是做的较为出众的了，在我供职的公司中就有多款民用级的蓝牙产品使用了EFR系列的SOC。

![image-20210111222505172](https://i.loli.net/2021/01/11/xNStjo84UbB76fv.png)



# Part3 设计思路

好吧我坦白了，最近这个月太忙了加上第一次邮寄的板子有问题，所以这期Funpack没有认真的去做，但是思路还是要提供下的，就讲下我拿到这个神奇小板子之后是怎么划水来完成这次任务的。

### 任务分析

![image-20210111225837992](https://i.loli.net/2021/01/11/xtqKg6kURQyiefG.png)

任务是需要通过上位机或者其他蓝牙设备读取开发板上温湿度传感器的数据，并且经过上位机的处理之后解析显示出来，当这个温度超过门限值之后点亮板载的LED灯。

### 任务拆解

任务可以拆解为以下几个部分：

- 环境搭建，安装所需的编译器，下载所需的资料
- 编写SOC代码操作GPIO为后面LED控制做准备
- 编写SOC代码读取传感器数据，并通过串口打印出来以验证数据正确性
- 学习蓝牙协议，使得开发板蓝牙和手机建立连接并能进行双向通信
- 整合SOC代码
- 参考官方demo修改安卓端程序



## 参考资料的获取

### 开发板软硬件资料

如果你想深度去了解这款SOC的话只需要去安装[Simplicity Studio](https://cn.silabs.com/developers/simplicity-studio)，就可以了，Silicon Labs 将 EFR32BG22芯片的所有资料都放在了软件中，因此可以安装“一个软件走天下“。

<img src="https://i.loli.net/2021/01/13/ionuJE4kSbX9KNH.png" alt="Snipaste_2021-01-13_22-26-39" style="zoom:80%;" />

### 上位机资料 

对应的上位机 也提供了安卓和IOS两个版本，源码也完全对开发者开放，代码托管在了github上：

IOS版本：https://github.com/SiliconLabs/thunderboard-ios

Android版本：https://github.com/SiliconLabs/thunderboard-android



## 开箱测试

我们拿到开发板之后，板子中跑了一个全外设的测试程序，可以通过手机Demo app去读取板卡上所有传感器的数据，到了这里我就萌生了划水的想法，既然demo程序这么抢到，是不是就可以跳过中间的步骤直接改个App 就交作业了？

![image-20210111233136320](https://i.loli.net/2021/01/11/3dr8TvzFJZcAqps.png)

## 板子中Demo被擦除了怎么办？

在SI IDE中提供了两种Demo形式，一种是源文件（Examples Project），另外一种是Bin文件（Demos），因此如果你的开发板的Demo例程被擦除了只需要从新烧录下图红框中的`Bluetooth - SoC Thunderboard EFR32BG22 (BRD4184A)`这个例程的Bin文件即可。

![image-20210115204554532](C:\Users\songz\Pictures\博客截图\image-20210115204554532.png)



烧录时候单击上图中的Run按钮，等待下图弹窗进度条调走完之后手动复位开发板即可。

![Snipaste_2021-01-13_22-28-09](https://i.loli.net/2021/01/13/QGI81bhcery9vdm.png)

## 修改APP Demo程序

安卓App需要安装使用android studio，安装教程可以参考（建议搭梯子安装）：

https://www.runoob.com/android/android-studio-install.html

简单的使用教程可以参考 ID: `**阿正啷个哩个啷**`的《7天Java0基础速成安卓开发》：

https://www.bilibili.com/video/BV1G7411t7zs

app的Demo已经提供了丰富的接口函数，我们只需要找到接口函数的位置然后调用下就好了，并不需要有太强的Java功底。我采取了一个取巧的办法，根据Demo中的Debug信息去查找接口的调用，当设备有任何动作时候都会触发回调然后打印Debug信息，如下图所示，分别是LED控制的回调打印和温度湿度上报的打印。

![image-20210115205633299](https://i.loli.net/2021/01/15/WBR2MvfC5yg1Kqo.png)



这样我们就可以很方便的直接在温湿度实时上报的接口中增加一个阈值判断然后通过接口下发LED状态控制即可完成本次任务。修改就下面几行：

![image-20210113000956361](https://i.loli.net/2021/01/13/5S1mBJ32FVMcTPH.png)



# Part4 成果输出

### 视频：https://www.bilibili.com/video/BV1ey4y1H7h7

### 代码：https://github.com/songzhishuo/thunderboard-android.git

# Part4 心得体会

这期没有利用好板子大会它的全部能力，下期一定全力以赴！同时感谢@漂移君在百忙之中给我们提供pyblue的教程！