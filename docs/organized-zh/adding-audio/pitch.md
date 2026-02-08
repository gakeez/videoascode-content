---
image: /generated/articles-docs-audio-pitch.png
title: 控制音高
sidebar_label: 控制音高
id: pitch
crumb: '音频'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

:::warning
音高校正目前仅在渲染期间应用。
:::

你可以使用 [`toneFrequency`](/docs/html5-audio#tonefrequency) prop 在渲染期间控制音频的音高。

接受 `0.01` 到 `2` 之间的值，其中 `1` 表示原始音高。小于 `1` 的值会降低音高，而大于 `1` 的值会提高音高。

[`toneFrequency`](/docs/html5-audio#tonefrequency) 为 0.5 会将音高降低一半，[`toneFrequency`](/docs/html5-audio#tonefrequency) 为 `1.5` 会将音高提高 50%。

```tsx twoslash {7} title="MyComp.tsx"
import {Html5Audio, staticFile, AbsoluteFill} from 'remotion';

export const MyComposition = () => {
  return (
    <AbsoluteFill>
      <div>Hello World!</div>
      <Html5Audio src={staticFile('audio.mp3')} toneFrequency={0.8} />
    </AbsoluteFill>
  );
};
```
