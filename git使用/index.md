+++
date = '2025-05-22T17:20:11+08:00'
draft = true
title = 'Git使用'
math = ''
license = ""
description = ""
categories = [
    "开发工具使用"
]
tags = [
    ""
]
image = ""

+++



## 问题解决
### git push 
#### fatal: unable to access ‘https://github.com/.../.git‘: Could not resolve host: github.com
```
git config --global --unset http.proxy   
git config --global --unset https.proxy    
```

解决方案：cmd下命令执行 ipconfig/flushdns
清理dns缓存

### Git 配置代理，解决 "Failed to connect to github.com port 443" 及 "Recv failure: Connection was reset" 等问题

确认你的代理程序提供的端口，一种常见的情况是 7890

```bash
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
```

配置完成后，可以使用以下命令查看并修改 Git 的配置：

```bash
git config --global --edit
```
