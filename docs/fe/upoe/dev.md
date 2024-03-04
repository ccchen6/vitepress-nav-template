# 开发者（API）使用说明

## 介绍

本文档是对文本大模型、语音模型、图片生成等海量 API 的调用进行说明，您可以通过任何语言的 HTTP 请求、Python 或社区维护的库与 API 进行交互。本 API 与 OPENAI 官网完全兼容。在正式使用前需要通过客服购买**API 令牌**。

## 安装

如果需要使用 Python 库的方式调用，请运行以下命令：

```
pip install openai
```

## 令牌验证

本 API 同 OpenAPI 一样需要使用 API 令牌 进行身份验证。访问您的令牌管理页面或者从客服处获取。

**请记住，您的 API 令牌的私密性！** 不要与他人共享或在任何客户端代码（浏览器、应用程序）中公开它。生产环境请求必须通过您自己的后端服务器进行路由，您的 API 令牌可以从环境变量或密钥管理服务安全地加载。
在你的`bash/zsh`环境中

```bash
export API_KEY="sk-xxxx" # 替换成您的API令牌
```

所有 API 请求都应在 AuthorizationHTTP 标头中包含您的 API 令牌，如下 http 请求所示：

```bash
curl https://api.rcouyi.com/v1/models \
  -H "Authorization: Bearer $API_KEY"
```

## 对话

### 发出请求

您可以将以下命令粘贴到终端中以运行您的第一个 API 请求。确保替换 $API_KEY 为您的私人 API 令牌。

- http:
  ```bash
  curl https://api.rcouyi.com/v1/chat/completions \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $API_KEY" \
     -d '{
      "model": "gpt-3.5-turbo",
      "messages": [{"role": "user", "content": "Say this is a test!"}],
      "temperature": 0.7
     }'
  ```
- python:

  - 方式 1：使用 openai 库更改 base url
    ```python
    # 如下使用方式注意openai的版本得1.0以下，比如0.28， pip install openai==0.28
    import openai
    openai.base_url = "https://api.rcouyi.com/"
    openai.api_key = $API_KEY # 替换成令牌管理页面中的秘钥
    resp = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "你是个有帮助的助手"},
            {"role": "user", "content": "你好么"},
        ]
    )
    print(resp[0]["message"]["content"]
    ```
  - 方式 2：使用 request 请求

    ```
    import requests
    import json
    # 你的API密钥，确保它已经设置为环境变量
    API_KEY = os.getenv('API_KEY')
    # 请求URL
    url = "https://api.rcouyi.com/v1/chat/completions"
    # 请求头
    headers = {
          "Content-Type": "application/json",
          "Authorization": f"Bearer {API_KEY}"
      }

    # 请求数据
    data = {
        "model": "gpt-3.5-turbo",
        "messages": [{"role": "user", "content": "Say this is a test!"}],
        "temperature": 0.7
    }
    # 发送POST请求
    response = requests.post(url, headers=headers, json=data)
    # 打印响应内容
    print(response.json())
    ```

  - 方式 3：流式（stream 方式） 调用

    ````
    import os
    from openai import OpenAI
        client = OpenAI(
            api_key=os.getenv('API_KEY'),
            base_url="https://api.rcouyi.com/v1/",
        )

        response = client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[
                {
                    "role": "system",
                    "content": "你是chatgpt，由 openAI 提供的人工智能助手，你更擅长中文和英文的对话。你会为用户提供安全，有帮助，准确的回答。同  时，你会拒绝一些涉及恐怖主义，种族歧视，黄色暴力等问题的回答。",
                },
                {
                    "role": "user",
                    "content": "你好，我叫李雷，1+1等于多少？"
                },
            ],
            temperature=0.3,
            stream=True,
        )

        collected_messages = []
        for idx, chunk in enumerate(response):
            # print("Chunk received, value: ", chunk)
            chunk_message = chunk.choices[0].delta
            if not chunk_message.content:
                continue
            collected_messages.append(chunk_message)  # save the message
            print(f"#{idx}: {''.join([m.content for m in collected_messages])}")
        print(f"Full conversation received: {''.join([m.content for m in collected_messages])}")
        ```
    上述两种非流式方式会得到：
    此请求查询 gpt-3.5-turbo 模型以完成以“说这是测试”提示开头的文本。您应该收到类似于以下内容的 json 响应：
    ````

  ```json
  {
    "id": "chatcmpl-8gqkDUt3Gs3711qhHS8xgs6YR1Wyt",
    "object": "chat.completion",
    "created": 1705223217,
    "model": "gpt-3.5-turbo-0613",
    "choices": [
      {
        "index": 0,
        "message": {
          "role": "assistant",
          "content": "This is a test!"
        },
        "logprobs": null,
        "finish_reason": "stop"
      }
    ],
    "usage": {
      "prompt_tokens": 13,
      "completion_tokens": 5,
      "total_tokens": 18
    },
    "system_fingerprint": null
  }
  ```

  对 流式 stream 格式的，返回类似如下：

  ```json
  data: {"id":"cmpl-1305b94c570f447fbde3180560736287","object":"chat.completion.chunk","created":1698999575,"model":"gpt3.5","choices":[{"index":0,"delta":{"role":"assistant"},"finish_reason":null}]}

  data: {"id":"cmpl-1305b94c570f447fbde3180560736287","object":"chat.completion.chunk","created":1698999575,"model":"gpt3.5","choices":[{"index":0,"delta":{"content":"你好"},"finish_reason":null}]}

  ...

  data: {"id":"cmpl-1305b94c570f447fbde3180560736287","object":"chat.completion.chunk","created":1698999575,"model":"gpt3.5","choices":[{"index":0,"delta":{"content":"。"},"finish_reason":null}]}

  data: {"id":"cmpl-1305b94c570f447fbde3180560736287","object":"chat.completion.chunk","created":1698999575,"model":"gpt3.5","choices":[{"index":0,"delta":{},"finish_reason":"stop","usage":{"prompt_tokens":19,"completion_tokens":13,"total_tokens":32}}]}

  data: [DONE]
  ```

### 参数说明

- temperature： 温度为 0，多样性越低，确信度越高。温度越高，多样性越高，真实性越低
- 其他参数均可参考 openAI 官网定义

| 字段        | 说明                                                                                                                     | 类型       | 取值                                                                                                                                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------------------------ | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| messages    | 包含迄今为止对话的消息列表。                                                                                             | List[Dict] | 这是一个结构体的列表，每个元素类似如下：`json{ "role": "user", "content": "你好"} ` role 只支持 system,user,assistant 其一，content 不得为空                                                                                 |
| model       | Model ID 可以通过 List Models 获取                                                                                       | string     | 可参考下列支持模型列表                                                                                                                                                                                                       |
| max_tokens  | 聊天完成时生成的最大 token 数。如果到生成了最大 token 数个结果仍然没有结束，finish reason 会是 “length”, 否则会是 “stop” | int        | max_tokens 是输入 + 输出的总长度。比如对一个模型，它的最大输入 + 输出总长度是 8192，当输入 messages 总长度为 4096 的时候，您最多只能设置为 4096，否则我们服务会返回不合法的输入参数（ invalid_request_error ），并拒绝回答。 |
| temperature | 使用什么采样温度，介于 0 和 1 之间。较高的值（如 0.7）将使输出更加随机，而较低的值（如 0.2）将使其更加集中和确定性。     | float      | 如果设置，值域须为 [0, 1] 我们推荐 0.3，以达到较合适的效果。                                                                                                                                                                 |
| top_p       | 另一种采样温度                                                                                                           | float      | 默认 1.0                                                                                                                                                                                                                     |
| n           | 为每条输入消息生成多少个结果                                                                                             | int        | 默认 1，不得大于 5 特别的，当 temperature 非常小靠近 0 的时候，我们只能返回 1 个结果，如果这个时候 n 设置并 > 1，我们服务会返回不合法的输入参数（ invalid_request_error ）。                                                 |
| stream      | 是否流式返回                                                                                                             | bool       | 默认 false, 可选 true                                                                                                                                                                                                        |

### 支持模型列表

- gpt-3.5-turbo
- gpt-4
- gpt-4-1106-preview
- gpt-4-all
- gpt-4-vision-preview
- bing
- claude-2.1
- gemini-pro
- chatglm_pro
- SparkDesk
- qwen-plus
- ERNIE-Bot-4

只需要在请求时把模型名称换成上述想要的名称即可，比如用 claude（请求前请设置好`API_KEY`，比如`export API_KEY="sk-xxx"`）

```bash
curl https://api.rcouyi.com/v1/chat/completions \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{
        "model": "claude-2.1",
        "messages": [{"role": "user", "content": "Say this is a test!"}],
        "temperature": 0.7
      }'
