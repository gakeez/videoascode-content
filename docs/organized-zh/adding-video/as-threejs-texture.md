---
image: /generated/articles-docs-videos-as-threejs-texture.png
title: 在 Three.js 中使用视频作为纹理
crumb: '视频'
sidebar_label: 作为 Three.js 纹理
---

如果你想在 Three.js 中将视频作为纹理嵌入，你有多种选择。

## 使用 `@remotion/media`<AvailableFrom v="4.0.387" />

我们建议你以 [`headless`](/docs/media/video#headless) 模式挂载 [`<Video>`](/docs/media/video) 标签，并使用 [`onVideoFrame`](/docs/media/video#onvideoframe) prop 在绘制新帧时更新 Three.js 纹理。

```tsx twoslash title="VideoTexture.tsx"
import {useThree} from '@react-three/fiber';
import {Video} from '@remotion/media';
import {ThreeCanvas} from '@remotion/three';
import React, {useCallback, useState} from 'react';
import {useVideoConfig} from 'remotion';
import {CanvasTexture} from 'three';

const videoSrc = 'https://remotion.media/video.mp4';

const videoWidth = 1920;
const videoHeight = 1080;
const aspectRatio = videoWidth / videoHeight;
const scale = 3;
const planeHeight = scale;
const planeWidth = aspectRatio * scale;

const Inner: React.FC = () => {
  const [canvasStuff] = useState(() => {
    const canvas = new OffscreenCanvas(videoWidth, videoHeight);
    const context = canvas.getContext('2d')!;
    const texture = new CanvasTexture(canvas);
    return {canvas, context, texture};
  });

  const {invalidate} = useThree();

  const onVideoFrame = useCallback(
    (frame: CanvasImageSource) => {
      canvasStuff.context.drawImage(frame, 0, 0, videoWidth, videoHeight);
      // 这在预览期间是必需的 - 帧循环与显示器刷新率同步
      // 通过设置此项，我们表示纹理已更新
      canvasStuff.texture.needsUpdate = true;
      // 这在渲染期间是必需的 - 帧循环仅在需要时触发。
      // 我们必须调用 "invalidate()" 来触发新的帧循环。
      invalidate();
    },
    [canvasStuff.context, canvasStuff.texture, invalidate],
  );

  return (
    <>
      <Video src={videoSrc} onVideoFrame={onVideoFrame} muted headless />
      <mesh>
        <planeGeometry args={[planeWidth, planeHeight]} />
        <meshBasicMaterial color={0xffffff} toneMapped={false} map={canvasStuff.texture} />
      </mesh>
    </>
  );
};

export const RemotionMediaVideoTexture: React.FC = () => {
  const {width, height} = useVideoConfig();

  return (
    <ThreeCanvas style={{backgroundColor: 'white'}} linear width={width} height={height}>
      <Inner />
    </ThreeCanvas>
  );
};
```

### 注意事项

- 通过使用 [`headless`](/docs/media/video#headless) prop，`<Video>` 标签不会返回任何内容，因此可以在 [`<ThreeCanvas>`](/docs/three-canvas) 中挂载而不影响渲染。
- 对于预览和渲染，当纹理更新时需要使用不同的方法来使其失效。请参阅

## 示例

- 查看 [React Three Fiber 模板](/templates/three) 的源代码以获取实际示例。
- 上述示例可以在 [Remotion 测试平台](https://github.com/remotion-dev/remotion/tree/main/packages/example/src/VideoTexture.tsx) 中找到。

## 使用 `<OffthreadVideo>`

_已弃用，推荐使用 `@remotion/media`_

你可以使用 [`@remotion/three`](/docs/three-canvas) 中的 [`useOffthreadVideoTexture()`](/docs/use-offthread-video-texture) hook 从视频中获取纹理。

**缺点：**

- 它需要在可以提取帧之前将整个视频下载到磁盘
- 它在 [客户端渲染](/docs/client-side-rendering) 中不起作用
- 它为每一帧创建一个新纹理，这比使用 `@remotion/media` 效率低

因此，此 API **已弃用**，推荐使用上述推荐方法。

## 使用 `<Html5Video>`

_已弃用，推荐使用 `@remotion/media`_

你可以使用 [`@remotion/three`](/docs/three-canvas) 中的 [`useVideoTexture()`](/docs/use-video-texture) hook 从视频中获取纹理。

它具有 [`<Html5Video>`](/docs/video-tags) 标签的所有缺点，因此 **已弃用**，推荐使用上述推荐方法。

## 另请参阅

- [`@remotion/three`](/docs/three)
- [React Three Fiber 模板](/templates/three)
- [视频标签对比](/docs/video-tags)
- [`<Video>`→ `headless` prop](/docs/media/video)
