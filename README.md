
## Realtek Switch Hacking
本 Repo 试图收集基于 Realtek 芯片的家用交换机/路由器的相关信息。  
包括但不限于：改造成网管机的教程、数据手册等各类文档、硬件设计、软件驱动等各类相关代码等。  
如果你想要贡献一些信息，欢迎 PR/issue。

## 已有内容   
### 改造教程  

[RTL8370N 芯片的 8 千兆交换机改网管](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/RTL8370N-SR8808M.md)  
[RTL8367N 芯片的 5 千兆交换机改网管](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/RTL8367N-GS105Ev2.md)  
注：RTL8370N/RTL8367N 等内置 8051 内核的交换芯片疑似需对内部 ROM 烧写代码后才能使用（见 [Issue #1](https://github.com/libc0607/Realtek_switch_hacking/issues/1)），而这段代码目前没有找到相关资料，意味着它可能不会从 Flash 启动。有大概率会翻，谨慎上车。  
[RTL8382L+RTL8218B 芯片的 16 千兆交换机改网管](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/RTL8382L.md)   

### 各类 Datasheet
注：这部分 Datasheet 全部来自于互联网，出处见 files 分支。 

#### 交换机
[RTL8305NB](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8305NB-CG_Datasheet_1.0_for_B-Link.pdf)  
[RTL8305SB](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/documents-switch-rtl8305sb-RTL8305SB-VD_Datasheet_1.5.pdf)   
[RTL8305SC](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8305SC-LF_PDF_C129791_2018-01-05.pdf)  
[RTL8306SD](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8306SD-GR_PDF_C21656_2014-03-19.pdf)  
[RTL8309G](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8309G-GR_PDF_C71951_2016-11-19.pdf)  
[RTL8363SB](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8363SB-CG_1.0.pdf)  
[RTL8366_8369](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8366_8369_DataSheet_1.1.pdf)  
[RTL8367N](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8367N-VB-CG_Datasheet_1.0.pdf)  
[RTL8367RB](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8367RB.pdf)  
[RTL8367S](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8367S-CG_Datasheet.pdf)  
[RTL8370(M)](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8370(M).pdf)  
[RTL8376](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8376_Datasheet_1.3.pdf)  
[RTL8380M_RTL8382M_RTL8382L](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8380M_RTL8382M_RTL8382L.PDF)  
 
 
#### 路由器
[RTL8196E](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/REALTEK-RTL8196E.pdf)  
[RTL8196C](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8196C-GR_Datasheet_0.7.pdf)  
[RTL8196E-VEx](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8196E-VEx-CG_Datasheet_1.1.pdf)  
[RTL8197D](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8197D%20DataSheet.pdf)  
[RTL8198](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8198_Datasheet_Cleaned_0.91.pdf)  

#### 驱动代码  
注：驱动代码和芯片之间的对应关系通常因为马甲的存在而不容易确定  
注2：这些除了 RTL8370 的几个 API 以外，我都没试过  
[RTL8370(RTL8367?)](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/RTL8370_RTL8367_API.zip)  
[RTL8325D](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/rtl8325d.zip)  
[RTL8365MB(RTL8367S/RTL8367C?)](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/rtl8365mb.7z)  
[RTL8367R](https://github.com/libc0607/RTL8370N_switch_hacking/blob/files/rtl8367r.zip)  

### 其他研究 
[我自己搞的 RTL8370 板子](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/RTL8370N-Demo.md)    
[用 ESP8266 + NodeMCU 通过 SMI 控制 RTL8370 的尝试](https://github.com/libc0607/RTL8370N_switch_hacking/blob/master/RTL8370-ESP8266.md)  

## License 协议
本 Repo 下个人做出的研究分析，和那个 PCB 及其相关设计：遵从 CC BY-NC-SA 3.0 协议。  
各家的固件，数据手册，资料等： 他们说了算（如果让我删我会的  。。  
本项目意在研究学习，请在下载使用后的 24 小时内停止运行并删除，如果拿去当奸商的话那后果自负。。   
如果有侵犯到你的权益，请通知我删除。   
