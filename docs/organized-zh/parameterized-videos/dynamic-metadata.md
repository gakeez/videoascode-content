---
image: /generated/articles-docs-dynamic-metadata.png
id: dynamic-metadata
title: 可变时长和尺寸
sidebar_label: 可变时长
crumb: 操作指南
---

你可以根据某些异步确定的数据更改视频的时长。
同样适用于视频的宽度、高度和帧率。

## 使用 `calculateMetadata()` 函数<AvailableFrom v="4.0.0"/>

考虑一个场景，视频被动态指定为背景，而合成的时长应与视频的时长对齐。

向 [`<Composition>`](/docs/composition) 传递一个 [`calculateMetadata`](/docs/composition#calculatemetadata) 回调函数。该函数应接收[合并后的属性](/docs/props-resolution)并计算元数据。

```tsx twoslash title="src/Root.tsx"
// @filename: get-media-metadata.ts
import {Input, ALL_FORMATS, UrlSource} from 'mediabunny';

export const getMediaMetadata = async (src: string) => {
  const input = new Input({
    formats: ALL_FORMATS,
    source: new UrlSource(src, {
      getRetryDelay: () => null,
    }),
  });

  const durationInSeconds = await input.computeDuration();
  const videoTrack = await input.getPrimaryVideoTrack();
  const dimensions = videoTrack
    ? {
        width: videoTrack.displayWidth,
        height: videoTrack.displayHeight,
      }
    : null;
  const packetStats = await videoTrack?.computePacketStats();
  const fps = packetStats?.averagePacketRate ?? null;

  return {
    durationInSeconds,
    dimensions,
    fps,
  };
};

// @filename: index.tsx
// ---cut---
import React from 'react';
import {Composition} from 'remotion';
import {Video} from '@remotion/media';
import {getMediaMetadata} from './get-media-metadata';

type MyCompProps = {
  src: string;
};

const MyComp: React.FC<MyCompProps> = ({src}) => {
  return <Video src={src} />;
};

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
      calculateMetadata={async ({props}) => {
        const {durationInSeconds} = await getMediaMetadata(props.src);

        return {
          durationInFrames: Math.floor(durationInSeconds * 30),
        };
      }}
    />
  );
};
```

:::note
有关 `get-media-metadata.ts` 的代码片段，请参阅[使用 Mediabunny 获取元数据](/docs/mediabunny/metadata)。
:::

`props` 默认为指定的 `defaultProps`，但可以通过[向渲染传递输入属性](/docs/props-resolution)来覆盖。

返回一个带有 `durationInFrames` 的对象来更改视频的时长。
此外或替代地，你也可以返回 `fps`、`width` 和 `height` 来更新视频的分辨率和帧率。

也可以通过同时返回一个 `props` 字段来[转换传递给组件的属性](/docs/data-fetching)。

## 使用 `useEffect()` 和 `getInputProps()`

在以下示例中，Remotion 被指示等待 [`getMediaMetadata()`](/docs/mediabunny/metadata) Promise 解析完成后再评估合成。

通过调用 [`delayRender()`](/docs/delay-render)，Remotion 将被阻止继续，直到调用 [`continueRender()`](/docs/continue-render)。

```tsx twoslash title="src/Root.tsx"
// @filename: get-media-metadata.ts
import {Input, ALL_FORMATS, UrlSource} from 'mediabunny';

export const getMediaMetadata = async (src: string) => {
  const input = new Input({
    formats: ALL_FORMATS,
    source: new UrlSource(src, {
      getRetryDelay: () => null,
    }),
  });

  const durationInSeconds = await input.computeDuration();
  const videoTrack = await input.getPrimaryVideoTrack();
  const dimensions = videoTrack
    ? {
        width: videoTrack.displayWidth,
        height: videoTrack.displayHeight,
      }
    : null;
  const packetStats = await videoTrack?.computePacketStats();
  const fps = packetStats?.averagePacketRate ?? null;

  return {
    durationInSeconds,
    dimensions,
    fps,
  };
};

// @filename: index.tsx
// ---cut---
import {useEffect, useState} from 'react';
import {Composition, continueRender, delayRender} from 'remotion';
import React from 'react';
const VideoTesting: React.FC = () => null;
// ---cut---
import {getMediaMetadata} from './get-media-metadata';

export const Index: React.FC = () => {
  const [handle] = useState(() => delayRender());
  const [duration, setDuration] = useState(1);

  useEffect(() => {
    getMediaMetadata('https://remotion.media/video.mp4')
      .then(({durationInSeconds}) => {
        setDuration(Math.round(durationInSeconds * 30));
        continueRender(handle);
      })
      .catch((err) => {
        console.log(`Error fetching metadata: ${err}`);
      });
  }, [handle]);

  return <Composition id="dynamic-duration" component={VideoTesting} width={1080} height={1080} fps={30} durationInFrames={duration} />;
};
```

:::note
有关 `get-media-metadata.ts` 的代码片段，请参阅[使用 Mediabunny 获取元数据](/docs/mediabunny/metadata)。
:::

要动态传递视频资源，你可以在渲染时[传递输入属性](/docs/props-resolution)，并在 React 代码中使用 [`getInputProps()`](/docs/get-input-props) 获取它们。

```tsx twoslash title="src/Root.tsx"
import {getInputProps} from 'remotion';

const inputProps = getInputProps();
const src = inputProps.src;
```

### 缺点

自 v4.0 起，这种技术不再被推荐，因为 `useEffect()` 不仅在 Remotion 最初计算视频元数据时执行，而且在生成渲染工作器时也会执行。

由于渲染过程可能是高度并发的，这可能会导致不必要的 API 调用和速率限制。

## 与尺寸覆盖一起使用

覆盖参数如 [`--width`](/docs/cli/render#--width) 将优先于你使用 `calculateMetadata()` 设置的变量尺寸。

[`--scale`](/docs/scaling) 参数具有最高优先级，将在覆盖参数和 `calculateMetadata()` 之后应用。

## 设计视频后更改尺寸和 FPS

如果你使用某些尺寸设计了视频，然后想要渲染不同的分辨率（例如 4K 而不是 Full HD），你可以使用[输出缩放](/docs/scaling)。

如果你使用特定的 FPS 设计了视频，然后想要更改帧率，你应该重构合成以确保[时序在更改帧率时保持一致](/docs/multiple-fps)。

## 与 `<Player>` 一起使用

如果传递给 [`<Player>`](/docs/player) 的元数据发生变化，它会做出反应。有两种可行的方式来动态设置 Player 的元数据：

<p>
  <Step>1</Step> 使用 <code>useEffect()</code> 执行数据获取：
</p>

```tsx twoslash title="MyApp.tsx"
// @filename: get-media-metadata.ts
import {Input, ALL_FORMATS, UrlSource} from 'mediabunny';

export const getMediaMetadata = async (src: string) => {
  const input = new Input({
    formats: ALL_FORMATS,
    source: new UrlSource(src, {
      getRetryDelay: () => null,
    }),
  });

  const durationInSeconds = await input.computeDuration();
  const videoTrack = await input.getPrimaryVideoTrack();
  const dimensions = videoTrack
    ? {
        width: videoTrack.displayWidth,
        height: videoTrack.displayHeight,
      }
    : null;
  const packetStats = await videoTrack?.computePacketStats();
  const fps = packetStats?.averagePacketRate ?? null;

  return {
    durationInSeconds,
    dimensions,
    fps,
  };
};

// @filename: index.tsx
const VideoTesting: React.FC = () => null;
// ---cut---
import React from 'react';
import {useEffect, useState} from 'react';
import {Player} from '@remotion/player';
import {getMediaMetadata} from './get-media-metadata';

export const Index: React.FC = () => {
  const [duration, setDuration] = useState(1);

  useEffect(() => {
    getMediaMetadata('https://remotion.media/video.mp4')
      .then(({durationInSeconds}) => {
        setDuration(Math.round(durationInSeconds * 30));
      })
      .catch((err) => {
        console.log(`Error fetching metadata: ${err}`);
      });
  }, []);

  return <Player component={VideoTesting} compositionWidth={1080} compositionHeight={1080} fps={30} durationInFrames={duration} />;
};
```

<p>
  <Step>2</Step> 调用你自己的 <code>calculateMetadata()</code> 函数，该函数从你的 Remotion 项目中复用：
</p>

```tsx twoslash title="MyApp.tsx"
import {useEffect, useState} from 'react';
import {CalculateMetadataFunction} from 'remotion';

const VideoTesting: React.FC = () => null;

// ---cut---
import {Player} from '@remotion/player';

type Props = {};

const calculateMetadataFunction: CalculateMetadataFunction<Props> = () => {
  return {
    props: {},
    durationInFrames: 1,
    width: 100,
    height: 100,
    fps: 30,
  };
};

type Metadata = {
  durationInFrames: number;
  compositionWidth: number;
  compositionHeight: number;
  fps: number;
  props: Props;
};

export const Index: React.FC = () => {
  const [metadata, setMetadata] = useState<Metadata | null>(null);

  useEffect(() => {
    Promise.resolve(
      calculateMetadataFunction({
        defaultProps: {},
        props: {},
        abortSignal: new AbortController().signal,
        compositionId: 'MyComp',
        isRendering: true,
      }),
    )
      .then(({durationInFrames, props, width, height, fps}) => {
        setMetadata({
          durationInFrames: durationInFrames as number,
          compositionWidth: width as number,
          compositionHeight: height as number,
          fps: fps as number,
          props: props as Props,
        });
      })
      .catch((err) => {
        console.log(`Error fetching metadata: ${err}`);
      });
  }, []);

  if (!metadata) {
    return null;
  }

  return <Player component={VideoTesting} {...metadata} />;
};
```

## 另请参阅

- [属性如何解析](/docs/props-resolution)
- [数据获取](/docs/data-fetching)
- [如何让合成的时长与我的视频相同？](/docs/miscellaneous/snippets/align-duration)
