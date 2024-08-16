---
title: 使用 python 原生库可视化 MNIST 数据集
date: 2020-12-08T00:44:10+08:00
categories: 
- Python
tags:
- Python
- MNIST
- PNG
---

## 1 简介

拿到MNIST数据集后，首先想以图片的形式，直观浏览一下这个数据集。可惜这个数据集诞生的比较早，为了节约存储开销，MNIST使用一种约定的编码方式，将60000张图片存储在一个文件里面。本文介绍如何使用python原生库，生成每个图片的对应png格式图片。

<!-- more -->

## 2 MNIST数据库文件格式

数据集有四个文件

* train-images-idx3-ubyte: 训练集图片 (60000个样本)
* train-labels-idx1-ubyte: 训练集标签
* t10k-images-idx3-ubyte: 测试集图片 (10000个样本)
* t10k-labels-idx1-ubyte: 测试集标签

测试集中，前5000个样本比后5000个样本更加纯净。
文件均以MSB优先存储。

具体文件格式如下

```txt
TRAINING SET LABEL FILE (train-labels-idx1-ubyte):
[offset] [type]          [value]          [description]
0000     32 bit integer  0x00000801(2049) magic number (MSB first)
0004     32 bit integer  60000            number of items
0008     unsigned byte   ??               label
0009     unsigned byte   ??               label
........
xxxx     unsigned byte   ??               label
The labels values are 0 to 9.

TRAINING SET IMAGE FILE (train-images-idx3-ubyte):
[offset] [type]          [value]          [description]
0000     32 bit integer  0x00000803(2051) magic number
0004     32 bit integer  60000            number of images
0008     32 bit integer  28               number of rows
0012     32 bit integer  28               number of columns
0016     unsigned byte   ??               pixel
0017     unsigned byte   ??               pixel
........
xxxx     unsigned byte   ??               pixel
Pixels are organized row-wise. Pixel values are 0 to 255. 0 means background (white), 255 means foreground (black).

TEST SET LABEL FILE (t10k-labels-idx1-ubyte):
[offset] [type]          [value]          [description]
0000     32 bit integer  0x00000801(2049) magic number (MSB first)
0004     32 bit integer  10000            number of items
0008     unsigned byte   ??               label
0009     unsigned byte   ??               label
........
xxxx     unsigned byte   ??               label
The labels values are 0 to 9.

TEST SET IMAGE FILE (t10k-images-idx3-ubyte):
[offset] [type]          [value]          [description]
0000     32 bit integer  0x00000803(2051) magic number
0004     32 bit integer  10000            number of images
0008     32 bit integer  28               number of rows
0012     32 bit integer  28               number of columns
0016     unsigned byte   ??               pixel
0017     unsigned byte   ??               pixel
........
xxxx     unsigned byte   ??               pixel
Pixels are organized row-wise. Pixel values are 0 to 255. 0 means background (white), 255 means foreground (black).
```

## 3 png文件结构

