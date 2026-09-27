#  红帽披露OpenShift重要漏洞，可绕过PGP校验推送恶意镜像  
 FreeBuf   2026-09-24 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX3mUzy79YMweQgXnf5fz2iac9Pia8xXzWDgCQ7rBcNbasED5IHMnswia5evylqORmrdn6KkMTria2O1ZK1DfG3wGk4AyGjXFH4MvIQ/640?wx_fmt=gif "")  
  
  
![image](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0ZZNqXgtIKeiaS8RyqLLO2wwTHmYVVQJotfwicQFj2T0ibzZqX4AoJ0LLsLjzrDORyxjzIVR5IIfZzwBSWRNz1X8adjWena4mTIw/640?wx_fmt=png "")  
  
  
红帽近日披露，OpenShift内置的oc-mirror工具存在一处重要安全漏洞。攻击者可利用该漏洞绕过PGP签名校验机制，向隔离部署的OpenShift环境植入恶意发行版镜像。  
  
  
该漏洞编号为CVE-2026-75939，CVSS v3.1评分为7.4，于2026年9月21日正式公开。漏洞存在于openshift/oc-mirror组件中，企业通常使用该组件将OpenShift发行版镜像、Operator目录及相关内容同步到私有镜像仓库。  
  
  
这类镜像同步操作对物理隔离或断网部署的环境尤为关键，这类环境无法直接从红帽官方仓库或公网下载软件。  
  
  
Part  
01  
  
校验顺序存在逻辑缺陷  
  
据红帽公告说明，oc-mirror对经PGP签名的发行版镜像存在校验逻辑错误。该工具会在未完成整个签名消息体处理的情况下，提前执行签名错误检查。  
  
  
这种错误的校验顺序会形成绕过条件：即使PGP消息为攻击者伪造签名，工具也可能将其判定为可信。  
  
  
Part  
02  
  
篡改流量可引发供应链投毒风险  
  
攻击者要利用该漏洞，需要具备拦截或篡改受影响oc-mirror实例与签名端点之间流量的能力。具备该条件后，攻击者可构造包含合法红帽发行版密钥ID的伪造PGP消息，提交给oc-mirror校验。  
  
  
由于校验顺序存在缺陷，oc-mirror会将恶意伪造的消息判定为合法内容，将恶意发行版载荷同步到隔离环境的私有仓库中。企业通常会将这类同步到内部仓库的内容视为已通过审批的可信软件，因此该漏洞会带来严重的供应链投毒风险。  
  
  
一旦恶意发行版镜像进入私有仓库，OpenShift管理员或自动化安装流水线就可能将其选中并部署到集群中。这会导致集群面临未授权代码执行、应用篡改、凭证窃取、敏感数据未授权访问等多重风险。  
  
  
Part  
03  
  
特定版本需落实临时防护措施  
  
红帽将该漏洞评为重要级，攻击向量为网络，利用过程无需攻击者具备前置权限，也不需要用户交互。不过该漏洞的攻击复杂度较高，攻击者必须成功操纵与签名校验相关的网络流量才能完成利用，CVSS评估显示其对机密性、完整性的影响为高，对可用性无影响。  
  
  
红帽明确表示，RHEL 8版本的对应插件不受影响，因为该版本中不存在这一组件。本次漏洞的受影响组件为红帽OpenShift容器平台4中的openshift4/oc-mirror-plugin-rhel9，受影响次要产品分支中的旧版本包通常均存在漏洞，除非官方明确标注其不受影响。  
  
  
漏洞披露时红帽表示，暂无符合其部署与稳定性标准的实用缓解方案。在安全补丁或更新包发布前，使用oc-mirror的企业应将发行版镜像同步流程视为高风险操作。  
  
  
管理员应限制签名端点的网络访问权限，谨慎部署TLS流量检测防护措施。同时要监控私有镜像仓库中同步内容的异常变更，在将镜像推送到生产环境前，通过独立可信渠道校验发行版镜像摘要。  
  
  
相关团队还应检查隔离环境私有仓库的访问控制规则，审计近期同步的所有发行版镜像。同时持续关注红帽发布的安全公告，及时获取修复更新信息。  
  
  
参考来源：  
  
Red Hat OpenShift Flaw Lets Attackers Bypass PGP Checks and Push Malicious Releases  
  
https://cybersecuritynews.com/red-hat-openshift-flaw/  
  
###   
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  
