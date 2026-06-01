---
title: "[fmpeg 专题笔记]"
date: 2022-05-24
categories: [多媒体]
tags:
  - fmpeg 专题笔记
description: fmpeg 分离音视频 fmpeg -i demo.mp4 -vcodec copy -an video.mp4 分离视频流 ffmpeg -i demo.mp4 -acodec copy -vn audio.mp4 分离音频流
---

### fmpeg 分离音视频
    
    
    fmpeg -i demo.mp4 -vcodec copy -an video.mp4  #分离视频流
     
    ffmpeg -i demo.mp4 -acodec copy -vn audio.mp4  #分离音频流
     
    ffmpeg -i demo.mp4 -an output.mp4     # 去掉视频中的音频
     
    ffmpeg -i demo.avi -vcodec copy -an output.avi #去掉视频中的音频

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/124943191) 迁移至本站。
