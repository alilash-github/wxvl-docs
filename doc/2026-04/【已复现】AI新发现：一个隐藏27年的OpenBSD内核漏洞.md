#  【已复现】AI新发现：一个隐藏27年的OpenBSD内核漏洞  
原创 360漏洞研究院
                    360漏洞研究院  360漏洞研究院   2026-04-09 07:25  
  
“扫描下方二维码，进入公众号粉丝交流群。更多一手网安资讯、漏洞预警、技术干货和技术交流等您参与！”  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/5nNKGRl7pFgrNicMticDTWVCUWbOwRuWcrYSpAlwDRibKNLbe3KialEfR0Y2PlPAvS4MN50asXETicAviaRy1gRicI2Dw/640?wx_fmt=gif&from=appmsg "")  
  
  
OpenBSD 曝出 TCP 协议栈高危远程内核崩溃漏洞，攻击者通过构造特制 TCP 序列号利用有符号整数溢出，可远程触发内核空指针解引用导致系统崩溃。  
  
  
目前 360 漏洞研究院已成功复现该漏洞并验证了危害。本文包含完整影响范围、修复方案、技术原理与复现细节，建议用户立即升级。  
  
  
<table><tbody><tr style="box-sizing: border-box;"><td colspan="4" data-colwidth="100.0000%" width="100.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;background-color: rgb(100, 130, 228);box-sizing: border-box;padding: 0px;"><section style="text-align: center;color: rgb(255, 255, 255);box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞概述</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞名称</span></strong></p></section></td><td colspan="3" data-colwidth="76.0000%" width="76.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">OpenBSD TCP远程内核崩溃漏洞</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞编号</span></strong></p></section></td><td colspan="3" data-colwidth="76.0000%" width="76.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">LDYVUL-2026-00058000</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">公开时间</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span style="box-sizing: border-box;"><span leaf="">2026-04-07</span></span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span style="color: rgb(0, 0, 0);box-sizing: border-box;"><span leaf="">POC状态</span></span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">未公开</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞类型</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">DOS</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">EXP状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">未公开</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span style="color: rgb(0, 0, 0);box-sizing: border-box;"><span leaf="">利用可能性</span></span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">低</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(0, 0, 0);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">技术细节状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">未公开</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">CVSS 3.1</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">7.5</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">在野利用状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">未发现</span></p></section></td></tr></tbody></table>  
  
  
**01**  
  
**漏洞影响范围**  
  
  
  
受影响的软件版本：  
  
暂未公开  
  
  
**02**  
  
**修复建议**  
  
  
  
**正式防护方案**  
  
该漏洞使用以下的Patch已经被官方修复。  
```
  if (SEQ_GT(sack.end, tp->snd_max))
      continue;
- // if (SEQ_LT(sack.start, tp->snd_una))
- //     continue;
+ if (SEQ_LT(sack.start, tp->snd_una))
+     continue;
```  
  
在函数tcp_sack_option中增加了对sack下界的检查。  
```
- if (SEQ_LT(tp->rcv_lastsack, sack.start)) {
- /* At this point, p points to the last hole on the list */
- // if (p != NULL && SEQ_LT(tp->rcv_lastsack, sack.start)) {
+ if (p != NULL && SEQ_LT(tp->rcv_lastsack, sack.start)) {
```  
  
在函数tcp_sack_option中增加了当p为NULL指针的检查。  
  
通过在这两处增加检查，即消除漏洞根因又增加纵深防御，从而消除了漏洞的影响。  
  
  
**03**  
  
**漏洞描述**  
  
  
  
2026年4月7日，Anthropic 公开一些由大模型Claude Mythos Preview挖掘的漏洞。其中包括一个OpenBSD的内核漏洞。有趣的是这是一个存在了27年的漏洞。  
  
该漏洞的起因源自OpenBSD内核TCP模块。  
  
漏洞由两个独立但可组合的缺陷构成。  
  
  
**Bug 1：缺少 sack.start 下界验证**  
  
代码位置在sys/netinet/tcp_input.c中的tcp_sack_option() 函数，约第 2461-2462 行，函数tcp_sack_option() 在处理每个 SACK 块时执行以下初始验证：  
```
if (SEQ_LEQ(sack.end, sack.start))
continue;                              // (1) end > start
if (SEQ_LEQ(sack.end, tp->snd_una))
continue;                              // (2) end > snd_una（非旧块）
if (SEQ_GT(th->th_ack, tp->snd_una)) {
if (SEQ_LT(sack.start, th->th_ack))
continue;                          // (3) 当 ACK 推进时，start >= th_ack
}
if (SEQ_GT(sack.end, tp->snd_max))
continue;                              // (4) end <= snd_max（在窗口内）
// 缺失的检查：
// if (SEQ_LT(sack.start, tp->snd_una))
//     continue;                           // (5) start >= snd_una ← 被注释掉了！
```  
  
