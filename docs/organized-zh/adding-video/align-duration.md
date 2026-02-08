---
image: /generated/articles-docs-miscellaneous-snippets-align-duration.png
title: 如何让 composition 的时长与我的视频相同？
sidebar_label: 使时间线时长相同
crumb: 'FAQ'
---

如果你有一个渲染视频的组件：

```tsx twoslash title="MyComp.tsx"
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('video.mp4')} />;
};
```

并且你想让 composition 的时长与视频相同，首先将视频源设为 React prop：

```tsx twoslash title="MyComp.tsx"
import React from 'react';
import {OffthreadVideo, staticFile} from 'remotion';

type MyCompProps = {
  src: string;
};

export const MyComp: React.FC<MyCompProps> = ({src}) => {
  return <OffthreadVideo src={src} />;
};
```

然后，定义一个 [`calculateMetadata()`](/docs/calculate-metadata) 函数，根据视频计算 composition 的时长。  
如有必要，[安装 `@remotion/media-parser`](/docs/media-parser)。

```tsx twoslash title="MyComp.tsx"
type MyCompProps = {
  src: string;
};

// ---cut---

import {CalculateMetadataFunction} from 'remotion';
import {parseMedia} from '@remotion/media-parser';

export const calculateMetadata: CalculateMetadataFunction<MyCompProps> = async ({props}) => {
  const {slowDurationInSeconds, dimensions} = await parseMedia({
    src: props.src,
    fields: {
      slowDurationInSeconds: true,
      dimensions: true,
    },
  });

  if (dimensions === null) {
    // 例如当传递 MP3 文件时：
    throw new Error('Not a video file');
  }

  const fps = 30;

  return {
    durationInFrames: Math.floor(slowDurationInSeconds * fps),
    fps,
    width: dimensions.width,
    height: dimensions.height,
  };
};
```

:::note
如果你的资源未启用 CORS，你可以使用 [`@remotion/media-utils`](/docs/media-utils) 中的 [`getVideoMetadata`](/docs/get-video-metadata) 函数代替 `parseMedia()`。
:::

最后，将 `calculateMetadata` 函数传递给 `Composition` 组件，并将之前硬编码的 `src` 定义为默认 prop：

```tsx twoslash title="Root.tsx"
// @filename: MyComp.tsx
import React from 'react';
import {CalculateMetadataFunction} from 'remotion';
import {getVideoMetadata} from '@remotion/media-utils';

export const MyComp: React.FC<MyCompProps> = () => {
  return null;
};
type MyCompProps = {
  src: string;
};

export const calculateMetadata: CalculateMetadataFunction<MyCompProps> = async ({props}) => {
  const data = await getVideoMetadata(props.src);
  const fps = 30;

  return {
    durationInFrames: Math.floor(data.durationInSeconds * fps),
    fps,
  };
};

// @filename: Root.tsx
// ---cut---

import React from 'react';
import {Composition} from 'remotion';
import {MyComp, calculateMetadata} from './MyComp';

export const Root: React.FC = () => {
  return (
    <Composition
      id="MyComp"
      component={MyComp}
      durationInFrames={300}
      fps={30}
      width={1920}
      height={1080}
      defaultProps={{
        src: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4',
      }}
      calculateMetadata={calculateMetadata}
    />
  );
};
```

## 如何让 composition 的时长与我的音频相同？

遵循相同的步骤，但使用 [`@remotion/media-utils`](/docs/media-utils) 中的 [`getAudioDurationInSeconds()`](/docs/get-audio-duration-in-seconds) 代替 [`parseMedia()`](/docs/media-parser) 来根据音频文件计算 composition 的时长。

## 另请参阅

- [可变时长](/docs/dynamic-metadata)
