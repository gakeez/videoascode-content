---
image: /generated/articles-docs-miscellaneous-snippets-hls.png
title: 'HLS 支持（HTTP 实时流媒体）'
sidebar_label: 'HTTP 实时流媒体'
id: hls
crumb: '视频'
---

Remotion 目前不原生支持 HLS / HTTP 实时流媒体 / `.m3u8` 文件。

要在所有浏览器中获得完整的 HLS 支持并在渲染期间使用，一旦 [Mediabunny](/docs/mediabunny) 添加了对它的支持，我们将通过 [`@remotion/media`](/docs/media/video) 支持 HLS。  
虽然我们无法控制时间线，但我们已达成一致，HLS 应该被放在路线图的高优先级位置。  
[在此处关注此问题的状态](https://github.com/Vanilagy/mediabunny/issues/89)。

## 使用 hls.js 在预览期间播放 HLS 视频

你可以在 [`<Player>`](/docs/player) 和 Remotion Studio 的预览期间播放 HLS 视频。

注意以下注意事项：

<Step>1</Step> 此代码仅展示如何将视频标签连接到 HLS 流，尚未在实际项目中测试。 <br />
<Step>2</Step> 使用此代码渲染视频为 MP4 时，音频将不起作用。在渲染期间使用替代源。  
<br /> 请参阅 [在预览和渲染中使用不同的标签](/docs/video-tags#using-a-different-tag-in-preview-and-rendering) 和 [`useRemotionEnvironment()`](/docs/use-remotion-environment) 了解如何根据你是渲染还是预览来使用不同的组件。
<br />

```tsx twoslash title="HlsDemo.tsx"
import Hls from 'hls.js';
import React, {useEffect, useRef} from 'react';
import {AbsoluteFill, RemotionVideoProps, Html5Video} from 'remotion';

const HlsVideo: React.FC<RemotionVideoProps> = ({src}) => {
  const videoRef = useRef<HTMLVideoElement>(null);

  useEffect(() => {
    if (!src) {
      throw new Error('src is required');
    }

    const startFrom = 0;

    const hls = new Hls({
      startLevel: 4,
      maxBufferLength: 5,
      maxMaxBufferLength: 5,
    });

    hls.on(Hls.Events.MANIFEST_PARSED, () => {
      hls.startLoad(startFrom);
    });

    hls.loadSource(src);
    hls.attachMedia(videoRef.current!);

    return () => {
      hls.destroy();
    };
  }, [src]);

  return <Html5Video ref={videoRef} src={src} />;
};

export const HlsDemo: React.FC = () => {
  return (
    <AbsoluteFill>
      <HlsVideo src="https://stream.mux.com/nqGuji1CJuoPoU3iprRRhiy3HXiQN0201HLyliOg44HOU.m3u8" />
    </AbsoluteFill>
  );
};
```

## Chrome v142+ 中的原生支持

从 Chrome v142（2025年10月）开始，Chrome 支持原生 HLS 播放。  
这意味着 [`<OffthreadVideo />`](/docs/offthreadvideo) 可以在 Chrome 浏览器中原生播放 HLS / HTTP 实时流媒体 / `.m3u8` 文件（预览期间，非渲染期间），无需任何额外设置。

## 另请参阅

- [此功能请求的 GitHub issue](https://github.com/remotion-dev/remotion/issues/2930)
