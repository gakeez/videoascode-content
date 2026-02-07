---
image: /generated/articles-docs-animation-math.png
title: 动画数学
crumb: "技术"
---

你可以通过加减乘除动画值来创建更复杂的动画。
考虑以下示例：

```tsx twoslash title="Enter and exit"
import { spring, useCurrentFrame, useVideoConfig } from "remotion";

const frame = useCurrentFrame();
const { fps, durationInFrames } = useVideoConfig();

const enter = spring({
  fps,
  frame,
  config: {
    damping: 200,
  },
});

const exit = spring({
  fps,
  config: {
    damping: 200,
  },
  durationInFrames: 20,
  delay: durationInFrames - 20,
  frame,
});

const scale = enter - exit;
```

- 在动画开始时，`enter` 的值为 `0`，它在动画过程中变为 `1`。
- 在序列结束之前，我们创建一个从 `0` 到 `1` 的 `exit` 动画。
- 从 `enter` 动画中减去 `exit` 动画，得到动画的整体状态，我们用它来动画化 `scale`。

<Demo type="animation-math" />

```tsx twoslash title="Full snippet"
import React from "react";
import {
  AbsoluteFill,
  spring,
  useCurrentFrame,
  useVideoConfig,
} from "remotion";

export const AnimationMath: React.FC = () => {
  const frame = useCurrentFrame();
  const { fps, durationInFrames } = useVideoConfig();

  const enter = spring({
    fps,
    frame,
    config: {
      damping: 200,
    },
  });

  const exit = spring({
    fps,
    config: {
      damping: 200,
    },
    durationInFrames: 20,
    delay: durationInFrames - 20,
    frame,
  });

  const scale = enter - exit;

  return (
    <AbsoluteFill
      style={{
        justifyContent: "center",
        alignItems: "center",
        backgroundColor: "white",
      }}
    >
      <div
        style={{
          height: 100,
          width: 100,
          backgroundColor: "#4290f5",
          borderRadius: 20,
          transform: `scale(${scale})`,
          justifyContent: "center",
          alignItems: "center",
          display: "flex",
          fontSize: 50,
          color: "white",
        }}
      >
        {frame}
      </div>
    </AbsoluteFill>
  );
};
```
