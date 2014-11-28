---
layout: default
title: 微信JsAPI支付调试总结
category: 开发
comments: true
---

# 微信JsAPI支付调试总结

第一次调试微信JsAPI接口，碰到各种天坑，总结一下给后来的同学避避雷。

#### 1. 第一步是使用OAuth2的授权接口获得用户的OpenID，在这步时:

* 需要先判断请求页面链接是否有code参数，如果没有该参数，需要重定向到https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx8888888888888888&redirect_uri=http://www.fangbei.org/wxpay/js_api_call.php&response_type=code&scope=snsapi_base&state=STATE#wechat_redirect