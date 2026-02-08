---
image: /generated/articles-docs-audio-delaying.png
title: 延迟音频
sidebar_label: 延迟音频
id: delaying
crumb: '音频'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

使用带有正值 [`from`](/docs/sequence#from) 的 [`<Sequence>`](/docs/sequence) 来延迟音频播放。

在以下示例中，音频将在 100 帧后开始播放。

```tsx twoslash {6-8}
import {AbsoluteFill, Html5Audio, Sequence, staticFile} from 'remotion';

export const MyComposition = () => {
  return (
    <AbsoluteFill>
      <Sequence from={100}>
        <Html5Audio src={staticFile('audio.mp3')} />
      </Sequence>
    </AbsoluteFill>
  );
};
```
