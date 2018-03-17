# RTL8370N_switch_hacking
---
这个repo应该不会更新了，也没什么可能更新的，关于RTL8370最近在试图用esp8266通过smi控制它，做了一堆Lua的绑定，这样用Lua就可以配置交换机了。  

如果你想帮忙测试/开发，请移步[libc0607/nodemcu-firmware](https://github.com/libc0607/nodemcu-firmware)，你需要一块nodemcu或兼容的板子（淘宝20元以内就能搞定） 和一个RTL8370芯片的交换机  

初步的效果见 [YouTube](https://www.youtube.com/watch?v=TsPTUKKPerY) 点亮LED  

This repository is deprecated. I'm planning to use NodeMCU to controll RTL8370 via SMI, and I've already made some Lua bindings of their APIs.   
See [libc0607/nodemcu-firmware](https://github.com/libc0607/nodemcu-firmware), welcome PRs/Test/Issues. You may need a NodeMCU board and a RTL8370 switch.  

LED blink demo at [YouTube](https://www.youtube.com/watch?v=TsPTUKKPerY)

## SMI got no ACK? / Can't open 192.168.1.1?  

楼主自己也试着做了 RTL8370N 的板子（见下图，渣布线轻喷但是ひでり真可爱啊），其实第一第二版基本的交换功能都能用，但是一些高级功能残废，表现为当试图从 Flash 启动时可以判断程序已成功加载但却无法打开 192.168.1.1，或是使用 SMI 总线控制时芯片不发送 ACK。  

楼主经过自己的多次**瞎 jb 分析**，猜测是芯片的某几个模块默认状态下无法工作（被锁定了？）。可能是被用于保护利益之类的（？）。   

而考虑到生产时的批量操作的可行性，以及 SR8808M 的迷之 4 Pin 引出 ，猜测 SP-08 的 EEPROM 里应该是包含（特定模块的初始化代码？/解锁特定模块的密码？/内置 8051 的 一部分ROM？），并且应该是在上电加载 EEPROM 的时候完成了这个过程。 SR8808M 的固件内有可能并没有这部分初始化代码（或因为判断不是他们生产的板子所以不启动？），导致打不开 192.168.1.1 。  

只是目前不太清楚这部分（初始化？/解锁？/写入 ROM ？）是只需要一次还是每次上电都要，因为 SP-08 那个拆掉 EEPROM 后只连接 Flash 也可以进 192.168.1.1 。  

所以大概思路就是：找一片 24c08，写入，然后将下文三个 pin 设置为 EEPROM to register 模式。  

RTL8370N 在将 EEPROM 里的数据加载到 Register 中之后， 会自动切换回 SMI 总线模式。  

楼主自己打板贴的是汕头产的芯片（笑），之前只能当傻瓜交换机用，一直收不到 ACK 。今晚楼主试着用这种思路测了一下，上电启动后连接 SMI，愉快地收到了芯片的 ACK。估计 Flash 启动也应该能行吧，有空测试完再更新下。  

啥资料都没有真是心累。。（瘫  

楼主正努力抽空出第三版电路板，之前两版 LED 都莫名失控，想改回最简单的 Parallel LED 模式，并且只留一路 3.3v 电源。。。。搞完肯定开源（Flag  


![My-Board-V2-1](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/Board-V2-2d.png)  

![My-Board-V2-2](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/Board-V2.png)  


关于这块板：LED 部分不知道能否正常工作。。。 BOM 在 JLC 那里有一份但是不全就懒得整理了。。。




## （直接焊 Flash 法的）目前进度
已经能打开网页界面了  
端口状态在网页上能正常显示  
![web-status](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/web-status.png)  
![web-duplex](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/web-duplex.png)  
试了下端口限速功能，可用。VLAN部分也测试过。   
其他功能没测试环境也懒得测试  

### todo：  

LED不正常，因为LED的模式没配置对  

网页上显示的端口编号和机身上不一致，这个问题您买根儿记号笔改改就好了  

其余功能还没测试  


## 关于RTL8370N
一个八口千兆交换机的芯片，集成了一个8051内核，可以控制控制交换机，开个Web管理啥的。  
一日有幸在Google遇见它的Datasheet，是来自某“全球最大中文IT社区”下载区，神秘代码8157377。。  
复位时通过三个引脚可以控制怎么启动。大概有如下几种模式  
![Datasheets-are-in-file-branch](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/boot-mode.png)  
这三个引脚的位置如图  
![pinmap](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/datasheet-pinmap.png)  

## 关于改造用的小白鼠
仕牌SP-SG08。原因：  
1.他们[官网](http://www.seapai.com.cn/a/gb2312/product/jiaohuanji/2014/0815/SP-SG08.html#hear2)写了芯片是RTL8370N  
2.拆机图能看到有Flash空焊盘([参考这里](http://bbs.mydigit.cn/read.php?tid=974442))  
3.淘宝78包邮（貌似..买不到了？  
![a-side](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/seapai-a-side.png)  
![a-side-pin](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/seapai-a-side-with-pinmap.png)  
![b-side](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/seapai-b-side.png)  

另：SR8808M.bin固件直接提取于自淘宝一种140左右的八口铁壳网管交换机。  

## 改造方法
购买清单：  
仕牌那个交换机    
W25P80  
0402电阻：22欧，4.7k，10k  
总计：8X元（看你在哪家买了）

从Flash启动条件：  
DISAUTOLOAD == 0 && DIS_8051 == 0 && EN_FLASH == 1  
DIS_AUTOLOAD(PIN 86) 已由R60拉低，不用管了  
DIS_8051(PIN 85)，焊R65为1，焊R66为0，阻值典型值4.7k  
EN_FLASH(PIN 84)，焊R73为0(默认已焊要拆掉)，焊背面R72为1，阻值典型值4.7k  


Flash电路部分：
找一片W25P80，编程器写入改好MAC的SR8808M.bin，焊上去  
CS：R10，22欧  
MISO：R11，22欧  
WP：R13，焊就接地，不焊悬空。我焊了个22欧的  
MOSI：R12（背面），22欧  
CLK：R14（背面），22欧  
HOLD：R8（背面），典型10k，我没找到焊了个5.1k的也还没坏

这是我个人的改造，为了看线路把绿油刮了，导致不敢用锡膏吹，  
只能用烙铁一点一点焊，渣渣焊工无视就好了。。  
![after-a](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/after-a.png)  
![after-b](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/after-b.png)  

## 注意事项
1.短接R18（空焊）即可恢复出厂设置，恢复后电脑设成192.168.1.2/24，  
  Chrome下Ctrl+Shift+N后打开地址192.168.1.1，认证 空/空（直接确定进入即可）  
2.固件备份出来的时候，交换机的地址是设置成了DHCP获取的。。  
  最好先接路由器上，看下自动获取到的地址，admin/admin进那个地址后再改设置  
3.如果同一个广播域内要使用两台或以上的这种改的交换机，一定要改MAC地址，  
  在固件的0xFC000处，或者看上面的图里的MAC地址然后在固件里搜一下就好了  

## 关于LED
LED的几种控制模式是由寄存器控制的。。原机背面的eeprom备份出的内容为SP08-EEPROM.bin，仅供参考  

在[@jiajun在Bitbucket里提供的资料](https://bitbucket.org/jiajun/rtl8xxx-switch)里得知 ：  

1.EEPROM的存储格式：前两Byte代表有效长度，后面每4Bytes一组，代表寄存器的地址和要写入该地址的值；  

2.跟LED有关的寄存器地址在0x1b26和0x1b27.（上面资料里RTL8370_reg.h中18620～18644行）  

现在倒是知道问题出在了什么地方，然而依然不知道它内置的8051怎么玩，也没有任何资料  

懒得解决了...打算用esp8266+nodemcu推倒重来了，见[libc0607/nodemcu-firmware](https://github.com/libc0607/nodemcu-firmware)   


  
## 一些简单的配置示例
比如这有一个迅雷下载宝接在端口2，端口1是WAN，端口3-8是LAN  
VLAN相关部分配置如下图   
![xiazaibao-as-router-setting](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/xiazaibao-as-router-setting.png)  
![single-arm-vlan](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/single-arm-vlan.png)  
![single-arm-pvid](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/single-arm-pvid.png)  
当然下载宝那个东西作路由器来用的话还缺一些包  


