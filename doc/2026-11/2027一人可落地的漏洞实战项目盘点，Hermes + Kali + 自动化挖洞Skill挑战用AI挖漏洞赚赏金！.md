#  2027一人可落地的漏洞实战项目盘点，Hermes + Kali + 自动化挖洞Skill挑战用AI挖漏洞赚赏金！  
强哥
                    强哥  网络漏洞侦探   2026-11-09 10:32  
  
一个人、一台笔记本、一个 AI Agent，能不能把"信息收集 → 漏洞排查 → 人工复现 → 报告提交"整条链路跑通？  
本文用一次AI挖漏洞的实战经历，带大家把AI辅助挖洞的四个阶段拆开讲清楚：架构怎么搭、提示词怎么写、误报怎么排、报告怎么交。  
  
  
  
PART 01  
  
2027 年，为什么"一个人"也能挖洞赚赏金  
  
漏洞挖掘过去长期是"团队活"：收集一个人、测试一个人、写报告再一个人。但从 2025 年起，这个成本结构被改写了——改写它的不是新漏洞，而是 **Agent 的工作方式**  
。大模型不再只是聊天框，它能读文件、调工具、远程执行命令、按固定方法论往下走，原本三人小组的分工被压成了一条**单人流水线**  
。  
  
2027 年，一个人可落地的挖漏洞赚赏金大致可以分为这四类：  
  
<table><thead><tr><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">项目类型</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">典型场景</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">单人可行性</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">收益结构</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">主要门槛</span></section></th></tr></thead><tbody><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">SRC 公开众测</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">补天、漏洞盒子公开项目</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">高</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">单洞计费，中低危为主</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">需要稳定节奏</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">企业专属 SRC</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">厂商自建响应中心</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">中</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">高危单价高，审核严</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">需要规范报告</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">业务逻辑漏洞专项</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">电商、SaaS、会员/积分体系</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">高</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">中高危为主，误报率低</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">需要懂业务流程</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">工具与 Skill 建设</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">自建挖洞知识库、Agent 工作流</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">高</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">效率倍增（间接收益）</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">需要沉淀方法论</span></section></td></tr></tbody></table>  
  
其中**"业务逻辑漏洞 + 自建 Skill 工作流"是单人最容易起量的组合**  
：这类漏洞不吃 0day、不吃逆向，吃的是对业务流程的理解；AI 恰好擅长把一条下单流程拆成十几个接口，逐个问"这里有没有校验"；方法论沉淀成 Skill 后，第二三次成本几乎为零。  
  
本文拆的就是这条流水线：**Hermes 当大脑，40 个 Skill 当方法论，Kali 当手脚，挖实际案例中的业务逻辑漏洞。**  
  
  
PART 02  
  
开工前的两件准备工作  
  
▍  
2.1 注册正规 SRC / 应急响应平台账号  
  
挖到漏洞只是"生产"，能不能变现取决于有没有合规出口。新手建议从**补天**  
和**漏洞盒子**  
起步：项目多、审核成熟、中低危也有明确收录标准。  
  
三点注意：实名认证必须做，否则无法提交与结算；先读平台规则再看漏洞，各平台的"可收录范围"完全不同；**只打平台明确授权的目标**  
——授权范围是红线，不是建议。  
  
▍  
2.2 本地部署 AI 自动化挖洞知识库  
  
这是整套流程的弹药库。所谓知识库，本质是一批**结构化的 Skill 文件**  
：每个 Skill 描述一件事该怎么做——读什么、查什么、按什么顺序查、输出成什么格式。  
  
本次实战用到 **40 个 Skill**  
：  
  
<table><thead><tr><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">Skill 分组</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">编号</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">负责什么</span></section></th></tr></thead><tbody><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">信息收集类</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">01–10</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">子域、端口、指纹、接口枚举</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">漏洞排查类</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">11–30</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">越权、逻辑、注入、文件类问题</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">漏洞验证类</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">31–39</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">请求构造、单点复现、结果判定</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">报告生成类</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">40</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">按 SRC 规范产出报告</span></section></td></tr></tbody></table>  
  
部署完成后，Skill 目录就是 Agent 唯一的方法论来源：**Agent 不靠"猜"决定下一步，而是读对应 Skill 文件，按写好的流程执行。**  
  
  
PART 03  
  
三件套架构：Agent 负责想，Skill 负责记，Kali 负责打  
  
