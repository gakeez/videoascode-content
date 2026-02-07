---
image: /generated/articles-docs-measuring.png
id: measuring
title: 测量 DOM 节点
sidebar_label: 测量元素
crumb: "操作指南"
---

如果你想测量一个 DOM 节点，你可以为其分配一个 [React Ref](https://react.dev/learn/manipulating-the-dom-with-refs)，然后使用 [`getBoundingClientRect()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect?retiredLocale=de) 浏览器 API 来获取节点的位置和大小。

在 Remotion 中，这并不那么容易，因为渲染视频的 `<div>` 元素应用了 `scale()` 变换，这会影响你从 `getBoundingClientRect()` 获取的值。

## 使用 `useCurrentScale()` 钩子进行测量

从 v4.0.111 开始，你可以使用 [`useCurrentScale()`](/docs/use-current-scale) 钩子来校正元素的尺寸。

```tsx twoslash title="MyComponent.tsx"
import { useCallback, useEffect, useState, useRef } from "react";
import { useCurrentScale } from "remotion";

export const MyComponent = () => {
  const ref = useRef<HTMLDivElement>(null);

  const [dimensions, setDimensions] = useState<{
    correctedHeight: number;
    correctedWidth: number;
  } | null>(null);

  const scale = useCurrentScale();

  useEffect(() => {
    if (!ref.current) {
      return;
    }

    const rect = ref.current.getBoundingClientRect();

    setDimensions({
      correctedHeight: rect.height / scale,
      correctedWidth: rect.width / scale,
    });
  }, [scale]);

  return (
    <div>
      <div ref={ref}>Hello World!</div>
    </div>
  );
};
```

## v4.0.110 之前的版本

为了获得准确的测量结果，你可以渲染一个固定宽度的额外元素（比如 `10px`）并同时测量它。然后，你可以将元素的宽度除以 `10` 来获得缩放因子。

```tsx twoslash title="MyComponent.tsx"
import { useCallback, useEffect, useState, useRef } from "react";

const MEASURER_SIZE = 10;

export const MyComponent = () => {
  const ref = useRef<HTMLDivElement>(null);
  const measurer = useRef<HTMLDivElement>(null);

  const [dimensions, setDimensions] = useState<{
    correctedHeight: number;
    correctedWidth: number;
  } | null>(null);

  useEffect(() => {
    if (!ref.current || !measurer.current) {
      return;
    }

    const rect = ref.current.getBoundingClientRect();
    const measurerRect = measurer.current.getBoundingClientRect();
    const scale = measurerRect.width / MEASURER_SIZE;

    setDimensions({
      correctedHeight: rect.height * scale,
      correctedWidth: rect.width * scale,
    });
  }, []);

  return (
    <div>
      <div ref={ref}>Hello World!</div>
      <div
        ref={measurer}
        style={{
          width: MEASURER_SIZE,
          position: "fixed",
          top: -99999,
        }}
      />
    </div>
  );
};
```

### 示例项目

- [源代码](https://github.com/remotion-dev/measure-item)
- [预览](https://measure-item.vercel.app)

## v4.0.103 之前的版本

在 Remotion 的早期版本中，由于挂载了组件但没有显示它，`getBoundingClientRect()` 可能在第一个 `useEffect()` 中返回所有值都为 `0` 的尺寸。

今后，你可以依赖尺寸为非零值。

## 另请参阅

- [react-use-measure](https://github.com/pmndrs/react-use-measure) - 在滚动容器内正确测量元素
