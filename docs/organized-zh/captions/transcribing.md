---
image: /generated/articles-docs-captions-transcribing.png
sidebar_label: 转录
title: 转录音频
crumb: 字幕
---

# 转录音频

Remotion 提供了多种内置选项用于转录音频以生成字幕：

- [`@remotion/install-whisper-cpp`](/docs/install-whisper-cpp) - 使用 [Whisper.cpp](https://github.com/ggerganov/whisper.cpp) 在服务器本地转录音频
- [`@remotion/whisper-web`](/docs/whisper-web) - 使用 WebAssembly 在浏览器中转录音频
- [`@remotion/openai-whisper`](/docs/openai-whisper) - 使用 OpenAI Whisper API 进行云端转录

## 对比

|                      | [`@remotion/install-whisper-cpp`](/docs/install-whisper-cpp) | [`@remotion/whisper-web`](/docs/whisper-web)    | [`@remotion/openai-whisper`](/docs/openai-whisper)                                    |
| -------------------- | ------------------------------------------------------------ | ----------------------------------------------- | ------------------------------------------------------------------------------------- |
| **环境**      | 服务器 (Node.js)                                             | 客户端 (浏览器)                                | 云端 (API)                                                                           |
| **速度**            | 快（取决于硬件）                                   | 慢（WASM 开销）                            | 快                                                                                  |
| **费用**             | 免费                                                         | 免费                                            | 付费 (OpenAI API 定价)                                                             |
| **离线支持**  | ✅                                                           | ✅                                              | ❌                                                                                    |
| **无需服务器** | ❌                                                           | ✅                                              | ✅                                                                                    |
| **转换函数** | [`toCaptions()`](/docs/install-whisper-cpp/to-captions)      | [`toCaptions()`](/docs/whisper-web/to-captions) | [`openaiWhisperApiToCaptions()`](/docs/openai-whisper/openai-whisper-api-to-captions) |

## `Caption` 类型

所有这些选项都可以输出 [`Caption`](/docs/captions/caption) 类型格式的字幕，推荐与 Remotion 一起使用。此格式：

- 支持使用 [`@remotion/captions`](/docs/captions/api) 中的 API，如 [`createTikTokStyleCaptions()`](/docs/captions/create-tiktok-style-captions)
- 与 [Remotion Editor Starter](https://remotion.pro/store/editor-starter) 使用的格式匹配
- 与 [Animated Captions](https://remotion.pro/store/animated-captions) 包兼容

## 替代方案

你可以使用其他转录音频的方式，如 [ElevenLabs](https://elevenlabs.io/)。
你也可以定义自己的字幕格式，而不依赖 `Caption` 类型 - 本页仅涉及内置选项。

## 另请参阅

- [`Caption`](/docs/captions/caption) - 字幕数据结构
- [`@remotion/captions`](/docs/captions/api) - 字幕操作工具
