---
title: "Ubuntu16.04 复制中文会乱码怎么办？"
date: 2023-11-29
categories: [Linux]
tags:
  - Ubuntu
description: 问题原因： zsh 的 magic 插件有 bug 解决方法： 在 ~/.zshenv 添加 export DISABLE MAGIC FUNCTIONS=true
---

**问题原因：** zsh 的 magic 插件有 bug  
**解决方法：** 在 ~/.zshenv 添加 export DISABLE_MAGIC_FUNCTIONS=true

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/134688213) 迁移至本站。
