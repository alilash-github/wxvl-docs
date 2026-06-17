#  思科修复已遭利用的 SD-WAN vManage 漏洞  
Sergiu Gatlan
                    Sergiu Gatlan  代码卫士   2026-06-16 07:42  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
**编译：代码卫士**  
  
****  
**思科发布安全更新，修复 Catalyst SD-WAN Manager 中的一个漏洞（CVE-2026-20262），该漏洞已被攻击者利用以实现 root 权限提升。**  
  
网络管理软件Catalyst SD-WAN Manager此前名为 SD-WAN vManage，允许管理员通过单一控制面板管理多达 6000 台 SD-WAN 设备。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfUHEGnciaj0IImfWUCmyqoqMlbUzaiaEEFMflFD24Ybgtkkkp6lUHk3Q3lFdG4QWGB7snXA9OjrE2FdQqwF0mAF0kjm1ELXx9JFs/640?wx_fmt=gif&from=appmsg "")  
  
**漏洞简述**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfV9YIhtXE5bblnXkSWYu9k0kicbUyKMIAErSFYtu3bNjC3ZcO7fTiamicuVPuoYBiaznvXibzdgRod95UsFNGvznPAgEOYnPKcDHCOY/640?wx_fmt=gif&from=appmsg "")  
  
  
  
思科表示，该问题源于文件上传过程中对用户提供的输入验证不足。低权限的远程攻击者可通过向受影响系统的 API 端点发送特制的 HTTP 请求，以 root 权限执行任意命令。  
  
思科在本周一发布的安全公告中称：“Cisco Catalyst SD-WAN Manager（前身为 SD-WAN vManage）的 Web UI 中存在一个漏洞，可能导致经过身份验证的远程攻击者在受影响系统的文件系统上创建文件或覆盖任意文件。攻击者可通过向受影响系统的 API 端点发送特制的 HTTP 请求来利用此漏洞。成功利用后，攻击者可以在底层操作系统上创建或覆盖任意文件，随后利用该文件将权限提升至 root。”  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfWibEEDDPcseRBQLnl37lYoYbBKibuJkhrkic70UJUAt3byjuvvV8aTP7Fm4dYGa7dsbTycWia5u2d3Am6Sfw7iaG84ft5puH3ibaNas/640?wx_fmt=gif&from=appmsg "")  
  
**影响版本**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfXkbcq5v3YUhDhGIh8ibbaQIdSWNSaqH0poy2I1oSu9bk6WdmAAMlyMKe3k7QIrKtVjWaUIHZwAwazbrpb5hQjvr5oFRBtFEVms/640?wx_fmt=gif&from=appmsg "")  
  
  
  
