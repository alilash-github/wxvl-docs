#  【大洞预警，已复现】GeoServer 未授权 RCE！ 默认配置直接 root！  
银遁安全
                    银遁安全  银遁安全团队   2026-11-25 04:11  
  
一、漏洞简介  
  
　　GeoServer 是 OSGeo 旗下的开源地理空间数据服务器，被广泛用于政企 GIS、自然资源、测绘、应急指挥等系统，默认以 /geoserver 为上下文路径对外提供 WMS/WFS/WCS 等 OGC 服务。由于大量实例直接暴露在公网且长期不升级，它一直是攻防双方共同关注的重点目标。  
  
　　2026 年 11 月 23 日，GeoServer 官方一次性发布 5 个安全公告，对应 CVE 编号为 **CVE-2026-129244**  
、**CVE-2026-129243**  
、**CVE-2026-120705**  
、**CVE-2026-129245**  
 与 **CVE-2026-129242**  
。单独看，它们分别是信息泄漏、鉴权绕过、任意文件读取、会话伪造和反序列化；组合起来，却是一条完整的**预认证 RCE 链**  
——攻击者从一条匿名的 WFS 请求出发，**不需要任何账号口令**  
，最终在服务器上以 root 身份执行任意命令。  
  
　　银遁安全团队已第一时间在本地靶场完成全链复现：**默认安装、默认配置、不装任何可选扩展、不改任何配置文件**  
，直接拿到 root 权限命令回显。  
  
二、攻击链拆解：五个 CVE 环环相扣  
  
**环节一**  
｜**CVE-2026-129244**  
：超长 storedQuery_id 触发异常，公开的 ExceptionText 回显数据目录绝对路径  
  
**环节二**  
｜**CVE-2026-129243**  
：service=x/WFS 变形写法绕过操作级鉴权，匿名写入 stored query  
  
**环节三**  
｜**CVE-2026-120705**  
：存储查询文本重解析时 XInclude 生效，内联读取 config.xml 与 users.xml  
  
**环节四**  
｜**CVE-2026-129245**  
：用泄漏的密钥与口令摘要伪造 remember-me，直接换取管理员会话  
  
**环节五**  
｜**CVE-2026-129242**  
：JDBC 用户组服务读取 OTHER 列时反序列化，加载 translet 执行系统命令  
  
　　这条链有三个特征值得高度警惕：  
  
**1. 全程匿名**  
：五个环节均不需要任何凭据，不存在"攻击者需要先拿到低权限账号"的前置条件；  
  
**2. 默认配置即满足**  
：官方容器默认以 root 运行、默认 admin 用户处于启用状态、默认 remember-me 密钥即为固定字符串，整条链在开箱即用的环境里直接成立；  
  
**3. 只补一环没用**  
：五处缺陷环环相扣，官方在修复版本中同时处置全部五处，任何"打补丁式"的单点修复都无法断链。  
  
三、漏洞复现  
  
　　使用官方镜像 **docker.osgeo.org/geoserver:3.0.0**  
 搭建本地隔离靶场。  
  
docker pull docker.osgeo.org/geoserver:3.0.0 docker run -d --name geoserver-lab \   -p 18080:8080 \   docker.osgeo.org/geoserver:3.0.0  
  
　　首页如下，版本号 3.0.0：  
  
