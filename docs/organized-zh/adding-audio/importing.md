---
image: /generated/articles-docs-audio-importing.png
title: 导入音频
sidebar_label: 导入音频
id: importing
crumb: '音频'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

[将音频文件放入 `public/` 文件夹](/docs/assets) 并使用 [`staticFile()`](/docs/staticfile) 引用它。
向你的组件添加一个 [`<Html5Audio/>`](/docs/html5-audio) 标签来添加声音。

```tsx twoslash title="MyComp.tsx"
import {AbsoluteFill, Html5Audio, staticFile} from 'remotion';

export const MyComposition = () => {
  return (
    <AbsoluteFill>
      <Html5Audio src={staticFile('audio.mp3')} />
    </AbsoluteFill>
  );
};
```

你也可以通过传递 URL 来添加远程音频：

```tsx twoslash title="MyComp.tsx"
import {AbsoluteFill, Html5Audio} from 'remotion';

export const MyComposition = () => {
  return (
    <AbsoluteFill>
      <Html5Audio src="https://example.com/audio.mp3" />
    </AbsoluteFill>
  );
};
```

默认情况下，音频将从开始播放，音量为最大音量，长度为完整长度。
你可以通过添加更多音频标签来混合多个音轨。
