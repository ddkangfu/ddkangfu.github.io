---
layout: default
title: OS X Yosemite升级后iTerm启动报错
category: 工具
comments: true
---

# OS X Yosemite升级后iTerm启动报错

上午手贱，将Mackbook升级到了Yosemite 10.10.1，结果启动iTerm后，直接就报如下错误：

```
dyld: Library not loaded: /usr/local/lib/libgdbm.1.dylib
    Referenced from: /usr/local/bin/zsh
    Reason: image not found
```

虽然不影响使用，但引起强迫症病发，遂开始修复这个问题。一通乱搜后，大神们都建议link一下，我就执行了下brew link pcre，又是报错：

```
➜  ~  brew link pcre
Linking /usr/local/Cellar/pcre/8.36...
Error: Could not symlink lib/pkgconfig/libpcre.pc
/usr/local/lib/pkgconfig is not writable.
```

好吧，没有权限，那把Owner改成当前用户总是没问题的吧!

```
➜  ~  chown -R wuxiaoning:admin /usr/local/lib/pkgconfig
```

重新link：

```
➜  ~  brew link pcre
Linking /usr/local/Cellar/pcre/8.36... 133 symlinks created
```

不放心又reinstall了一下：

```
➜  ~  brew reinstall pcre
==> Reinstalling pcre
==> Downloading https://downloads.sf.net/project/machomebrew/Bottles/pcre-8.36
yosemite.bottle.tar.gz
Already downloaded: /Library/Caches/Homebrew/pcre-8.36.yosemite.bottle.tar.gz
==> Pouring pcre-8.36.yosemite.bottle.tar.gz
🍺  /usr/local/Cellar/pcre/8.36: 146 files, 5.9M
```

重新打开了一下iTerm，终于不再报那个错了。强迫症的威力真是无穷啊！！！