这里不详细展开说，只说为了存储8位灰度图的文件结构。详细可以参考这篇介绍png文件结构很好的[文章](https://rimson.top/2019/08/23/png-format/)。

### 3.1 文件署名 File Signature

开头8字节是表示该文件是png格式的文件署名。

```txt
89 50 4E 47 0D 0A 1A 0A
```

### 3.2 png数据块 Chunk

文件署名后面紧跟着的都是格式相同的数据块，数据块由四个部分组成。

|名称|长度|说明|
|-|-|-|
|Length|4 bytes|指定 Chunk Data 的长度|
|Chunk Type|4 bytes|指定数据块类型, 如 b”IHDR”|
|Chunk Data|可变|按照 Chunk Type 存放指定数据|
|CRC|4 bytes|检查是否有错误的循环冗余代码|

本文只用到了三种数据块。

* IHDR: 文件头数据块
* IDAT: 图像数据块
* IEND: 图像结束数据块

#### 3.2.1 文件头数据块 IHDR

这个数据块被称为文件头数据块，必须作为第一个数据块，并用13个字节按照顺序来表示图像数据的基本信息：

|域|长度|说明|
|-|-|-|
|Width|4 bytes|图片宽度，以像素为单位|
|Height|4 bytes|图片高度，以像素为单位|
|Bit Depth|1 byte|图像深度|
|Color Type|1 byte|颜色类型|
|Compression Method|1 byte|压缩算法|
|Filter Method|1 byte|滤波方法|
|Interlace Method|1 byte|扫描方法|

其中图像深度设置为8，代表用8bits表示一个像素；颜色类型设置为0，代表使用灰度模式；压缩算法、滤波方法、扫描方法都设置为0。关于压缩算法等可以参考[PNG规范](https://www.w3.org/TR/2003/REC-PNG-20031110/#10Compression)。

#### 3.2.2 图像数据块 IDAT

存储实际的图片数据，一个图片数据流可能包含多个连续顺序的 IDAT 数据块。对n行m列图像A创建 IDAT 数据块的步骤：

1. 将图片像素值按行保存 A.size=n*m
2. 采取 IHDR 指定的过滤方法，对对每一行进行过滤。因为之前的过滤方式是0，也就是不过滤，但还是要把过滤方式(一个字节0)保存在每一行的最开头 B.size=n*(1+m)
3. 采取 IHDR 指定的压缩方法，对过滤的数据进行压缩，根据规范0号png压缩算法对应的8号zlib压缩算法。IDAT=zlib.compress(B)

可以看到 IHDR 非常重要，存储了很多关键信息，包括输出的数据和压缩算法。如果想要读取图片信息，将上面三个步骤逆序执行即可。

#### 3.3.3 图像结束数据块 IEND

表示 PNG 文件结束，必须放在最后面，且 Chunk Data 为空。

## 4 代码

```python
import os
import struct
import zlib

def process_bar(percent, start_str='', end_str='100%', total_length=25):
    bar = '#'*int(percent*total_length)
    bar = '\r' + start_str + bar.ljust(total_length) + ' {:0>4.1f}%|'.format(percent*100) + end_str
    print(bar, end=('' if (percent <= 0.999999) else '\n'), flush=True)

def gen_png_head(width, height):
    signature = b'\x89\x50\x4e\x47\x0d\x0a\x1a\x0a'

    fmt_IHDR = ">IIBBBBB"
    length = struct.calcsize(fmt_IHDR)
    chunk_type = b'IHDR'
    IHDR = struct.pack(fmt_IHDR, width, height, 8, 0, 0, 0, 0)
    chunk_type_data = chunk_type + IHDR
    crc = zlib.crc32(chunk_type_data)
    return signature + struct.pack('>I', length) + chunk_type_data + struct.pack('>I', crc)

def gen_png_body(data: bytes, width):
    new_data = bytes()
    for row in range( len(data)//width ):
        new_data += b'0'
        new_data += data[row*width:(row+1)*width]
    data = zlib.compress(new_data, 8)
    chunk_type = b'IDAT'
    chunk_type_data = chunk_type + data
    crc = zlib.crc32(chunk_type_data)
    return struct.pack('>I', len(data)) + chunk_type_data + struct.pack('>I', crc)

def gen_png_end():
    chunk_type = b'IEND'
    crc = zlib.crc32(chunk_type)
    return struct.pack('>I', 0) + chunk_type + struct.pack('>I', crc)

def ubyte2png(idx3_filename, idx1_filename, png_dir):
    if not os.path.exists(png_dir):
        os.makedirs(png_dir)
    with open(idx3_filename, 'rb') as image_f, open(idx1_filename, 'rb') as label_f:
        image_data = image_f.read()
        image_fmt_header = ">IIII"
        image_offset = struct.calcsize(image_fmt_header)
        magic_number, num_images, height, width = struct.unpack(image_fmt_header, image_data[:image_offset])
        print(f"{png_dir}>> 魔数: {magic_number}, 图片数量: {num_images}, 图片大小：{height}x{width}")
        image_size = height * width

        label_data = label_f.read()
        label_fmt_header = ">II"
        label_offset = struct.calcsize(label_fmt_header)
        magic_number, num_labels = struct.unpack(label_fmt_header, label_data[:label_offset])
        if num_labels != num_images:
            raise Exception(
                f"number of labels({num_labels}) are not equal to number of images({num_images})"
            )

        png_head = gen_png_head(width, height)
        png_end = gen_png_end()

        for i in range(num_images):

            png_body = gen_png_body(
                image_data[ image_offset+image_size*i : image_offset+image_size*(i+1) ],
                width
            )

            label = struct.unpack(">B", label_data[ label_offset+i : label_offset+i+1 ])[0]

            with open(f"{png_dir}/No.{i+1} - {label}.png", 'wb') as f:
                f.write(png_head)
                f.write(png_body)
                f.write(png_end)

            process_bar((i+1)/num_images)

if __name__ == "__main__":
    ubyte2png("train-images.idx3-ubyte", "train-labels.idx1-ubyte", "train")
    ubyte2png("t10k-images.idx3-ubyte", "t10k-labels.idx1-ubyte", "test")
```
