# 0x3 Misc
[TOC]

## 隐写题-答题思路
1. 1.先看题目, 根据题目搜索关键字 2.  按提示转base64搜，末尾字符搜，多处合并 3. 附件的文件名
2. 文件属性-详细信息 ，各种奇怪的数据，加转码。
3. 010editor, 搜索Text型, flag, Unicode型 flag
3. 打开看文件内容
4. 文件异常：高度，宽度是否正常。
5. winhex分析，前部中部尾部
0. foremost file
0. 7z解压, 不要光用winrar
0. binwlak -e filename
6. binwalk试下 binwalk -e new.jpg

        * tiff data, 用 Stegano_Stegsolve的 frame browser
        * 有zlib 可能需要 lsb, RGB 加密等，用Stegano_Stegsolve各种尝试。
7. 010editor 分析, 配合 dd命令 分离，或者直接010 editor中分离
    
        010editor 搜索 `\{[a-z\d]{4,}`, 选项勾上 Regular expression
        strings xiaojiejie.jpeg | grep -E "\{[a-z]{4,}"

8. PNG文件：一定要使用windows照片查看器看，会忽略crc检验。
8. PNG文件：调整下高度，看看有没有flag。
8. PNG文件：改了宽度。暴破下过CRC32验证
8. GIF文件：stegsolve 按帧看
9. pdf文件: chrome打开全选复制出来看看
8. 图片隐写 stegsolve 看下lsb隐写, zsteg看=> `zsteg pcat.png`
8. 图片隐写 Steghide 看下有没有隐写。 steghide extract -sf filename
10. 密码相关, steghide尝试密码123456
10. 图片隐写 outguess -k 密码-r 需要破解的文件名 -t 转出的文件名
            outguess -k gemlove -r sheng_huo_zhao_zhao.jpg -t flag.txt
