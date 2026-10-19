#  【安全圈】Linux内核爆出9.3分超高危漏洞，可“越狱”虚拟机攻击宿主机  
 安全圈   2026-10-17 11:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/aBHpjnrGylgOvEXHviaXu1fO2nLov9bZ055v7s8F6w1DD1I0bx2h3zaOx0Mibd5CngBwwj2nTeEbupw7xpBsx27Q/640?wx_fmt=other&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
**关键词**  
  
  
  
高危漏洞  
  
  
Debian 在最新安全公告中，披露存在于 Linux 内核的“关键”（Critical）漏洞，**CVSS 3.1 评分高达 9.3 分，攻击者可利用 IPv4 校验和元数据校验缺陷，从虚拟机客户机突破隔离边界，影响宿主机。**  
  
****  
![](https://mmbiz.qpic.cn/mmbiz_jpg/sbq02iadgfyEQsjjymDC8eIfKDSWlCfnORpcBZ0r3rmovkHL7boRbS65ILNYe4uwARDpcr8fjRUzwwB8fbAByjBicrxTrIMlFU70jVCUMMP9Q/640?wx_fmt=jpeg "")  
  
  
根源方面，该漏洞追踪编号为 CVE-2026-99999，问题源于 Linux 网络栈对延迟校验和处理不当：当带有异常 checksum_start 元数据的数据包进入 IPv4 处理流程后，skb_checksum_help 可能在内核已经解析并信任 IPv4 头之后，改写其中的关键字段。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/sbq02iadgfyFKjrCxEnRlHFvlzgZfbZECPC0ZWjiaI3Kr32YqCojn9tJK5yn7alJlj2P4ib1Fib7WuRaFDnnlt8IZTsvPCvCJegicVSaHaj4VZIo/640?wx_fmt=jpeg&from=appmsg "")  
  
  
研究人员称，攻击者可借此使内核将原本 20 字节的 IPv4 头误判为 60 字节，进而读取逻辑数据包之外的宿主机内存，并在受控实验环境中实现进一步写入与宿主机代码执行。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/sbq02iadgfyGJHyQlIap7vF7zYOlPuc13WrAdcxjUia031o7ycrNNQ0QsnDXaWEricMvEfELhFw1HQxqmmItbph6ZfD6iaLDQticxpxg6vIjq1ks/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/sbq02iadgfyF6xbJogjY4QG6OsS8JXHiasYKdenjD31Eic7uqz1JTKNtJMjfOdpCcwe5ibQOvP8q1P3rz5vibI8iaCFvRkjUD15HuCIV2QORsSg0I/640?wx_fmt=jpeg&from=appmsg "")  
  
  
影响方面，该漏洞并非 KVM 本身的缺陷，而是宿主机 Linux 网络栈的问题；但在 KVM 等虚拟化环境中，恶意客户机可能借此攻击宿主机，因此对云厂商、多租户沙箱和虚拟化平台风险尤其突出。  
  
  
修复方面，上游修复补丁为 6785011f8b16，标题为“ipv4: validate checksum_start before completing checksum”，已在 Linux 6.12.113、6.18.57、7.2.11 及 7.3-rc7 等版本中修复。  
  
  
对于用户而言，运行 Linux 虚拟化、云主机或容器沙箱的机构应尽快升级内核至已修复版本，尤其是 Debian、Ubuntu、RHEL 及各类公有云镜像用户。  
  
  
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
  
  
