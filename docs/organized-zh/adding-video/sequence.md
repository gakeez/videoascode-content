---
image: /generated/articles-docs-videos-sequence.png
title: 按顺序播放视频
sidebar_label: 按顺序播放视频
crumb: '操作指南'
---

如果你想按顺序播放多个视频，你可以：

<Step>1</Step> 定义一个组件，渲染一系列 [`<Series>`](/docs/series) 包裹的 [`<OffthreadVideo>`](/docs/offthreadvideo) 组件。
<br/><Step>2</Step> 创建一个 [`calculateMetadata()`](/docs/calculate-metadata) 函数，获取每个视频的时长。
<br/><Step>3</Step> 注册一个 [`<Composition>`](/docs/composition)，指定视频列表。

## 基本示例

首先创建一个组件，使用 [`<Series>`](/docs/series) 和 [`<OffthreadVideo>`](/docs/offthreadvideo) 组件渲染视频列表：

```tsx twoslash title="VideosInSequence.tsx"
import React from 'react';
import {OffthreadVideo, Series} from 'remotion';

type VideoToEmbed = {
  src: string;
  durationInFrames: number | null;
};

type Props = {
  videos: VideoToEmbed[];
};

export const VideosInSequence: React.FC<Props> = ({videos}) => {
  return (
    <Series>
      {videos.map((vid) => {
        if (vid.durationInFrames === null) {
          throw new Error('Could not get video duration');
        }

        return (
          <Series.Sequence key={vid.src} durationInFrames={vid.durationInFrames}>
            <OffthreadVideo src={vid.src} />
          </Series.Sequence>
        );
      })}
    </Series>
  );
};
```

在同一文件中，创建一个计算 composition 元数据的函数：

<Step>1</Step> 调用 [`parseMedia()`](/docs/media-parser) 获取每个视频的时长。
<div style={{marginTop: -14}} />
<Step>2</Step> 创建一个 [`calculateMetadata()`](/docs/calculate-metadata) 函数，获取每个视频的时长。
<div style={{marginTop: -14}} />
<Step>3</Step> 将所有时长相加，得到 composition 的总时长。

```tsx twoslash title="VideosInSequence.tsx"
import React from 'react';
import {OffthreadVideo, staticFile, Series, CalculateMetadataFunction} from 'remotion';
import {parseMedia} from '@remotion/media-parser';

type VideoToEmbed = {
  src: string;
  durationInFrames: number | null;
};

type Props = {
  videos: VideoToEmbed[];
};

// ---cut---
export const calculateMetadata: CalculateMetadataFunction<Props> = async ({props}) => {
  const fps = 30;
  const videos = await Promise.all([
    ...props.videos.map(async (video): Promise<VideoToEmbed> => {
      const {slowDurationInSeconds} = await parseMedia({
        src: video.src,
        fields: {
          slowDurationInSeconds: true,
        },
      });

      return {
        durationInFrames: Math.floor(slowDurationInSeconds * fps),
        src: video.src,
      };
    }),
  ]);

  const totalDurationInFrames = videos.reduce((acc, video) => acc + (video.durationInFrames ?? 0), 0);

  return {
    props: {
      ...props,
      videos,
    },
    fps,
    durationInFrames: totalDurationInFrames,
  };
};
```

在你的 [root 文件](/docs/terminology/root-file)中，创建一个 [`<Composition>`](/docs/composition)，使用 `VideosInSequence` 组件和导出的 `calculateMetadata` 函数：