整套体系只有三个组件，边界必须划清，否则流程会乱。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/CkwxvMGmWhefrlVhbic8ias2TLS8k3QI6wCtISPNwJAEg2C6Qo1pESNiaTTAorCUyicBMRwjtNfLQrGOdG7AK9JWUeicnibfv2GeaY047VYCS14wM/640?wx_fmt=png&from=appmsg "")  
  
图 1 · 决策层、知识层、执行层三方分工  
  
**Hermes 是决策层**  
：理解任务、判断阶段、决定调用哪个 Skill，不直接碰目标系统。  
  
**Skill 集是知识层**  
：把"下一步干什么"写成可执行文档——收集怎么做、越权怎么测、报告写哪几部分。  
  
**Kali 是执行层**  
：预装常用工具，需要执行命令、发请求时由 Hermes 调度完成。  
  
为什么必须分层？混在一起会失控：让大模型凭感觉测站点，它会漏、会重复、会越走越偏；全塞进一个对话框，上下文一长就丢细节。  
  
  
PART 04  
  
阶段一：环境自检，不要跳过  
  
环境就绪后**不能马上开挖**  
。后面每个阶段都要同时用到技能库、Kali、靶场，任一环节出问题，都会让后续操作变成"看起来跑了、其实没跑"。所以第一轮任务是自检，不是挖洞。  
  
一条能用的提示词必须回答四个问题：你是谁、环境是什么、这轮干什么、结果怎么反馈。  
  
【角色与授权】  
你是一名资深 Web 安全工程师。本次测试目标已获得书面授权，  
测试范围仅限下列目标地址及其子路径。禁止对范围外资产发起任何请求。  
   
【基本信息】  
目标地址：http://target.example.com  
测试账号：test_user / ********  
技能库目录：/skills/web-vuln  
Kali 工具机：192.168.x.x，账号 root / ********  
   
【本轮任务】  
1. 确认技能库目录存在，且内部 skill 文件可正常读取；  
2. 确认 Kali 工具机可正常连接，并可远程执行命令；  
3. 确认目标站点可正常访问（HTTP 200，页面渲染正常）；  
4. 使用提供的测试账号登录，确认账号可用、会话正常。  
   
【输出要求】  
用表格输出环境检查情况，列出：检查项 / 检查结果 / 判断依据。  
任一项异常时，明确指出异常点与建议修复方向。  
  
四段各有用处：**角色与授权**  
划定行为边界；**基本信息**  
一次性交代要连的东西，避免多轮追问；**本轮任务**  
是四项**可验证**  
的检查，而非"看看正常不正常"；**输出要求**  
让结论可直接对照。  
  
<table><thead><tr><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">检查项</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">结果</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">判断依据</span></section></th></tr></thead><tbody><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">技能库可读</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">通过</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">目录存在，40 个 skill 文件均可解析</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">Kali 可连</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">通过</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">远程命令返回正常，工具链可用</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">靶场可访问</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">通过</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">首页 HTTP 200，静态资源完整</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">测试账号可登录</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">通过</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">登录成功，会话 Cookie 正常下发</span></section></td></tr></tbody></table>  
  
