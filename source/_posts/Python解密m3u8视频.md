---
title: Python解密m3u8视频
date: 2018-05-07 00:00:18
tags: Python
---

### 背景说明
>说到了m3u8，就不得不先在此提一下它的一些历史以及用途和格式标准定义。在此，我们借鉴下面这个地址的博文[***点我传送***][1_hls]，如果读者对此感兴趣，请自行了解。如果只是单纯的需要解密m3u8视频并将*.ts合并成一个视频文件(如mp4)，请往下阅读。

### 文件夹结构说明
> 先来看看m3u8文件的庐山真面目，请看图1.1，黄色框起来的部分就对应其中的一个加密的*.ts文件：
> 图1.1![图1.1, 来自某m3u8视频文件](/img/201805/2018-05-07_000536.png)

> 我的目录结构如图1.2所示
> 图1.2![图1.2, 我的目录结构](/img/201805/2018-05-07_002257.png)
> ***normal_list***:  文件夹里面存放我处理过后的m3u8文件：**(这里的处理指的是将上图中黄色高亮框起来的部分的路径替换成正确的路径，URI和ts文件路径 因为m3u8是从手机qq浏览器缓存下来的，所以m3u8文件中的路径最初并不是现在这个路径，替换方法请读者自己解决，博主使用Python写了一个小脚本使用正则表达式批量替换的)**
> ***output***: 文件夹是打算作为解密之后mp4文件的输出路径，即最终视频mp4的是存放这里的。
> ***video***: 文件夹是存放的是下载下来的m3u8真正的视频数据文件，就是有很多*.ts和*.key文件，从图1.1里面的URI字段也可以看出来这一点

### 思路
> 从图1.1中可以看出，这个m3u8采用的加密方式是AES-128, 关于AES加密算法，请读者自己去了解。AES加密算法有很多种模式，m3u8用的方式是[*CBC*][2_cbcwiki](Cipher Block Chaining)模式。因此，可以这样来解密
1. 遍历所有的m3u8文件
2. 解析每个m3u8，获取URI, IV, TS路径，放到一个列表L中
3. 遍历L，加载读取URI中的key, 加载读取IV, 加载读取TS数据，使用AES解密，将结果累加到字节数组res中
4. 将res写入文件就是最终解密成功的视频文件


### 环境
+ `Python 3.6.5`
+ `pycryptodom`: Python的一个加密解密的第三方库
+ 安装：
```
pip install pycryptodome
```

### Python代码实现
```python
from Crypto.Cipher import AES
import re, os

input_path = "D:/Videos/Manu/normal_list" #输入路径，请替换成自己的
output_path = "D:/Videos/Manu/output" #输出路径，请替换成自己的

# 这里是为了划分数据段，因为AES128加密每次操作的数据长度是128位，也就是16个字节
def splite_data(data):
    res = []
    LEN = 16
    idx = 0
    seg = data[idx:LEN]
    while len(seg) == LEN:
        res.append(seg)
        idx += LEN
        seg = data[idx:idx+LEN]
    if LEN > len(seg) > 0:
        res.append(seg.zjust(LEN, b'\0'))
    return res

# 解密单个ts文件
def decrypt_single_ts(key_path, iv_str, ts_path):
    fi = open(key_path, 'rb')
    key = fi.read()
    fi.close()
    fi = open(ts_path, 'rb')
    ts = fi.read()
    fi.close()
    iv = bytes.fromhex(iv_str)
    pad_len = AES.block_size - len(ts) % AES.block_size
    if pad_len != AES.block_size:
        ts = ts[:-pad_len] + bytes([0] * pad_len)
    cipher = AES.new(key, AES.MODE_CBC, iv=iv)
    out_data = cipher.decrypt(ts)
    if pad_len != AES.block_size:
        out_data = out_data[:-pad_len]
    return out_data

# 解密并合并一个m3u8文件
def decrypt_single(m3u8_path):
    fp = open(m3u8_path, 'r')
    lines = fp.readlines()
    fp.close()
    pat_uri = r"URI=\"(.+)\"" #搜索URI的模式
    pat_iv = r"IV=0x(\w+)" #搜索IV的模式
    regex_uri = re.compile(pat_uri)
    regex_iv = re.compile(pat_iv)
    datas = b''
    for idx in range(2, len(lines), 3):
        if lines[idx] is not None and "ENDLIST" not in lines[idx]:
            key = regex_uri.search(lines[idx])[1].strip()
            iv = regex_iv.search(lines[idx])[1].strip()
            ts = lines[idx + 2].strip()
            datas += decrypt_single_ts(key, iv, ts)
    return datas


def decrypt():
    file_list = os.listdir(input_path) # m3u8文件列表
    for fi in file_list:
        full_path = os.path.join(input_path, fi)
        data = decrypt_single(full_path) #解密一个m3u8
        fi_output_path = os.path.join(output_path, os.path.splitext(fi)[0] + ".mp4") #获取输出路径
        fi = open(fi_output_path, 'wb')
        fi.write(data) #写入解密数据
        fi.close()

if __name__ == '__main__':
    decrypt()
```

### 注意事项：
+ 请先替换m3u8文件中的URI和key的路径
+ 请将输入和输出路径替换成自己的
+ ***替换成功后才可以使用上述代码进行解密操作***

[1_hls]: https://blog.csdn.net/cabbage2008/article/details/50522190
[2_cbcwiki]: https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation