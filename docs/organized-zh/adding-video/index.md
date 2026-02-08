---
image: /generated/articles-docs-videos-index.png
title: 将视频嵌入 Remotion
sidebar_label: 添加视频
crumb: '操作指南'
---

你可以使用 [`<OffthreadVideo>`](/docs/offthreadvideo) 组件将现有视频嵌入 Remotion。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src="https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4" />;
};
```

## 使用本地文件

将文件放入 [`public` 文件夹](/docs/terminology/public-dir)，并使用 [`staticFile()`](/docs/staticfile) 引用它。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('video.mp4')} />;
};
```

## 裁剪

通过使用 [`trimBefore`](/docs/offthreadvideo#trimbefore) prop，你可以移除视频的前几秒。  
在下面的示例中，视频的前两秒被跳过（假设 composition FPS 为 30）。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('video.mp4')} trimBefore={60} />;
};
```

同样，你可以使用 [`trimAfter`](/docs/offthreadvideo#trimafter) 来裁剪视频末尾。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('video.mp4')} trimBefore={60} trimAfter={120} />;
};
```

## 延迟

使用 [`<Sequence>`](/docs/sequence) 组件来延迟视频的出现。  
在下面的示例中，视频将在第 60 帧开始播放。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile, Sequence} from 'remotion';

export const MyComp: React.FC = () => {
  return (
    <Sequence from={60}>
      <OffthreadVideo src={staticFile('video.mp4')} />
    </Sequence>
  );
};
```

## 大小和位置

你可以使用 CSS 来调整视频的大小和位置。  
你会发现 `width`、`height`、`position`、`left`、`top`、`right`、`bottom`、`margin`、`aspectRatio` 和 `objectFit` 这些属性很有用。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return (
    <OffthreadVideo
      src={staticFile('video.mp4')}
      style={{
        width: 640,
        height: 360,
        position: 'absolute',
        top: 100,
        left: 100,
      }}
    />
  );
};
```

## 音量

你可以使用 [`volume`](/docs/offthreadvideo#volume) prop 来设置视频的音量。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('video.mp4')} volume={0.5} />;
};
```

你还可以使用 [`muted`](/docs/offthreadvideo#muted) prop 来静音视频。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('video.mp4')} muted />;
};
```

有关更多控制音量的方法，请参阅 [使用音频](/docs/audio/volume)。

## 速度

你可以使用 [`playbackRate`](/docs/offthreadvideo#playbackrate) prop 来更改视频的速度。

```tsx twoslash
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('video.mp4')} playbackRate={2} />;
};
```

这只适用于速度恒定的情况。另请参阅：[随时间改变视频速度](/docs/miscellaneous/snippets/accelerated-video)。

## 循环

请参阅：[循环播放 `<OffthreadVideo>`](/docs/offthreadvideo#looping-a-offthreadvideo)

## GIF

请参阅：[使用 GIF](/docs/gif)

## 另请参阅

- [`<OffthreadVideo>`](/docs/offthreadvideo)
- [使用音频](/docs/using-audio)
