---
image: /generated/articles-docs-layers.png
id: layers
title: 图层
crumb: '设计视频'
---

与普通网站不同，视频有固定的尺寸。这意味着，使用 `position: "absolute"` 是可以的！

在 Remotion 中，你经常想要将元素"分层"叠加在一起。
这是视频编辑器和 Photoshop 中的常见模式。

一个简单的方法是使用 [`<AbsoluteFill>`](/docs/absolute-fill) 组件：

```tsx twoslash title="MyComp.tsx"
import React from 'react';
import {AbsoluteFill, Img, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return (
    <AbsoluteFill>
      <AbsoluteFill>
        <Img src={staticFile('bg.png')} />
      </AbsoluteFill>
      <AbsoluteFill>
        <h1>这段文字显示在视频上方！</h1>
      </AbsoluteFill>
    </AbsoluteFill>
  );
};
```

这会将文字渲染在图像上方。

如果你只想在特定持续时间内显示元素，可以使用 [`<Sequence>`](/docs/sequence) 组件，它默认也是绝对定位的。

```tsx twoslash title="MyComp.tsx"
import React from 'react';
import {AbsoluteFill, Img, staticFile, Sequence} from 'remotion';

export const MyComp: React.FC = () => {
  return (
    <AbsoluteFill>
      <Sequence>
        <Img src={staticFile('bg.png')} />
      </Sequence>
      <Sequence from={60} durationInFrames={40}>
        <h1>这段文字在 60 帧后出现！</h1>
      </Sequence>
    </AbsoluteFill>
  );
};
```

在树中位置越低的绝对定位元素，在图层堆栈中的位置就越高。
如果你了解这种行为，你可以利用它并在大多数情况下避免使用 `z-index`。

## 另请参阅

- [`<AbsoluteFill>`](/docs/absolute-fill)
- [`<Sequence>`](/docs/sequence)
- [构建基于时间线的视频编辑器](/docs/building-a-timeline)
