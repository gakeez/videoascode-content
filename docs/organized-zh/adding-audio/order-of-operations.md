---
image: /generated/articles-docs-audio-order-of-operations.png
title: 操作顺序
sidebar_label: 操作顺序
id: order-of-operations
crumb: '音频'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import {AvailableFrom} from '../../src/components/AvailableFrom';

在 Remotion v4.0.141 之前，音频和视频操作的应用顺序并未定义。预览和渲染中的行为可能会不同。

从 Remotion v4.0.141 开始，操作顺序保证如下：

1. 修剪音频（使用 [`trimBefore`](/docs/html5-audio#trimbefore--trimafter)）。
2. 偏移音频（通过将其放入 [`<Sequence>`](/docs/sequence)）。
3. 拉伸音频（通过添加 [`playbackRate`](/docs/html5-audio#playbackrate)）。

以 30 FPS、60 帧长的合成为例：

1. 一个 [`<Html5Audio>`](/docs/html5-audio) 标签的 [`trimBefore`](/docs/html5-audio#trimbefore--trimafter) 值为 45。音频的前 1.5 秒被修剪掉。
2. [`<Html5Audio>`](/docs/html5-audio) 标签位于一个从 `30` 开始的 [`<Sequence>`](/docs/sequence) 中。音频仅在 1.0 秒时间线标记处从 1.5 秒音频位置开始播放。
3. [`<Html5Audio>`](/docs/html5-audio) 的 [`playbackRate`](/docs/html5-audio#playbackrate) 为 `2`。音频速度提高 2 倍，但起始位置和开始偏移不受影响。
4. 合成长 60 帧，所以音频必须在 3.5 秒标记处停止：
   > (comp_duration - offset) \* playback_rate + start_from  
   > (60 - 30) \* 2 + 45 => 帧 105 或 3.5 秒标记
5. 结果：1.5 秒 - 3.5 秒的音频部分被剪切出来，并在 Remotion 时间线的第 30 到 59 帧之间以 2 倍速度播放。
