---
image: /generated/articles-docs-video-manipulation.png
id: video-manipulation
title: 视频操作
sidebar_label: 操作像素
crumb: '操作指南'
---

import {VideoCanvasExamples} from '../components/GreenscreenExamples/index';

你可以使用 [`drawImage()`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/drawImage) API 将 [`<OffthreadVideo>`](/docs/offthreadvideo)、[`<Video>`](/docs/media/video) 或 [`<Html5Video>`](/docs/html5-video) 的帧绘制到 `<canvas>` 元素上。

:::note
在预览期间，使用 [`requestVideoFrameCallback()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLVideoElement/requestVideoFrameCallback) API。  
浏览器支持：Firefox 130（2024年8月）、Chrome 83、Safari 15.4。
:::

## 基本示例

在此示例中，一个 [`<OffthreadVideo>`](/docs/offthreadvideo) 被渲染并隐藏。  
每个发出的帧都被绘制到 Canvas 上，并应用灰度 [`filter`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/filter)。

<VideoCanvasExamples type="base" />
<br />

```tsx twoslash
import React, {useCallback, useEffect, useRef} from 'react';
import {AbsoluteFill, useVideoConfig, OffthreadVideo} from 'remotion';
// ---cut---
export const VideoOnCanvas: React.FC = () => {
  const video = useRef<HTMLVideoElement>(null);
  const canvas = useRef<HTMLCanvasElement>(null);
  const {width, height} = useVideoConfig();

  // 处理帧
  const onVideoFrame = useCallback(
    (frame: CanvasImageSource) => {
      if (!canvas.current) {
        return;
      }
      const context = canvas.current.getContext('2d');

      if (!context) {
        return;
      }

      context.filter = 'grayscale(100%)';
      context.drawImage(frame, 0, 0, width, height);
    },
    [height, width],
  );

  return (
    <AbsoluteFill>
      <AbsoluteFill>
        <OffthreadVideo
          // 隐藏原始视频标签
          style={{opacity: 0}}
          onVideoFrame={onVideoFrame}
          src="http://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4"
        />
      </AbsoluteFill>
      <AbsoluteFill>
        <canvas ref={canvas} width={width} height={height} />
      </AbsoluteFill>
    </AbsoluteFill>
  );
};
```

## 绿幕示例

在此示例中，我们遍历图像缓冲区中的每个像素，如果它是绿色的，就将其透明化。拖动下面的滑块使视频变透明。

<VideoCanvasExamples type="greenscreen" />
<br />

```tsx twoslash
declare global {
  interface VideoFrameMetadata {
    presentationTime: DOMHighResTimeStamp;
    expectedDisplayTime: DOMHighResTimeStamp;
    width: number;
    height: number;
    mediaTime: number;
    presentedFrames: number;
    processingDuration?: number;
    captureTime?: DOMHighResTimeStamp;
    receiveTime?: DOMHighResTimeStamp;
    rtpTimestamp?: number;
  }
  type VideoFrameRequestCallbackId = number;
  interface HTMLVideoElement extends HTMLMediaElement {
    requestVideoFrameCallback(callback: (now: DOMHighResTimeStamp, metadata: VideoFrameMetadata) => any): VideoFrameRequestCallbackId;
    cancelVideoFrameCallback(handle: VideoFrameRequestCallbackId): void;
  }
}
import React, {useCallback, useEffect, useRef} from 'react';
import {AbsoluteFill, useVideoConfig, OffthreadVideo} from 'remotion';

// ---cut---
export const Greenscreen: React.FC<{
  opacity: number;
}> = ({opacity}) => {
  const canvas = useRef<HTMLCanvasElement>(null);
  const {width, height} = useVideoConfig();

  // 处理帧
  const onVideoFrame = useCallback(
    (frame: CanvasImageSource) => {
      if (!canvas.current) {
        return;
      }
      const context = canvas.current.getContext('2d');

      if (!context) {
        return;
      }

      context.drawImage(frame, 0, 0, width, height);
      const imageFrame = context.getImageData(0, 0, width, height);
      const {length} = imageFrame.data;

      // 如果像素非常绿，就降低 alpha 通道
      for (let i = 0; i < length; i += 4) {
        const red = imageFrame.data[i + 0];
        const green = imageFrame.data[i + 1];
        const blue = imageFrame.data[i + 2];
        if (green > 100 && red < 100 && blue < 100) {
          imageFrame.data[i + 3] = opacity * 255;
        }
      }
      context.putImageData(imageFrame, 0, 0);
    },
    [height, width],
  );

  return (
    <AbsoluteFill>
      <AbsoluteFill>
        <OffthreadVideo style={{opacity: 0}} onVideoFrame={onVideoFrame} src="https://remotion.media/greenscreen.mp4" />
      </AbsoluteFill>
      <AbsoluteFill>
        <canvas ref={canvas} width={width} height={height} />
      </AbsoluteFill>
    </AbsoluteFill>
  );
};
```

## v4.0.190 之前

在 v4.0.190 之前，[`<OffthreadVideo>`](/docs/offthreadvideo) 和 [`<Html5Video>`](/docs/html5-video) 不支持 `onVideoFrame` prop。  
你只能使用 `requestVideoFrameCallback` API 来操作 `<Html5Video>`。  
点击 [此处](https://github.com/remotion-dev/remotion/blob/b966d0e99cfb91478ca697675f822284da1f9055/packages/docs/docs/video-manipulation.mdx) 查看此页面的旧版本。
