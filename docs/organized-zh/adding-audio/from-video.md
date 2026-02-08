---
image: /generated/articles-docs-audio-from-video.png
title: 使用视频中的音频
sidebar_label: 使用视频中的音频
id: from-video
crumb: '音频'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

[`<Video>`](/docs/media/video)、[`<Html5Video>`](/docs/html5-video) 和 [`<OffthreadVideo>`](/docs/offthreadvideo) 标签中的音频也会包含在输出中。

同样的原则适用于音频 - 你可以 [修剪](/docs/audio/trimming)、[延迟](/docs/audio/delaying)、[静音](/docs/audio/muting)、[加速](/docs/audio/speed) 和 [降低音量](/docs/audio/volume) 你的视频。

```tsx twoslash {6} title="MyComp.tsx"
import {AbsoluteFill, OffthreadVideo, staticFile} from 'remotion';

export const MyComposition = () => {
  return (
    <AbsoluteFill>
      <OffthreadVideo src={staticFile('video.mp4')} playbackRate={2} volume={0.5} />
    </AbsoluteFill>
  );
};
```
