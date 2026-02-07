---
image: /generated/articles-docs-noise-visualization.png
title: 噪声可视化
id: noise-visualization
crumb: "技术"
---

使用 [`@remotion/noise`](/docs/noise) 包，你可以为视频添加噪声效果。

## 噪声点阵演示

此示例展示了如何使用 [`noise3D()`](/docs/noise/noise-3d) 函数创建浮动的点阵"表面"。

- 第 1 和第 2 维度用于空间域。
- 第 3 维度用于时间域。

<Demo type="noise"/>

```tsx twoslash
import { noise3D } from "@remotion/noise";
import React from "react";
import { interpolate, useCurrentFrame, useVideoConfig } from "remotion";

const OVERSCAN_MARGIN = 100;
const ROWS = 10;
const COLS = 15;

const NoiseComp: React.FC<{
  speed: number;
  circleRadius: number;
  maxOffset: number;
}> = ({ speed, circleRadius, maxOffset }) => {
  const frame = useCurrentFrame();
  const { height, width } = useVideoConfig();

  return (
    <svg width={width} height={height}>
      {new Array(COLS).fill(0).map((_, i) =>
        new Array(ROWS).fill(0).map((__, j) => {
          const x = i * ((width + OVERSCAN_MARGIN) / COLS);
          const y = j * ((height + OVERSCAN_MARGIN) / ROWS);
          const px = i / COLS;
          const py = j / ROWS;
          const dx = noise3D("x", px, py, frame * speed) * maxOffset;
          const dy = noise3D("y", px, py, frame * speed) * maxOffset;
          const opacity = interpolate(
            noise3D("opacity", i, j, frame * speed),
            [-1, 1],
            [0, 1]
          );

          const key = `${i}-${j}`;

          return (
            <circle
              key={key}
              cx={x + dx}
              cy={y + dy}
              r={circleRadius}
              fill="gray"
              opacity={opacity}
            />
          );
        })
      )}
    </svg>
  );
};
```

## 另请参阅

- [`@remotion/noise`](/docs/noise)