![](https://mmbiz.qpic.cn/mmbiz_png/CkwxvMGmWhdiciam4ibqJA1De64gPdsF5eZXw5xiccpg0ibPykqIiaYn8dmIZ95HnpyEKT1wCyKpR4wDXia3k30dvpBFbUllcSCuGVVSG1FwS1IpW4/640?wx_fmt=png&from=appmsg "")  
  
图 2 · 四项环境自检与对应排障方向  
  
这一步的价值不在"检查"，而在**建立可信基线**  
——后面所有异常结果都能拿它对比：是漏洞，还是环境问题。  
  
  
PART 05  
  
阶段二：接口收集 → 业务逻辑漏洞排查  
  
目标站点是**提供云盘会员购买服务的平台**  
，核心业务三步：选套餐 → 创建订单 → 完成支付。围绕这三步有三个可疑点：会员是否限购，限购在前端还是服务端？订单金额能否被改，价格是前端传还是服务端算？支付状态有没有校验？  
  
动手前先立一条原则：**不要一上来对着页面盲测**  
。页面上每次点击背后都是一次接口调用，先把接口理清，才知道该在哪下手。  
  
▍  
5.1 两段式提示词：先收集，再排查  
  
【第一部分：接口收集】  
读取 04 号技能（接口信息收集），按技能流程收集目标站点接口。  
重点梳理：VIP 购买、订单创建、支付相关接口。  
   
【第二部分：逻辑漏洞排查】  
完成接口收集后，读取 25 号技能（逻辑漏洞排查）。  
注意：不要重新从头分析网站，而是结合第一部分已收集的接口信息，  
围绕 VIP 购买、下单、支付流程，逐一排查业务逻辑问题。  
   
【输出要求】  
1. 接口清单：表格输出 接口功能 / 接口地址 / 请求方法 / 关键参数；  
2. 漏洞情况：若发现，逐条列出 漏洞名称 / 涉及接口 / 关键参数 /  
   漏洞类型 / 漏洞详情 / 实际危害；若未发现，明确说明"本轮未发现漏洞"。  
  
有个容易忽略的细节：**"不要重新从头分析网站"**  
。不写这句，Agent 会在第二阶段重爬站点、丢掉第一阶段的接口清单。**多阶段任务里，最贵的成本是上下文丢失。**  
  
▍  
5.2 接口清单就是攻击面地图  
  
<table><thead><tr><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">接口功能</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">接口地址</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">方法</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">关键参数</span></section></th></tr></thead><tbody><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">用户登录</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">/api/user/login</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">POST</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">username, password</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">产品查询</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">/api/vip/products</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">GET</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">type</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">订单创建</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">/api/order/create</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">POST</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">product_id, quantity, price</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">订单列表</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">/api/order/list</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">GET</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">status, page</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">合并支付</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">/api/pay/merge</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">POST</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">order_ids</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">会员信息</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">/api/user/vip</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">GET</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">—</span></section></td></tr></tbody></table>  
  
看到"订单创建"接口里带着   
price  
 参数，第一反应就该是：这个价格是服务端算的，还是客户端传的？  
  
▍  
5.3 发现的漏洞：体验价限购校验缺失  
  
**体验价每人限购一次，服务端校验缺失。**  
  
平台规则写明"新用户专享体验价会员，每账号限购一次"。实测中第一次购买正常创建了一笔 **0.1 元**  
订单；等几秒再次提交，**仍能创建新的体验价订单**  
——"只能买一次"只存在于前端提示，服务端没有任何拦截。  
  
按结果推算：3 笔 0.1 元订单合并支付 0.3 元，得到 3 个月会员；正常价约 25 元/月，**实际成本只有正常价的 1/250**  
。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/CkwxvMGmWhfxMojykTTVSlbn9AjBRl2vmpAEic8uTC9P5o6Yu2C4ibvKXLc4ZVysAPCE9zMsiaicbkLVGiaJPB1IkYt9vZ5EMZtDicdKZAuzTTOJg/640?wx_fmt=png&from=appmsg "")  
  
图 3 · 体验价限购校验缺失的触发路径  
  
漏洞技术含量不高，但**危害判断需要业务理解**  
：不是"少收几毛钱"，而是限购整体失效、促销预算被批量套利、会员收入被系统性稀释。这类问题在 SRC 里属典型中危业务逻辑漏洞，报告写得好通常能收录。  
  
  
PART 06  
  
阶段三：Burp Suite 人工复现——AI 说成立，不算成立  
  
**AI 找到漏洞，不等于漏洞成立。**  
 自动化一定有误报：AI 的描述是基于接口参数和 Skill 判定规则推出来的，**不一定符合真实行为**  
——看到   
price  
 可改就判"金额可篡改"，实际服务端有二次校验；看到重复下单成功就判"限购失效"，实际订单只是待支付、支付环节还会拦。所以第三阶段唯一目的就是：**用人工操作把结论完整复现一遍。**  
  
新手常卡在"知道漏洞是什么，但不知道从哪下手"，可以让 AI 先把复现过程整理成方案：  
  
【本轮任务】  
针对上一轮发现的"体验价限购校验缺失"漏洞，  
整理一份使用 Burp Suite 进行人工复现的详细方案。  
   
【方案要求】  
按实际操作顺序拆分为具体步骤，每步说明：  
1. 需要做什么；  
2. 应拦截哪个请求（接口地址 + 方法）；  
3. 需要关注哪些请求参数；  
4. 发送后预期出现什么响应与页面现象。  
   
