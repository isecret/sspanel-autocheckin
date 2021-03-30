# SSPanel 自动签到 V2.0 支持多站点多用户

![SSPanel_Auto_Checkin](https://github.com/isecret/sspanel-autocheckin/workflows/SSPanel_Auto_Checkin/badge.svg)

<details>
   <summary>目录</summary>

- [SSPanel 自动签到 V2.0 支持多站点多用户](#sspanel-自动签到-v20-支持多站点多用户)
  - [说明](#说明)
  - [升级警告](#升级警告)5
  - [使用方法](#使用方法)
    - [方式一：Github Actions（推荐）](#方式一github-actions推荐)
    - [方式二：部署本地或服务器](#方式二部署本地或服务器)
      - [安装依赖](#安装依赖)22
      - [克隆仓库](#克隆仓库)
      - [修改配置](#修改配置)
      - [运行脚本签到](#运行脚本签到)
      - [添加定时任务](#添加定时任务)
  - [常见问题](#常见问题)
    - [关于定时任务不执行](#关于定时任务不执行)
    - [Action 定时任务运行结果只显示推送信息](#action-定时任务运行结果只显示推送信息)
    - [TGBot 推送相关参数获取](#tgbot-推送相关参数获取)
    - [Fork 之后如何同步原作者的更新内容](#fork-之后如何同步原作者的更新内容)
      - [方式一： 保留自己内容](#方式一-保留自己内容)
      - [方式二： 源作者内容直接覆盖自己内容](#方式二-源作者内容直接覆盖自己内容)

</details>

## 相关说明

- 适用于使用 SSPanel 用户管理面板搭建的网站，网站页面底部会有 `Powered by SSPANEL` 字段
- 支持使用配置文件读取账户信息，支持多机场多用户签到
- 支持一日多次签到
- 支持推送签到信息到 QQ、微信和 Telegram
- 若有 bug 请到 [Issues](https://github.com/isecret/sspanel-autocheckin/issues/new) 反馈

## 升级警告

V2.0 版本支持多站点多用户签到，不兼容 V1.0 版本配置，升级脚本后需要重新配置

## 使用方法

### 方式一：Github Actions（推荐）

> Fork 后的项目 Github Actions 默认处于关闭状态，需要手动开启 Actions，执行一次工作流。后续定时任务(cron)才会自动执行。具体操作信息看：[关于定时任务不执行](#关于定时任务不执行)。

Fork 该仓库，进入仓库后点击 `Settings`，右侧栏点击 `Secrets`，点击 `New secret`。按需添加以下值：

| Secret Name          | Secret Value                                   | 参数说明                                                                        | 是否可选               |
| -------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------- | ---------------------- |
| `USERS`              | `https://abc.com----abc@abc.com----abc123456;` | 用户组，格式为 `签到站点----用户名----密码`，多个站点或用户使用 `;` 分隔        | 必填，至少存在一组     |
| `PUSH_KEY`           | `SCxxxxxxxxxxxxx`                              | 微信推送 ，填写自己申请[Server 酱](http://sc.ftqq.com/?c=code)的`SC KEY`        | 可选                   |
| `PUSH_TURBO_KEY`           | `xxxxxxxxxxxxxxx`                              | 企业微信推送 ，填写自己申请[Server 酱 Turbo](https://sct.ftqq.com/sendkey)的`SendKey`        | 可选                   |
| `QMSG_KEY`           | `e6fxxxxxxxxxxxx`                              | QQ 推送 ，填写自己申请[Qmsg 酱](https://qmsg.zendee.cn/me.html#/)的 `QMSG_KEY`  | 可选                   |
| `TELEGRAMBOT_TOKEN`  | `123456:ABC-DEF1234xxx-xxx123ew11`             | TGBot 推送，填写自己向[@BotFather](https://t.me/BotFather) 申请的 Bot Token     | 可选，和下面的一起使用 |
| `TELEGRAMBOT_CHATID` | `11xxxxxx03`                                   | TGBot 推送，填写[@getuseridbot](https://t.me/getuseridbot)私聊获取到的纯数字 ID | 可选，和上面一起使用   |
| `DISPALY_CONTEXT`    | `1`                                            | 任务执行时是否显示详细信息，`1` 显示 `0` 关闭，默认值 `1`                       | 可选                   |


> TGBot 推送相关参数获取步骤可以点击 [TGBot 推送相关参数获取](#TGBot 推送相关参数获取) 查看。

定时任务将于每天凌晨 `2:20` 分和晚上 `20:20` 执行，如果需要修改请编辑 `.github/workflows/work.yaml` 中 `on.schedule.cron` 的值（注意，该时间时区为国际标准时区，国内时间需要 -8 Hours）。

### 方式二：部署本地或服务器

#### 安装依赖

```bash
# Ubuntu/Debain
apt-get install jq

# CentOS
yum install jq

# MacOS
brew install jq
```

#### 克隆仓库

```bash
git clone --depth=1 https://github.com/isecret/sspanel-autocheckin.git
# 因为国内网络克隆速度不是很理想的，选择下面的
git clone --depth=1 https://hub.fastgit.org/isecret/sspanel-autocheckin.git
```

#### 修改配置

复制 `env.example` 为 `.env` ，然后编辑 `.env` 文件修改配置。

```bash
cp env.example .env
vim .env
```

用户配置格式如下：相关参数获取步骤可看上方表格的**参数说明**列。

```ini
# 用户信息。格式：域名----账号----密码，多个账号使用 ; 分隔，支持换行但前后引号不能删掉
USERS="https://abc.com----abc@abc.com---abc123456;
https://abc.com----abc@abc.com---abc123456;
https://abc.com----abc@abc.com---abc123456;"
# Server 酱推送 SC KEY
PUSH_KEY="PUSH_KEY"
# Qmsg 酱推送 QMSG_KEY
QMSG_KEY="QMSG_KEY"
# TelegramBot 推送 Token
TELEGRAMBOT_TOKEN=TELEGRAMBOT_TOKEN
# TelegramBot 推送用户 ID
TELEGRAMBOT_CHATID="TELEGRAMBOT_CHATID"
# 执行任务时是否显示签到详情
DISPALY_CONTEXT=1
```

不会使用 vim 操作的直接复制下面命令（修改相关参数）到终端运行即可。

> ```bash
> cat > .env <<EOF
> # 用户信息。格式：域名----账号----密码，多个账号使用 ; 分隔，支持换行但前后引号不能删掉
> USERS="https://abc.com----abc@abc.com---abc123456;
> https://abc.com----abc@abc.com---abc123456;
> https://abc.com----abc@abc.com---abc123456;"
> # Server 酱推送 SC KEY
> PUSH_KEY="PUSH_KEY"
> # Qmsg 酱推送 QMSG_KEY
> QMSG_KEY="QMSG_KEY"
> # TelegramBot 推送 Token
> TELEGRAMBOT_TOKEN=TELEGRAMBOT_TOKEN
> # TelegramBot 推送用户 ID
> TELEGRAMBOT_CHATID="TELEGRAMBOT_CHATID"
> # 执行任务时是否显示签到详情
> DISPALY_CONTEXT=1
> EOF
> ```

#### 运行脚本签到

脚本添加可执行权限后运行。

```bash
$ chmod +x ssp-autocheckin.sh && ./ssp-autocheckin.sh
SSPanel Auto Checkin v2.1.5 签到通知
## 用户 1

- 【签到站点】: DOMAIN
- 【签到用户】: EMAIL
- 【签到时间】: 2020-12-26 19:03:19
- 【签到状态】: 续命1天, 获得了 111 MB流量.
- 【用户等级】：VIP1
- 【用户余额】: 2.98 CNY
- 【用户限速】: 100 Mbps
- 【总流量】: 317.91 GB
- 【剩余流量】: 248.817 GB
- 【已使用流量】: 69.0929 GB
- 【等级过期时间】: 2021-05-12 16:03:35
- 【账户过期时间】: 2021-07-26 16:03:35
- 【上次签到时间】: 2020-12-26 02:53:23

【Server 酱推送结果】: 成功
【Qmsg 酱推送结果】: 成功
【TelegramBot 推送结果】: 成功

---------------------------------------
```

#### 添加定时任务

签到成功后，即可添加定时任务。

```bash
24 10 * * * bash /path/to/ssp-autocheckin.sh >> /path/to/ssp-autocheckin.log 2>&1
```

## 常见问题

### 关于定时任务不执行

因为 Github 默认 Fork 后的项目 Github Actions 处于关闭状态，定时任务执行需要手动开启 Actions，执行一次工作流。解决方法有三种：

- 修改项目相关文件，比如这个 `README.md`，新增一个空格也算，然后提交。

- 进入 Actions，手动执行一次工作流。

  ![Github Action Run Workflow](https://cdn.jsdelivr.net/gh/isecret/sspanel-autocheckin@master/assets/github_actions_run_workflow.png)

- 进入 **Fork 后的项目**，点击右上角的 <kbd>star</kbd> 按钮。

  ![Github Star](https://cdn.jsdelivr.net/gh/isecret/sspanel-autocheckin@master/assets/github_star.png)

### Action 定时任务运行结果只显示推送信息

因为签到详细信息涉及用户隐私问题，所以对任务结果中对域名和用户名进行了脱敏处理，如果你仍希望关闭任务结果的显示，可以配置 Secret `DISPALY_CONTEXT` 的值为 `0` 将只展示任务推送结果，而不显示具体签到详情。

### TGBot 推送相关参数获取

> 需要`TELEGRAMBOT_TOKEN`和`TELEGRAMBOT_CHATID`一起使用，前者用于调用 bot，后者用于指定推送目标。

| `TELEGRAMBOT_CHATID`获取                                                                                                    | `TELEGRAMBOT_TOKEN`获取                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| ![GET_TELEGRAMBOT_CHATID](https://cdn.jsdelivr.net/gh/isecret/sspanel-autocheckin@master/assets/GET_TELEGRAMBOT_CHATID.png) | ![GET_TELEGRAMBOT_TOKEN](https://cdn.jsdelivr.net/gh/isecret/sspanel-autocheckin@master/assets/GET_TELEGRAMBOT_TOKEN.png) |

### Fork 之后如何同步原作者的更新内容

用户可选手动 PR 同步或者使用插件 Pull App 自动同步原作者的更新内容。手动 PR 同步可以参考 [手动 PR 同步教程](https://www.cnblogs.com/hzhhhbb/p/11488861.html)。自动同步需要安装 [![](https://prod.download/pull-18h-svg) Pull app](https://github.com/apps/pull) 插件。使用插件 Pull App 自动同步步骤如下。

1. 安装 [![](https://prod.download/pull-18h-svg) Pull app](https://github.com/apps/pull) 插件。

2. 安装过程中会让你选择要选择那一种方式;

   - `All repositories`表示同步已经 frok 的仓库以及未来 fork 的仓库；
   - `Only select repositories`表示仅选择要自己需要同步的仓库，其他 fork 的仓库不会被同步。

   根据自己需求选择，实在不知道怎么选择，就选 `All repositories`。

   点击 `install`，完成安装。

   ![Install Pull App](https://cdn.jsdelivr.net/gh/isecret/sspanel-autocheckin@master/assets/install_pull_app.png)

   Pull App 可以指定是否保留自己已经修改的内容，分为下面两种方式，如果你不知道他们的区别，就请选择方式二；如果你知道他们的区别，并且懂得如何解决 git 冲突，可根据需求自由选择任一方式。

#### 方式一： 保留自己内容

> 该方式会在上游代码更新后，判断上游更新内容和自己分支代码是否存在冲突，如果有冲突则需要自己手动合并解决（也就是不会直接强制直接覆盖）。如果上游代码更新涉及 `workflow` 里的文件内容改动，这时也需要自己手动合并解决。

步骤如下：

1. 确认已安装 [![pull](https://prod.download/pull-18h-svg) Pull app](https://github.com/apps/pull) 插件。

2. 编辑 pull.yml (在 `.github` 目录下) 文件，将第 5 行内容修改为 `mergeMethod: merge`，然后保存提交。 （默认就是 `merge`，如果未修改过，可以不用再次提交）

完成后，上游代码更新后 pull 插件就会自动发起 PR 更新自己分支代码！只是如果存在冲突，需要自己手动去合并解决冲突。

当然也可以立即手动触发同步：`https://pull.git.ci/process/${owner}/${repo}`

#### 方式二： 源作者内容直接覆盖自己内容

> 该方式会将源作者的内容直接强制覆盖到自己的仓库中，也就是不会保留自己已经修改过的内容。

步骤如下：

1. 确认已安装 [![pull](https://prod.download/pull-18h-svg) Pull app](https://github.com/apps/pull) 插件。

2. 编辑 pull.yml (在 `.github` 目录下) 文件，将第 5 行内容修改为 `mergeMethod: hardreset`，然后保存提交。

完成后，上游代码更新后 pull 插件会自动发起 PR 更新**覆盖**自己仓库的代码！

当然也可以立即手动触发同步：`https://pull.git.ci/process/${owner}/${repo}`
