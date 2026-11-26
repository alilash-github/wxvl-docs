#  真的很j肋吗？五个 CVE 打穿一台 GeoServer：未认证 RCE 全链路拆解【附EXP】  
原创 FL_Clover
                    FL_Clover  网络安全007   2026-11-25 03:20  
  
总结就一句话：**不用任何账号密码，仅靠一个默认开放的 WFS 接口，就能在 GeoServer 上以进程权限（容器镜像里常常是 root）执行任意系统命令。**  
 这条攻击路径不是某一个漏洞，而是五个各自独立、编号不同的CVE「环环相扣」拼出来的组合拳——只补其中一环，等于没补。  
  
如果你只想知道结论，先看下面这张流程图，后面再逐环拆开讲。  
  
![flow_chain](https://mmbiz.qpic.cn/sz_mmbiz_png/IGws6hSXN0xBT07cUH19YUuXBEw3VEEApmnnUiaaFXGia41Cf3xWSa8MmVibRcnaKEKUDPniaPMiaGxicZYiaCl6fAN8aTyJFW5FlxAeoD7YLVfTZc/640?wx_fmt=png&from=appmsg "")  
  
图 1：GeoServer 未认证 RCE——五个 CVE 环环相扣的利用链  
  
一、为什么值得盯：GeoServer 是 GIS 世界的「命门」  
  
GeoServer 是基于 Java 的开源地理空间数据服务器，对外发布 WMS、WFS、WCS 等 OGC 标准服务，**承载着政务、测绘、自然资源、水利、交通等行业 GIS 平台的图层与空间数据**  
。它的部署面非常广，而且大量实例是「照着官方文档一键起」的默认配置——默认开放的 WFS、默认的用户服务、默认的 remember-me 签名密钥 geoserver  
。  
  
**「默认即不安全」这句话在这次漏洞链上体现得淋漓尽致。**  
 整条链路的利用成熟度已经到「已复现、有 exp」的程度，暴露面向公网的 GeoServer 需要按**高危、需立即处置**  
来对待。  
<table><thead><tr><th style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(30, 58, 95);color: rgb(255, 255, 255);text-align: left;"><section><span leaf="">项目</span></section></th><th style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(30, 58, 95);color: rgb(255, 255, 255);text-align: left;"><section><span leaf="">内容</span></section></th></tr></thead><tbody><tr><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(247, 249, 252);font-weight: 700;color: rgb(44, 62, 80);vertical-align: top;"><section><span leaf="">漏洞类型</span></section></td><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;color: rgb(63, 63, 63);vertical-align: top;"><section><span leaf="">预认证远程代码执行（Pre-Auth RCE）</span></section></td></tr><tr><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(247, 249, 252);font-weight: 700;color: rgb(44, 62, 80);vertical-align: top;"><section><span leaf="">利用方式</span></section></td><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;color: rgb(63, 63, 63);vertical-align: top;"><section><span leaf="">五个 CVE 串联（WFS → XInclude → Cookie 伪造 → HSQLDB 反序列化）</span></section></td></tr><tr><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(247, 249, 252);font-weight: 700;color: rgb(44, 62, 80);vertical-align: top;"><section><span leaf="">影响版本</span></section></td><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;color: rgb(63, 63, 63);vertical-align: top;"><code style="background: rgb(242, 244, 248);color: rgb(192, 57, 43);padding: 1px 5px;border-radius: 3px;font-family: Menlo, Consolas, monospace;font-size: 13px;"><span leaf="">≤ 2.27.6</span></code><section><span leaf="">、</span><code style="background: rgb(242, 244, 248);color: rgb(192, 57, 43);padding: 1px 5px;border-radius: 3px;font-family: Menlo, Consolas, monospace;font-size: 13px;"><span leaf="">2.28.0–2.28.5</span></code><span leaf="">、</span><code style="background: rgb(242, 244, 248);color: rgb(192, 57, 43);padding: 1px 5px;border-radius: 3px;font-family: Menlo, Consolas, monospace;font-size: 13px;"><span leaf="">3.0.0–3.0.1</span></code></section></td></tr><tr><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(247, 249, 252);font-weight: 700;color: rgb(44, 62, 80);vertical-align: top;"><section><span leaf="">修复版本</span></section></td><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;color: rgb(63, 63, 63);vertical-align: top;"><code style="background: rgb(242, 244, 248);color: rgb(192, 57, 43);padding: 1px 5px;border-radius: 3px;font-family: Menlo, Consolas, monospace;font-size: 13px;"><span leaf="">2.27.7</span></code><section><span leaf="">、</span><code style="background: rgb(242, 244, 248);color: rgb(192, 57, 43);padding: 1px 5px;border-radius: 3px;font-family: Menlo, Consolas, monospace;font-size: 13px;"><span leaf="">2.28.6</span></code><span leaf="">、</span><code style="background: rgb(242, 244, 248);color: rgb(192, 57, 43);padding: 1px 5px;border-radius: 3px;font-family: Menlo, Consolas, monospace;font-size: 13px;"><span leaf="">3.0.2</span></code><span leaf=""> 及对应分支更高版本</span></section></td></tr><tr><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(247, 249, 252);font-weight: 700;color: rgb(44, 62, 80);vertical-align: top;"><section><span leaf="">权限要求</span></section></td><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;color: rgb(63, 63, 63);vertical-align: top;"><strong style="font-weight: 700;color: rgb(192, 57, 43);"><span leaf="">无需任何凭据（匿名）</span></strong></td></tr><tr><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(247, 249, 252);font-weight: 700;color: rgb(44, 62, 80);vertical-align: top;"><section><span leaf="">用户交互</span></section></td><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;color: rgb(63, 63, 63);vertical-align: top;"><section><span leaf="">无需</span></section></td></tr><tr><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(247, 249, 252);font-weight: 700;color: rgb(44, 62, 80);vertical-align: top;"><section><span leaf="">报告 CVSS</span></section></td><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;color: rgb(63, 63, 63);vertical-align: top;"><strong style="font-weight: 700;color: rgb(192, 57, 43);"><span leaf="">9.8（Critical）</span></strong></td></tr><tr><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;background: rgb(247, 249, 252);font-weight: 700;color: rgb(44, 62, 80);vertical-align: top;"><section><span leaf="">默认配置可触发</span></section></td><td style="border: 1px solid rgb(208, 215, 222);padding: 9px 10px;color: rgb(63, 63, 63);vertical-align: top;"><section><span leaf="">是</span></section></td></tr></tbody></table>  
  
二、组合拳的本质：「四处铺路，一处致命」  
  
单看这五个 CVE，每一个「单独拎出来」都只是中低危的信息泄露或权限绕过，但把它们串起来就质变了：  
  
• 前四处负责把「匿名」一步步抬到「管理员」，并读出足以伪造身份的材料；  
  
• 第五处（反序列化 RCE）本来需要管理员权限才能投递数据——**而第四处伪造出来的管理员会话，恰好把这块拼图补上了。**  
  
这就是为什么按「五个独立可修复步骤」分别给了编号，却共享同一批修复版本；也是为什么**只打一个补丁消除不了风险**  
的原因。  
  
三、逐环拆解：技术要点讲透  
  
下面按攻击顺序，把每一环「错在哪、怎么被触发」讲到代码/协议级别。  
  
① WFS 异常泄露数据目录（CVE-2026-129244）  
  
GetFeature  
 请求里的 storedQuery_id  
 参数会进入 StoredQueryProvider.getStoredQuery()  
。关键缺陷在 toFilename()  
：它只做了「清理特殊字符 + 拼 .xml  
」，**却没有对 ID 长度做任何限制**  
。  
  
于是攻击者构造一个超长 ID，底层 Files.readAttributes()  
 会抛出文件系统异常（路径过长），而 getStoredQuery()  
 保留了底层异常继续往上抛，OWS 服务又把整条异常链原样写进了公开响应。响应里那个绝对路径，把尾部的 /wfs/query/.xml  
 去掉，剩下的就是**数据目录（data directory）的绝对路径**  
。  
  
这一步的价值：拿到了后续所有「本地文件读取」的绝对路径基座。  
  
② 服务名不一致绕过操作鉴权（CVE-2026-129243）  
  
GeoServer 的 services.properties  
 里明确只允许**管理员**  
执行 wfs.CreateStoredQuery  
（创建存储查询）。但这里存在一个「解析口径不一致」的经典鉴权绕过：  
  
• Dispatcher.findService()  
 会把 x/WFS  
 这种带路径分隔符的服务名**截断解析**  
成 WFS  
，从而找到真实的 WFS 服务并继续执行；  
  
• 但请求对象里的 service  
 字段仍然是原始值 x/WFS  
；  
  
• OperationSecurityCallback  
 在匹配权限规则时用的是这个**原始值**  
，于是 x/WFS  
 无法命中针对 wfs.CreateStoredQuery  
 的规则——**鉴权被绕过，匿名请求也能写入 stored query。**  
  
一句话：路由用的是「解析后的名字」，鉴权用的是「原始的名字」，两边对不上，门就开了。  
  
③ 二次解析触发 XInclude 本地文件读取（CVE-2026-120705）  
  
这是整条链里技术含量最高、也最「优雅」的一环。  
  
攻击者写入的 stored query 里，放的是一段**被转义的 xi:include 文本**  
（即字面量形式的 XML，而不是直接给元素）。处理流程是这样的：  
  
• 1. 请求 XML 在解码阶段，CopyingHandler.characters()  
 把 <  
、>  
 这类被转义的字符**又原样写进了缓冲区**  
；  
  
• 2. QueryExpressionTextTypeBinding.encode()  
 会**用 SAX 把这段查询文本再解析一次**  
——转义文本因此「变活了」，成了真正的 xi:include  
 元素，并被落盘；  
  
• 3. 后续 DescribeStoredQueries  
 调用 StoredQueryProvider.parseStoredQuery()  
 重新加载该文件时，**parser 开启了 XInclude，且实体解析器允许本地 file: 协议**  
。  
  
结果：XInclude 会把本地文件内容直接内联进响应返回。攻击者借此读到两个致命文件：  
  
• security/config.xml  
 → **remember-me 签名密钥**  
（默认值就是 geoserver  
）；  
  
• security/usergroup/default/users.xml  
 → **admin 的口令摘要（digest）**  
。  
  
「一段文本被解析成 XML，XML 又被再解析一次」——二次解析 + 允许 file 协议，就是这个漏洞的根。  
  
④ 伪造 remember-me 管理员会话（CVE-2026-129245）  
  
拿到 key 和口令摘要后，**根本不需要破解密码**  
。Spring Security 的 remember-me 签名是这样算的：  
```
SHA256(username : expiry : password-digest : key)
```  
  
其中 username 为 admin@default  
。攻击者用泄露到的 password-digest  
 和 key  
 反推签名，再把 username、expiry、算法标识、签名编码成 remember-me cookie。认证过滤器验签通过，直接下发一个**管理员的 JSESSIONID**  
。  
  
根因是 remember-me 密钥**从数据目录读取且沿用了固定默认值 geoserver**  
——一旦密钥可预测、又能读到口令摘要，伪造会话就是水到渠成。  
  
⑤ JDBC 用户服务反序列化执行命令（CVE-2026-129242）  
  
有了管理员会话，就打开了 REST API 这扇门。攻击者通过管理员 REST 接口：  
  
• 把一个**恶意构造的 HSQLDB 数据库文件**  
写入数据目录；  
  
• 创建一个指向它的 **JDBC 用户服务**  
。  
  
之后，当系统查询用户组时，JDBCUserGroupService.getUserByUsername()  
 会对 user_props.propvalue  
 列调用 ResultSet.getObject()  
。该列类型是 OTHER  
，HSQLDB 驱动会走 JavaObjectData.getObject()  
 → InOutUtil.deserialize()  
 → **ObjectInputStream.readObject()**  
——一个数据库里的字段值，就这样被当成 Java 对象反序列化执行了。  
  
反序列化利用链（gadget）大致如下：  
```
PriorityQueue.readObject -> BeanComparator.compare -> TemplatesImpl.getOutputProperties -> CommandTranslet.
-> ProcessBuilder("/bin/sh", "-c", command)
```  
  
命令以 **GeoServer 进程权限**  
运行；官方容器镜像里进程通常是 root，于是直接 uid=0(root)  
。  
  
下面这张时序图，把「匿名请求如何一步步在各组件间流转、在哪一步读到 key、在哪一步落盘、在哪一步执行命令」画在了一起：  
  
![flow_seq](https://mmbiz.qpic.cn/sz_mmbiz_png/IGws6hSXN0wcK2VLmd4g6W0qQfWUxTJpzcuZowdibib1PTictQGibNs38GqFkFoGqcPxW5ARxklpkmVawrP0zx8JIiaTMiaTW1oJPy1Fia77Q1hGLo/640?wx_fmt=png&from=appmsg "")  
  
图 2：匿名请求在 GeoServer 各组件间的流转时序  
  
四、复现与 exp 使用（仅供授权测试）  
  
git社区中已有可用的 exp（Gitee：mhtsec/geoserver-preauth-rce  
，以及 GitHub 上的 geoserver-unauth-rce-poc  
）。**使用条件**  
：  
  
• Linux 目标，WFS 与 REST 接口可达；  
  
• default  
 用户服务中的 admin  
 已启用；  
  
• 目标为上述受影响版本、且大致是默认配置。  
  
典型调用形态（示意）：  
```
python3 -m pip install requests # 执行单条命令 
python3 exp.py -u http://TARGET/geoserver -c id # 执行多条命令 
python3 exp.py -u http://TARGET/geoserver -c 'id; whoami' # 自签 HTTPS + 超时控制 
python3 exp.py -k --timeout 60 -u https://TARGET/geoserver -c id
```  
  
【风险提示】以上仅用于**你拥有资产或已获得书面授权**  
的渗透测试与应急响应复现。未授权测试他人系统属违法行为。  
  
五、修复与加固清单  
  
1）升级（根治，唯一可靠方式）  
  
按分支升级到对应安全版本：  
  
• 2.27.x  
 → **2.27.7**  
  
• 2.28.x  
 → **2.28.6**  
  
• 3.0.x  
 → **3.0.2**  
  
官方下载入口：https://geoserver.org/download/ 。升级前确认所用扩展与数据目录兼容性。**因为是多缺陷链路，只更新单个组件 / 只修一环无效。**  
  
2）临时缓解（升级前的止血，只能堵入口，不能除根）  
  
• **收紧 WFS 对外暴露**  
：仅允许可信来源访问，能走内网就不走公网；  
  
• 对匿名访问**启用统一身份认证与访问控制**  
；  
  
• **关闭非必要的存储查询（stored query）功能**  
；  
  
• 限制**管理界面、REST 接口、用户服务**  
的访问来源（这直接切断了第⑤步的投递通道）。  
  
要点：这些措施只阻断链路入口，**无法消除配置泄露、会话伪造、反序列化本身的风险**  
，必须与升级同步推进；且可能影响依赖匿名 WFS 的业务，升级后及时回收临时策略。  
  
3）应急排查建议（防守方视角）  
  
• **资产测绘**  
：先把手上所有对外 GeoServer（含 FOFA/鹰图上的 product="GeoServer"  
、标题特征、/geoserver/web/  
 指纹）拉清单，比对版本号；  
  
• **看数据目录有没有异常**  
：wfs/query/  
 下是否出现来路不明的 .xml  
，是否被创建过指向本地 .data/.script  
 的 JDBC 用户服务、是否出现可疑 HSQLDB 库文件；  
  
• **看会话与日志**  
：是否有异常 remember-me cookie 换取管理员 JSESSIONID  
 的痕迹；  
  
• **看进程落地**  
：GeoServer 进程（尤其 root）下是否派生过可疑 /bin/sh -c  
 子进程；  
  
• 命中疑似迹象，按「已入侵」处置，而非「已修补」了事。  
  
  
参考来源  
  
[1]   
https://cryptocat.me/blog/research/analysis/geoserver_preauth_rce/  
  
[2]   
https://gitee.com/mhtsec/geoserver-preauth-rce  
  
[3] https://github.com/geoserver/geoserver/releases  
  
[4]  
https://github.com/Crypto-Cat/geoserver-unauth-rce-poc  
  
  
**免责声明：**  
  
   
本文章仅做网络安全技术研究使用！另利用网络安全007公众号所提供的所有信息进行违法犯罪或造成任何后果及损失，均由**使用者自身承担负责**  
，与网络安全007公众号**无任何关系**  
，也不为其负任何责任，**请各位自重！**  
公众号发表的一切文章如有侵权烦请私信联系告知，我们会立即删除并对您表达最诚挚的歉意！感谢您的理解！**让我们一起为中国网络安全事业尽一份自己的绵薄之力！**  
  
  
---推荐阅读---  
  
[攻防演习系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4480577090483748870#wechat_redirect)  
  
  
[技术文章系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4483666897053253633#wechat_redirect)  
  
  
[未授权漏洞系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4483456618323345413#wechat_redirect)  
  
  
[HW专项系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4483461171911426059#wechat_redirect)  
  
  
[应急响应系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=2735815599062548484#wechat_redirect)  
  
  
[工具推荐系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4483471065368592394#wechat_redirect)  
  
  
[漏洞通告系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4728706414628405252#wechat_redirect)  
  
  
  
写作不易，分享快乐  
  
期待你的 **分享**  
●**点赞●在看●关注●收藏**  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/IGws6hSXN0yC4UvVZTh0GWblmLs0dtN1Sfnf88e3vkpokovgdsQAfPI16CnM3C7S6uNVNGHtnsiaFU1via2Bibo92ria29FVIMstgj6wQDg9XbI/640?wx_fmt=png&from=appmsg "")  
  
  
  