![靶场首页](https://mmbiz.qpic.cn/mmbiz_png/14zTrTiaCfEWTIibUGZMIhbRRleosAtUyvyoT1XYe0Lh81gC3HzozQzjn0jR5CfGlsJL0Mx3mlk6Ag3PCl9ghQaDR6XggdlmX9GGibBQy2hNe8/640?wx_fmt=png&from=appmsg "")  
  
图 1｜靶场环境：GeoServer 3.0.0 默认首页（匿名可访问）  
  
环节一：超长 storedQuery_id 泄漏数据目录  
  
　　向 /geoserver/wfs 提交一个超长 storedQuery_id，GeoServer 会将其拼成 <数据目录>/wfs/query/<id>.xml 去磁盘查找，文件不存在时抛出的异常信息没有脱敏，把**完整绝对路径**  
原样返回给了匿名请求方：  
  
![路径泄漏](https://mmbiz.qpic.cn/sz_mmbiz_png/14zTrTiaCfEWWoIlP6vFEqIvfNhNMQdfdPZsXGEOvQ78h0fnswWIU8DMQGCSbBibgXB2GSXeXjyrls82ONvKIR7W1Tpq4eIKvVdSlVASMGAgk/640?wx_fmt=png&from=appmsg "")  
  
图 2｜环节一：异常回显 /opt/geoserver_data/wfs/query/AAAA….xml，数据目录就此暴露  
  
环节二 + 三：匿名写入 stored query，XInclude 内联读取安全配置  
  
　　利用 service=x/WFS 的变形写法绕过 wfs.CreateStoredQuery 的操作级规则后，匿名用户即可保存一个携带转义 xi:include 的存储查询。GeoServer 在保存时将其反转义重解析，DescribeStoredQueries 取回时 XInclude 默认开启且实体解析器置空（只能防 XXE、防不住 XInclude），预置的 file:// 包含在响应中被原地展开——**security/config.xml 与 users.xml 的内容直接出现在了公开响应里**  
：  
  
![文件读取](https://mmbiz.qpic.cn/sz_mmbiz_png/14zTrTiaCfEUnYeaBCXnBgiacpINPdqmaWnzuyicNgia5lSZGsv8kBvKxlWXia29vH1yKKnPTaCN460FibJ3BQ9Z99swFv62PDYHSKRTCszrFOAsM/640?wx_fmt=png&from=appmsg "")  
  
图 3｜环节三：remember-me key（默认值 geoserver）与 admin 口令摘要被内联读出  
  
环节四：伪造 remember-me，直接换取管理员会话  
  
　　Spring Security 的 remember-me 签名是 SHA256(用户名:过期时间:口令:密钥)，四个输入中两个已经在上一环到手，签名可以本地计算，**完全不需要爆破口令**  
。带着伪造 cookie 访问任意管理页，GeoServer 自动建立管理员会话：  
  
![管理员会话](https://mmbiz.qpic.cn/sz_mmbiz_png/14zTrTiaCfEXYOMxhHNeJBCfZ93iadsFd5QLBZuNys1OsYBXT1qpsBfibJmWTvlauIoTNn2CF58mMbwJTmW3nWBOu4WAyITdu4Xz2xhrNLVpdA/640?wx_fmt=png&from=appmsg "")  
  
图 4｜环节四：伪造 cookie 登录成功，直接进入服务器状态管理页  
  
![REST 管理接口](https://mmbiz.qpic.cn/mmbiz_png/14zTrTiaCfEV8VDc27uVs3B4f0SBuuMmaslic3cplOHZqxJywOkHafR6eqKFkR0djb813xqsLia7ZuxAgmv9kibKfD4qXPKePpCDsfGicVR7tXVw/640?wx_fmt=png&from=appmsg "")  
  
图 5｜环节四：伪造会话被管理员 REST 接口接受，可读取用户组服务  
  
环节五：JDBC 反序列化，命令执行拿到 root  
  
　　以管理员会话通过 REST 上传构造好的 HSQLDB 文件库并注册为 JDBC 用户组服务后，查询用户属性触发 OTHER 列反序列化，BeanComparator + TemplatesImpl 加载 translet 执行 /bin/sh。公开 PoC 一条命令跑完全链：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/14zTrTiaCfEUP7tscic2apiaQ0OtDtiakOc8AehI3GWqzwMIbZTtrPs8QJXTPSKNondFLycRxsKoWCGMTUTpaAsibhicnCeJElUaRbZg7v1KCaTYc/640?wx_fmt=png&from=appmsg "")  
  
图 6｜环节五：全链执行 id 命令，返回 uid=0(root) gid=0(root)  
  
修复版对照：3.0.2 在第一环即被阻断  
  
　　在 3.0.2 上重放同样的超长 storedQuery_id 请求：异常信息只回显 "No such stored query"，**不再包含任何文件系统路径**  
；匿名 service=x/WFS 创建 stored query 也返回 401。第一环断掉后，整条链无从谈起：  
  
![修复版对照](https://mmbiz.qpic.cn/sz_mmbiz_png/14zTrTiaCfEVWq3TmR0evfibFL3Uvc4OsYVmicNIgCA1Um1cjB4ReQUkAQvosC3IniaoEMHbx8XWDP8g8GASbibokreYHFFP1T7qVST4IibcCd04U/640?wx_fmt=png&from=appmsg "")  
  
图 7｜修复版 3.0.2：同样的请求不再泄漏数据目录路径  
  
四、影响范围与风险研判  
  
**受影响版本**  
：GeoServer ≤ 2.27.6；2.28.0 – 2.28.5；3.0.0 – 3.0.1。**修复版本**  
：2.27.7 / 2.28.6 / 3.0.2。  
  
　　利用前置条件在默认安装下即全部满足：目标为 Linux 且运行用户可执行 /bin/sh（官方镜像默认以 root 运行）；Java 17 或 21；默认 admin@default 用户组服务；未对匿名用户关闭 WFS。  
  
　　GeoServer 大量部署于**政企 GIS、自然资源、测绘与应急指挥**  
等场景，承载图层与空间数据，往往还作为内网业务系统的基础组件。该链命中后攻击者可：读取全部空间数据与业务配置、写入 WebShell、以 root 权限横向移动——等同主机完全沦陷。官方容器默认以 root 运行，意味着**提权成本为零**  
。  
  
五、应急处置指南  
  
**首选升级：**  
升级到 2.27.7 / 2.28.6 / 3.0.2 及以上，这是唯一能同时消除五个缺陷的做法。升级前务必备份数据目录与安全配置，并确认所用扩展与数据目录的兼容性。  
  
**无法立即升级时的临时缓解：**  
  
　　1. 收紧匿名 WFS 权限：在 Security → Services 中移除匿名角色对 WFS 的访问，重点覆盖 CreateStoredQuery 与 DescribeStoredQueries 操作；  
  
　　2. 业务不需要时直接关闭 Stored Query 功能；  
  
　　3. 收缩暴露面：/geoserver 仅允许内网 / VPN 访问，前面加统一认证或反向代理 ACL；  
  
　　4. 降低运行权限：容器去除 root 运行，数据目录尽量只读挂载。  
  
六、注意：仅做这些防护挡不住这条链  
  
**1. 只改 admin 口令：无效。**  
口令摘要可通过 XInclude 再读一遍即可拿到新值，伪造 remember-me 所需的摘要与密钥都在文件读取范围内；轮换 remember-me 密钥同理，config.xml 同样可被读取。  
  
**2. 封禁管理后台端口：无效。**  
整条链入口是 80/443 业务端口上的 /geoserver/wfs 匿名请求，与后台管理端口无关。  
  
**3. 依赖常规 WAF 规则：不可靠。**  
x/WFS 变形写法、超长参数、XML 请求体内的 xi:include，通用规则难以全覆盖，WAF 只能作为辅助手段，绝不能作为核心防护依赖。  
  
七、检测与排查建议  
  
**流量侧：**  
拦截 storedQuery_id 长度 > 200 的请求、请求体中包含 xi:include、service 参数出现 x/WFS 变形写法；对 /rest/security/usergroupservices 的创建与删除操作设置告警。  
  
**主机侧：**  
排查数据目录下是否出现 gs-rce-* 临时目录、payload.properties / payload.script / request.txt / result.txt 文件；是否存在名为 jdbc_geoserver_poc 的 JDBC 用户组服务；GeoServer 进程下是否派生 /bin/sh -c 异常子进程。  
  
免责声明：本文仅供学习与防御研究使用，请勿利用文中技术对未授权目标进行测试，由此产生的一切后果由使用者自行承担。  
  
