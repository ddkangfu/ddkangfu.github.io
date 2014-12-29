---
layout: default
title: Mac上安装PIL
category: 开发
comments: true
---

# Mac上安装PIL

由于项目需要生成缩略图，所以要安装PIL。以前听人说过安装PIL会遇到各种错误，只好降低心理预期，一步步的安装吧。

首先下载需要的库文件：

* libjpeg[http://www.ijg.org](http://www.ijg.org)
* zlib[http://zlib.net](http://zlib.net)
* PIL[http://www.pythonware.com/products/pil/#pil117](http://www.pythonware.com/products/pil/#pil117)

安装libjpeg(默认安装在了/usr/local/lib下):

```
tar zxvf jpegsrc.v9a.tar.gz
cd jpeg-7
./configure --enable-shared --enable-static 
make
sudo make install
```

安装zlib(默认安装在了/usr/local/lib下):

```
tar zxvf zlib-1.2.8.tar.gz
./configure
make
sudo make install
```

安装PIL：

```
tar zxvf Imaging-1.1.7
```

修改setup.py文件：

```
JPEG_ROOT = "/usr/local/include"
ZLIB_ROOT = "/usr/local/include"
```

编译PIL：

```
python setup.py build_ext -i
```

测试一下：

```
python selftest.py
```

看到如下信息，说明安装成功了！

```
--------------------------------------------------------------------
PIL 1.1.7 TEST SUMMARY
--------------------------------------------------------------------
Python modules loaded from ./PIL
Binary modules loaded from ./PIL
--------------------------------------------------------------------
--- PIL CORE support ok
--- TKINTER support ok
--- JPEG support ok
--- ZLIB (PNG/ZIP) support ok
--- FREETYPE2 support ok
*** LITTLECMS support not installed
--------------------------------------------------------------------
Running selftest:
--- 57 tests passed.
```

如果安装过程中出现“fatal error: 'freetype/fterrors.h' file not found”的错误，可使用如下命令进行修复：

```
ln -s /usr/local/include/freetype2 /usr/local/include/freetype
```
