# Synology Cloudflare DDNS 脚本 📜

这个脚本是用来把 [Cloudflare](https://www.cloudflare.com/) 作为 DDNS 添加到 [群晖](https://www.synology.com/) NAS 上的。脚本使用了 Cloudflare API v4。

## 如何使用

### 通过 SSH 访问 NAS

1. 登录到你的 DSM
2. 转到 控制面板 > 终端机和 SNMP > 启动 SSH 服务
3. 使用你的客户端通过 SSH 访问 NAS。
4. 使用你的 NAS 管理员帐户进行连接。

### 在 NAS 中运行命令

1. 从本仓库下载 `cloudflareddns.sh` 到 `/usr/syno/bin/ddns/cloudflareddns.sh`

```
wget https://raw.githubusercontent.com/oXnMe/SynologyCloudflareDDNS/master/cloudflareddns.sh -O /usr/syno/bin/ddns/cloudflareddns.sh
```

2. 赋予执行权限

```
chmod +x /usr/syno/bin/ddns/cloudflareddns.sh
```

3. 添加 `cloudflareddns.sh` 到 DDNS 列表

由于 DSM 7.0 的改动，需要先转到 控制面板 > 外部访问 > DDNS 中自定义供应商如下：
服务供应商 `Cloudflare`，Query URL `https://www.cloudflare.com`
再修改配置文件：
`vi /etc/ddns_provider.conf`
把 `[USER_Cloudflare]` 下的 `modulepath=DynDNS` 修改为 `modulepath=/usr/syno/bin/ddns/cloudflare.sh`

### 获取 Cloudflare 参数

1. 转到 Cloudflare 域名概览页面复制区域 ID。
2. 转到 我的个人资料 > **API 令牌** > **创建令牌**，需要 `区域 > DNS > 编辑` 权限，复制 API 令牌。

### 设置 DDNS

1. 登录到你的 DSM
2. 转到 控制面板 > 外部访问 > DDNS 新增
3. 输入以下内容：
   - 服务供应商：`*Cloudflare`
   - 主机名称：`www.example.com`
   - 用户名/电子邮件：`<区域 ID>`
   - 密码/密钥：`<API 令牌>`

### 关于跟原版的区别

由于在路由上配置了透明代理，而群晖的 IP 检测服务时不时会抽风，导致获取公网 IP 会获取到代理的 IP，所以在脚本里额外加了一层公网 IP 的判断。

在升级到 DSM 7.0 之后，按照之前的修改方法并不会在 DDNS 的服务商列表中看到添加的 Cloudflare，所以在说明中修改为我尝试后可用的方法。
