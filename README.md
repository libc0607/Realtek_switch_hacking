# RTL8370N_switch_hacking
---
这个repo应该不会更新了，也没什么可能更新的，关于RTL8370最近在试图用esp8266通过smi控制它，做了一堆Lua的绑定，这样用Lua就可以配置交换机了。  

如果你想帮忙测试/开发，请移步[libc0607/nodemcu-firmware](https://github.com/libc0607/nodemcu-firmware)，你需要一块nodemcu或兼容的板子（淘宝20元以内就能搞定） 和一个RTL8370芯片的交换机  

初步的效果见 [YouTube](https://www.youtube.com/watch?v=TsPTUKKPerY) 点亮LED  

This repository is deprecated. I'm planning to use NodeMCU to controll RTL8370 via SMI, and I've already made some Lua bindings of their APIs.   
See [libc0607/nodemcu-firmware](https://github.com/libc0607/nodemcu-firmware), welcome PRs/Test/Issues. You may need a NodeMCU board and a RTL8370 switch.  

LED blink demo at [YouTube](https://www.youtube.com/watch?v=TsPTUKKPerY)


关于本分支下的数据手册来源：
 - RTL8367S RTL8367N 来自 逆天 PCB http://bbs.ntpcb.com 
 - RTL8370(M) 以及其他部分路由 SoC 手册 来自 CSDN
 - 标有形如 C***** 代号的 来自立创商城
 - 其余来自于 Google （未记录具体来源）
 
全部资料收集自互联网，意在学习相关硬件技术。如果违反了什么什么协议，请开个 issue 通知删除，带来不便非常抱歉！


## 目前进度
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
![boot-mode](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/pic/boot-mode.png)  
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
个人觉得仕牌其实算是挺良心的，以前的大小屌丝，还有这个交换机  
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


