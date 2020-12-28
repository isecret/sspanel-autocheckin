# SSPanel 自动签到 V2.0 支持多站点多用户

![SSPanel_Auto_Checkin](https://github.com/isecret/sspanel-autocheckin/workflows/SSPanel_Auto_Checkin/badge.svg)

> 注意：关于定时任务(cron)不执行的情况，你可能需要修改项目相关文件，比如这个 README.md，新增一个空格也算，然后提交就行。

## 升级警告

V2.0 版本支持多站点多用户签到，不兼容 V1.0 版本配置，升级脚本后需要重新配置

## 使用方法

### 方式一：Github Actions（推荐）

Fork 该仓库，进入仓库后点击 `Settings`，右侧栏点击 `Secrets`，点击 `New secret`。添加一下值：

| 键       | 值                                             | 说明                                                                                   |
| -------- | ---------------------------------------------- | -------------------------------------------------------------------------------------- |
| USERS    | `https://abc.com----abc@abc.com----abc123456;` | 用户组，格式为 `签到站点----用户名----密码`，多个站点或用户使用 `;` 分隔，至少存在一组 |
| PUSH_KEY | `SCxxxxxxxxxxxxx`                              | Server 酱 SC KEY，非必填                                                               |

定时任务将于每天凌晨 `2:20` 分和晚上 `20:20` 执行，如果需要修改请编辑 `.github/workflows/work.yaml` 中 `on.schedule.cron` 的值（注意，该时间时区为国际标准时区，国内时间需要 -8 Hours）。

### 方式二：部署本地或服务器

脚本依赖：

- `jq` 安装命令: Ubuntu: `apt-get install jq`、CentOS: `yum install jq`、MacOS: `brew install jq`

克隆或下载仓库 `ssp-autocheckin.sh` 脚本，复制 `env.example` 为 `.env` 并修改配置。

```
cp env.example .env
vim .env
# 用户配置格式如下：域名----账号----密码，多个账号使用 ; 分隔，支持换行但前后引号不能删掉
USERS="https://abc.com----abc@abc.com---abc123456;
https://abc.com----abc@abc.com---abc123456;
https://abc.com----abc@abc.com---abc123456;"
# Server 酱推送 SC KEY
PUSH_KEY="PUSH_KEY"
```

然后执行，签到成功后，即可添加定时任务。

```bash
$ bash /path/to/ssp-autocheckin.sh
【签到站点】: DOMAIN

【签到用户】: EMAIL

【签到时间】: 2020-12-26 19:03:19

【签到状态】: 续命1天, 获得了 111 MB流量.

【用户余额】: 2.98 CNY

【用户限速】: 100 Mbps

【总流量】: 317.91 GB

【剩余流量】: 248.817 GB

【已使用流量】: 69.0929 GB

【等级过期时间】: 2021-05-12 16:03:35

【账户过期时间】: 2021-07-26 16:03:35

【上次签到时间】: 2020-12-26 02:53:23


【推送结果】: 成功

---------------------------------------
```

如下：

```bash
24 10 * * * bash /path/to/ssp-autocheckin.sh >> /path/to/ssp-autocheckin.log 2>&1
```
