#  Web 漏洞扫描智能体 -- vulnscan-agent  
chenke0820
                    chenke0820  网络安全者   2026-11-25 03:04  
  
===================================  
  
**免责声明**  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，安全性自测，如有侵权请联系删除。  
个人微信：ivu123ivu  
  
  
一定要在虚拟机运行，工具下载链接：  
  
公众号后台回复：20261125  
  
链接仅一天有效，每日更新  
  
****  
一句话概括这个项目  
  
它不负责攻破，只负责把「哪里不合规」整理成一份能读的清单。  
  
最近看到一个定位很克制的项目：**vulnscan-agent**  
。它把自己描述为「面向 AI 编码智能体的 Web 漏洞扫描能力包」——不是一套独立的扫描器产品，而是一层可以被智能体调用的能力。  
  
最有意思的是它的克制：所有探测都是  
只读请求  
，不含写操作、脱库或提权逻辑；整个项目  
零第三方依赖  
，只用 Node.js 内置模块。我按 README 在本机把它跑了一遍，下面的截图都是真实运行的结果。  
  
01  
  
PART  
  
  
定位与架构：一套核心，两个入口  
  
ARCHITECTURE · 设计  
  
项目的核心是   
src/scanner.mjs  
。它上面挂了两个入口：一个是给智能体用的   
MCP stdio 服务  
，另一个是给 humans 用的   
Web 控制台  
。两者共用同一套扫描核心，所以行为完全一致——这是个很清醒的工程取舍。  
  
AI 智能体  
  
MCP 工具调用  
  
→  
  
扫描核心  
  
只读探测 + 归并  
  
→  
  
报告  
>  
  
md / txt / html  
  
浏览器走 HTTP API，智能体走 stdio JSON-RPC，底层同一份逻辑  
  
![](https://mmbiz.qpic.cn/mmbiz_png/PQNvx9ufMAhwVY20E2OzKx4P1pjdOnFhPnT1J4C8SpJr5ib85YphsMCwVLJq2OozN6icvibE8z54mcezshQy0OgtCB9oPgXAZ7Fnw5k85WHKhs/640?wx_fmt=png&from=appmsg "")  
  
— 本机实跑：对本地演示靶场的一次综合扫描  
  
.  
.  
.  
bash  
  
git clone https://github.com/chenke0820/vulnscan-agent.git  
  
cd vulnscan-agent  
  
npm run demo　　# 本地靶场 :8099  
  
npm run web　　 # 图形控制台 :7788  
  
默认只监听 127.0.0.1。演示靶场内含故意漏洞，仅限本地教学，不要部署到公网。  
  
02  
  
PART  
  
  
四条探测链路：各司其职  
  
CAPABILITIES · 能力  
  
能力被拆成四个边界清晰的工具。这种拆分的好处是：智能体可以按需要挑选，而不是每次都跑一整套重流程。  
  
scan_web · 综合体检  
  
状态码、标题、服务与框架指纹、缺失的安全响应头、Cookie 属性，外加一组常见敏感路径。适合作为入口处的第一眼扫描。  
  
http_probe · 基线探测  
  
只请求一个 URL，返回状态、标题、响应长度和全部响应头，用来建立对照基线。  
  
test_sqli · SQL 注入检测  
  
针对 GET 参数，结合数据库报错特征与布尔响应的长度差异做判定，并给出参数名、长度差与阈值作为证据。  
  
test_xss · 反射型检测  
  
注入唯一载荷并检查是否原样回显，报告里保留载荷原文与转义后的证据片段。  
  
README 也主动写清了局限：SQL 注入只覆盖报错型与布尔型的 GET 参数，不含时间盲注、POST 与二次注入；XSS 只覆盖反射型且要求原样回显。把「不做什么」写出来，本身就是一种诚实。  
  
03  
  
PART  
  
  
误报与证据：让结论可复核  
  
SIGNAL QUALITY · 信号质量  
  
扫描器最难的部分不是「发现」，而是「别乱报」。这个项目在两处做了明确的处理。  
  
软 404 识别  
  
有些服务器对任意路径都返回 200。代码会把响应体与首页基线做归一化比较，命中「站点创建成功 / Not Found / 页面不存在」等占位特征时判为软 404，  
不计入风险  
。403 则记录为「存在但受保护」。  
  
分级与建议  
  
发现项按   
严重 / 高危 / 中危 / 低危 / 提示  
 排列，每条附带证据和修复建议。比如敏感路径对应的是「移出 Web 根目录或返回 403/404」，安全响应头对应的是「在网关统一补齐 HSTS、CSP 等」——建议可以直接读，不用再翻译。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/PQNvx9ufMAgCEJoc4pAPwsEqMd3jiannVG3ddia5nWbRa40I605w8dxllmeT7ia11xQbk3XoTTPvUDvQHcKe8RiaIMjibqIDiajSrjuwEsItK9jPE/640?wx_fmt=png&from=appmsg "")  
  
— 本机实跑：问题清单、明细表与证据展开  
  
我这次对本地靶场跑出的结果是 5 项：高危 1（敏感路径可直接访问）、中危 1（缺失 5 个安全响应头）、低危 2（服务与框架指纹）、提示 1（robots.txt）。控制台会把每一项展开成「详情 — 证据 — 修复建议」三段。  
  
04  
  
PART  
  
  
报告与接入：从结果到交付  
  
DELIVERY · 交付  
  
扫描结果可以直接在控制台生成 Markdown、纯文本或 HTML 报告，按严重性排序，并写入   
reports/  
 目录，支持预览与下载。智能体那一侧则走 MCP：在配置里注册两个服务，就能让助手自己调用扫描与报告工具。  
  
.  
.  
.  
~/.pi/agent/mcp.json  
  
{  
  
　"mcpServers": {  
  
　　"vulnscan": {  
  
　　　"command": "node",  
  
　　　"args": ["<绝对路径>/mcp/vulnscan.mjs"]  
  
　　}  
  
　}  
  
}  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/PQNvx9ufMAgHvcMgINViakGlSLYqRccr107ak0ql7O1P3I9BXLf8hWv6BOWOmubiawhcg8yM7wP1w4AneZKz70Riazd2DGJibQJqNibnp9Kial3oQ/640?wx_fmt=png&from=appmsg "")  
  
— 仓库 README 官方截图：完整控制台界面  
  
✦ 一个小细节  
  
我用 stdio 向 MCP 服务发了一次   
tools/list  
，四个工具及参数说明都正常返回。报告目录之外的读写会被拒绝，避免路径穿越。  
  
///  
  
LAST  
  
  
写在最后：边界比能力更重要  
  
BOUNDARY · 边界  
  
这个项目的 README 里反复强调一件事：  
只对你拥有或已获得书面授权的目标扫描  
。默认监听回环地址、靶场不对外、报告目录做路径限制——这些设计把「克制」落实到了代码里，而不是停留在声明上。  
  
同时也要记得：自动化扫描无法替代人工测试。报告里的结论应结合业务上下文复核，尤其是软 404 与自定义错误页可能带来的偏差。  
  
工具的价值，在于让每一次判断都有据可查。  
  
****  
****  
****  
  
一定要在虚拟机运行，工具下载链接：  
  
公众号后台回复：20261125  
  
链接仅一天有效，每日更新  
  
  
  
**·****今 日 推 荐**  
**·**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/PQNvx9ufMAjTbFVUc2L0QNoH2BHrWSZRmpN4Tqqa41iahHwHsYMLSILvwmMmBibn9bBic96yEnxhCs1mmFsAo0CniaeXby22PPTBt01AKs1LXKQ/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  
  
