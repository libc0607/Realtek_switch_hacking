## SG116M(RTL8382L) 的固件初始化部分逆向  

楼主第一次接触 逆向相关/MIPS汇编/IDA使用 这些，这几天查了不少资料，可能写得有问题，请指正。  


首先从 SG116M 的 Flash 中读固件  
载入 IDA Pro，架构选择 MIPS Big Endian，ROM 段在 0xBFC00000  
关于 0xBFC00000 是啥，推荐去看下 HackPascal 的科普  

在 0xBFC00000 处按下 C，然后它会带我们来到 0xBFC00400 处  
```
ROM:BFC00000 loc_BFC00000:                            # DATA XREF: ROM:BFC005A0↓o
ROM:BFC00000                 b       loc_BFC00400
ROM:BFC00004                 nop
```

看下 0xBFC00400，首先是一堆谜之初始化。因为我们没有内置 CPU 的手册，就认为它初始化正确并跳到下面继续看风景。  
从 0xBFC00598 看起。  

```
ROM:BFC00598                 la      $t0, unk_BF000000
ROM:BFC005A0                 la      $t1, loc_BFC00000
ROM:BFC005A8                 la      $t2, loc_BFC00400
ROM:BFC005B0
ROM:BFC005B0 loc_BFC005B0:                            # CODE XREF: ROM:BFC005BC↓j
ROM:BFC005B0                 lw      $t3, 0($t1)
ROM:BFC005B4                 addiu   $t1, 4
ROM:BFC005B8                 sw      $t3, 0($t0)
ROM:BFC005BC                 bne     $t1, $t2, loc_BFC005B0
ROM:BFC005C0                 addiu   $t0, 4
```

这一段把 0xBFC00000 ~ 0xBFC00400 复制到了 0xBF000000 ~ 0xBF000400。  
容易猜到，0xBF000000 ~ 0xBF020000 是它内置的 128k SRAM。  
所以这段就是把 Flash 开头的中断向量表复制到了 SRAM 开头。  
往下看。  

```
OM:BFC005C4                 la      $t0, dword_BF000400
ROM:BFC005CC                 lui     $t3, 0xA000
ROM:BFC005D0                 or      $t0, $t3
ROM:BFC005D4                 la      $t1, unk_BFC00690
ROM:BFC005DC                 la      $t2, loc_BFC01480
ROM:BFC005E4                 addi    $t1, (unk_BFC0068C - 0xBFC00690)
ROM:BFC005E8
ROM:BFC005E8 loc_BFC005E8:                            # CODE XREF: ROM:BFC005F4↓j
ROM:BFC005E8                 addiu   $t1, 4
ROM:BFC005EC                 lw      $t3, 0($t1)
ROM:BFC005F0                 sw      $t3, 0($t0)
ROM:BFC005F4                 bne     $t1, $t2, loc_BFC005E8
ROM:BFC005F8                 addiu   $t0, 4
```

这一段把 0xBFC00690 ~ 0xBFC01480 复制到了 0xBF000400 ~ 0xBF0011F0。  

```
ROM:BFC005FC                 la      $t1, unk_BF0011F0
ROM:BFC00604                 la      $t2, unk_BF00D6B8
ROM:BFC0060C                 addi    $t1, (unk_BF0011EC - 0xBF0011F0)
ROM:BFC00610
ROM:BFC00610 loc_BFC00610:                            # CODE XREF: ROM:BFC00614↓j
ROM:BFC00610                 addi    $t1, 4
ROM:BFC00614                 bne     $t1, $t2, loc_BFC00610
ROM:BFC00618                 sw      $zero, 0($t1)
```

这一段将 0xBF0011F0 ~ 0xBF00D6B8 初始化为 0，总大小约 50k，应该是作为 Heap 来使用。

```
ROM:BFC0061C                 la      $t0, unk_BF00D6B8
ROM:BFC00624                 la      $t1, loc_BFC01480
ROM:BFC0062C                 la      $t2, unk_BF010058
ROM:BFC00634                 lui     $t3, 0xA000
ROM:BFC00638                 or      $t0, $t3
ROM:BFC0063C                 or      $t2, $t3
ROM:BFC00640
ROM:BFC00640 loc_BFC00640:                            # CODE XREF: ROM:BFC00650↓j
ROM:BFC00640                 lw      $t3, 0($t1)
ROM:BFC00644                 addiu   $t1, 4
ROM:BFC00648                 sw      $t3, 0($t0)
ROM:BFC0064C                 addiu   $t0, 4
ROM:BFC00650                 bne     $t0, $t2, loc_BFC00640
ROM:BFC00654                 nop
```

这一段把 0xBFC01480 ~ 0xBFC03E20 复制到了 0xBF00D6B8 ~ 0xBF010058。猜测是会频繁运行所以声明了要放到 SRAM 段运行的代码之类的。  


```
ROM:BFC00658                 lui     $t0, 0xBF00
ROM:BFC0065C                 lui     $t1, 2
ROM:BFC00660                 add     $t0, $t1
ROM:BFC00664                 li      $t1, 0x800
ROM:BFC00668                 subu    $t0, $t1
ROM:BFC0066C                 move    $sp, $t0
```

这一段将栈顶指针移动到了 0xBF01F800，故 Stack 就应该是 0xBF010058 ~ 0xBF01F800 这一段，约 60k 左右。  
上面的 2k 不知道保留是做了什么，还没看到相关操作。  
所以我们大概知道了 SRAM 的结构以及 Flash 中代码段的大致结构，这为后面拼装另一个固件提供了参考。  












