#  【安全圈】黑客利用 Collabora RCE 漏洞为 Nextcloud 服务器植入后门  
 安全圈   2026-10-17 11:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/aBHpjnrGylgOvEXHviaXu1fO2nLov9bZ055v7s8F6w1DD1I0bx2h3zaOx0Mibd5CngBwwj2nTeEbupw7xpBsx27Q/640?wx_fmt=other&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
**关键词**  
  
  
  
漏洞  
  
  
威胁行为者正在利用 Nextcloud 的 Collabora Online - 内置 CODE Server 应用（richdocumentscode）中一个现被追踪为 CVE-2026-110447 的严重命令执行漏洞。  
  
  
多份管理员报告表明，攻击始于 9 月中旬，大约在正式安全公告、GitHub 公告和 CVE 发布的一个月之前。  
  
  
CVE-2026-110447 影响 26.4.303 之前的 richdocumentscode 版本，位于该 Nextcloud 应用启动捆绑 Collabora 服务器的机制中。  
  
  
该漏洞仅影响可选的内置 CODE server 应用。据 Collabora 称，独立 Collabora Online 服务器、Docker 和 RPM/DEB 安装以及 Nextcloud All-in-One 部署不受影响。  
  
  
这些攻击涉及向该应用的 proxy.php 端点发送恶意请求，触发操作系统命令注入。  
  
  
成功利用的一个关键前提是，内置 CODE server 必须处于停止状态（例如重启、升级、崩溃之后）。无需身份验证或用户交互。  
  
  
受恶意活动影响的管理员报告称，攻击最早可追溯到 9 月 10 日，针对多个端点。  
  
  
根据报告，攻击者植入了 PHP webshell，并利用它们搜索密码数据库、密码管理器工件和加密货币钱包。  
  
  
一些用户还报告称，勒索软件运营者正在利用该漏洞，但 BleepingComputer 无法独立证实这一点。  
  
  
建议系统管理员升级到 26.4.303 或更高版本，如果不需要则移除该服务器应用。厂商警告称，仅禁用该应用是不够的。  
  
  
除了维护者在 9 月中旬发出的警告外，10 月 3 日还发布了一份社区警告，敦促管理员因正在进行的利用而立即更新。  
  
  
然而，缺少 CVE 编号使管理员更难准确定位问题并实施有效的防御。  
  
  
网络安全公司 Second Security 的一项研究说明了安全修复与正式漏洞通知之间的差距。  
  
  
该公司分析了开源漏洞的真实案例，发现 8 月份公开修复与 CVE 发布之间的中位间隔为 39 天，而 1 月份为 5.1 天。  
  
  
研究人员认为，AI 辅助漏洞发现的日益增多正在给 CVE 处理带来压力，并导致发布延迟。  
  
  
在许多情况下，研究人员发现公开的利用代码在安全修复后数小时内、并在相应 CVE 发布前几天就出现在网上。  
  
  
Second Security 指出，AI 工具的使用使攻击者能够轻松为已发布的修复生成可用的利用代码。  
  
  
在 Collabora 的案例中，修复于 9 月 9 日提交，26.4.303 版本于 9 月 11 日发布。公告和 CVE 于 10 月 14 日发布，间隔超过四周。  
  
  
BleepingComputer 联系了 Nextcloud，询问其对这些恶意活动报告的回应，以及有关攻击和受影响系统数量的细节。  
  
  
Nextcloud 告诉 BleepingComputer，Collabora 于 9 月中旬报告了该漏洞，更新已通过应用商店提供。  
  
  
该公司为披露延迟辩解称，这给了用户更新时间，且其未观察到大规模利用。  
  
  
在今天早些时候关于该活动的一篇博客文章中，Collabora 将延迟公告主要归因于 GitHub 的 CVE 处理积压，称其因 AI 驱动的安全报告数量增加而承受压力。  
  
  
 END    
  
  
阅读推荐  
  
  
[【安全圈】调价后，DeepSeek还是崩了5小时](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079367&idx=1&sn=55f57a7fbf76ce793069a3ed8391fc56&scene=21#wechat_redirect)  
  
  
  
[【安全圈】日本汉堡王遭未授权访问，1118 万条客户信息泄露](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079367&idx=2&sn=fdc0fec3f5adc2890659518499a24029&scene=21#wechat_redirect)  
  
  
  
[【安全圈】不到一年发出 10 亿条诈骗信息，微软联手日本警方捣毁一跨国犯罪网络](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079367&idx=3&sn=df923f4e5742bf1035d1dc7e1cc721df&scene=21#wechat_redirect)  
  
  
  
[【安全圈】没抓到人，黑客却主动退还 320 枚比特币](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079355&idx=1&sn=7ba920f7e03d583b0b9149aabb2b21eb&scene=21#wechat_redirect)  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEDQIyPYpjfp0XDaaKjeaU6YdFae1iagIvFmFb4djeiahnUy2jBnxkMbaw/640?wx_fmt=png "")  
  
**安全圈**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
←扫码关注我们  
  
**网罗圈内热点 专注网络安全**  
  
**实时资讯一手掌握！**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
**好看你就分享 有用就点个赞**  
  
**支持「****安全圈」就点个三连吧！**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
  
