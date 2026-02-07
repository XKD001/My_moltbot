# OpenClaw 完全新手入门指南

**原文标题**：OpenClaw 完全新手入门：用最短路径把 AI 个人管家跑起来（Mac + Telegram）

**原文作者**：范凯（Robbin Fan）

**原文日期**：Feb 07, 2026

**原文链接**：https://fankaishuoai.substack.com/p/openclaw-ai

**收录时间**：2026-02-07

---

## 概述

OpenClaw 是一个智能体（AI Agent）框架，这篇文章是范凯写的 OpenClaw 入门系列文章的第一篇。这篇入门指南主要帮助新手解决三个核心问题：

1. 顺利把 OpenClaw 安装好，不在前期卡壳
2. 通过 Telegram / WhatsApp 和 OpenClaw 实现随时随地的交流
3. 初步了解 OpenClaw 究竟能干什么

---

## 第一部分：安装前的准备工作

为了让你顺利安装和运行 OpenClaw，有几个前提条件必须提前准备好。

### 1.1 硬件设备：准备一台 Mac 电脑

首先，你需要一台 Mac 电脑。无论是 Mac mini 还是 MacBook 都可以。

**Mac mini 推荐原因**：
- 非常适合作为一台长期运行的 AI 主机
- 可以放在家里 24 小时稳定工作
- 这是作者个人推荐的方案

**MacBook 使用建议**：
- 如果你是初学者，直接用自己的 MacBook 也完全没问题
- 但注意不要给 OpenClaw 太多的权限
- 普通的 Apple 芯片机型基本都够用

### 1.2 AI 模型：准备好 OpenClaw 调用的 AI 模型

OpenClaw 是一个调度和执行系统，真正负责"思考"和"写代码"的，是它背后调用的 AI 模型。

**海外三大模型推荐**：
| 模型名称 | 说明 |
|---------|------|
| Claude 4.6 Opus | 效果最好的模型之一 |
| GPT 5.3 Codex | OpenAI 的代码专用模型 |
| Gemini 3 Pro | Google 的模型 |

如果你是这些服务的订阅用户，建议提前把对应的命令行工具安装好：
- Claude Code
- Codex CLI
- Gemini CLI

**国内高性价比模型推荐**：
| 模型名称 | 特点 |
|---------|------|
| 智谱 GLM-4.7 | 作者使用示例 |
| MiniMax M2.1 | 性价比较高 |
| 月之暗面 Kimi 2.5 | 用户友好 |

**费用参考**：一个月大概几十块人民币，对新手来说非常友好。

**特别提示**：如果你已经订阅了 GitHub Copilot，也可以直接用，它里面同样包含 GPT、Gemini 和 Claude 的基础模型能力。

**作者示例**：范凯使用智谱的 GLM-4.7 Coding Plan 套餐作为演示。

### 1.3 通讯软件：选择一个与 OpenClaw 交流的渠道

我们和 OpenClaw 交流，并不像 ChatGPT 那样通过网页，也不像 Claude Code 那样通过命令行，而是通过聊天工具，像给自己的 AI 管家下命令一样去管理她。

**推荐优先级**：

| 软件 | 特点 | 适用人群 |
|------|------|---------|
| **Telegram** ⭐⭐⭐⭐⭐ | 简单强大，手机使用非常方便 | 首选推荐 |
| WhatsApp | 接入最简单，适合轻量使用 | 备选方案 |
| Discord | 偏工程化，功能强大 | 进阶用户 |
| 飞书 | 国内用户可用，但流程复杂 | 后续单独介绍 |

**注意事项**：
- WhatsApp 不要大量发消息，容易触发封号
- Discord 更复杂，适合进阶用户
- 确保网络环境可以畅通的访问外网

---

## 第二部分：软件的安装

在正式安装 OpenClaw 之前，我们需要先把运行环境准备好。总共四步：

```
Homebrew → fnm → Node.js → OpenClaw
```

只要顺着这个顺序来，基本不会出问题。

### 2.1 第一步：安装 Homebrew

**为什么需要 Homebrew**：
- 运行 OpenClaw 并不是必须依赖 Homebrew
- OpenClaw 会调用大量的 Skill，很多 Skill 需要安装各种命令行工具
- 这些工具如果手动安装，很容易出错
- Homebrew 相当于 macOS 上的统一软件管理器

**安装步骤**：

1. 打开终端（按 Command + Space，搜索"终端"或"Terminal"打开）
2. 复制并执行以下命令：
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

3. 执行过程中会让你输入 macOS 登录密码
4. 安装完成后，终端会提示一些"Next steps"
5. 复制并执行类似下面的命令：
```bash
echo >> ~/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

**验证安装**：
```bash
brew -v
```
看到类似 `Homebrew 5.0.13` 的信息，表示安装成功。

### 2.2 第二步：使用 Homebrew 安装 fnm

fnm 是一个 Node.js 版本管理工具。

**安装步骤**：
```bash
# 安装 fnm
brew install fnm

