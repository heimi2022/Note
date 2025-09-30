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





## Failed to connect to github.com port 443 after 21090 ms: 

1. 使用VPN下解决方案(已验证可用)
   处于 VPN 环境下时，GitHub 连接失败往往是由于代理服务器与 Git 配置不一致导致的。具体表现为 Git 在尝试通过代理访问 GitHub 时失败，或者代理的端口不正确。以下是针对该情况的解决步骤：
   1. 打开 设置 > 网络与互联网 > 代理，找到代理设置，并记录当前代理端口。假设端口号为 1234。
   2. 配置git使用代理
      git config --global http.proxy http://127.0.0.1:1234
      git config --global https.proxy http://127.0.0.1:1234
   3. 验证代理设置是否生效
      git config --global -l
      这将列出当前的 Git 配置信息，确保其中的 http.proxy 和 https.proxy 设置为你刚刚配置的端口。
   4. 刷新 DNS 缓存
      windows:ipconfig /flushdns
      Mac:
          sudo dscacheutil -flushcache
          sudo killall -HUP mDNSResponder

2. 未使用VPN解决方案
   如果 Git 配置了代理，而你并不需要它，或者你的网络环境不适合使用代理，那么需要取消 Git 的代理设置。使用以下命令取消代理：
   1. 取消代理
      git config --global --unset http.proxy
      git config --global --unset https.proxy
   2. 检查代理是否已经被成功移除
      git config --global -l
      如果没有显示 http.proxy 和 https.proxy 相关的条目，说明代理已经被成功移除。
   3. 重试git操作
      取消代理设置后，重新执行 Git 操作，看看是否可以顺利连接到 GitHub。如果问题依然存在，建议检查本地网络连接，确保没有防火墙或其他网络配置阻止了端口 443 的访问。

