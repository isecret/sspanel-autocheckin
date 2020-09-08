# SSPanel 自动签到

![SSPanel_Auto_Checkin](https://github.com/isecret/sspanel-autocheckin/workflows/SSPanel_Auto_Checkin/badge.svg)

## 使用方法

### 方式一：Github Actions（推荐）

Fork 该仓库，进入仓库后点击 `Settings`，右侧栏点击 `Secrets`，点击 `New secret`。分别添加 `DOMAIN`、`USERNAME` 和 `PASSWD` 的值，对应为你的 `域名`、`用户名` 和 `密码`。

定时任务将于每天凌晨 `2:20` 分执行，如果需要修改请编辑 `.github/workflows/work.yaml` 中 `on.schedule.cron` 的值。

### 方式二：部署本地或服务器

脚本依赖：
- `jq` 安装命令: Ubuntu: `apt-get install jq`、CentOS: `yum install jq`、MacOS: `brew install jq`

克隆或下载仓库 `ssp-autocheckin.sh` 脚本，复制 `env.example` 为 `.env` 并修改配置。

```
cp env.example .env
vim .env
DOMAIN="https://****.best" # 域名
USERNAME="EMAIL" # 登录名
PASSWD="PASSWORD" # 密码
```

然后执行，签到成功后，即可添加定时任务。

```bash
bash /path/to/ssp-autocheckin.sh
[2020-08-24 14:05:18] "登录成功"
[2020-08-24 14:05:19] "获得了 59 MB流量"
```

如下：

```bash
24 10 * * * bash /path/to/ssp-autocheckin.sh >> /path/to/ssp-autocheckin.log 2>&1
```
test