【判定要求】  
给出漏洞成立的判断依据，便于逐条对照实际操作结果。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/CkwxvMGmWhc08k0pM3qo9Vo7HOr05gw6dsWr43tVdhXKSDRq3yJ00oibNbyyQvbiaunFu9a3pDsIzszRicasBtRRYiaxia2TFCYYwmajUdT7JKrw/640?wx_fmt=png&from=appmsg "")  
  
图 4 · Burp Suite 人工复现的请求时序  
  
实际操作顺序：  
  
1.  
 启动 Burp 内置浏览器，访问站点并用测试账号登录；  
  
2.  
 进会员购买页找到"新用户专享体验价月卡"，**按正常流程完成第一笔下单**  
，状态为待支付；  
  
3.  
 切回 Burp 的 **HTTP history**  
，**对照接口清单**  
按地址与参数定位下单请求——这正是那张清单的真正用处；  
  
4.  
 右键 **Send to Repeater**  
，确认地址、方法、请求头、参数完整后点 **Send**  
，服务端**再次返回"订单创建成功"**  
；  
  
5.  
 **重复 Send**  
，本次累计创建 **12 笔**  
待支付订单，勾选后**合并支付**  
，再进会员中心看到期时间——有效期**延长 12 个月**  
。  
  
到这一步先别写报告，对照三件事：  
  
<table><thead><tr><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">判定要素</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">本次结果</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">是否满足</span></section></th></tr></thead><tbody><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">限购规则是否被突破</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">同一账号创建 12 笔体验价订单</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">✅</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">突破是否带来实际收益</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">合并支付后会员延长 12 个月</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">✅</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">是否存在其他解释</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">无（非缓存、非前端展示错误）</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">✅</span></section></td></tr></tbody></table>  
  
三项全满足漏洞才算**成立**  
。任何一项打问号都要重测——只看到"订单创建成功"就下结论，很可能只是一条未支付脏数据。  
  
  
PART 07  
  
阶段四：报告生成——把"发现"变成"可结算的资产"  
  
找到漏洞后最难的往往不是技术而是**怎么写**  
。同样一个限购绕过，有人写出来是"中危已收录"，有人是"信息不足，不予处理"。这里不自己设计格式，直接让 Agent 读 **40 号技能（report_gen）**  
：  
  
【技能指定】  
读取技能库中 40 号技能（漏洞报告生成），  
严格按照该技能定义的报告结构与规范生成内容。  
   
【报告依据】  
本次发现的"体验价限购校验缺失"漏洞，  
已通过上一轮 Burp Suite 人工复现确认成立。  
请依据实际复现过程与验证结果整理报告，  
不得编造未经实际验证的信息、请求或响应。  
   
【输出要求】  
将报告保存为 Markdown 文件，便于直接查看与补充截图。  
  
第二条是**纪律条款**  
，必须写。复现步骤、请求响应、危害说明，每条都要有真实证据支撑。AI 可以组织语言、整理结构，但**不能替你把没测过的结果"补"上去**  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/CkwxvMGmWhc6mOqHvb8HCJBbewBjY84QYJJ2g8MiaefyaJyW5hAgibWIR6zsibYGZ289V3Own7QS8h9lVQw5JK5RicUhxMPow68BbChcb7t8icEA/640?wx_fmt=png&from=appmsg "")  
  
图 5 · 九段式漏洞报告结构  
  
<table><thead><tr><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">章节</span></section></th><th style="padding: 9px 8px;border: 1px solid rgb(20, 73, 107);background: rgb(20, 73, 107);color: rgb(255, 255, 255);font-size: 12.8px;font-weight: bold;text-align: left;line-height: 1.5;"><section><span leaf="">本次报告应包含的内容</span></section></th></tr></thead><tbody><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">标题</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">云盘会员平台体验价限购校验缺失导致低价批量开通 VIP</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">影响资产</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">会员购买与订单接口</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">漏洞类型</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">业务逻辑漏洞 / 限制绕过</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">危害等级</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">中危</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">漏洞描述</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">限购仅前端提示，服务端未校验账号已购记录</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">复现步骤</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">登录 → 正常下单 → Repeater 重放 N 次 → 合并支付 → 查有效期</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">请求与响应</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">下单请求原文、重放成功响应、会员有效期变化</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">危害说明</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(246, 249, 252);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">限购失效、促销预算被套利、会员收入被稀释</span></section></td></tr><tr><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">修复建议</span></section></td><td style="padding: 9px 8px;border: 1px solid rgb(227, 232, 239);background: rgb(255, 255, 255);font-size: 12.8px;color: rgb(63, 63, 63);line-height: 1.6;word-break: break-word;"><section><span leaf="">按账号+商品维度落库校验、下单接口幂等、支付回调二次核验</span></section></td></tr></tbody></table>  
  