检查 (4) 确保 sack.end 不超过发送窗口上界，但没有任何检查确保 sack.start >= snd_una（检查 (3) 仅在 th_ack > snd_una 时生效，而攻击中 th_ack == snd_una，条件不满足，检查被跳过）。这意味着攻击者可以提供一个任意的 sack.start 值，包括距离正常窗口约 2^31 的值。  
  
  
**Bug 2：缺少 p != NULL 空指针保护**  
  
代码位置在sys/netinet/tcp_input.c，约第 2568-2570 行,hole 遍历循环结束后，代码检查是否需要在链表尾部追加新 hole：  
```
// 修复后应为：
// if (p != NULL && SEQ_LT(tp->rcv_lastsack, sack.start)) {

// 实际代码（缺少 p != NULL 保护）：
if (SEQ_LT(tp->rcv_lastsack, sack.start)) {
    ...
    p->next = temp;   // ← 当 p == NULL 时，内核崩溃！
    ...
}
```  
  
如果 hole walk 删除了链表中唯一的 hole，则 p 变为 NULL（通过第 2516 行的 p = cur = NULL），随后 p->next = temp解引用空指针，触发内核 panic。  
  
  
**组合利用：有符号整数溢出创造"不可能"条件**  
  
正常情况下，Bug 2 中的路径不可达——要同时满足 “删除 hole"（需要 sack.start <= hole.start）和 "触发追加"（需要 sack.start > rcv_lastsack），而 rcv_lastsack > hole.start，看似矛盾。  
  
但利用 Bug 1 允许的越界 sack.start，再结合有符号整数溢出，可以让一个值同时"小于" hole 起始且"大于" rcv_lastsack。  
  
  
**04**  
  
**漏洞复现**  
  
  
  
360 漏洞研究院已成功复现OpenBSD TCP远程内核崩溃漏洞。通过在qemu虚拟机中安装OpenBSD系统，然后执行poc程序，成功复现了漏洞。具体复现信息如下图所示：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/dZ7ia5iaWFzzibgPGReYg2SWotnicxVWL9T1uG9nmicKQWvbuYs20Xib99DrpSBO4l9BgDB1xHqD5GZRmWfMyQf8NqWb4y0TPcuspLCOnX79nk1WQ/640?wx_fmt=png&from=appmsg "")  
  
OpenBSD TCP远程内核崩溃漏洞复现  
  
  
**05**  
  
**时间线**  
  
  
  
2026年04月09日，360漏洞研究院发布本安全风险通告。  
  
  
**06**  
  
参考链接  
  
  
  
https://red.anthropic.com/2026/mythos-preview/[#ftnt]()  
_ref4  
  
https://ftp.openbsd.org/pub/OpenBSD/patches/7.8/common/025_sack.patch.sig  
  
  
07  
  
更多漏洞情报  
  
  
  
建议您订阅360数字安全-漏洞情报服务，获取更多漏洞情报详情以及处置建议，让您的企业远离漏洞威胁。  
  
  
邮箱：360VRI@360.cn  
  
网址：https://vi.loudongyun.360.net  
  
  
  
“洞”悉网络威胁，守护数字安全  
  
  
**关于我们**  
  
  
360 漏洞研究院，隶属于360数字安全集团。其成员常年入选谷歌、微软、华为等厂商的安全精英排行榜, 并获得谷歌、微软、苹果史上最高漏洞奖励。研究院是中国首个荣膺Pwnie Awards“史诗级成就奖”，并获得多个Pwnie Awards提名的组织。累计发现并协助修复谷歌、苹果、微软、华为、高通等全球顶级厂商CVE漏洞3000多个，收获诸多官方公开致谢。研究院也屡次受邀在BlackHat，Usenix Security，Defcon等极具影响力的工业安全峰会和顶级学术会议上分享研究成果，并多次斩获信创挑战赛、天府杯等顶级黑客大赛总冠军和单项冠军。研究院将凭借其在漏洞挖掘和安全攻防方面的强大技术实力，帮助各大企业厂商不断完善系统安全，为数字安全保驾护航，筑造数字时代的安全堡垒。  
  
