---
image: /generated/articles-docs-using-randomness.png
id: using-randomness
sidebar_label: 随机性
title: 使用随机性
crumb: "掷骰子"
---

以下做法在 Remotion 中是一种反模式：

```tsx twoslash
import { useState } from "react";
// ---cut---
const MyComp: React.FC = () => {
  const [randomValues] = useState(() =>
    new Array(10).fill(true).map((a, i) => {
      return {
        x: Math.random(),
        y: Math.random(),
      };
    }),
  );
  // Do something with coordinates
  return <></>;
};
```

虽然在预览时这可以工作，但在渲染时会出错。原因是 Remotion 会启动多个网页实例来并行渲染帧，而随机值在每个实例上都会不同。

## 修复问题

使用 Remotion 的 [`random()`](/docs/random) API 来获取确定性的伪随机值。传入一个种子（数字或字符串），只要种子相同，返回值就会相同。

```tsx twoslash {5-6}
import { random } from "remotion";
const MyComp: React.FC = () => {
  // No need to use useState
  const randomValues = new Array(10).fill(true).map((a, i) => {
    return {
      x: random(`x-${i}`),
      y: random(`y-${i}`),
    };
  });

  return <></>;
};
```

现在随机值在所有线程上都会保持一致。

## 误报

你在使用 `Math.random()` 时收到了 ESLint 警告，但你完全了解上述情况？使用 `random(null)` 来获取真正的随机值而不会收到警告。

## 例外：在 `calculateMetadata()` 内部

在 [`calculateMetadata()`](/docs/calculate-metadata) 中使用真正的随机值是安全的，因为它只被调用一次，不会并行执行。

## 另请参阅

- [`random()`](/docs/random)