该漏洞影响所有配置的部署类型，包括本地部署、Cisco SD-WAN Cloud-Pro、Cisco SD-WAN Cloud（思科托管）以及 Cisco SD-WAN for Government（FedRAMP）。影响版本及修复版本如下：  
<table><thead><tr><td data-colwidth="290" width="290" valign="top" style="border: 1px solid #1d3652;background: #eeeeee;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="font-size: 15px;letter-spacing: 1px;"><strong><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">Cisco Catalyst SD-WAN </span></span></strong><strong><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:宋体;"><span leaf="">版本</span></span></strong></span></p></td><td data-colwidth="210" width="210" valign="top" style="border-top: 1px solid #1d3652;border-right: 1px solid #1d3652;border-bottom: 1px solid #1d3652;border-image: initial;border-left: none;background: #eeeeee;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="font-size: 15px;letter-spacing: 1px;"><strong><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:宋体;"><span leaf="">首次修复版本</span></span></strong></span></p></td></tr></thead><tbody><tr><td data-colwidth="290" width="290" valign="top" style="border-right: 1px solid #1d3652;border-bottom: 1px solid #1d3652;border-left: 1px solid #1d3652;border-image: initial;border-top: none;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="font-size: 15px;letter-spacing: 1px;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.9.9.1 </span></span><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:宋体;"><span leaf="">及更早版本</span></span></span></p></td><td data-colwidth="210" width="210" valign="top" style="border-top: none;border-left: none;border-bottom: 1px solid #1d3652;border-right: 1px solid #1d3652;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.9.9.2</span></span></p></td></tr><tr><td data-colwidth="290" width="290" valign="top" style="border-right: 1px solid #1d3652;border-bottom: 1px solid #1d3652;border-left: 1px solid #1d3652;border-image: initial;border-top: none;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="font-size: 15px;letter-spacing: 1px;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.12.7.1</span></span><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:宋体;"><span leaf="">及更早版本</span></span></span></p></td><td data-colwidth="210" width="210" valign="top" style="border-top: none;border-left: none;border-bottom: 1px solid #1d3652;border-right: 1px solid #1d3652;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.12.7.2</span></span></p></td></tr><tr><td data-colwidth="290" width="290" valign="top" style="border-right: 1px solid #1d3652;border-bottom: 1px solid #1d3652;border-left: 1px solid #1d3652;border-image: initial;border-top: none;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="font-size: 15px;letter-spacing: 1px;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.15.4.4</span></span><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:宋体;"><span leaf="">及更早版本</span></span></span></p></td><td data-colwidth="210" width="210" valign="top" style="border-top: none;border-left: none;border-bottom: 1px solid #1d3652;border-right: 1px solid #1d3652;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.15.4.5</span></span></p></td></tr><tr><td data-colwidth="290" width="290" valign="top" style="border-right: 1px solid #1d3652;border-bottom: 1px solid #1d3652;border-left: 1px solid #1d3652;border-image: initial;border-top: none;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="font-size: 15px;letter-spacing: 1px;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.15.5.2</span></span><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:宋体;"><span leaf="">及更早版本</span></span></span></p></td><td data-colwidth="210" width="210" valign="top" style="border-top: none;border-left: none;border-bottom: 1px solid #1d3652;border-right: 1px solid #1d3652;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.15.5.3</span></span></p></td></tr><tr><td data-colwidth="290" width="290" valign="top" style="border-right: 1px solid #1d3652;border-bottom: 1px solid #1d3652;border-left: 1px solid #1d3652;border-image: initial;border-top: none;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.18.3</span></span></p></td><td data-colwidth="210" width="210" valign="top" style="border-top: none;border-left: none;border-bottom: 1px solid #1d3652;border-right: 1px solid #1d3652;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">20.18.3.1</span></span></p></td></tr><tr><td data-colwidth="290" width="290" valign="top" style="border-right: 1px solid #1d3652;border-bottom: 1px solid #1d3652;border-left: 1px solid #1d3652;border-image: initial;border-top: none;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="font-size: 15px;letter-spacing: 1px;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">26.1.1.1</span></span><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:宋体;"><span leaf="">及更早版本</span></span></span></p></td><td data-colwidth="210" width="210" valign="top" style="border-top: none;border-left: none;border-bottom: 1px solid #1d3652;border-right: 1px solid #1d3652;padding:5px 10px;"><p style="text-align:left;margin-bottom: 15px;display: block;margin-left: 5px;margin-right: 5px;text-indent: 0em;"><span style="color: #333333;font-size: 15px;letter-spacing: 1px;font-family:Arial, sans-serif;"><span leaf="">26.1.1.2</span></span></p></td></tr></tbody></table>  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfV3BN2TwrG3tPMEXFD8Sh7OlNfT5rETaD5HhboDYObm1BDhR7mxichgGIzQ2OWXS67rRNFwVmSqmgRzNAY41cDpLcDtqfXXKx7w/640?wx_fmt=gif&from=appmsg "")  
  
**修复措施**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfUMzfyrcPfC1ywZJqtibdFwppKS4icLcTuMAqhadz6wJb5jF3L3Md5n5wOSShaBBUQFKgsaxqZhZTTbkMjoYVRmeyTE2xkNfQgkg/640?wx_fmt=gif&from=appmsg "")  
  
  
  
思科表示，产品安全事件响应团队（PSIRT）在本月早些时候已获知 CVE-2026-20262 被利用的情况，并“强烈”建议客户对系统进行修补。虽然思科未透露这些攻击的细节，但提供了入侵威胁指标（IOC），警告管理员检查 SD-WAN vmanage-server、vmanage-appserver 和 serviceproxy-access 日志中是否有上传 index.jsp 和 .war 文件的尝试。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfVuolCB9ypmcF92ibPwBdibkibu2FJuF24jfv1wBDdciauxb9UibmPMp9zAolfHsNkZVvdBEibmh38FGIO4mkdNGMvBJx98gq3WibvFkM/640?wx_fmt=gif&from=appmsg "")  
  
