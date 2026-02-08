---
image: /generated/articles-docs-videos-transparency.png
title: 嵌入透明视频
sidebar_label: 添加透明视频
crumb: '操作指南'
---

你可以在 Remotion 中嵌入透明视频。

## 使用 Alpha 通道

要嵌入带有 alpha 通道的视频，请使用 [`<OffthreadVideo>`](/docs/offthreadvideo) 组件并添加 [`transparent`](/docs/offthreadvideo#transparent) prop。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('transparent.webm')} transparent />;
};
```

## 不使用 Alpha 通道

要嵌入没有 alpha 通道但只有黑色背景的视频，请将 `mixBlendMode: "screen"` 添加到 [`<OffthreadVideo>`](/docs/offthreadvideo) 组件的 [`style`](/docs/offthreadvideo#style) prop 中。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return (
    <OffthreadVideo
      src={staticFile('nottransparent.mp4')}
      style={{
        mixBlendMode: 'screen',
      }}
    />
  );
};
```

## 绿幕效果

要根据像素颜色移除背景，请参阅 [绿幕示例](/docs/video-manipulation#greenscreen-example)。

## 渲染透明视频

要导出带有透明度的视频，请参阅 [透明视频](/docs/transparent-videos/)
