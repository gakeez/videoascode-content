---
image: /generated/articles-docs-miscellaneous-snippets-accelerated-video.png
title: '随时间改变视频速度'
sidebar_label: 随时间改变速度
crumb: '代码片段'
---

:::note
这目前还不能与 [`@remotion/media`](/docs/media) 中的 [`<Video>`](/docs/media/video) 一起使用。
:::

import {AcceleratedVideoExample} from '../../../components/AcceleratedVideoPlayerExample.tsx';

要随时间加速视频 - 例如从常规速度开始，然后逐渐加快 - 我们需要编写一些代码以确保我们遵守 Remotion 的规则。

这并不像插值 [`playbackRate`](/docs/offthreadvideo#playbackrate) 那么简单：

```tsx twoslash title="❌ 不起作用"
import {interpolate, OffthreadVideo} from 'remotion';
let frame = 0;
// ---cut---
<OffthreadVideo playbackRate={interpolate(frame, [0, 100], [1, 5])} src="https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4#disable" />;
```

这是因为 Remotion 会独立于其他帧评估每一帧。如果 `frame` 是 100，`playbackRate` 评估为 5，Remotion 将渲染视频的第 500 帧，这是不希望的，因为它没有考虑到速度到目前为止一直在增加到 5。

正确的方法是计算到当前帧为止已经经过的总时间，通过累积之前帧的播放速率。然后我们从该帧开始视频，并将当前帧的播放速率设置为确保视频流畅播放。

```tsx twoslash title="✅ AcceleratedVideo.tsx"
import React from 'react';
import {interpolate, Sequence, useCurrentFrame, OffthreadVideo} from 'remotion';

const remapSpeed = (frame: number, speed: (fr: number) => number) => {
  let framesPassed = 0;
  for (let i = 0; i <= frame; i++) {
    framesPassed += speed(i);
  }

  return framesPassed;
};

export const AcceleratedVideo: React.FC = () => {
  const frame = useCurrentFrame();

  const speedFunction = (f: number) => interpolate(f, [0, 500], [1, 5]);

  const remappedFrame = remapSpeed(frame, speedFunction);

  return (
    <Sequence from={frame}>
      <OffthreadVideo trimBefore={Math.round(remappedFrame)} playbackRate={speedFunction(frame)} src="https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4#disable" />
    </Sequence>
  );
};
```

:::note
我们正在 [禁用附加到视频 URL 的媒体片段提示](/docs/media-fragments)，方法是在 URL 末尾添加 `#disable`。
:::

请注意，Remotion Studio 中的时间线可能会在你播放视频时移动，因为我们随时间重新定位序列。只要我们以幂等的方式计算帧，这就没问题。

## 演示

<AcceleratedVideoExample />

## 另请参阅

- [`<OffthreadVideo>`](/docs/offthreadvideo)
- [不同片段不同速度](/docs/miscellaneous/snippets/different-segments-at-different-speeds)
- [跳剪](/docs/miscellaneous/snippets/jumpcuts)
