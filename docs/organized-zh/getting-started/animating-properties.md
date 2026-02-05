---
image: /generated/articles-docs-animating-properties.png
id: animating-properties
title: 动画属性
crumb: "基础概念"
---

import {AnimatingProperties, Springs} from '../components/DocsDark'

动画通过随时间改变属性来实现。
让我们创建一个简单的淡入动画。

如果我们想要在 60 帧内淡入文本，我们需要逐渐改变 `opacity`，使其从 0 到 1。

```tsx twoslash {4, 15} title="FadeIn.tsx"
import { AbsoluteFill, useCurrentFrame } from "remotion";
// ---cut---
export const FadeIn = () => {
  const frame = useCurrentFrame();

  const opacity = Math.min(1, frame / 60);

  return (
    <AbsoluteFill
      style={{
        justifyContent: "center",
        alignItems: "center",
        backgroundColor: "white",
        fontSize: 80,
      }}
    >
      <div style={{ opacity: opacity }}>Hello World!</div>
    </AbsoluteFill>
  );
};
```

<AnimatingProperties />

## 使用 interpolate 辅助函数

使用 [`interpolate()`](/docs/interpolate) 函数可以使动画更易读。上面的动画也可以写成：

```tsx twoslash
import { useCurrentFrame } from "remotion";
const frame = useCurrentFrame();
// ---cut---
import { interpolate } from "remotion";

const opacity = interpolate(frame, [0, 60], [0, 1], {
  /*                        ^^^^^   ^^^^^    ^^^^
  Variable to interpolate ----|       |       |
  Input range ------------------------|       |
  Output range -------------------------------|  */
  extrapolateRight: "clamp",
});
```

在这个例子中，我们将帧 0 到 60 映射到它们的不透明度值 `(0, 0.0166, 0.033, 0.05 ...`)，并使用 [`extrapolateRight`](/docs/interpolate#extrapolateright) 设置来限制输出，使其永远不会大于 1。

## 使用弹簧动画

弹簧动画是一种自然的动画原语。默认情况下，它们会随时间从 0 动画到 1。这次，让我们动画化文本的缩放。

```tsx twoslash {7-12, 20}
import { spring, useCurrentFrame, useVideoConfig } from "remotion";

export const MyVideo = () => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const scale = spring({
    fps,
    frame,
  });

  return (
    <div
      style={{
        flex: 1,
        textAlign: "center",
        fontSize: "7em",
      }}
    >
      <div style={{ transform: `scale(${scale})` }}>Hello World!</div>
    </div>
  );
};
```

你应该会看到文本跳进来。

<Springs />
<br />

默认的弹簧配置会导致一点过冲，意味着文本会稍微反弹。查看 [`spring()`](/docs/spring) 文档页面以了解如何自定义。

## 始终使用 `useCurrentFrame()` 来制作动画

注意渲染过程中可能出现的闪烁问题，如果你编写的动画不是由 [`useCurrentFrame()`](/docs/use-current-frame) 驱动的 - 例如 CSS 过渡。

[阅读更多关于 Remotion 渲染工作原理的信息](/docs/flickering) - 理解它将帮助你避免后续问题。