初稿生成后还有两步必须人工做：**补截图**  
，把每步截图填进预留位，这是审核员判断"是否真实复现"的核心依据；**逐条核对**  
，描述、请求响应、复现结果逐字对照后再提交。  
  
  
PART 08  
  
AI 挖洞的四条红线  
  
工具越强越要讲边界，这套流程能跑通的前提是始终在授权范围内运行。  
  
**授权先行。**  
 只测平台明确授权的资产。厂商自有域名、第三方托管系统、云服务商基础设施处理方式完全不同，不确定就不碰。  
  
**最小影响。**  
 能下一单就不下十单。本次 12 笔 0.1 元订单已是"能证明危害"的上限，再多就是给业务方添麻烦。  
  
**不碰真实数据。**  
 只用自己注册的测试账号，不读取、不导出其他用户的订单与个人信息。业务逻辑漏洞最容易"顺手"越界。  
  
**报告不夸大。**  
 按实际影响写。能把 25 元月卡打成 0.1 元，就写"单价稀释 250 倍、限购失效"，不要写成"可无限获取会员导致平台破产"。  
  
  
PART 09  
  
90 天落地路线  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/CkwxvMGmWhebDyWiau29ElnA3Mb6MQnueeCv6iap89XtpkFWl9mm3OfI2p7Q8pXxFlolZ34aHon31oI10FpIU8ibIMwV5kx82osYpdx6xKibF4Y/640?wx_fmt=png&from=appmsg "")  
  
图 6 · 四阶段 90 天落地节奏  
  
**基础期**  
把规则读透、环境跑起来，规则不清后面全是返工；**跑通期**  
目标不是挖到洞，而是**把流程走完整**  
，报告合格、提交成功就算通；**产出期**  
先在公开众测用低危练手，再切专项突破；**沉淀期**  
把踩过的坑写回 Skill——**Skill 是会越用越准的资产**  
，第一次调提示词很费劲，第五次基本能复制粘贴。  
  
  
PART 10  
  
写在最后  
  
AI挖洞的技术门槛并不高。有价值的是**流程**  
：环境先自检建立基线，接口先梳理形成攻击面地图，AI 先排查保证不漏项，人工再复现把误报挡在报告之外，报告按规范出把发现变成可结算的资产。  
  
这五步里，AI 承担重复劳动，人承担判断责任。**这个分工，就是 2027 年一个人能挖洞赚赏金的核心逻辑。**  
  
工具迭代会比漏洞更快，今天用 Hermes + Skill + Kali，明天可能是别的组合，但"先收集、再排查、后验证、终报告"这条主线不会变——**变的只是执行它的人手够不够多，而现在，一个人就够了。**  
  
  
🎁 学习资料领取  
  
想系统入门网络安全、把上面这套流程真正跑起来，这里整理了一份配套资料包：  
  
✅ **AI 自动化挖洞知识库**  
（40 个 Skill 完整结构说明）  
  
✅ **四阶段提示词模板**  
（环境自检 / 接口收集 / 复现方案 / 报告生成）  
  
✅ **零基础网络安全入门视频教程**  
（从环境搭建到第一个漏洞）  
  
✅ **SRC 漏洞报告模板**  
（九段式，可直接套用）  
  
✅ **新手靶场清单与练习路线**  
  
**领取方式：关注公众号，后台回复关键词「01」，即可直接获取全部资料包。**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/CkwxvMGmWhdlPbZcuaRuCTDqSkRkuNksU4w8W6PcchXJnAGB4TrdXKDXomLrUicOYtzmxuoL4mDicmGS03EnjeABKxTzSSZzjC16M2p4LhmIA/640?wx_fmt=png&from=appmsg "")  
  
****  
⚠️ 所有技术请仅用于**已获得合法授权**  
的测试环境与 SRC 平台项目。未经授权对任何系统进行测试均属违法行为，本文内容仅供学习交流。  
  
  
