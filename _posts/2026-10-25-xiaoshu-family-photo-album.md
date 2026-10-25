---
title: "小树时光相册：照片留在自家局域网"
date: 2026-10-25
categories: [Web应用]
tags:
  - FastAPI
  - Raspberry Pi
  - Docker
  - SQLite
  - 家庭相册
  - 开源
description: 自架局域网家庭相册：文件系统即图库、时光/经典/地图浏览、分享二维码与内容去重；树莓派 Docker 可跑，源码 MIT 开源。
---

# 小树时光相册（XiaoShu Family Photo Album）

**项目状态**： ✅ v3.0.9 可日常使用 | ✅ 树莓派 Docker 已验证 | 📖 文档与安全说明已整理 | 🔓 MIT 开源

### 📋 项目简介

手机云相册方便，但家庭照片一多，就容易遇到几件事：

- 想**数据留在家里**，不太放心整库丢给第三方；
- NAS / 树莓派常开，却缺一个**够用、好部署**的浏览与入库界面；
- 希望**文件本身可迁移**——换盘、备份时，时间、简述、地点跟着文件走，而不是锁死在某一家私有数据库里。

**小树时光相册**按这个思路做：照片和视频按时间整理进本机目录，元数据尽量写进文件/侧车；浏览器里用时光线、经典网格、地图三种方式浏览，并可用二维码把选定范围分享给家人。适合树莓派、NAS 一类**家庭或可信局域网**常开小主机。

