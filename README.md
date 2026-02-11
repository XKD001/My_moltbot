# My_moltbot

方式 1 本质是什么

你不是把 OPENAI_API_KEY 塞进 OpenClaw。

而是让 OpenClaw 通过 OAuth 引导你用 ChatGPT 账号登录，拿到一个“可代表你调用 Codex 订阅额度”的授权凭证（token），然后 OpenClaw 用这个凭证去调 OpenAI 的 Codex 云端模型。

这条路的优势：

不用管理 API Key；

走的是你 ChatGPT/Codex 订阅的授权链路（适合个人机器/快速跑通）。

你需要做的 3 步（含解释）
第 1 步：跑 onboarding 或直接跑 OAuth

二选一：

A. 用向导（推荐）

openclaw onboard --auth-choice openai-codex


这会启动 OpenClaw 的配置向导，把“模型提供方= openai-codex”以及后续必要配置串起来。

B. 已经装好，只补登录

openclaw models auth login --provider openai-codex


这一步会弹出/打印一个登录链接，让你在浏览器完成 OAuth。

第 2 步：浏览器里完成 OAuth（它在做什么）

当你打开链接并完成授权后，通常会发生这些事（不同版本表现略有差异）：

你在 OpenAI/ChatGPT 登录页完成登录与授权

OAuth 回调把授权结果交回 OpenClaw

OpenClaw 把 token 保存到本机的配置/凭证存储里（以后发请求会自动带上）

你可以把它理解成：“OpenClaw 获得了一个可刷新/可过期的通行证”，而不是永久密钥。

第 3 步：把默认模型指向 openai-codex/...

在 OpenClaw 配置里把 primary model 设为 Codex provider，例如：

{
  "agents": {
    "defaults": {
      "model": {
        "primary": "openai-codex/gpt-5.3-codex"
      }
    }
  }
}


这是官方给的配置片段。

注意：有的版本/构建可能还没把 gpt-5.3-codex 加进 OAuth 的可用模型列表，会出现 “Unknown model” 之类报错；遇到这种情况先改用文档里示例的 openai-codex/gpt-5.2 或 openai-codex/gpt-5.2-codex（以你 openclaw models list/UI 显示为准）。

怎么确认你“真的连上了”

常用两条：

看认证状态 / 是否缺 token

openclaw models status


如果它提示需要登录、token 过期、provider 未配置，基本都能在这里看到。

发一条简单请求验证模型可用

openclaw agent --message "写一个最小可运行的 Python http server"

常见问题排查（你大概率会遇到的）

登录成功但还是提示未认证：多半是你在另一个用户/另一个机器账户下跑 OpenClaw，token 写到了别的 profile；或者 gateway/daemon 用的是不同的用户权限在跑（macOS 上尤其常见）。

Unknown model：你配置的 model 名称不在当前版本的 allowlist/列表里，先用文档示例型号或用 models list 里出现的那个。

公司网络/代理导致回调失败：换网络或临时关掉拦截（OAuth 回调需要正常打开浏览器跳转）。

安全提醒（方式 1 也需要注意）

OAuth 不是“更安全就无脑开权限”。OpenClaw 一旦获得系统/文件/命令的能力，本质就是“能执行你允许的操作”。所以：

不要随便安装不明 skills/扩展；

外部消息通道（Telegram/飞书）尽量做白名单、权限最小化（只读→再到可执行）。

如果你愿意贴一下你现在的两样信息（不用发隐私）：

你跑的是 openclaw onboard 还是 models auth login

你希望用的模型名（比如 openai-codex/gpt-5.3-codex）
我就能直接给你一套“从当前状态 → 跑通并验证”的精确命令序列（含如何看日志定位卡在哪一步）。