10. 图片隐写 用strings xx.jpg | more 或010editor，搜索flag, ctf
10. 图片隐写 2进制的拼图,用010editor或winhex看形状。
10. 图片隐写 提示 刷新/F5, 使用F5-steganography
10. 图片隐写 彩图提取二维码。stegsolve选一适合的黑白帧。调整大小/去取某通道某 bit 信息/随机上色/etc. 都行。
        
    11. 或者[BJDCTF#bincat2](http://www.fzwjscj.xyz/index.php/archives/30/#bincat2)，对比两小图的区别，然后循环大图对比指定偏移量。
11. 文字隐写 snow可Google:space tab steg, baidu: 空格 制表符 隐写
11. 文字隐写 snow.exe -C flag.txt
12.         base64隐写
10. 脑洞信息 图片是倒着的, dragon倒过来就是 nogard
10. 脑洞信息 galf_si_erehw 提示: 可能是将hex数据倒转过来
11. 看hex值符合哪些特点，倒序 | 文件头 |
11. Detect it Easy 查看, 见下面的具体用法
10. pcapng 取证： 1. foremost分解看看。 2. 用bytes搜索pass, flag 3. 追踪流 4.见底下详解
11. zip/rar 文件， 使用unzip或者winrar打开 逐个解压，因为包里可能有不需要密码的。
11. zip/rar 密码，先看题目提示，图片文件用010 editor搜索设置 Unicode型, 搜pass, 4位试下。不行再看总结。
11. zip/rar 明文攻击, 里面文件有可能是网上有的。搜一下下载个进行明文攻击。
12. 流量分析 icmp, 长度转ascii码, 见下面 简单流量分析
11. doc文件 1. 显示隐藏文字 2. 解压看有没有隐藏图片
12. cap文件, 802.11协议的数据包  aircrack-ng shipin.cap 见底下
12. 01001010 等二进制转字符串试试。
13. xor文件 xortools破解。见下面
14. hex文件 IDA分析, 或 xxd -r -p MissionImprobable.TEENSY31.hex out
13. 路由相关使用 routerpassview 搜 username 或者是password
14. 音频, Audition 看看。多是摩斯码。
15. vmdk 一定要在Linux下7z解压: __7z x filename__
16. 查找webshell, 使用D盾审计。
17. 视频文件, Premiere看比较清楚。
18. sage 解方程，二元一次，一元多次方程，见下面
19. 太空站文件 了慢扫描图像工具：MMSSTV https://hamsoft.ca/pages/mmsstv.php



## png隐写

    00 00 00 0D 49 48 44 52 --0D头块长13, 49 48 44 52 IHDR标识
    00 00 01 f4 00 00 01 a4  宽:00 00 01 f4即500 高: 00 00 01 a4即 400
    08 06 00 00 00 cb d6 df 8a --- CRC校验 29:32 这4个字节是CRC

有时会改宽度，需要CRC校验的。

0x12,13 宽度, 大端模式, 999为03e7

0x16,17 高度

左上角为基点分别是向右拉和向下。宽度一般不会改，会进行CRC校验的。

pngcheck -v filename

Image photography线上和离线加密方式不同。

### png格式介绍1
参考链接

https://dev.gameres.com/Program/Visual/Other/PNGFormat.htm

https://blog.csdn.net/hherima/article/details/45847043

https://www.bilibili.com/video/BV1V4411Z7VA

00|01|02|03|04|05|06|07|08|09|0A|0B|0C|0D|0E|0F
--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
89|50|4E|47|0D|0A|1A|0A|00|00|00|0D|49|48|44|52
00|00|00|0F|00|00|00|20|08|06|00|00|00|CD|2C|22
宽度1/4|2/4|3/4|4/4|高度1/4|2/4|3/4|4/4|Bit depth|ColorType|Compression Method|Filter method|Interlace Method|crc 1/4|2/4|3/4
A9 |
4/4|

### png格式介绍2

    89 50 4E 47 0D 0A 1A 0A 00 00 00 0D 49 48 44 52
    00 00 07 80 00 00 04 B0 

前四个字节00 00 00 0D（即为十进制的13）代表数据块的长度为13，数据块包含了png图片的宽高等信息，该段格式是固定的

之后的四个字节49 48 44 52（即为ASCII码的IHDR）是文件头数据块的标示，该段格式也是固定的

之后进入13位数据块，前8个字节00 00 05 56 00 00 03 00中：

前四个字节00 00 05 56（即为十进制的1366），代表该图片的宽，该段数据是由图片的实际宽决定的

后四个字节00 00 03 00（即为十进制的768），代表该图片的高，该段数据是由图片的实际高度决定的

这8个字节都属于13位数据块的内容，因此数据块应再向后数5个字节，即为00 00 05 56 00 00 03 00 08 02 00 00 00

剩余的4位40 5C AB 95为该png的CRC检验码，也就是本篇文章要重点讨论的地方，该段数据是由IDCH以及十三位数据块（即上文中的49 48 44 52 00 00 05 56 00 00 03 00 08 02 00 00 00）计算得到的
### 17 steghide隐写01\
将Misc.png拖入winhex发现尾部不对。
1. 用foremost 解压 Misc.png
2. 用steghide 解密 000112.jpg，出现密码
3. 打开pdf，输入密码。（或者暴破）

### 19 outguess隐写
outguess -r angrybird.jpg 11.txt

# 工具使用

## wifi.cap wifi密码破解 aircrack, airdecap-ng

    aircrack-ng -w 常用密码.txt wifi.cap
    aircrack-ng -w /usr/share/wordlists/rockyou.txt wifi.cap

解密流量包

    airdecap-ng shipin.cap -e 0719 -p 88888888
    airdecap-ng shipin.cap -e 0719(前面的essid) -p 88888888
### 解密 http 包

过滤语句 http.request.method==POST 找到压缩包分卷每个进行保存。

binwalk分析一下 发现有一个rar压缩包 364应该是数据包的一些头等等全部去掉。

    dd if=a1 bs=1 skip=364 of=b1
    dd if=a2 bs=1 skip=364 of=b2
    dd if=a3 bs=1 skip=364 of=b3
    dd if=a4 bs=1 skip=364 of=b4
    dd if=a5 bs=1 skip=364 of=b5
    cat b1 b2 b3 b4 b5 > fly.rar

参考 https://www.cnblogs.com/cat47/p/11567399.html

https://blog.csdn.net/sinat_36188088/article/details/53333565
## notepad++
二进制转字符-转10进制

## binwalk -- kali 分析文件工具 分析头和尾
binwalk -e new.jpg

-e = extract 直接解压文件

\u1111\u2222 --- 要在Chrome里直接 F12 console加引号输出 , "\u1111\u2222" 回车。

## foremost -- kali 分析文件工具,并拆分隐藏的文件
[windows上使用foremost](https://www.cnblogs.com/cnnnnnn/p/8994362.html)

foremost new.jpg 

## Stegsolve --- LSB隐写
java -jar Stegsolve.jar

Analasys - Data Extract

最低位先用0 (alpha没变形没选)

√LSB First

测试每个 Bit Plane Order

      找到明显标志后保存 - Save Test 

SaveBin

## zsteg
zsteg -h

    # 查看LSB信息
    zsteg pcat.png

    检测zlib
    # -b的位数是从1开始的
    zsteg zlib.bmp -b 1 -o xy -v

## file 命令分析文件 或者winhex

## volatility 内存取证

`volatility -f <文件名> --profile=<配置文件> <插件> [插件参数]`

获取–profile的参数 

    volatility -f mem.vmem imageinfo

    volatility -f mem.vmem –profile=WinXPSP2x86 volshell

shell的命令： 

    dt("内核关键数据结构名称")
    如
    dt("_PEB")

剪贴板
    
    volatility -f memeories.vmem --profile=Win10x86_14393 clipboard

列举进程：

    volatility -f mem.vmem –profile=WinXPSP2x86 pslist

列举缓存在内存的注册表 ：

    volatility -f mem.vmem –profile=WinXPSP2x86 hivelist

hivedump 打印出注册表中的数据 ：

    volatility -f mem.vmem –profile=WinXPSP2x86 hivedump -o 注册表的 virtual 地址

获取SAM表中的用户 ：

    volatility -f mem.vmem –profile=WinXPSP2x86 printkey -K "SAM\Domains\Account\Users\Names" 

可以看到有4个用户

获取最后登录系统的账户 ：

    volatility -f mem.vmem –profile=WinXPSP2x86 printkey -K "SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" 

提取出内存中记录的 当时正在运行的程序有哪些，运行过多少次，最后一次运行的时间等信息 

    volatility -f mem.vmem –profile=WinXPSP2x86 userassist

将内存中的某个进程数据以 dmp 的格式保存出来 。

    volatility -f mem.vmem –profile=WinXPSP2x86 -p [PID] -D [dump 出的文件保存的目录]

二进制编辑器 hexeditor 将以上保存的 dmp 文件打开，并进行调查取证的工作 。

    hexeditor 1736.dmp

二进制.png

你还可以使用 strings 这个工具将它的字符串打印出来。 

    例：

    strings 1736.dmp > 1736.txt 

    strings 1608.dmp > 1736.txt | grep shellcode 

提取内存中保留的 cmd 命令使用情况 。

    volatility -f mem.vmem –profile=WinXPSP2x86 cmdscan

获取到当时的网络连接情况 。

    volatility -f mem.vmem –profile=WinXPSP2x86 netscan

获取 IE 浏览器的使用情况。 

    volatility -f mem.vmem –profile=WinXPSP2x86 iehistory 

获取内存中的系统密码，我们可以使用 hashdump 将它提取出来 。

    volatility -f mem.vmem –profile=WinXPSP2x86 hashdump -y （注册表 system 的 virtual 地址 ）-s （SAM 的 virtual 地址）

    volatility -f mem.vmem –profile=WinXPSP2x86 hashdump -y 0xe1035b60 -s 0xe16aab60 

最大程度上将内存中的信息提取出来，那么你可以使用 timeliner 这个插件。它会从多个位置来收集系统的活动信息 

    volatility -f mem.vmem –profile=WinXPSP2x86 timeliner


打开kali，使用volatility 查看进程，可以发现一个TrueCrypy.exe的进程。 

    volatility -f mem.vmem –profile=WinXPSP2x86 pslist

TrueCrypy.exe是一款加密程序，而我们可以推出，suspicion为加密的结果。 

我们需要从内存dump出key来。 

    volatility -f mem.vmem –profile=WinXPSP2x86 memdump -p 1464 -D ctf/ 

dump出来的文件为1464.dmp 。

Elcomsoft Forensic Disk Decryptor 的使用。

我们需要借助Elcomsoft Forensic Disk Decryptor（Elcomsoft硬盘取证解密器，简称为EFDD）软件来获取key和破解文件  。
### bitlocker解密, vmem, vhdx
参见  网鼎杯2020白虎组 密码柜

windows挂载后, 有密钥情况下。

manage-bde -unlock G: -RecoveryPassword 294173-189123-573023-455081-459382-434610-344091-286275

### xor解密

xortool -c 20 filename 得到密钥。 脚本跑

```python
#coding:utf-8

f = open('xor','rb')
w = open('xor1.png','ab')
line = f.read()
key = 'WDCTF'
# print hex(ord(line[1]))
for i in range(len(line)):
    w.write(chr(ord(line[i])^ord(key[i % 5])))
```
### sage 计算，解方程等

```
# 三元一次方程
var('x y z')
solve([3*x-y+z==185,
2*x+3*y-z==321,
x+y+z==173], [x, y, z])

# 一元二次方程
var('x')
f(x)=x*x+x-7943722218936282
f.roots()
```


## Detect it Easy
DIE看时发现资源区段显示加壳，很可疑。

![注意，前面](./imgs/Software_die02.jpg)

查看资源，发现Flag关键字。

![注意，前面](./imgs/Software_die01.jpg)

在图1处右击查看16进制，发现PNG头。转储出来。恢复PNG头。显示出flag

也可以用ResHackerFX或 [ResourcesExtract1.18](https://www.xiazaiba.com/html/2983.html) 提取
## 文件头
字符串 PK是 zip的开头,  5d480506xxxxx 为尾部


## 题库list

1. 简单流量分析

[工业信息安全技能大赛(ICSC)2019](https://www.secshi.com/19184.html)

不久前，运维人员在日常安全检查的时候发现现场某设备会不时向某不知名ip发出非正常的ICMP PING包。这引起了运维人员的注意，他在过滤出ICMP包分析并马上开始做应急处理很可能已被攻击的设备。运维人员到底发现了什么?flag形式为 flag{}