完整工程与文档见 GitHub：  
**[XiaoShuFamilyPhotoAlbum](https://github.com/liu-XiaoShu/XiaoShuFamilyPhotoAlbum)**

---

### 🎯 核心特性

* **三种浏览**：时光（按年/月）、经典（按日网格）、地图（按地点聚点）。
* **搜索**：标题 / 地点 / 描述 / 感悟 / 文件名；多词空格分词；可筛照片或视频；结果可「加载更多」。
* **入库**：拖放或选择上传；队列可暂停 / 继续 / 重试；自动解析 EXIF；内容指纹标出相册已有相同画面。
* **苹果 HEIC / 实况**：原件入库；看图可预览伴生视频。
* **规范命名**：`年/月/YYYY-MM-DD-HHMMSS-简述-md5.ext`，便于归档与去重。
* **看图与编辑**：灯箱沉浸浏览；旋转裁剪、视频剪辑封面；可下载原文件。
* **超级用户 + 分享二维码**：按年份或指定媒资授权；可选允许编辑 / 入库；访客可见范围 = 授权范围 ∪ 自己上传。
* **存储与查重**（超管）：磁盘占用与重复组处理；重建索引不删照片。
* **回收站**：软删除可恢复；彻底删除仅超管。
* **地图**：国内高德纠偏；港澳台与境外不二次偏移。

**已知不足**：图片旁白（录音与幻灯片出声）仍不稳定，当前版本不当作可靠功能。

---

### 🔐 权限（摘要）

| 身份 | 能做什么 |
| :--- | :--- |
| 未创建超管 | 只能打开登录页注册；相册本身不可读写 |
| 未登录（已有超管） | 不能看图、下载；请立刻建号，避免同网段抢注 |
| 超级用户 | 全部管理能力（设置、分享、存储、访问数据、彻底删除等） |
| 分享访客 | 仅授权范围 ∪ 自己上传；编辑/入库按创建时勾选；不能进设置，不能彻底删除 |

更细的分享能力对照表见仓库 [doc/安全说明.md](https://github.com/liu-XiaoShu/XiaoShuFamilyPhotoAlbum/blob/main/doc/%E5%AE%89%E5%85%A8%E8%AF%B4%E6%98%8E.md)。

部署后请**立刻创建超级用户**，并且**不要把服务端口映射到公网**——本项目面向可信局域网，不是多租户云相册。

---

### 🧱 技术栈（摘要）

| 层级 | 技术 |
| :--- | :--- |
| 后端 | Python · FastAPI · uvicorn |
| 图像 / 视频 | Pillow · piexif · pillow-heif · ffmpeg |
| 前端 | 原生 HTML / CSS / JS · Leaflet |
| 数据 | 文件系统相册 + SQLite 索引 |
| 部署 | Docker / docker-compose · 国内镜像源友好 |

设计取舍一句话：**文件系统即图库**，SQLite 只做加速浏览的索引；重建索引不会删除或改写原片。

---

### 🚀 本地跑起来（极简）

需要 Python **3.8+**（Docker 镜像为 3.11）；视频相关功能建议装 `ffmpeg`。

```bash
git clone https://github.com/liu-XiaoShu/XiaoShuFamilyPhotoAlbum.git
cd XiaoShuFamilyPhotoAlbum

python3 -m pip install --target .vendor -r requirements.txt
cp config.example.json config.json

python3 run.py          # 默认 HTTPS
# python3 run.py --http # 仅调试
```

| 入口 | 地址 |
| :--- | :--- |
| 本机 | `https://127.0.0.1:8765`（自签证书需点继续） |
| 局域网 | `https://<本机IP>:8765` |
| 登录 | `/login` |
| 健康检查 | `GET /api/health` |

---

### 🏠 树莓派 / Docker（思路）

仓库提供 `docker-compose.yml`、`docker-compose.pi.yml` 与 `scripts/pi/` 一键脚本。常见做法：

```bash
cp -n config.example.json config.json
# 外接盘可参考 config.pi.example.json，路径改成你自己的挂载点
mkdir -p album inbox data/thumbs data/brand data/map_pack data/geo

./scripts/pi/deploy.sh
```

实践里建议：

1. **相册与待入库放同一块盘**，入库可用 rename，避免跨盘整文件拷贝打满 IO。  
2. compose 里的 `/mnt/album-data` 只是示例，部署时改成实际路径。  
3. 配置 `HOST_UID` / `HOST_GID`，方便新文件归属宿主机用户。  
4. 默认 HTTPS；家人设备信任一次自签证书即可。

更完整的启停、重置密码、备份说明见仓库 [doc/部署使用说明.md](https://github.com/liu-XiaoShu/XiaoShuFamilyPhotoAlbum/blob/main/doc/%E9%83%A8%E7%BD%B2%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E.md)。

---

### 📦 日常怎么用（简）

1. 创建超级用户并登录（默认进入**时光**浏览）。  
2. **管理 → 入库**：拖入或选择 → 自动解析 → 勾选后「入库选中项」。失败项会标红保留，刷新列表后仍可见。  
3. **浏览**：时光 / 经典 / 地图；需要时点「搜索」。  
4. **设置**：品牌、主题、地图、分享二维码。  
5. **存储**（超管）：看占用、查重复并处理。  

细节见 [doc/使用说明.md](https://github.com/liu-XiaoShu/XiaoShuFamilyPhotoAlbum/blob/main/doc/%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E.md)。

---

### 📜 许可

本项目采用 **[MIT License](https://github.com/liu-XiaoShu/XiaoShuFamilyPhotoAlbum/blob/main/LICENSE)**。

公开仓库请用干净克隆；**不要**把家庭照片、`config.json`、访客库、证书推进 Git。

---

### 🔗 相关链接

* 源码仓库：[liu-XiaoShu/XiaoShuFamilyPhotoAlbum](https://github.com/liu-XiaoShu/XiaoShuFamilyPhotoAlbum)
* 文档入口：[doc/README.md](https://github.com/liu-XiaoShu/XiaoShuFamilyPhotoAlbum/blob/main/doc/README.md)
* 作者：[xiaoshu](https://github.com/liu-XiaoShu) · `liu_xiaoshu@163.com`

如果这篇对你有用，欢迎 Star；有问题或想法也可以在仓库提 Issue。