```

## 语音

### TTS

将文本转换为语音。`post https://api.openai.com/v1/audio/speech`

```bash
curl https://api.rcouyi.com/v1/audio/speech \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "tts-1",
    "input": "你好啊",
    "voice": "alloy"
  }' \
  --output speech.mp3
```

参数说明：

- model：必填，下列为支持模型列表：
  - tts-1，tts-1-hd
- input：必填，要语音化的内容
- voice：必填，声音种类
  - alloy, echo, fable, onyx, nova, shimmer
- response_format：选填，返回文件的种类，默认为 mp3，可选种类为：
  - mp3, opus, aac, flac
- speed：选填，语速，默认为 1，值越小，速度越慢
  - 支持 0.25 到 4.0 之间
- 返回为语音文件

### 语音转文本

将音频转录为输入语言。
`post https://api.openai.com/v1/audio/transcriptions`

```bash
curl https://api.openai.com/v1/audio/transcriptions \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: multipart/form-data" \
  -F file="@/path/to/file/audio.mp3" \
  -F model="whisper-1"
```

返回

```json
{
  "text": "Imagine the wildest idea that you've ever had, and you're curious about how it might scale to something that's a 100, a 1,000 times bigger. This is a place where you can get to do that."
}
```

参数说明：

- file：必需的，要转录的音频文件对象（不是文件名），采用以下格式之一：flac、mp3、mp4、mpeg、mpga、m4a、ogg、wav 或 webm。
- model：必需的，要使用的模型的 ID。当前仅 whisper-1 可用。
- language：可选的，输入音频的语言。以 ISO-639-1 格式提供输入语言将提高准确性和延迟。
- prompt：可选的，用于指导模型风格或继续之前的音频片段的可选文本。提示应与音频语言相匹配。
- response_format：可选的，默认为 json，转录输出的格式，采用以下选项之一：json、text、srt、verbose_json 或 vtt。
- temperature：可选的，默认为 0，采样温度，介于 0 和 1 之间。较高的值（如 0.8）将使输出更加随机，而较低的值（如 0.2）将使其更加集中和确定性。如果设置为 0，模型将使用对数概率自动升高温度，直到达到特定阈值。
  返回：转录的文本。
