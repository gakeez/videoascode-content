---
image: /generated/articles-docs-audio-muting.png
title: 静音音频
sidebar_label: 静音音频
id: muting
crumb: '音频'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import {AvailableFrom} from '../../src/components/AvailableFrom';

你可以将 [`muted`](/docs/html5-audio#muted) prop 传递给 [`<Audio>`](/docs/media/audio)、[`<Video>`](/docs/media/video)、[`<OffthreadVideo>`](/docs/offthreadvideo)、[`<Html5Audio>`](/docs/html5-audio) 和 [`<Html5Video>`](/docs/html5-video)，甚至可以随时间改变它。

当 [`muted`](/docs/html5-audio#muted) 为 true 时，音频将在该时间点被省略。在以下示例中，我们在第 2 秒到第 4 秒之间静音轨道。

```tsx twoslash {9}
import {AbsoluteFill, staticFile, useCurrentFrame, useVideoConfig} from 'remotion';
import {Html5Audio} from 'remotion';

export const MyComposition = () => {
  const frame = useCurrentFrame();
  const {fps} = useVideoConfig();

  return (
    <AbsoluteFill>
      <Html5Audio src={staticFile('audio.mp3')} muted={frame >= 2 * fps && frame <= 4 * fps} />
    </AbsoluteFill>
  );
};
```
