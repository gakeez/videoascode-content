---
image: /generated/articles-docs-the-fundamentals.png
id: the-fundamentals
title: 基础概念
crumb: 入门指南
---

## React 组件

```twoslash include example
import { AbsoluteFill, useCurrentFrame } from "remotion";

export const MyComposition = () => {
  const frame = useCurrentFrame();

  return (
    <AbsoluteFill
      style={{
        justifyContent: "center",
        alignItems: "center",
        fontSize: 100,
        backgroundColor: "white",
      }}
    >
      The current frame is {frame}.
    </AbsoluteFill>
  );
};
// - MyComposition
```

Remotion 的理念是给你一个帧号和一个空白画布，你可以使用 [React](https://react.dev) 渲染任何你想要的内容。这是一个示例 React 组件，将当前帧渲染为文本：

```tsx twoslash title="MyComposition.tsx"
// @include: example-MyComposition
```

视频是图像随时间变化的函数。如果你每帧都改变内容，最终会得到一个动画。

## 视频属性

视频有 4 个属性：

- `width`：像素宽度。
- `height`：像素高度。
- `durationInFrames`：视频的总帧数。
- `fps`：视频的帧率。

你可以通过 [`useVideoConfig()`](/docs/use-video-config) hook 获取这些值：

```tsx twoslash
import {AbsoluteFill, useVideoConfig} from 'remotion';

export const MyComposition = () => {
  const {fps, durationInFrames, width, height} = useVideoConfig();

  return (
    <AbsoluteFill
      style={{
        justifyContent: 'center',
        alignItems: 'center',
        fontSize: 60,
        backgroundColor: 'white',
      }}
    >
      This {width}x{height}px video is {durationInFrames / fps} seconds long.
    </AbsoluteFill>
  );
};
```

:::note
视频的第一帧是 `0`，最后一帧是 `durationInFrames - 1`。
:::

## 合成（Compositions）

合成是 React 组件和视频元数据的组合。

通过在 `src/Root.tsx` 中渲染 [`<Composition>`](/docs/composition) 组件，你可以注册一个可渲染的视频并使其在 Remotion 侧边栏中可见。

```tsx twoslash title="src/Root.tsx"
import {Composition} from 'remotion';
// @include: example-MyComposition
// ---cut---

export const RemotionRoot: React.FC = () => {
  return (
    <Composition
      id="MyComposition"
      durationInFrames={150}
      fps={30}
      width={1920}
      height={1080}
      component={MyComposition}
    />
  );
};
```

:::note
你可以在 `src/Root.tsx` 中注册多个合成，方法是将它们包裹在 React Fragment 中：
`<><Composition/><Composition/></>`。另请参阅：[如何组合多个合成？](/docs/miscellaneous/snippets/combine-compositions)
:::
