---
layout: default
title: Bootstrap中使用模式对话框
category: 开发
comments: true
---

# Bootstrap中使用模式对话框

有时候我们在使用Bootstrp时需要弹出一个对话框，这个功能已经集成在了Bootstrap的Javascript插件中了。简单说就是定义一个特定格式的div，然后通过链接或JQuery代码触发模式对话框弹出，点击关闭或除模式对话框以外的区域就可以关闭对话框。

但今天我有一个小需求，就是使用键盘或点击对话框以外的区域时不能关闭对话框，根据这个需要查阅了一下相关的API说明，发现只有是否响应Esc键的参数，并没有其它可以实现该功能的设置项，但是看到事件中有一个`hide.bs.modal`事件，用来在`hide`方法调用之后立即触发该事件，好像可以有突破点，试验一下吧。

```
$('#myModal').on('hidd.bs.modal', function (e) {
    return false;
});
```

直接返回false，阻止事件的进一步传播，然后运行一下，使用JQuery调用模式对话框：

```
$('#MyButton').click(function(){
    $('#MyDlg').model({keyboard: false}); //屏蔽键盘事件
});
```

运行之后，按ESC键或点击对话框以外的区域均不能关闭对话框，目标达成。