# 激活 fnm
echo 'eval "$(fnm env --use-on-cd)"' >> ~/.zshrc
source ~/.zshrc

# 验证 fnm
fnm --version
```

看到类似 `fnm 1.38.1` 的信息，表示安装成功。

### 2.3 第三步：用 fnm 安装 Node.js

OpenClaw 官方推荐使用 Node.js 22。

**安装步骤**：
```bash
fnm install 22
fnm use 22
fnm default 22

# 验证 Node.js
node -v
npm -v
```

应该看到类似：
- `node: v22.22.0`
- `npm: 10.9.4`

### 2.4 第四步：用 Node.js 安装 OpenClaw

**安装命令**：
```bash
npm i -g openclaw
```

**故障排除**：
如果碰到任何问题，可以把错误信息粘贴到 AI 聊天窗口，让 AI 教你解决。但如果你严格按照步骤走下来，不会出问题。

---

## 第三部分：申请 Telegram 机器人的 Token

因为我们使用 Telegram 和 OpenClaw 进行交流，需要预先申请一个 Telegram 机器人的 Token。

### 3.1 第一步：找到 BotFather

1. 打开 Telegram，在顶部的搜索栏输入：`@BotFather`
2. 认准那个带蓝色认证对勾的官方账号
3. 点击底部的 Open 按钮

### 3.2 第二步：创建新机器人

1. 在弹窗的窗口中点击："Create a New Bot"
2. 设置显示名称：比如 `RobbinOpenClaw`（这是别人在聊天列表中看到的名字）
3. 设置 ID (Username)：
   - 必须以 bot 结尾
   - 不能和别人的重复
   - 例如：`robbin_openclaw_bot`
4. 点击 Copy 按钮，复制生成的 Token

---

## 第四部分：配置并且运行 OpenClaw

现在深吸一口气，我们前面所有的工作都不是浪费的，因为接下来你就可以一步成功了。

### 4.1 第一步：启动配置面板

**命令**：
```bash
openclaw onboard
```

程序启动后，会看到一个终端 UI 界面，可以一路无脑选择 yes。

### 4.2 第二步：配置 LLM 模型（以 GLM-4.7 为例）

1. 进入模型设置：在菜单中选择 Model Settings
2. 选择提供商：找到并选择 Zhipu 或者 Z.A
3. 填写 API Key：将你的 GLM 4.7 的 API Key 粘贴进去
4. 指定模型名称：在 Model Name 一栏填入 `glm-4.7`
5. 保存：点击页面下方的 Save 或按提示保存

### 4.3 第三步：配置 Channel 通讯渠道（Telegram）

1. 进入渠道设置：选择 Telegram Bot
2. 填写 Token：在 Bot Token 字段中填入你从 @BotFather 那里拿到的长字符串

### 4.4 第四步：保存并运行

1. 配置完成 Channel 之后，会选择安装哪些 Skills
2. 这一步可以跳过，后面在 Web 管理控制台安装
3. 一路跳过或者默认即可
4. 最后结束安装，可以选择打开 Web UI 浏览器

**Web 管理控制台**：访问 OpenClaw 的管理控制台，后续配置和管理都可以通过这个界面完成。

### 4.5 第五步：验证连接

1. 打开 Telegram，搜索到你刚才创建的机器人，例如 `robbin_openclaw_bot`
2. 发送一条消息：`/start` 或者 `你好`
3. Bot 会回复一个配对码（例如：`PAIR-ABC123`）
4. 回到终端 OpenClaw 窗口，运行命令来批准绑定：
   ```bash
   openclaw pairing approve telegram PAIR-ABC123
   ```
5. 当你看到类似 `Approved telegram sender 123456789` 的信息，表示成功！

**🎉 恭喜你**：你已经成功安装、运行 OpenClaw，接下来只需要通过 Telegram 就可以完全操控你的 AI 智能管家了。

**后续操作**：
- 跟机器人智能管家多聊几句，介绍你自己
- 可以给机器人起个名字，并设定一些性格
- 她会把这些信息写入到存储里面

### 4.6 第六步：配置 WhatsApp（可选）

有些朋友没有 Telegram，但有 WhatsApp，配置过程比较简单，但功能没有 Telegram 那么强大。

**步骤**：
1. 在终端输入：`openclaw configure`
2. 在配置界面中选择 channels（通道），然后选择 WhatsApp
3. 系统会生成一个二维码
4. 用手机打开 WhatsApp，扫描这个二维码
5. 在手机上确认"关联一个新设备"
6. 在 OpenClaw 里面填写你的手机号码：`+86 加你的手机号`
7. 显示 `linked`（已连接）即可退出

**验证方法**：
在 WhatsApp 里用自己的手机号搜索到自己，然后给自己发消息，OpenClaw 就会自动回复。

---

## 第五部分：我可以用 OpenClaw 来干什么

很多人安装 OpenClaw 之后，不知道用它来干啥。说实话，现在真正拖慢我们的，并不是工具本身，而是想象力。

### 5.1 让 OpenClaw 接管你的日程和备忘录

**以前的工作方式**：
- 在苹果备忘录里一条条记碎片信息
- 记多了以后基本很难整理
- 日程提醒要打开日历 App 手工输入

**有了 OpenClaw 之后**：
- 任何碎片想法，直接说："帮我记到苹果备忘录里"
- 任何日程安排，直接口述时间和提醒："明天下午三点开会，提前一小时提醒我，写进苹果日历"

**示例**：
每周日晚上 10 点有"范凯说 AI 周日晚聊"的直播，可以直接告诉 OpenClaw，她会帮你创建一个循环日历事件。

**更厉害的是**：它会把你的日程和备忘录彻底重构成一个知识库。你只需要在 Telegram 里直接问，它就会到知识库里帮你提取、组合、甚至生成新的内容。

### 5.2 让 OpenClaw 做真正的知识管理

**传统工具的问题**：
- Evernote、语雀这类工具本质都是在存文件
- 内容存进去以后，目录结构、分类逻辑、标签体系，全靠自己维护
- 时间一长，笔记越多，结构越复杂，维护成本越高，最后反而越来越难用

**有了 OpenClaw 之后**：
- 知识本来就不是树状的，而是网状的
- 你不再需要纠结目录和分类
- 可以把所有笔记直接交给 OpenClaw
- 它会自动读一遍、重构结构、建立关联
- 把碎片内容整理成一个真正网状的知识库

**核心价值**：你不再"翻笔记"，只需要在 Telegram 里直接问，它就会帮你提取、组合、甚至生成新的内容。

**从这一刻开始**：知识不再是存档，而是一个会持续进化的系统。

### 5.3 给 OpenClaw 一个真实的数字人身份

**范凯的实际操作**：
1. 把一台安卓手机打开 USB 调试
2. 用一根 Type-C 线接到 Mac mini 上

**结果**：OpenClaw 真的可以控制这台手机。

**已实现功能**：
- 发微信
- 打电话
- 收发短信

**虽然速度还有点慢，但整个流程是完全跑通的。**

**后续操作**：
- 去淘宝下单了一台红米手机
- 给它专用，再配一张 SIM 卡
- 让它自己注册账号、收验证码、加群
- 和这个世界发生真实交互

**意义**：从这一刻开始，OpenClaw 不再只是一个程序，它开始拥有一个真实的数字身份。

**想象空间**：几乎没有什么事情是它做不到的。

---

## 第六部分：限制我们的是想象力

到这里，你已经完成了从安装到上手的整个过程，也初步看到了 OpenClaw 能够打开的巨大空间。

**核心观点**：
- OpenClaw 已经不只是一个工具，而是在朝着每个人身边的"个人 AI 管家"快速进化
- 真正限制我们的早就不是技术能力
- 模型已经足够强大
- 自动化流程已经跑通
- 真实设备也已经被 AI 接管

**现在缺的**：只剩下想象力和使用方式。

**后续计划**：
范凯会围绕 OpenClaw 持续更新一系列实战内容：
- 效率跃迁
- 知识系统重构
- 个人 AI 操作系统的构建
- 帮助你在 AI 时代完成转型
- 打造属于自己的长期竞争力

**关注渠道**：
- 微信公众号：范凯说 AI
- Substack：https://fankaishuoai.substack.com

---

## 附录：相关资源

### 工具下载
- **Homebrew**：https://brew.sh
- **OpenClaw**：https://github.com/openclaw/openclaw
- **fnm**：https://github.com/Schniz/fnm

### AI 模型
- **Claude**：https://claude.com
- **GPT**：https://chat.openai.com
- **Gemini**：https://gemini.google.com
- **智谱 GLM**：https://www.zhipuai.com
- **MiniMax**：https://www.minimaxi.com
- **Kimi**：https://kimi.ai

### 通讯工具
- **Telegram**：https://telegram.org
- **WhatsApp**：https://whatsapp.com
- **Discord**：https://discord.com

### 原文作者
- **范凯（Robbin Fan）**
- Substack：https://fankaishuoai.substack.com
- 微信：范凯说 AI

---

**文档编辑者**：小蝌蚪 🐸
**最后更新**：2026-02-07
**原文作者**：范凯
**原文链接**：https://fankaishuoai.substack.com/p/openclaw-ai