```tsx twoslash title="Root.tsx"
// @filename: VideosInSequence.tsx
import React from 'react';
import {OffthreadVideo, staticFile, Series, CalculateMetadataFunction} from 'remotion';
import {parseMedia} from '@remotion/media-parser';

type VideoToEmbed = {
  src: string;
  durationInFrames: number | null;
};

type Props = {
  videos: VideoToEmbed[];
};

export const calculateMetadata: CalculateMetadataFunction<Props> = async ({props}) => {
  const fps = 30;
  const videos = await Promise.all([
    ...props.videos.map(async (video): Promise<VideoToEmbed> => {
      const {slowDurationInSeconds} = await parseMedia({
        src: video.src,
        fields: {
          slowDurationInSeconds: true,
        },
      });

      return {
        durationInFrames: Math.floor(slowDurationInSeconds * fps),
        src: video.src,
      };
    }),
  ]);

  const totalDurationInFrames = videos.reduce((acc, video) => acc + video.durationInFrames!, 0);

  return {
    props: {
      ...props,
      videos,
    },
    fps,
    durationInFrames: totalDurationInFrames,
  };
};

export const VideosInSequence: React.FC<Props> = ({videos}) => {
  return (
    <Series>
      {videos.map((vid) => {
        if (vid.durationInFrames === null) {
          throw new Error('Could not get video duration');
        }

        return (
          <Series.Sequence key={vid.src} durationInFrames={vid.durationInFrames}>
            <OffthreadVideo src={staticFile('video.mp4')} />
          </Series.Sequence>
        );
      })}
    </Series>
  );
};

// @filename: Root.tsx
// ---cut---

import React from 'react';
import {Composition, staticFile} from 'remotion';
import {VideosInSequence, calculateMetadata} from './VideosInSequence';

export const Root: React.FC = () => {
  return (
    <Composition
      id="VideosInSequence"
      component={VideosInSequence}
      width={1920}
      height={1080}
      defaultProps={{
        videos: [
          {
            durationInFrames: null,
            src: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4',
          },
          {
            durationInFrames: null,
            src: staticFile('localvideo.mp4'),
          },
        ],
      }}
      calculateMetadata={calculateMetadata}
    />
  );
};
```

## 添加预加载

如果你只关心渲染时视频看起来流畅，可以跳过此步骤。  
如果你还希望在 Player 中获得流畅的预览播放，请考虑以下内容：

视频只会在即将播放时加载。  
为了获得更流畅的预览播放，我们应该对所有视频做两件事：

<Step>1</Step> 向 [`<Series.Sequence>`](/docs/series) 添加 [`premountFor`](/docs/series#premountfor) prop。这将在播放前不可见地预加载视频标签，给它一些加载时间。{' '}
<div style={{marginTop: -14}} />
<Step>2</Step> 添加 [`pauseWhenBuffering`](/docs/offthreadvideo#pausewhenbuffering) prop。如果视频仍需加载，这将使 Player 进入 [缓冲状态](/docs/player/buffer-state)。

```tsx twoslash title="VideosInSequence.tsx" {14, 17}
import React from 'react';
import {OffthreadVideo, Series, useVideoConfig} from 'remotion';

type VideoToEmbed = {
  src: string;
  durationInFrames: number | null;
};

type Props = {
  videos: VideoToEmbed[];
};

// ---cut---
export const VideosInSequence: React.FC<Props> = ({videos}) => {
  const {fps} = useVideoConfig();

  return (
    <Series>
      {videos.map((vid) => {
        if (vid.durationInFrames === null) {
          throw new Error('Could not get video duration');
        }

        return (
          <Series.Sequence key={vid.src} premountFor={4 * fps} durationInFrames={vid.durationInFrames}>
            <OffthreadVideo pauseWhenBuffering src={vid.src} />
          </Series.Sequence>
        );
      })}
    </Series>
  );
};
```

## 浏览器自动播放策略

移动浏览器对阻止 composition 开始后进入的视频自动播放更为严格。

如果你想确保所有视频都能流畅播放，还请 [阅读有关浏览器自动播放行为的说明](/docs/player/autoplay#media-that-enters-the-video-after-the-start) 并根据需要自定义行为。

## 另请参阅

- [`<OffthreadVideo>`](/docs/offthreadvideo)
- [`<Series>`](/docs/series)
- [`calculateMetadata()`](/docs/calculate-metadata)
- [`parseMedia()`](/docs/media-parser)
- [`<Composition>`](/docs/composition)
- [Root 文件](/docs/terminology/root-file)
- [Player 缓冲状态](/docs/player/buffer-state)
- [避免 Player 闪烁](/docs/troubleshooting/player-flicker)
- [解决自动播放问题](/docs/player/autoplay)
