---
image: /generated/articles-docs-audio-trimming.png
title: 修剪音频
sidebar_label: 修剪音频
id: trimming
crumb: '音频'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

[`<Html5Audio />`](/docs/html5-audio) 标签支持 [`trimBefore`](/docs/html5-audio#trimbefore--trimafter) 和 [`trimAfter`](/docs/html5-audio#trimbefore--trimafter) props。
使用它们，你可以修剪掉音频的部分内容。

```tsx twoslash {8} title="MyComp.tsx"
import {AbsoluteFill, Html5Audio, staticFile, useVideoConfig} from 'remotion';

export const MyComposition = () => {
  const {fps} = useVideoConfig();

  return (
    <AbsoluteFill>
      <Html5Audio src={staticFile('audio.mp3')} trimBefore={2 * fps} trimAfter={4 * fps} />
    </AbsoluteFill>
  );
};
```

这将使音频播放 `00:02:00` 到 `00:04:00` 的范围，意味着音频将播放 2 秒。

音频仍将在开始时立即播放 - 要了解如何将音频延迟到合成中稍后显示，请参阅[下一篇文章](/docs/audio/delaying)。

:::note[旧版 props]
你也可以使用已弃用的 `startFrom` 和 `endAt` props，但新的 `trimBefore` 和 `trimAfter` props 更受推荐。
:::
