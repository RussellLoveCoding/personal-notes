# 了不起的自托管服务实践

[toc]

## 目标

1. homelab
2. 开放性
3. 发挥数字价值
   1. 打破数字壁垒
   2. 提高生产力
   3. 家庭存储

## 从硬件开始搭建服务器

互联网大厂在解决了自身所遇到的大规模的运维、并发等服务难题后，将计算资源抽象出来，提供 PAAS等云服务平台，使得计算、存储等资源自来水一样可供弹性突发使用，即使对于个人而言也十分友好，就是太贵了。

由于二手市场的存在和开源社区的繁荣，自己搭建也绝非难事。

- [postmarketOS](https://postmarketos.org/)
- [闲置手机搭建linux服务器](./awesome-self-hosted-services/闲置设备改造服务器.md)
- [围绕二手洋垃圾 AMD  epyc 7d12, 7542,  Intel E5 2690 v4 CPU 搭建 云电脑服务器、通用型服务器](./awesome-self-hosted-services/DIY-servers.md)

## HomeLab 方案

参见 [DIY-Server.smd](./awesome-self-hosted-services/DIY-servers.md)

## 云电脑

参见 [DIY-Server.smd](./awesome-self-hosted-services/DIY-servers.md)


## 存储方案

需求：

1. 低调地提供稳定安全的多端数据同步与备份服务；
2. 生态与整合：低调无缝地融合到 linux, android, windows, ios 为本地盘，仿佛忘记了这是个网盘
3. 低调无缝地融合到，如协作办公(WPS/MS office/自己搭建一个 onlyoffice)、笔记同步(typora/onenotes) 、媒体(影音娱乐)、书籍阅读
4. 在可用性方面可能有待提升。
5. 对于具有 linux 和 编程经验的人搭建并不是难事，开箱即用当然最好，但如果不是，也不需要花费太多时间精力去维护。
6. 能够分享、协作
7. 能够增量备份

网上较多人推荐的可选解决方案如下

群晖自带应(Drive, Active Backup For Business, High Availability 套件)；Nextcloud：(NEXTCLOUD+photoprism)；Seafile：(Seafile + SeaDrive2.0(客户端 2.0 支持 cfapi))；ownCloud；FileRun；Syncthing； Cloudreve；Syncthing ，支持 P2P 传输。

下面是这几种方案的横向对比：

| 功能/解决方案      | 群晖自带应用                                                 | Nextcloud                                            | Seafile                                          | ownCloud                                             | FileRun                                            | Syncthing                                               | Cloudreve                                          |
| ------------------ | ------------------------------------------------------------ | ---------------------------------------------------- | ------------------------------------------------ | ---------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------- | -------------------------------------------------- |
| 复杂程度           | 简单，开箱即用                                               | 中等，需要自己搭建                                   | 中等，需要自己搭建                               | 中等，需要自己搭建                                   | 中等，需要自己搭建                                 | 中等，需要自己搭建                                      | 中等，需要自己搭建                                 |
| 多端同步           | 支持，有手机 app 和 PC 客户端                                | 支持，有手机 app 和 PC 客户端                        | 支持，有手机 app 和 PC 客户端                    | 支持，有手机 app 和 PC 客户端                        | 支持，有手机 app 和 PC 客户端                      | 支持，有手机 app 和 PC 客户端                           | 支持，有手机 app 和 PC 客户端                      |
| 增量备份和历史版本 | 支持                                                         | 支持                                                 | 支持                                             | 支持                                                 | 支持                                               | 支持                                                    | 支持                                               |
| 分享和下载直链     | 支持                                                         | 支持                                                 | 支持                                             | 支持                                                 | 支持                                               | 不支持                                                  | 支持                                               |
| 异地高可用部署     | 支持                                                         | 支持                                                 | 支持                                             | 支持                                                 | 支持                                               | 支持                                                    | 支持                                               |
| 生态               | 与群晖的其他产品有很好的整合性                               | 有很多插件可以扩展功能，与其他应用的整合性较好       | 有一些插件可以扩展功能，与其他应用的整合性较好   | 有一些插件可以扩展功能，与其他应用的整合性较好       | 与其他应用的整合性较好                             | 与其他应用的整合性较好                                  | 与其他应用的整合性较好                             |
| 优缺点             | 优点是使用方便，功能齐全，缺点是可能不如其他开源解决方案灵活 | 优点是功能强大，可扩展性好，缺点是需要自己搭建和维护 | 优点是功能强大，性能好，缺点是需要自己搭建和维护 | 优点是功能强大，可扩展性好，缺点是需要自己搭建和维护 | 优点是界面友好，功能强大，缺点是需要自己搭建和维护 | 优点是支持 P2P 传输，性能好，缺点是不支持分享和下载直链 | 优点是界面友好，功能强大，缺点是需要自己搭建和维护 |

## 家庭媒体中心

video/audio/photo

目标：私有 youtube + 手机相册服务 + 音乐服务器

参考：youtube 很好：很好地融合 UGC 和公开发行视频

记住你是需要一个豆瓣，需要一个索引 + 一键播放的功能。

需求：

1. 照片服务器，能够云同步个人相册；同时本地应用能够分类；家庭共享与画廊。
2. 类似 YouTube 融合个人私人视频和公开发行视频；融合到豆瓣里，即在豆瓣里新增一个一键播放；具有流媒体服务器，能够转码，或者原画播放视频；海报墙；能够 DLNA 投屏。对于怎么生成个人历史啊等等，建议使用豆瓣 APP.
3. 音乐服务器：目前最简单的方案是使用 unblockneteasemusic, 手机端是想办法将  webdav 映射到本地，再用 网易云音乐扫。
4. 多端需求

解决方案：

视频: emby, jellyfin, plex

音乐: 

下面是这几种方案的横向对比：



案例：

1. 115 + 阿里云 + alist + 索引服务器

夸克网盘+阿里网盘+115 网盘+alist+emby。

Emby, Jellyfin和Plex都是流行的媒体服务器软件，它们都可以在家庭媒体中心上部署，用于管理和播放个人的音乐、电影、电视节目等媒体文件。以下是它们的一些主要特点和区别：

1. Emby：
   - Emby是一个付费软件，虽然有免费版本，但是一些高级功能需要购买Emby Premiere才能使用。
   - Emby支持各种设备，包括Windows、Mac、Linux、Android、iOS等。
   - Emby的用户界面友好，易于使用。
   - Emby支持实时转码，可以根据设备和网络条件自动调整播放质量。

2. Jellyfin：
   - Jellyfin是Emby的一个开源分支，完全免费，包括一些Emby需要付费才能使用的功能。
   - Jellyfin支持的设备较少，主要是Windows、Mac、Linux和Android。
   - Jellyfin的用户界面相对较为简洁，可能需要一些时间来熟悉。
   - Jellyfin也支持实时转码，但是性能可能不如Emby。

3. Plex：
   - Plex也是一个付费软件，有免费版本，但是一些高级功能需要购买Plex Pass才能使用。
   - Plex支持的设备最多，包括Windows、Mac、Linux、Android、iOS、各种智能电视和游戏机等。
   - Plex的用户界面非常友好，易于使用，而且有很多个性化设置。
   - Plex支持实时转码，性能非常好，而且有一些独特的功能，比如能够自动下载电影和电视节目的字幕。

总的来说，如果你希望有一个完全免费的解决方案，那么Jellyfin是最好的选择。如果你希望有更多的功能和更好的用户体验，那么可以考虑购买Emby或者Plex的付费版本。

## 生产力

- [ ] 远程办公，office 套件: ppt, 画图等等
- [ ] 密码服务：[bitwarden](./awesome-self-hosted-services/vaultwarden.md)
- [ ] vscode server: 
- 翻译服务: 集成 deepl/openai, 
- AI 聊天.
- 私人助理Leon: Open-source personal assistant who can live on your server.
- revealjs:Framework for easily creating beautiful presentations using HTML.
- Habitica:Habit tracker app which treats your goals like a Role Playing Game. Previously called HabitRPG.
- Firefly III:Firefly III is a modern financial manager. It helps you to keep track of your money and make budget forecasts. It supports credit cards, has an advanced rule engine and can import data from many banks.

## 云游戏

参见 [DIY-Server.smd](./awesome-self-hosted-services/DIY-servers.md)

##  [网络](./awesome-self-hosted-services/networking.md)

- Proxy:Nginx Proxy Manager: Nginx Proxy Manager is an easy way to accomplish reverse proxying hosts with SSL termination.
- frp
- xray
- VPN

自动化:

- Apache Airflow: Airflow is a platform to programmatically author, schedule, and monitor workflows. Website  Source Code
- Eonza
- LazyLibrarian: https://gitlab.com/LazyLibrarian/LazyLibrarian
- Lidarr: Lidarr is a music collection manager for Usenet and BitTorrent users.
- Medusa: Automatic Video Library Manager for TV Shows. It watches for new episodes of your favorite shows, and when they are posted it does its magic.
- MetaTube: A Web GUI to automatically download music from YouTube add metadata from Spotify, Deezer or Musicbrainz.
- MeTube: Web GUI for youtube-dl, with playlist support. Allows downloading videos from dozens of websites.
- Radarr:Radarr is an independent fork of Sonarr reworked for automatically downloading movies via Usenet and BitTorrent, à la Couchpotato.
- Sonarr:Automatic TV Shows downloader and manager for Usenet and BitTorrent. It can grab, sort and rename new episodes and automatically upgrade the quality of files already downloaded when a better quality format becomes available.

通讯

- 邮件转发：AnonAddy: Open source email forwarding service for creating aliases.
- DebOps: Your Debian-based data center in a box. A set of general-purpose Ansible roles that can be used to manage Debian or Ubuntu hosts.
- DebOps
  Your Debian-based data center in a box. A set of general-purpose Ansible roles that can be used to manage Debian or Ubuntu hosts.
- docker-mailserver: Production-ready fullstack but simple mail server (SMTP, IMAP, LDAP, Antispam, Antivirus, etc.) running inside a container. Only configuration files, no SQL database.
- Mail-in-a-Box: Turns any Ubuntu server into a fully functional mail server with one command. Website  Source Code
- Postal: A complete and fully featured mail server for use by websites & web servers.

数据库管理

- NocoDB： No-code platform that turns any database into a smart spreadsheet (alternative to Airtable or Smartsheet).
- MindsDB：MindsDB is an open source self hosted AI layer for existing databases that allows you to effortlessly develop, train and deploy state-of-the-art machine learning models using standard queries.
- Directus：An Instant App & API for your SQL Database. Directus wraps your new or existing SQL database with a realtime GraphQL+REST API for developers, and an intuitive admin app for non-technical users.

DNS

- AdGuard Home: Free and open source, userfriendly ads & trackers blocking DNS server.
- Pi-hole: A blackhole for Internet advertisements with a GUI for management and monitoring.


电子书
- Calibre Web: Web app providing a clean interface for browsing, reading and downloading eBooks using an existing Calibre database.
- Kavita:Cross-platform e-book/manga/comic/pdf server and web reader with user management, ratings and reviews, and metatdata support.

信息流聚合

- FreshRSS:Self-hostable RSS feed aggregator.

文件传输和同步：

- Nextcloud Access and share your files, calendars, contacts, mail and more from any device, on your terms.
- ownCloud： All-in-one solution for saving, synchronizing, viewing, editing and sharing files, calendars, address books and more.

文件下载

- qBittorrent：Free cross-platform bittorrent client with a feature rich Web UI for remote access.

- Transmission：Fast, easy, free Bittorrent client.

物联网-智能家居：

- Domoticz：Home Automation System that lets you monitor and configure various devices like: Lights, Switches, various sensors/meters like Temperature, Rain, Wind, UV, Electra, Gas, Water and much more.

- EMQX：An ultra-scalable open-source MQTT broker. Connect 100M+ IoT devices in one single cluster, move and process real-time IoT data with 1M msg/s throughput at 1ms latency.
- Home Assistant：Open-source home automation platform.
- Home Assistant：Open-source home automation platform.

流媒体

- AzuraCast：A modern and accessible self-hosted web radio management suite.

- Kodi：Multimedia/Entertainment center, formerly known as XBMC. Runs on Android, BSD, Linux, macOS, iOS and Windows.

Jellyfin

- Media server for audio, video, books, comics, and photos with a sleek interface and robust transcoding capabilities. Almost all modern platforms have clients, including Roku, Android TV, iOS, and Kodi.

书签：

- Buku:A powerful bookmark manager and a personal textual mini-web.

画廊

- Self-hosted photo and video backup solution directly from your mobile phone.

相片备份方案：

- Nextcloud Memories:Fast, modern and advanced photo management suite. Runs as a Nextcloud app.

- Immich:Self-hosted photo and video backup solution directly from your mobile phone.

- PhotoPrism:Personal photo management powered by Go and Google TensorFlow. Browse, organize, and share your personal photo collection, using the latest technologies to automatically tag and find pictures.


喜闻乐见的食谱/吃货 部分：

- Mealie：Material design inspired recipe manager with category and tag management, shopping-lists, meal-planner, and site customizations. Mealie is focused on simple user interactions to keep the whole family using the app.

远程访问：

- Firezone：Self-hosted secure remote access gateway that supports the WireGuard protocol. It offers a Web GUI, 1-line install script, multi-factor auth (MFA), and SSO.

搜索引擎

- Jina：Cloud-native neural search framework for any kind of data.

自托管解决方案：

- CasaOS：A simple, easy-to-use, elegant open-source Home Cloud system.
