#  漏洞预警：LiteLLM未鉴权命令执行  
 字节跳动安全中心   2026-04-13 12:20  
  
**漏洞简介**  
  
**LiteLLM是一个大模型接口兼容和负载均衡工具，存在SDK集成和Proxy代理服务两种使用方式。当LiteLLM以Proxy服务方式的默认配置启动时，可以通过多个漏洞造成任意代码执行。经网络空间测绘，约1～2万LiteLLM代理服务面向公网开放。**  
  
**BVD-2026-10001038**  
- **评级评分：严重，9.8/10.0**  
  
- CVSS向量：  
  
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H  
  
- 漏洞状态：字节跳动安全团队已捕获该漏洞的在野利用行为，上报项目维护方与监管部门，项目维护方反馈已于v1.83.0版本加固  
  
  
**BVD-2026-35029**  
- 评级评分：高危，8.8/10.0  
  
- CVSS向量：  
  
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H  
  
- 漏洞状态：项目维护方已于v1.83.0版本加固  
  
  
**BVD-2026-10001209**  
- 评级评分：高危，8.8/10.0  
  
- CVSS向量：  
  
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H  
  
- 漏洞状态：安全研究员已公布详情与PoC  
  
  
**利用条件**  
- LiteLLM Proxy 服务默认配置  
  
  
**修复方案**  
- 指定host配置使代理服务仅对127.0.0.1或可信网络环境开放  
  
  
- 指定以下配置增加高复杂度鉴权密钥：  
  
master_key/LITELLM_MASTER_KEY   
  
  
- 涉及Proxy服务使用场景时，升级LiteLLM至1.83.0或更高版本  
  
**参考链接**  
  
```
https://github.com/BerriAI/litellm/pull/24625
https://github.com/BerriAI/litellm/commit/9c826d25a0e73f6e62c0d9665cd787b2ed3b92b7
https://github.com/BerriAI/litellm/security/advisories/GHSA-53mr-6c8q-9789
https://www.x41-dsec.de/lab/advisories/x41-2026-001-litellm/
https://docs.litellm.ai/
```  
  
  
