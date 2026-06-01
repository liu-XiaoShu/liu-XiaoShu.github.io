---
title: "[python专题笔记]学习/工作遇到的坑记录－pyftp"
date: 2021-11-25
categories: [Python]
tags:
  - python专题笔记
description: pyftp 在远程服务器上不能创建层级目录 问题: 在远程服务器上只能创建当前子目录，不能创建层级目录 解决方法: 分步创建，创建一个进入一个，再进行后面的创建，最后退回到最开始路径 def Mkdir(self, RemoteDir
---

## pyftp 在远程服务器上不能创建层级目录

  * 问题: 在远程服务器上只能创建当前子目录，不能创建层级目录
  * 解决方法: 分步创建，创建一个进入一个，再进行后面的创建，最后退回到最开始路径


    
    
        def Mkdir(self, RemoteDir):
            """
                功能:   在FTP远程创建目录
                        可以创建层级目录
                参数:   RemoteDir: 远程需要创建
                        的目录
                返回值: Bool True/False
            """
            start_path = self.ftp.pwd()
            try:
                now_path = ""
                for dir_index in range(len(RemoteDir.split("/"))):
                    new_dir_name = RemoteDir.split("/")[dir_index]
                    if new_dir_name not in self.ftp.nlst(self.ftp.pwd()):
                        self.ftp.mkd(new_dir_name)
                    now_path = os.path.join(now_path, new_dir_name)
                    self.ftp.cwd(new_dir_name)
                self.ftp.cwd(start_path)
                return True
            except:
                self.ftp.cwd(start_path)
                print ('\033[0;41;37m[FTP 服务模块 ] 创建FTP远程目录 FAILED 路径: %s\033[0m'%RemoteDir)
                return False

---

> 本文由 [CSDN 博客](https://blog.csdn.net/weixin_41596275/article/details/121532460) 迁移至本站。
