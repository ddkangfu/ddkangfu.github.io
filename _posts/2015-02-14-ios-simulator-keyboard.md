---
layout: default
title: Xcode Simulator中UITextField和UITextView不弹出键盘解决办法
category: 开发
comments: true
---

# Xcode Simulator中UITextField和UITextView不弹出键盘解决办法

最近使用XCode写一个app时，发现TextField控件和TextView控件在编辑状态下不能弹出键盘。

经过查找，发现是Simulator设置的问题，只需要在iOS Simulator中的菜单中的Hardware->Keyboard->Connect Hardware Keyboard 前面的勾取消即可。

由于此功能的快捷键为 `⇧⌘K`，有时候不小心按错，就会将Simulator的输入连接到硬件键盘上问题，会给我们造成输入框的键盘不能正常工作的错觉，我们了解了这个问题的原因后，再碰到这样的问题能及时解决不再迷惑就可以了。