**以往类似漏洞**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfULgTzuAvf8Dia0PntITJ9hib92AQwVE5wsR1oFLk4Q8HkR0ZfRsxcDucuDw1DEavDjpvxmysBL96wc07bs0rWhn4MJ6OySBLTTc/640?wx_fmt=gif&from=appmsg "")  
  
  
  
今年 2 月，思科修补了另一个 Catalyst SD-WAN Manager 信息泄露安全漏洞（CVE-2026-20133），该漏洞被标记为在 4 月底已被积极利用。两周后，思科又警告了另外两个漏洞（CVE-2026-20128 和 CVE-2026-20122）在野利用。上个月，思科还将一个最高严重级别的 Catalyst SD-WAN Controller 认证绕过漏洞（CVE-2026-20182）标记为0day漏洞，该漏洞已被积极利用，攻击者可在未修复的设备上获取管理员权限。更近期，在 6 月初，思科提醒注意另一个未修复的 Catalyst SD-WAN Manager 零日漏洞（CVE-2026-20245）已在攻击中被利用，可使攻击者获得 root 权限。  
  
过去几年中，美国网络安全与基础设施安全局（CISA）已标记了 91 个在野利用的思科漏洞，其中 5 个出现在 Cisco Catalyst SD-WAN Manager 中，另有 6 个被用于勒索软件攻击。   
  
  
 开源  
卫士试用地址：  
https://oss.qianxin.com/#/login  
  
 代码卫士试用地址：https://sast.qianxin.com/#/login  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[思科：Unified CM 严重漏洞的 PoC 已发布](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526228&idx=2&sn=cbc3a57a98b3117c33dc6847aa4c4e14&scene=21#wechat_redirect)  
  
  
[思科：注意无补丁但已遭利用的 SD-WAN 高危 0day](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526228&idx=1&sn=aa8606b21d2d15f6de0e55d784eef216&scene=21#wechat_redirect)  
  
  
[思科：速修复满分 Secure Workload 未授权 API 访问漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526078&idx=1&sn=2b669f642fd4b13d42c79cc8a544e482&scene=21#wechat_redirect)  
  
  
[思科：注意已遭利用的满分 SD-WAN 新 0day](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526019&idx=1&sn=a356c936f290fca11bdc81d87da6081f&scene=21#wechat_redirect)  
  
  
[思科紧急修复高危 ISE 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525791&idx=1&sn=8e8b1cc8aa09816bba96ee685aa24394&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://www.bleepingcomputer.com/news/security/cisco-fixes-sd-wan-vmanage-flaw-exploited-in-zero-day-attacks/  
  
  
题图：Pixa  
bay Licens  
e  
  
  
**本文由奇安信编译，不代表奇安信观点。转载请注明“转自奇安信代码卫士 https://codesafe.qianxin.com”。**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSf7nNLWrJL6dkJp7RB8Kl4zxU9ibnQjuvo4VoZ5ic9Q91K3WshWzqEybcroVEOQpgYfx1uYgwJhlFQ/640?wx_fmt=jpeg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSN5sfviaCuvYQccJZlrr64sRlvcbdWjDic9mPQ8mBBFDCKP6VibiaNE1kDVuoIOiaIVRoTjSsSftGC8gw/640?wx_fmt=jpeg "")  
  
**奇安信代码卫士 (codesafe)**  
  
国内首个专注于软件开发安全的产品线。  
  
   ![](https://mmbiz.qpic.cn/mmbiz_gif/oBANLWYScMQ5iciaeKS21icDIWSVd0M9zEhicFK0rbCJOrgpc09iaH6nvqvsIdckDfxH2K4tu9CvPJgSf7XhGHJwVyQ/640?wx_fmt=gif "")  
![]( "")  
  
   
觉得不错，就点个 “  
在看  
” 或 "  
赞  
” 吧~  
  
