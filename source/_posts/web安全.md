---
title: web安全
date: 2017-01-27 16:19:06
tags: 安全
---
### hidden
虽然 hidden 参数的值能被用户自己改写，但在面对信息泄漏以及被第三方篡改等危险时，hidden 参数却坚不可摧。与 hidden 参数形成鲜明对比的是后面将要介绍的 Cookie 和会话（Session）变量。Cookie 和会话变量的缺点是容易招致会话固定攻击。尤其是在尚未登录、并且又使用了地域型域名的情况下，受到 Cookie Monster Bug 的影响，根本就没有有效的办法来防止会话变量的泄漏。
* * *
### Basic
Basic 认证成功一次以后，再发送请求时，浏览器就会自动附带 Authorization 消息头。因此，认证对话框只在最初的时候显示一次，看上去认证状态似乎被记住了，但实际上每次请求时都会发送 ID 和密码，认证状态并没有被保存在任何地方。换言之，Basic 认证也是无状态的。而正是因为 Basic 认证的无状态性，所以也就不存在注销（Logout）的概念。
<!--more-->
* * *
### Cookie
Cookie 能让浏览器记忆少量数据，但保存应用程序的数据时几乎不会使用 Cookie，理由如下。
- Cookie 能保存的值的数量和字符串长度有限
- Cookie 的值能被用户自己看到或更改，所以不适用于存储敏感信息

可以在 Cookie 中保存会话 ID，实际对应的值则保存在服务器端的方法。这种方法被称为“使用 Cookie 的会话管理”


会话 ID 一旦遭到泄漏就有可能发生伪装事件，所以必须采取防范措施。会话 ID 泄漏主要有以下几种原因。
- 发行 Cookie 时的属性指定有问题
- 会话 ID 在网络上被监听
- 通过跨站脚本漏洞等应用中的安全隐患被泄漏
- 由于 PHP 或浏览器等平台存在安全隐患而被泄漏
- 会话 ID 保存在 URL 中的情况下，会通过 Referer 消息头泄漏

使用Cookie需要注意
1. 使用开发工具提供的会话管理机制   
2. 认证后改变会话ID    
3. 原则上不设置Cookie的Domain属性    
4. 给Cookie加上HttpOnly属性后可以加大攻击难度
* * *
### 跨域
能够进行跨域访问的浏览器功能。
1. frame元素与iframe元素
2. img
3. script
4. css
5. form元素的action属性    
恶意利用 form 元素的特性来实施攻击的方式被称为跨站请求伪造（CSRF）。CSRF 攻击是让用户在不知情的情况下提交 form，从而肆意使用应用中的功能。
