---
image: /generated/articles-docs-audio-speed.png
title: 控制播放速度
sidebar_label: 音频速度
id: speed
crumb: '音频'
---

# 控制播放速度<AvailableFrom v="2.2" />

你可以使用 [`playbackRate`](/docs/html5-audio#playbackrate) prop 来控制音频的速度。

- `1` 是默认值。
- `0.5` 减慢音频速度，使其时长变为两倍
- `2` 加快音频速度，使其速度变为两倍
- Google Chrome 支持 `0.0625` 到 `16` 之间的播放速率。

```tsx twoslash {6} title="MyComp.tsx"
import {AbsoluteFill, Html5Audio, staticFile} from 'remotion';

export const MyComposition = () => {
  return (
    <AbsoluteFill>
      <Html5Audio src={staticFile('audio.mp3')} playbackRate={2} />
    </AbsoluteFill>
  );
};
```
