## Ai Agent

输入---模型处理---输出

中间会涉及工具调用、思维链等等

#### 提问角色role

system 模型的上下文，前置，设定等

assitant 默认回答

user 用户的提问

#### 流式输出

文字逐渐显示

参数 stream = True（开启流），flask = True（缓冲区）

#### 幻觉

插入参考文档或者答案

### 提示词

**few shot**

**zero shot**

**例如ppt制作Ai Agent**

提供关键信息---思路、标题--大纲---工具调用（生成ppt的插件）---输出ppt

中间每个内容都会有大模型根据promot进行输入

完整的promot模板

 

```
1. 角色设定
2. 任务描述激活传送门
3. 背景信息
4. 输出要求
5. 示例参考

更复杂的任务就需要
链式思考CoT
正反提示
迭代优化
```

**RAG检索知识增强**

graph TD
A[用户查询] --> B[查询嵌入]
B --> C[知识库检索]
C --> D[相关文档]
D --> E[上下文拼接]
E --> F[LLM生成]
F --> G[输出响应]

cli_a924c0c70ef9dbce

E0bmT9d6F0TDTeZBShmrWm0i72mHVumq

C:、\Program Files\Google\Chrome\Application

```
openclaw config set browser.executablePath "/mnt/Program Files/Google/Chrome/Application/chrome.exe"
```

```
openclaw cron add \
  --name "每10分钟Ai推文" \
  --cron "* * * * *" \
  --tz "Asia/Shanghai" \
  --message "请抓取网站 https://github.com/topics/ai 的内容，提取当前最热门的AI项目或资讯，并生成一条包含标题和简介的推文。" \
  --channel feishu \
  --to "ou_03b0a891cc6708ccd7b99154a1de5bfb" \
  --best-effort-deliver \
  --session isolated \
  --wake now

{
  "name": "每10分钟Ai推文",
  "schedule": { "kind": "cron", "expr": "* * * * *", "tz": "Asia/Shanghai" },
  "sessionTarget": "isolated",
  "wakeMode": "now",
  "payload": {
    "kind": "agentTurn",
    "message": "请抓取网站 https://github.com/topics/ai 的内容，提取当前最热门的AI项目或资讯，并生成一条包含标题和简介的推文。"
  },
  "delivery": {
    "mode": "announce",
    "channel": "feishu",
    "to": "ou_03b0a891cc6708ccd7b99154a1de5bfb",
    "bestEffort": true
  }
}
```

## **面试**

我有几个问题想先和你逐个探讨一下：
1、你如何理解 OpenClaw 这种浏览器 Agent 的底层结构？

2、如果让你设计一个能自动操作浏览器完成任务的系统，你觉得架构应该怎么拆？
3、如果 OpenClaw 部署后运行不稳定，你第一步会怎么排查？
4、假设你从没接触过 OpenClaw，我给你 3 天时间，你会怎么快速吃透它？
5、你觉得 OpenClaw 这种产品的核心难点是什么？
6、如果 OpenClaw 运行过程中，LLM 决策越来越偏离目标，你会怎么修正？

回答：

1、简单来讲就是openclaw是集成一系列工具+llm的智能体，它拿到一个任务，进行解析，需要具体那个工具（操作系统、游览器等）、工具调用、或者会检索知识库，最后返回执行结果。

优化点：

引入 “感知-决策-行动”循环：强调它是一个闭环系统。
提到交互介质：浏览器 Agent 的特殊性在于它的输入是 DOM Tree 或 Screenshot（多模态），输出是键盘鼠标事件或 JavaScript 注入。
提到状态管理：浏览器页面的状态变化如何反馈给 LLM。

2、前置工具、游览器执行层（点击、请求、键盘等）、数据获取层（截图、dom解析等等）、数据存储（csv、数据库等）。

​	优化建议：Ai游览器自动操作

- **大脑层：** 规划器、任务拆解、记忆检索。
- **感知层：** 视觉模型（看图）+ 文本提取（DOM 解析/Accessibility Tree）。
- **执行层：** Playwright/Selenium 驱动。
- **记忆层：** 短期记忆和长期记忆（向量数据库）。

3、大致查看运行日志；先排查外部因素：网络、硬件、系统等等；然后就是大模型调用层，查看大模型响应情况是否稳定；然后排查openclaw本身部署时是否正确，配置等等；然后工具调用，权限等。

	优化建议：
增加对 “非确定性” 的排查：比如 LLM 输出的 JSON 格式是否正确？Action 参数是否越界？
增加对 “网页环境” 的排查：是否出现了非预期的弹窗？页面加载是否完全？DOM 结构是否发生动态变化？

4、官方文档快速部署、网上开源社区随便找一个案例进行搭建理解流程、然后熟悉各种工具，api使用、3天完全可以吃透大部分内容。

- 优化建议： 

  想打动面试官，可以加一点“深度”：

  - **源码阅读**：我会重点看它的 Prompt 是怎么写的，它是如何处理 DOM 压缩的（这是浏览器 Agent 的核心难点）。
  - **Bad Case 分析**：我会尝试跑几个失败案例，看它在哪一步卡住，从而理解它的边界。

5、如果是使用者：token消耗、权限控制、稳定性。openclaw本身开发：上下文压缩优化、各种工具（系统调用、游览器集成等）集成、agent多任务执行等。

	DOM 的噪声处理：网页源码极其庞大且充满噪声，如何让 LLM 准确找到按钮？（对应你的压缩观点，可以展开说是 DOM 的去噪和摘要）。
长程任务的稳定性：任务执行了 50 步，中间一步错了怎么回退？怎么保持上下文不遗忘？
反爬虫与对抗：网站如何识别你是机器人，Agent 如何规避。

6、一般来说llm偏离，大部分时候是任务清单不够清晰准确，这时候需要对提示词进行优化。涉及coT,清晰示例等等，并且可以加约束子agent，对主agent的执行步骤进行清查，如发现不符合预期进行纠正提醒。

- **优点：** 提出了 Prompt 优化和子 Agent 监督（Critic Agent）的概念，这是 Agent 设计的高级技巧，这点回答得很好。

- 补充建议：

   

  还可以补充技术层面的修正：

  - **重置上下文**：LLM 记忆过载会导致注意力分散，需要及时的 Context Truncate 或 Summarize。
  - **Human-in-the-loop**：在关键节点引入人工确认。