Personal Notes

## 一些值得尝试的 self-hosted service

**HomeLab 方案**

- 计划中...
- 效果：远程工作站、云电脑和远程游戏。 everything everywhere. 

搭建服务器

- ​	[postmarketOS](https://postmarketos.org/)
- [闲置手机搭建linux服务器](./awesome-self-hosted-services/闲置设备改造服务器.md)

> 个人网站和博客： 带有住宅IP的云服务器可以用于托管个人网站、博客和在线媒体内容，使个人用户能够通过自己的IP地址提供在线内容和服务。
>
> 家庭媒体中心： 带有住宅IP的云服务器可以用作家庭媒体中心，存储和共享家庭照片、视频和音乐，使家庭成员可以从任何地方访问和分享多媒体内容。
>
> 远程桌面和远程访问： 通过带有住宅IP的云服务器，个人用户可以远程访问和控制他们的计算机或家庭网络，方便远程办公、文件访问和远程技术支持。
>
> 私人游戏服务器： 带有住宅IP的云服务器可以用于托管私人游戏服务器，允许个人用户创建和管理自己的游戏世界，与朋友一起畅玩游戏。
>
> 数据备份和存储： 通过带有住宅IP的云服务器，个人用户可以进行数据备份和存储，将重要文件和数据安全地存储在云端，以防止数据丢失和硬件故障。
>
> 私人虚拟专用网络： 带有住宅IP的云服务器可以用于设置私人代理，提供加密的网络连接，增加网络安全性和隐私保护。
>
> 住宅IP和机房IP在用途、可靠性、带宽和性能等方面存在差异。带有住宅IP的云服务器可以为个人用户提供托管个人网站、家庭媒体中心、远程访问、私人游戏服务器、数据备份和存储以及私人VPN等功能。通过选择适合的云服务提供商和合适的住宅IP配置，个人用户可以充分利用带有住宅IP的云服务器来满足个人和家庭的互联网需求。
>
>
> USA-IDC是站长比较推荐和认可的一个服务商，可提供家宽/商宽的IP，不会面临风控的问题，完全模拟正常使用网络，他们家的机器分布机房挺多，价格也很低，点击下方入口联系客服购买全线VPS都打折

生产力

- 翻译服务: 集成 deepl/openai, 
- AI 聊天.
- 私人助理Leon: Open-source personal assistant who can live on your server.
- revealjs:Framework for easily creating beautiful presentations using HTML.
- Habitica:Habit tracker app which treats your goals like a Role Playing Game. Previously called HabitRPG.
- Firefly III:Firefly III is a modern financial manager. It helps you to keep track of your money and make budget forecasts. It supports credit cards, has an advanced rule engine and can import data from many banks.

[网络](./awesome-self-hosted-services/networking.md)：
- Proxy:Nginx Proxy Manager: Nginx Proxy Manager is an easy way to accomplish reverse proxying hosts with SSL termination.
- frp
- xray

自建网盘可以投屏一键播放

自建网盘-备份方案：

ownCloud： All-in-one solution for saving, synchronizing, viewing, editing and sharing files, calendars, address books and more.

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
