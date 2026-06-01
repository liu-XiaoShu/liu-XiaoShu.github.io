---
title: "Ubuntu16.04 复制中文会乱码怎么办？"
date: 2023-11-29
categories: [Linux]
tags:
  - Ubuntu
  - zsh
description: Ubuntu 16.04 + zsh 复制中文乱码：magic 插件 bug，在 ~/.zshenv 禁用即可。
---

> 本文属于 **Linux / Ubuntu 专题** 系列。

## 📌 简介

在 Ubuntu 16.04 使用 zsh 时，终端里复制中文偶尔会变成乱码。通常是 zsh 的 magic 插件导致，可按下面方式关闭。

---

## 问题原因

zsh 的 **magic** 插件存在 bug，会影响剪贴板/复制行为。

---

## 解决方法

在 `~/.zshenv` 中加入：

```bash
export DISABLE_MAGIC_FUNCTIONS=true
```

保存后重新打开终端，或执行 `source ~/.zshenv` 使配置生效。

---

> 📎 **说明**：本文由 [CSDN「小树笔记」](https://blog.csdn.net/weixin_41596275/article/details/134688213) 迁移至 GitHub Pages，并在本站持续维护。
